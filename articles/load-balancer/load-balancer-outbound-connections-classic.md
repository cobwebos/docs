---
title: Azure 中的出站连接（经典）| Microsoft Docs
description: 本文介绍 Azure 如何让云服务与公共 Internet 服务通信。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: ec13109173f89b53e32f903febcec13c7f38c574
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="outbound-connections-classic"></a>出站连接（经典）

Azure 通过多种不同的机制为客户部署提供出站连接。 本文介绍具体的方案、其应用方式、工作原理以及管理方式。

>[!NOTE]
>本文仅介绍经典部署。  有关 Azure 中的所有资源管理器部署方案，请查看[出站连接](load-balancer-outbound-connections.md)。

Azure 中的部署可与 Azure 外部的公用 IP 地址空间中的终结点进行通信。 当实例启动到公共 IP 地址空间中的目标的出站流时，Azure 会动态将专用 IP 地址映射到公共 IP 地址。 创建此映射后，此出站发起流的返回流量还可以抵达发起流的专用 IP 地址。

Azure 使用源网络地址转换 (SNAT) 来执行此功能。 当多个专用 IP 地址伪装成单个公共 IP 地址时，Azure 将使用[端口地址转换 (PAT)](#pat) 来伪装专用 IP 地址。 临时端口用于 PAT，是基于池大小[预先分配](#preallocatedports)的。

有多种[出站方案](#scenarios)。 可根据需要结合这些方案。 请认真分析这些方案，以了解在部署模型和应用方案中应用这些方案时的功能、约束和模式。 查看有关[管理这些方案](#snatexhaust)的指导。

## <a name="scenarios"></a>方案概述

Azure 提供三种不同的方法来实现出站连接经典部署。  并非所有经典部署都可使用这三种方案：

| 场景 | 方法 | 说明 | Web 辅助角色 | IaaS | 
| --- | --- | --- | --- | --- |
| [1.具有实例级公共 IP 地址的 VM](#ilpip) | SNAT，不使用端口伪装 |Azure 使用分配了公共 IP 的虚拟机。 此实例具有所有可用的临时端口。 | 否 | 是 |
| [2. 公共负载均衡终结点](#publiclbendpoint) | 使用端口伪装 (PAT) 通过 SNAT 连接到公共终结点 |Azure 与多个专用终结点共享公共 IP 地址公共终结点。 Azure 使用公共终结点的临时端口进行 PAT。 | 是 | 是 |
| [3.独立 VM](#defaultsnat) | 使用端口伪装 (PAT) 的 SNAT | Azure 自动指定用于 SNAT 的公共 IP 地址，与整个部署共享此公共 IP 地址，并使用公共终结点 IP 地址的临时端口进行 PAT。 此方案是前述方案的回退方案。 如果需要可见性和控制，则我们不建议采用。 | 是 | 是|

这是适用于 Azure 中资源管理器部署的一部分出站连接功能。  

经典模型中的不同部署具有不同的功能：

| 经典部署 | 可用的功能 | 
| --- | --- |
| 虚拟机 | 方案 [1](#ilpip)、[2](#publiclbendpoint) 或 [3](#defaultsnat) |
| Web 辅助角色 | 仅限方案 [2](#publiclbendpoint)、[3](#defaultsnat) | 

[缓解策略](#snatexhaust)也存在相同的差异。

用于在经典部署中进行 PAT 的[临时端口预先分配算法](#ephemeralports)与 Azure 资源管理器资源部署相同。

### <a name="ilpip"></a>方案 1：具有实例级公共 IP 地址的 VM

在此场景中，向 VM 分配了实例级公共 IP (ILPIP)。 就出站连接而言，VM 是否包含负载均衡终结点并不重要。 此方案优先于其他方案。 使用 ILPIP 时，VM 将 ILPIP 用于所有出站流。  

分配到 VM 的公共 IP 属于 1 对 1 关系（而不是 1 对多关系），并作为无状态的 1 对 1 NAT 进行实现。  不使用端口伪装 (PAT)，并且 VM 具有所有可供使用的临时端口。

如果应用程序启动很多出站流，并且遇到 SNAT 端口耗尽的情况，可以考虑分配 [ILPIP 以缓解 SNAT 约束](#assignilpip)。 请查看[管理 SNAT 耗尽](#snatexhaust)。

### <a name="publiclbendpoint"></a>方案2：公共负载均衡终结点

在此方案中，VM 或 Web 辅助角色通过负载均衡的终结点来与公共 IP 地址关联。 没有分配给 VM 的公共 IP 地址。 

当负载均衡的 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共负载均衡终结点的公共 IP 地址。 Azure 使用 (SNAT) 来执行此功能。 Azure 还使用 [PAT](#pat) 来伪装公共 IP 地址后面的多个专用 IP 地址。 

使用负载均衡器的公共 IP 地址前端的临时端口区分由 VM 产生的各个流。 创建出站流后，SNAT 动态使用[预先分配的临时端口](#preallocatedports)。 在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。

SNAT 端口是根据[了解 SNAT 和 PAT](#snat) 部分中所述预先分配的。 它们是可能会耗尽的有限资源。 因此了解它们的[使用](#pat)方式很重要。 请查看[管理 SNAT 耗尽](#snatexhaust)，了解如何根据需要进行设计和缓解。

如果存在[多个公共负载均衡终结点](load-balancer-multivip.md)，则其中的所有公共 IP 地址都是[出站流的候选项](#multivipsnat)，并且会随机选择其中的一个。  

### <a name="defaultsnat"></a>方案 3：没有关联的公共 IP 地址

在此方案中，VM 或 Web 辅助角色不属于公共负载均衡终结点。  VM 上没有分配 ILPIP 地址。 当 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共源 IP 地址。 用于此出站流的公共 IP 地址是不可配置的，并且不会影响订阅的公共 IP 资源限制。  Azure 会自动分配此地址。

Azure 结合端口伪装 ([PAT](#pat)) 使用 SNAT 来执行此功能。 此方案类似于[方案 2](#lb)，但无法控制使用的 IP 地址。 这是方案 1 和方案 2 不存在时的回退方案。 如果需要控制出站地址，则我们不建议使用此方案。 如果出站连接是应用程序的关键部分，应该选择另一种方案。

SNAT 端口是根据[了解 SNAT 和 PAT](#snat) 部分中所述预先分配的。  共享公共 IP 地址的 VM 或 Web 辅助角色数目确定了预分配的临时端口数。   因此了解它们的[使用](#pat)方式很重要。 请查看[管理 SNAT 耗尽](#snatexhaust)，了解如何根据需要进行设计和缓解。

## <a name="snat"></a>了解 SNAT 和 PAT

### <a name="pat"></a>端口伪装 SNAT (PAT)

当部署建立出站连接时，将会重写每个出站连接源。 源从专用 IP 地址空间重写到与部署关联的公共 IP（基于上述方案）。 在公共 IP 地址空间中，流的 5 元组（源 IP 地址、源端口、IP 转换协议、目标 IP 地址、目标端口）必须唯一。  

重写专用源 IP 地址后，临时端口（SNAT 端口）用于实现此目的，因为多个流源自单个公共 IP 地址。 

每个到单个目标 IP 地址、端口和协议的流使用一个 SNAT 端口。 对于到相同的目标 IP 地址、端口和协议的多个流，每个流使用一个 SNAT 端口。 这可以确保源自相同的公共 IP 地址，并到相同的目标 IP 地址、端口和协议的流的唯一性。 

每个流均流到不同目标 IP 地址、端口和协议的多个流共用一个 SNAT 端口。 目标 IP 地址、端口和协议使流保持唯一，无需使用其他源端口来区分公共 IP 地址空间中的流。

如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 当流关闭时，负载均衡器将回收 SNAT 端口，并使用 [4 分钟空闲超时](#idletimeout)回收空闲流中的 SNAT 端口。

有关可以使用哪些模式来缓解通常导致 SNAT 端口耗尽的状态，请查看[管理 SNAT](#snatexhaust) 部分。

### <a name="preallocatedports"></a>端口伪装 SNAT (PAT) 的临时端口预先分配

使用端口伪装 SNAT ([PAT](#pat)) 时，Azure 使用某种算法根据后端池的大小来确定可用的预先分配 SNAT 端口数目。 SNAT 端口是可用于特定公共 IP 源地址的临时端口。

部署实例时，Azure 根据共享给定公共 IP 地址的 VM 或 Web 辅助角色实例数目预分配 SNAT 端口。  创建出站流后，当流关闭或[空闲超时](#ideltimeout)时，[PAT](#pat) 动态使用（不超过预先分配的限制）和释放这些端口。

下表显示了针对后端池大小层的 SNAT 端口预分配：

| Instances | 每个实例的预分配 SNAT 端口数 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

请记住，可用的 SNAT 端口数不会直接转换为流数。 可以针对多个唯一目标重用单个 SNAT 端口。 仅当需要使流保持唯一时，才使用端口。 有关设计和缓解指导，请参阅[如何管理这项可耗尽的资源](#snatexhaust)；另请参阅介绍 [PAT](#pat) 的部分。

更改部署大小可能会影响建立的某些流。 如果后端池大小递增并转换为下一层，则在转换为下一个更大的后端池层期间，一半的预先分配 SNAT 端口将被回收。 与回收的 SNAT 端口关联的流会超时，必须重新建立连接。 如果尝试新流，则只要预先分配的端口可用，则该流就能立即成功。

如果部署减小并转换到更低的层，可用的 SNAT 端口数会增多。 在这种情况下，现有的分配 SNAT 端口及其相应的流不会受到影响。

SNAT 端口分配特定于 IP 传输协议（TCP 和 UDP 是分别维护的），并在以下条件下释放：

### <a name="tcp-snat-port-release"></a>TCP SNAT 端口释放

- 如果两个服务器/客户端均发送 FIN/ACK，则 SNAT 端口在 240 秒后释放。
- 如果出现 RST，则 SNAT 端口在 15 秒后释放。
- 已达到空闲超时

### <a name="udp-snat-port-release"></a>UDP SNAT 端口释放

- 已达到空闲超时

## <a name="problemsolving"></a>解决问题 

本部分旨在帮助解决 SNAT 耗尽的问题，以及 Azure 中的出站连接可能出现的其他情况。

### <a name="snatexhaust"></a>应对 SNAT (PAT) 端口耗尽问题
根据[没有关联的公共 IP](#defaultsnat) 和[公共负载均衡终结点](#publiclbendpoint)中所述，用于 [PAT](#pat) 的[临时端口](#preallocatedports)是可耗尽的资源。

如果知道正在启动与同一目标 IP 地址和端口的多个出站 TCP 或 UDP 连接，观察失败的出站连接，或者支持人员通知已耗尽 SNAT 端口（[PAT](#pat) 使用的预先分配[临时端口](#preallocatedports)），则有几个常见缓解选项可供选择。 查看这些选项，确定可用且最适合自己的方案的选项。 一个或多个选项可能有助于管理此方案。

如果难以理解出站连接行为，可以使用 IP 堆栈统计 (netstat)。 或者使用数据包捕获来观察连接行为。

#### <a name="connectionreuse"></a>修改应用程序以重复使用连接 
在应用程序中重复使用连接，可以降低对用于 SNAT 的临时端口的需求。 这尤其适用于 HTTP/1.1 这样的协议，在这些协议中，默认重复使用连接。 其他使用 HTTP 作为其传输（如 REST）的协议也可以因此受益。 

对每个请求来说，重复使用总是优于单独的原子 TCP 连接。 重复使用可以带来更高的性能和非常高效的 TCP 事务。

#### <a name="connection pooling"></a>修改应用程序以使用连接池
可以在应用程序中使用连接池方案，其中在一组固定的连接（在可能的情况下，重复使用每一个）上，内部分布请求。 此方案会限制正在使用的临时端口的数量，并创建更加可预测的环境。 此方案还可以通过在操作答复上单个连接阻塞时允许多个同时操作，增加请求的吞吐量。  

连接池可能已经存在于正在使用的框架中，以开发应用程序或应用程序的配置设置。 可将连接池与连接重复使用相结合。 多个请求使用指向相同目标 IP 地址和端口的可预测固定数量的端口。 请求还可以从 TCP 事务的高效使用中受益，从而减少延迟和资源利用。 UDP 事务也能从中受益，因为管理 UDP 流数又能避免耗尽情况和管理 SNAT 端口利用率。

#### <a name="retry logic"></a>修改应用程序以使用主动性较低的重试逻辑
当用于 [PAT](#pat) 的[预先分配临时端口](#preallocatedports)耗尽或应用程序故障发生时，无衰减或回退逻辑的积极重试或暴力重试会使耗尽状况再次发生或一直持续。 使用主动性较低的重试逻辑，可以降低对临时端口的需求。 

临时端口有 4 分钟的空闲超时（不可调整）。 如果重试太过积极，则消耗没有机会进行自行清除。 因此，应用程序停用事务的方式和频率对于设计至关重要。

#### <a name="assignilpip"></a>将实例级公共 IP 分配给每个 VM
分配 ILPIP 会将方案更改为 [VM 的实例级公共 IP](#ilpip)。 用于各 VM 的公共 IP 的所有临时端口都可供 VM 使用。 （与以下方案相反：公共 IP 的临时端口与同相应部署关联的 VM 的所有临时端口共享。）需要作出一些权衡，例如将大量个人 IP 地址列入白名单所产生的潜在影响。

>[!NOTE] 
>此选项不适用于 Web 辅助角色。

### <a name="idletimeout"></a>保持 keepalive 重置出站空闲超时

出站连接有 4 分钟的空闲超时。 此超时不可调整。 但是，可以根据需要使用传输（例如 TCP keepalive）或应用层 keepalive 来刷新空闲流和重置此空闲超时。  请咨询任何打包软件的供应商，了解是否支持此功能，或者如何启用此功能。  通常，只需在一端生成 keepalive 就能重置空闲超时。 

## <a name="discoveroutbound"></a>发现 VM 使用的公共 IP
有多种方法来确定出站连接的公共源 IP 地址。 OpenDNS 提供了一种服务可以向你显示 VM 的公共 IP 地址。 

使用 nslookup 命令，可以将名称 myip.opendns.com 的 DNS 查询发送到 OpenDNS 解析程序。 该服务返回用于发送此查询的源 IP 地址。 在 VM 中运行以下查询时，返回的是用于该 VM 的公共 IP。

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>后续步骤

- 详细了解资源管理器部署中使用的[负载均衡器](load-balancer-overview.md)。
- 深入了解资源管理器部署中提供的[出站连接](load-balancer-outbound-connections.md)方案。
