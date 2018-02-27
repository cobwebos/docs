---
title: "了解 Azure 中的出站连接 | Microsoft Docs"
description: "本文介绍了 Azure 如何使 VM 与公共 Internet 服务通信。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: kumud
ms.openlocfilehash: e1a2b4c281194ec4c70e4d9fe1bc97c5aa61436e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="understanding-outbound-connections-in-azure"></a>了解 Azure 中的出站连接

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]


Azure 通过多种不同的机制为客户部署提供出站连接。  本文介绍具体的方案、其应用方式、工作原理以及管理方式。

Azure 中的部署可与 Azure 外部的公用 IP 地址空间中的终结点进行通信。 当实例启动到公共 IP 地址空间中的目标的出站流时，Azure 会动态将专用 IP 地址映射到公共 IP 地址。  创建此映射后，此出站发起流的返回流量还可以抵达发起流的专用 IP 地址。

Azure 使用源网络地址转换 (SNAT) 来执行此功能。  当多个专用 IP 地址伪装成单个公共 IP 地址时，Azure 将使用[端口地址转换 (PAT)](#pat) 来伪装专用 IP 地址。  临时端口用于 PAT，是基于池大小[预先分配](#preallocatedports)的。

有多种[出站方案](#scenarios)。 可根据需要结合这些方案。 请认真分析这些方案，以了解在部署模型和应用方案中应用这些方案时的功能、约束和模式。  查看有关[管理这些方案](#snatexhaust)的指导。

## <a name="scenarios"></a>方案概述

Azure 主要有两个部署模型：Azure 资源管理器模型和经典模型。 负载均衡器和相关资源是使用 [Azure 资源管理器资源](#arm)时显式定义的。  经典部署将负载均衡器的概念抽象化，并通过[云服务](#classic)的终结点定义表示类似的功能。 适用于部署的[方案](#scenarios)取决于所用的部署模型。

### <a name="arm"></a>Azure 资源管理器

Azure 目前提供三种不同的方法实现 Azure 资源管理器资源的出站连接。  [经典](#classic)部署包含其中的一部分方案。

| 方案 | 方法 | 说明 |
| --- | --- | --- |
| [1.具有实例级公共 IP 地址的 VM（有或没有负载均衡器）](#ilpip) | SNAT，不使用端口伪装 |Azure 使用分配实例 NIC 的 IP 配置的公共 IP。  此实例具有所有可用的临时端口。 |
| [2.与 VM 关联的公共负载均衡器（实例上没有实例级公共 IP 地址）](#lb) | 使用负载均衡器前端进行端口伪装 (PAT) 的 SNAT |Azure 与多个专用 IP 地址共享公共负载均衡器前端的公共 IP 地址，并使用前端的临时端口进行 PAT。 |
| [3.独立 VM（无负载均衡器，无实例级公共 IP 地址）](#defaultsnat) | 使用端口伪装 (PAT) 的 SNAT | Azure 自动指定用于 SNAT 的公共 IP 地址，与可用性集的多个专用 IP 地址共享此公共 IP 地址，并使用此公共 IP 地址的临时端口。  此方案是前面方案 1 和 2 的回退方案，如果需要可见性和控制，则我们不建议采用。 |

如果不希望 VM 与 Azure 外部的公共 IP 地址空间中的终结点通信，则可以根据需要使用网络安全组 (NSG) 来阻止访问。  [阻止出站连接](#preventoutbound)中详细介绍了 NSG 的使用。  本文不会介绍有关在无任何出站访问权限的情况下，如何设计和管理虚拟网络的设计和实施指导。

### <a name="classic"></a>经典（云服务）

适用于经典部署的方案是适用于 [Azure 资源管理器](#arm)部署和负载均衡器基本版的一部分方案。

经典虚拟机具有针对 Azure 资源管理器资源所述的相同三种基本方案（[1](#ilpip)、[2](#lb)、[3](#defaultsnat)）。 经典 Web 辅助角色只有两种方案（[2](#lb)、[3](#defaultsnat)）。  [缓解策略](#snatexhaust)也存在相同的差异。

用于在经典部署中进行 PAT 的[临时端口预先分配](#ephemeralprots)算法与 Azure 资源管理器资源部署相同。  

### <a name="ilpip"></a>方案 1：具有实例级公共 IP 地址的 VM

在此场景中，向 VM 分配了实例级公共 IP (ILPIP)。 就出站连接而言，VM 是否经过负载均衡并不重要。  此方案优先于其他方案。 使用 ILPIP 时，VM 将 ILPIP 用于所有出站流。  

不使用端口伪装 (PAT)，并且 VM 具有所有可供使用的临时端口。

如果应用程序启动很多出站流，并且遇到 SNAT 端口耗尽的情况，可以考虑分配 [ILPIP 以缓解 SNAT 约束](#assignilpip)。 请查看[管理 SNAT 耗尽](#snatexhaust)。

### <a name="lb"></a>方案 2：无实例级公共 IP 地址的负载均衡 VM

在此方案中，VM 是公共负载均衡器池的一部分。  没有分配给 VM 的公共 IP 地址。 必须为负载均衡器资源配置一个负载均衡器规则，以在公共 IP 前端与后端池之间创建链接。 如果没有完成此规则配置，则行为将如适用于[没有实例级公共 IP 的独立 VM](#defaultsnat) 方案中所述。  不需要在规则中添加后端池的正常运行的侦听器或者运行状况探测就能成功实现。

当负载均衡的 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共负载均衡器前端的公共 IP 地址。 Azure 使用源网络地址转换 (SNAT) 来执行此功能，并使用[端口地址转换 (PAT)](#pat) 来伪装公共 IP 地址后面的多个专用 IP 地址。 使用负载均衡器的公共 IP 地址前端的临时端口区分由 VM 产生的各个流。 创建出站流后，SNAT 动态使用[预先分配的临时端口](#preallocatedports)。 在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。

SNAT 端口是根据[了解 SNAT 和 PAT](#snat) 部分中所述预先分配的，是可能会耗尽的有限资源。 因此了解它们的[使用](#pat)方式很重要。 请查看[管理 SNAT 耗尽](#snatexhaust)，了解如何根据需要进行设计和缓解。

如果[多个（公共）IP 地址与一个负载均衡器基本版相关联](load-balancer-multivip-overview.md)，则所有这些公共 IP 地址都是[出站流的候选项，并且会选择其中的一个](#multivipsnat)。  

可使用[用于负载均衡器的 Log Analytics](load-balancer-monitor-log.md) 和[要监视 SNAT 端口耗尽消息的警报事件日志](load-balancer-monitor-log.md#alert-event-log)来监视负载均衡器基本版的出站连接运行状况。

### <a name="defaultsnat"></a>方案 3：无实例级公共 IP 地址的独立 VM

VM 不是 Azure 负载均衡器池的一部分，并且没有分配给它的实例级公共 IP (ILPIP) 地址。 当 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共源 IP 地址。 用于此出站流的公共 IP 地址是不可配置的，并且不会影响订阅的公共 IP 资源限制。 Azure 结合端口伪装 ([PAT](#pat)) 使用源网络地址转换 (SNAT) 来执行此功能。 此方案类似于[方案 2](#lb)，但无法控制使用的 IP 地址。  这是方案 1 和方案 2 不存在时的回退方案。  如果需要控制出站地址，则不建议使用此方案。

SNAT 端口是根据[了解 SNAT 和 PAT](#snat) 部分中所述预先分配的，是可能会耗尽的有限资源。 因此了解它们的[使用](#pat)方式很重要。 请查看[管理 SNAT 耗尽](#snatexhaust)，了解如何根据需要进行设计和缓解。

### <a name="combinations"></a>多个组合方案

可以结合前面部分中所述的方案来实现特定的效果。  存在多个方案时，优先顺序如下：[方案 1](#ilpip) 优先于[方案 2](#lb)和 [3](#defaultsnat)（仅限 Azure 资源管理器），[方案 2](#lb) 覆盖[方案 3](#defaultsnat)（Azure 资源管理器和经典）。

例如，在 Azure 资源管理器部署中，应用程序严重依赖于与有限数量的目标建立出站连接，但也通过负载均衡器前端接收入站流。 在此情况下，可以结合方案 1 和 2 来缓解问题。  请查看[管理 SNAT 耗尽](#snatexhaust)中所述的其他模式。

### <a name="multivipsnat"></a>对出站流使用多个前端

当[多个（公共）IP 前端](load-balancer-multivip-overview.md)适用于出站流时，负载均衡器基本版将选择单个前端用于出站流。  此项选择不可配置，应将选择算法视为随机。  可以根据[组合方案](#combinations)中所述，为出站流指定特定的 IP 地址。

## <a name="snat"></a>了解 SNAT 和 PAT

### <a name="pat"></a>端口伪装 SNAT (PAT)

公共负载均衡器资源与 VM 实例相关联时，将重写每个出站连接源。 出站连接源从虚拟网络专用 IP 地址空间重新写入负载均衡器的前端公共 IP 地址。 在公共 IP 地址空间中，流的 5 元组（源 IP 地址、源端口、IP 转换协议、目标 IP 地址、目标端口）必须唯一。  重写专用源 IP 地址后，临时端口用于实现此目的，因为多个流源自单个公共 IP 地址。  这些临时端口称为 SNAT 端口。 

每个到单个目标 IP 地址、端口和协议的流使用一个 SNAT 端口。 对于到相同的目标 IP 地址、端口和协议的多个流，每个流使用一个 SNAT 端口。 这可以确保源自相同的公共 IP 地址，并到相同的目标 IP 地址、端口和协议的流的唯一性。 

每个流均流到不同目标 IP 地址、端口和协议的多个流共用一个 SNAT 端口。 目标 IP 地址、端口和协议使流保持唯一，无需使用其他源端口来区分公共 IP 地址空间中的流。

如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 当流关闭时，负载均衡器将回收 SNAT 端口，并使用 [4 分钟空闲超时](#idletimeout)回收空闲流中的 SNAT 端口。

请查看[管理 SNAT](#snatexhaust) 部分，其中介绍了可以使用哪些模式来缓解通常导致 SNAT 端口耗尽的状态。

### <a name="preallocatedports"></a>端口伪装 SNAT (PAT) 的临时端口预先分配

使用端口伪装 SNAT ([PAT](#pat)) 时，Azure 使用某种算法根据后端池的大小来确定可用的预先分配 SNAT 端口数目。  SNAT 端口是可用于给定公共 IP 源地址的临时端口。

Azure 向每个 VM 的 NIC IP 配置预先分配 SNAT 端口。 将 IP 配置添加到池后，将会根据后端池的大小预先分配此 IP 配置的 SNAT 端口。 对于经典 Web 辅助角色，将会根据每个角色实例进行分配。  创建出站流后，当流关闭或[空闲超时](#ideltimeout)时，[PAT](#pat) 动态使用（不超过预先分配的限制）和释放这些端口。

下表显示了针对后端池大小层的 SNAT 端口预分配：

| 池大小（VM 实例） | 每个 IP 配置的预先分配 SNAT 端口|
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

请务必记住，可用的 SNAT 端口数不会直接转换为连接数。 可以针对多个唯一目标重用单个 SNAT 端口。 仅当需要使流保持唯一时，才使用端口。 请参阅[如何管理这项可耗尽的资源](#snatexhaust)，其中介绍了设计和缓解指导；另请参阅介绍 [PAT](#pat) 的部分。

更改后端池大小可能会影响建立的某些流。 如果后端池大小递增并转换为下一层，则在转换为下一个更大的后端池层期间，一半的预先分配 SNAT 端口将被回收。 与回收的 SNAT 端口关联的流会超时，必须重新建立连接。 只要预先分配的端口可用，新的连接尝试就能立即成功。

如果后端池减小并转换到更低层级，可用的 SNAT 端口数会增多。 在这种情况下，现有的分配 SNAT 端口及其相应的流不会受到影响。

## <a name="snatexhaust"></a>管理 SNAT (PAT) 端口耗尽

用于 [PAT](#pat) 的[临时端口](#preallocatedports)是可用尽的资源，如[无实例级公共 IP 地址的独立 VM](#defaultsnat) 和[无实例级公共 IP 地址的负载均衡 VM](#lb) 中所述。

如果知道正在启动与同一目标 IP 地址和端口的多个出站 TCP 或 UDP 连接，观察失败的出站连接，或者支持人员通知已耗尽 SNAT 端口（[PAT](#pat) 使用的预先分配[临时端口](#preallocatedports)），则有几个常见缓解选项可供选择。  查看这些选项，确定可用且最适合自己的方案的选项。  一个或多个选项可能有助于管理这种场景。

如果难以理解出站连接行为，可以使用 IP 堆栈统计 (netstat)，或者使用数据包捕获来观察连接行为。  可以在实例的来宾 OS 中执行这些数据包捕获，或使用[网络观察程序来捕获数据包](../network-watcher/network-watcher-packet-capture-manage-portal.md)。

### <a name="connectionreuse"></a>修改应用程序以重复使用连接 
在应用程序中重复使用连接，可以降低对用于 SNAT 的临时端口的需求。  这尤其适用于 HTTP/1.1 这样的协议，在这些协议中，默认重复使用连接。  其他使用 HTTP 作为其传输（如 REST）的协议也可以因此受益。  对每个请求来说，重复使用总是优于单独的原子 TCP 连接，并且会导致更高性能和非常高效的 TCP 事务。

### <a name="connection pooling"></a>修改应用程序以使用连接池
可以在应用程序中使用连接池方案，其中在一组固定的连接（在可能的情况下，重复使用每一个）上，内部分布请求。  这会限制正在使用的临时端口的数量，并创建更加可预测的环境。  还可以通过在操作答复上单个连接阻塞时允许多个同时操作，增加请求的吞吐量。  连接池可能已经存在于正在使用的框架中，以开发应用程序或应用程序的配置设置。  可将它与连接重复使用相结合，并且多个请求使用指向相同目标 IP 地址和端口的可预测固定数量的端口，同时还可以从 TCP 事务的高效使用中受益，从而减少延迟和资源利用。  UDP 事务也能从中受益，因为管理 UDP 流数又能避免耗尽情况和管理 SNAT 端口利用率。

### <a name="retry logic"></a>修改应用程序以使用主动性较低的重试逻辑
当用于 [PAT](#pat) 的[预先分配临时端口](#preallocatedports)耗尽或应用程序故障发生时，无衰减或回退逻辑的积极重试或暴力重试会使耗尽状况再次发生或一直持续。 使用主动性较低的重试逻辑，可以降低对临时端口的需求。   临时端口有 4 分钟的空闲超时（不可调整），如果重试太过积极，则消耗没有机会进行自行清除。  因此，应用程序停用事务的方式和频率对于设计至关重要。

### <a name="assignilpip"></a>将实例级公共 IP 分配给每个 VM
这会场景更改为 [VM 的实例级公共 IP](#ilpip)。  用于各 VM 的公共 IP 的所有临时端口都可供 VM 使用（与以下场景相反：公共 IP 的临时端口与同相应后端池关联的 VM 的所有临时端口共享）。  需要作出一些权衡，比如公共 IP 地址的额外成本和将大量个人 IP 地址列入白名单所产生的潜在影响。

>[!NOTE] 
>此选项不适用于 Web 辅助角色。

### <a name="idletimeout"></a>保持 keepalive 重置出站空闲超时

出站连接有 4 分钟的空闲超时。 这是不可调整的。 但是，可以根据需要使用传输（例如 TCP keepalive）或应用层 keepalive 来刷新空闲流和重置此空闲超时。

## <a name="discoveroutbound"></a>发现指定 VM 所使用的公共 IP
有多种方法来确定出站连接的公共源 IP 地址。 OpenDNS 提供了一种服务可以向你显示 VM 的公共 IP 地址。 使用 nslookup 命令，可以将名称 myip.opendns.com 的 DNS 查询发送到 OpenDNS 解析程序。 该服务返回用于发送此查询的源 IP 地址。 在 VM 中执行以下查询时，返回的是用于该 VM 的公共 IP。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>阻止出站连接
有时允许 VM 创建出站流是不可取的，或者可能需要管理哪些目标可以通过出站流访问或哪些目标可以启动入站流。 在此情况下，使用[网络安全组 (NSG)](../virtual-network/virtual-networks-nsg.md) 管理 VM 可以访问的目标以及可以启动入站流的公共目标。 将 NSG 应用于负载均衡的 VM 时，需要注意[默认标记](../virtual-network/virtual-networks-nsg.md#default-tags)和[默认规则](../virtual-network/virtual-networks-nsg.md#default-rules)。

必须确保 VM 可以接收来自 Azure 负载均衡器的运行状况探测请求。 如果 NSG 阻止来自 AZURE_LOADBALANCER 默认标记的运行状况探测请求，那么 VM 的运行状况探测程序会失败，并且 VM 被标记为停机。 负载均衡器停止向此 VM 发送新流。

## <a name="next-steps"></a>后续步骤

- 详细了解[负载均衡器基本版](load-balancer-overview.md)。
- 详细了解[网络安全组](../virtual-network/virtual-networks-nsg.md)。
- 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。
