---
title: Azure 中的出站连接
titleSuffix: Azure Load Balancer
description: 本文介绍 Azure 如何使 VM 与公共 Internet 服务通信。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/24/2020
ms.author: allensu
ms.openlocfilehash: 4368a025ecc158afa1ee78b8abd86bd6db42ba75
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438659"
---
# <a name="outbound-connections-in-azure"></a>Azure 中的出站连接

Azure 负载均衡器可通过各种机制提供出站连接。 本文介绍了各种方案以及如何管理它们。 如果通过 Azure 负载均衡器进行出站连接时遇到问题，请参阅[出站连接故障排除指南](../load-balancer/troubleshoot-outbound-connection.md)。

>[!NOTE]
>本文涵盖了资源管理器部署。 Microsoft 建议资源管理器用于生产工作负荷。

## <a name="terminology"></a>术语

| 术语 | 适用的协议 | 详细信息|
| ---------|---------| -------|
| 源网络地址转换 (SNAT) | TCP、UDP | Azure 中的部署可与 Azure 外部的公共 IP 地址空间中的终结点进行通信。 当实例启动到公共 IP 地址空间中的目标的出站流时，Azure 会动态将专用 IP 地址映射到公共 IP 地址。 创建此映射后，此出站发起流的返回流量还可以抵达发起流的专用 IP 地址。 Azure 使用**源网络地址转换 (SNAT)** 来执行此功能。|
| 端口伪装 SNAT (PAT)| TCP、UDP |  当多个专用 IP 地址伪装成单个公共 IP 地址时，Azure 将使用**端口地址转换 (PAT)** 来伪装/隐藏专用 IP 地址。 临时端口用于 PAT，是基于池大小[预先分配](#preallocatedports)的。 当公共负载均衡器资源与没有专用公共 IP 地址的 VM 实例关联时，将重写每个出站连接源。 出站连接源从虚拟网络专用 IP 地址空间重新写入负载均衡器的前端公共 IP 地址。 在公共 IP 地址空间中，流的 5 元组（源 IP 地址、源端口、IP 转换协议、目标 IP 地址、目标端口）必须唯一。 端口伪装 SNAT 可与 TCP 或 UDP IP 协议一起使用。 重写专用源 IP 地址后，临时端口（SNAT 端口）用于实现此目的，因为多个流源自单个公共 IP 地址。 伪装 SNAT 算法的端口为 UDP 与 TCP 分配不同的 SNAT 端口。|
| SNAT 端口| TCP | SNAT 端口是可用于特定公共 IP 源地址的临时端口。 每个到单个目标 IP 地址、端口的流使用一个 SNAT 端口。 对于到相同的目标 IP 地址、端口和协议的多个 TCP 流，每个 TCP 流使用一个 SNAT 端口。 这可以确保源自相同的公共 IP 地址，并到相同的目标 IP 地址、端口和协议的流的唯一性。 每个流均流到不同目标 IP 地址、端口和协议的多个流共用一个 SNAT 端口。 目标 IP 地址、端口和协议使流保持唯一，无需使用其他源端口来区分公共 IP 地址空间中的流。|
|SNAT 端口 | UDP | UDP SNAT 端口由与 TCP SNAT 端口不同的算法管理。  负载均衡器对 UDP 使用称为“端口受限锥形 NAT”的算法。  无论目标 IP 地址、端口如何，每个流都会使用一个 SNAT 端口。|
| 耗尽 | - | 如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 当流关闭时，负载均衡器将回收 SNAT 端口，并使用 [4 分钟空闲超时](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)回收空闲流中的 SNAT 端口。 由于使用的算法不同，UDP SNAT端口的耗尽速度通常比 TCP SNAT 端口快得多。 在进行设计和规模测试时必须考虑到这种差异。|
| SNAT 端口释放行为 | TCP | 如果服务器/客户端均发送 FINACK，则 SNAT 端口在 240 秒后释放。 如果出现 RST，则 SNAT 端口在 15 秒后释放。 如果已达到空闲超时，则会释放端口。|
| SNAT 端口释放行为 | UDP |如果已达到空闲超时，则会释放端口。|
| SNAT 端口重用 | TCP、UDP | 释放某个端口以后，即可根据需要重复使用该端口。  可以将 SNAT 端口视为一个适用于给定场景的从低到高的序列，第一个可用 SNAT 端口用于新的连接。|

### <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>端口分配算法

使用 PAT 时，Azure 使用某种算法根据后端池的大小来确定可用的预先分配的 SNAT 端口数目。 对于与负载均衡器关联的每个公共 IP 地址，有 64,000 个端口可用作每个 IP 传输协议的 SNAT 端口。 将分别为 UDP 和 TCP 预分配相同数量的 SNAT 端口，并根据 IP 传输协议独立地使用这些端口。  但是，SNAT 端口使用情况会因流是 UDP 还是 TCP 而有所不同。 创建出站流时，将动态消耗这些端口（直至达到预先分配的限制），当流关闭或达到[空闲超时](../load-balancer/troubleshoot-outbound-connection.md#idletimeout)时将释放这些端口。 仅当需要使流保持唯一时，才使用端口。

#### <a name="dynamic-snat-ports-allocated"></a><a name="snatporttable"></a> 已分配动态 SNAT 端口

下表显示了针对后端池大小层的 SNAT 端口预分配：

| 池大小（VM 实例） | 每个 IP 配置的预先分配 SNAT 端口 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

更改后端池大小可能会影响建立的某些流：

- 如果后端池大小递增并转换为下一层，则在转换为下一个更大的后端池层期间，一半的预先分配 SNAT 端口将被回收。 与回收的 SNAT 端口关联的流会超时，必须重新建立连接。 如果尝试新流，则只要预先分配的端口可用，则该流就能立即成功。
- 如果后端池减小并转换到更低层级，可用的 SNAT 端口数会增多。 在这种情况下，现有的分配 SNAT 端口及其相应的流不会受到影响。

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>出站连接方案概述

| 方案 | 方法 | IP 协议 | 说明 |
|  --- | --- | --- | --- |
|  1.具有公共 IP 地址的 VM（有或没有 Azure 负载均衡器） | SNAT，不使用端口伪装 | TCP、UDP、ICMP、ESP | Azure 将分配给实例 NIC 的 IP 配置的公共 IP 用于所有出站流。 此实例具有所有可用的临时端口。 VM 是否负载均衡无关紧要。 此方案优先于其他方案。 分配到 VM 的公共 IP 属于 1 对 1 关系（而不是 1 对多关系），并实现为无状态的 1 对 1 NAT。 |
| 2.与 VM 关联的公共负载均衡器（VM/实例上没有公共 IP 地址） | 使用负载均衡器前端进行端口伪装 (PAT) 的 SNAT | TCP、UDP | 在此方案中，必须为负载均衡器资源配置一项负载均衡器规则，以在公共 IP 前端与后端池之间创建链接。 如果没有完成此规则配置，则行为将如方案 3 所述。 不需要在规则中添加后端池的正常运行的侦听器或者运行状况探测就能成功实现。 当 VM 创建出站流时，Azure 会通过 SNAT 将此出站流的专用源 IP 地址转换为公共负载均衡器前端的公共 IP 地址。 使用负载均衡器的前端公共 IP 地址的临时端口区分由 VM 产生的各个流。 创建出站流后，SNAT 动态使用[预先分配的临时端口](#preallocatedports)。 在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。 SNAT 端口是预先分配的，如[“分配的默认 SNAT 端口”表](#snatporttable)中所述。 |
| 3.VM（无负载均衡器，无公共 IP 地址）或与基本内部负载均衡器关联的 VM | 使用端口伪装 (PAT) 的 SNAT | TCP、UDP | 当 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共源 IP 地址。 此公共 IP 地址**不可配置**，无法保留，并且不计入订阅的公共 IP 资源限制。 如果重新部署 VM、可用性集或虚拟机规模集，则将释放此公共 IP 地址并请求新的公共 IP 地址。 请不要使用此方案将 IP 地址加入允许列表。 请改用方案 1 或 2，在其中显式声明出站行为。 SNAT 端口是预先分配的，如[“分配的默认 SNAT 端口”表](#snatporttable)中所述。

## <a name="outbound-rules"></a><a name="outboundrules"></a>出站规则

 使用出站规则可以更方便地配置公共[标准负载均衡器](load-balancer-standard-overview.md)的出站网络地址转换。  你可以根据具体的需求，以完全声明性的方式控制出站连接，以缩放和优化此功能。 本部分扩展了上面所述的方案 2 (B)。

![负载均衡器出站规则](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

使用出站规则，你可以使用负载均衡器从头开始定义出站 NAT。 你还可以缩放和优化现有出站 NAT 的行为。

使用出站规则可以控制：

- 哪些虚拟机应转换为哪些公共 IP 地址。
- 应如何分配出站 SNAT 端口。
- 要为哪些协议提供出站转换。
- 用于出站连接空闲超时的持续时间（4-120 分钟）。
- 是否要在空闲超时时发送 TCP Reset
- 通过单个规则同时控制 TCP 和 UDP 传输协议

### <a name="outbound-rule-definition"></a>出站规则定义

与所有负载均衡器规则一样，出站规则遵循用户熟悉的与负载均衡和入站 NAT 规则相同的语法：**前端** + **参数** + **后端池**。 出站规则为后端池识别的、要转换为前端的所有虚拟机配置出站 NAT。   参数针对出站 NAT 算法提供更精细的控制。

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>使用多个 IP 地址缩放出站 NAT

前端提供的每个附加 IP 地址可提供额外的 64,000 个临时端口，供负载均衡器用作 SNAT 端口。 规划大规模方案时可以使用多个 IP 地址，并可以使用出站规则来缓解容易出现 [SNAT 耗尽](troubleshoot-outbound-connection.md#snatexhaust)的模式。  

你还可以直接在出站规则中使用[公共 IP 前缀](https://aka.ms/lbpublicipprefix)。  使用公共 IP 前缀可以更轻松地缩放，并可简化将源自 Azure 部署的流加入允许列表的操作。 可以在负载均衡器资源中配置直接引用公共 IP 地址前缀的前端 IP 配置。  这样，负载均衡器将以独占方式控制公共 IP 前缀，而出站规则将自动使用公共 IP 前缀中包含的所有公共 IP 地址来建立出站连接。  公共 IP 前缀范围内的每个 IP 地址可提供额外的 64,000 个临时端口，供负载均衡器用作 SNAT 端口。

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 出站流空闲超时和 TCP 重置

出站规则提供一个配置参数用于控制出站流空闲超时，并使该超时符合应用程序的需求。 出站空闲超时默认为 4 分钟。 你可以了解[如何配置空闲超时](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)。 负载均衡器的默认行为是在达到出站空闲超时时以静默方式丢弃流。  使用 `enableTCPReset` 参数可以启用更有预测性的应用程序行为，并控制在发生出站空闲超时时是否要发送双向 TCP 重置 (TCP RST)。 查看[在空闲超时时 TCP 重置](https://aka.ms/lbtcpreset)，了解详细信息，包括区域可用性。

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>阻止出站连接

负载均衡规则提供出站 NAT 的自动编程。 但是，某些方案受益于或者要求通过负载均衡规则禁用出站 NAT 的自动编程，以便能够控制或优化行为。  
可通过两种方式使用此参数：

1. 通过为负载均衡规则禁用出站 SNAT，选择禁止将入站 IP 地址用于出站 SNAT
  
2. 同时优化用于入站和出站连接的 IP 地址的出站 SNAT 参数。  必须禁用自动出站 NAT 编程才能让出站规则接管控制权。  例如，若要更改也用于入站连接的某个地址的 SNAT 端口分配，则必须将 `disableOutboundSnat` 参数设置为 true。  如果尝试使用出站规则来重新定义同时用于入站连接的某个 IP 地址的参数，但尚未释放负载均衡规则的出站 NAT 编程，则配置出站规则的操作将会失败。

>[!IMPORTANT]
> 如果将此参数设置为 true，但没有任何出站规则来定义出站连接，则虚拟机将不会建立出站连接。  VM或应用程序的某些操作可能依赖于公网连接。 请务必了解方案的依赖关系，并考虑此项更改造成的影响。

有时允许 VM 创建出站流是不可取的。 或者，可能需要管理哪些目标可以通过出站流访问或哪些目标可以启动入站流。 在此情况下，可以使用[网络安全组](../virtual-network/security-overview.md)管理 VM 可访问的目标。 还可以使用 NSG 来管理可启动入站流的公共目标。

将 NSG 应用于负载均衡的 VM 时，需要注意[服务标记](../virtual-network/security-overview.md#service-tags)和[默认安全规则](../virtual-network/security-overview.md#default-security-rules)。 必须确保 VM 可以接收来自 Azure 负载均衡器的运行状况探测请求。

如果 NSG 阻止来自 AZURE_LOADBALANCER 默认标记的运行状况探测请求，那么 VM 的运行状况探测程序将失败，并且 VM 被标记为停机。 负载均衡器停止向此 VM 发送新流。

## <a name="scenarios-with-outbound-rules"></a>具有出站规则的方案

| # | 方案| 详细信息|
|---|---|---|
| I | 将出站连接整理成一组特定的公共 IP 地址| 可以使用出站规则来整理出站连接，使之看上去像是源自一组特定的公共 IP 地址，以简化允许列表方案。  此源公共 IP 地址可与负载均衡规则使用的 IP 地址相同，也可以是与负载均衡规则使用的 IP 地址不同的一组公共 IP 地址。  1. 创建[公共 IP 前缀](https://aka.ms/lbpublicipprefix)（或者从公共 IP 前缀创建公共 IP 地址）2. 创建公共标准负载均衡器 3. 创建引用所要使用的公共 IP 前缀（或公共 IP 地址）的前端 4. 重复使用某个后端池或创建一个后端池，并将 VM 放入公共负载均衡器的后端池 5. 在公共负载均衡器中配置出站规则，以使用前端为这些 VM 的出站 NAT 编程。 如果不希望将负载均衡规则用于出站连接，则需要在负载均衡规则中禁用出站 SNAT。
| II | 修改 SNAT 端口分配| 可以使用出站规则[基于后端池大小优化自动 SNAT 端口分配](load-balancer-outbound-connections.md#preallocatedports)。 例如，如果你的两个虚拟机共享用于出站 NAT 的单个公共 IP 地址，则在遇到 SNAT 耗尽时，你可能希望增加分配的 SNAT 端口数，而不再使用默认的 1024 个端口。 每个公共 IP 地址最多可以提供 64,000 个临时端口。  如果使用单个公共 IP 地址前端配置出站规则，则总共可以向后端池中的 VM 分配 64,000 个 SNAT 端口。  对于两个 VM，可以使用出站规则最多分配 32,000 个 SNAT 端口 (2x32,000 = 64,000)。 可以使用出站规则来优化默认情况下分配的 SNAT 端口。 你可以分配比默认 SNAT 端口分配提供的端口更多或更少的端口。出站规则的所有前端中的每个公共 IP 地址最多提供 64,000 个可用作 SNAT 端口的临时端口。  负载均衡器以 8 的倍数分配 SNAT 端口。 如果提供的值不能被 8 整除，则会拒绝配置操作。  如果尝试分配的 SNAT 端口数超过了可用端口数（基于公共 IP 地址数确定），则会拒绝配置操作。  例如，如果为每个 VM 分配 10,000 个端口，并且后端池中的 7 个 VM 共享单个公共 IP 地址，则会拒绝该配置（7 x 10,000 个 SNAT 端口 > 64,000 个 SNAT 端口）。  将更多的公共 IP 地址添加到出站规则的前端即可实现该方案。 可以通过将端口数指定为 0，恢复为[基于后端池大小的默认 SNAT 端口分配](load-balancer-outbound-connections.md#preallocatedports)。 在这种情况下，根据[此表](#snatporttable)，前 50 个 VM 实例将获得 1024 个端口，而 51-100 个 VM 实例将获得 512 个端口，依此类推。|
| III| 仅启用出站连接 | 可以使用公共标准负载均衡器为一组 VM 提供出站 NAT。 在此方案中，可以单独使用出站规则，而无需其他任何规则。|
| IV | 仅对 VM 使用出站 NAT（无入站连接） | 定义一个公共标准负载均衡器，将 VM 放入后端池，配置一个出站规则用来为出站 NAT 编程，并整理出站连接，使其看上去源自特定的公共 IP 地址。 还可以使用公共 IP 前缀来简化出站连接源的允许列表操作。 1. 创建公共标准负载均衡器。 2. 创建一个后端池，并将 VM 放入公共负载均衡器的后端池。 3. 在公共负载均衡器中配置出站规则，以便为这些 VM 的出站 NAT 编程。
| V| 内部标准负载均衡器方案的出站 NAT| 使用内部标准负载均衡器时，只有显式声明出站连接之后，出站 NAT 才可用。 你可以通过以下步骤，使用出站规则为内部标准负载均衡器后面的 VM 创建出站连接，以定义出站连接：1. 创建公共标准负载均衡器。 2. 除了内部负载均衡器，还要创建一个后端池，并将 VM 放入公共负载均衡器的后端池。 3. 在公共负载均衡器中配置出站规则，以便为这些 VM 的出站 NAT 编程。 有关此方案的详细信息，请参阅 [此示例](https://docs.microsoft.com/azure/load-balancer/egress-only)。 |
| VI | 使用公共标准负载均衡器为出站 NAT 启用 TCP 和 UDP 协议 | 使用公共标准负载均衡器时，提供的自动出站 NAT 编程与负载均衡规则的传输协议相匹配。 1. 在负载均衡规则中禁用出站 SNAT。 2. 在同一个负载均衡器上配置出站规则。 3. 重复使用 VM 已用的后端池。 4. 指定“协议”：“所有”作为出站规则的一部分。 只使用入站 NAT 规则时，不会提供出站 NAT。 1. 将 VM 放入后端池。 2. 使用公共 IP 地址或公共 IP 前缀定义一个或多个前端 IP 配置 3. 在同一个负载均衡器上配置出站规则。 4. 指定“协议”：“所有”作为出站规则的一部分 |


## <a name="limitations"></a>限制

- 每个前端 IP 地址的最大可用临时端口数为 64,000。
- 可配置的出站空闲超时范围为 4 到 120 分钟（240 到 7200 秒）。
- 负载均衡器不支持将 ICMP 用于出站 NAT。
- 出站规则只能应用于 NIC 的主 IP 配置。  不能为 VM 或 NVA 的辅助 IP 创建出站规则。 支持多个 NIC。
- 由于 VNet 之前的服务和其他平台服务功能的副作用，如果仅使用内部标准负载均衡器，则可以访问没有 VNet 和其他 Microsoft 平台服务的辅助角色。 请勿依赖此副作用，因为相应的服务本身或底层平台可能会在不通知的情况下进行更改。 在仅使用内部标准负载均衡器时，必须始终假定需要明确创建出站连接。 本文中所述的方案 3 不可用。

## <a name="next-steps"></a>后续步骤

- 详细了解[标准负载均衡器](load-balancer-standard-overview.md)。
- 请参阅 [Azure 负载均衡器常见问题解答](load-balancer-faqs.md)。
- 详细了解标准公共负载均衡器的[出站规则](load-balancer-outbound-rules-overview.md)。
- 详细了解[负载均衡器](load-balancer-overview.md)。
- 详细了解[网络安全组](../virtual-network/security-overview.md)。
- 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。
