---
title: 出站连接
titleSuffix: Azure Load Balancer
description: 本文介绍 Azure 如何使 VM 与公共 Internet 服务通信。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: 0fcd0315afcbf38af2b8175deda748522cb335ec
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996848"
---
# <a name="outbound-connections"></a>出站连接

Azure 负载均衡器可通过各种机制提供出站连接。 本文介绍了各种方案以及如何管理它们。 


## <a name="scenarios"></a>方案

* 具有公共 IP 的虚拟机。
* 无公共 IP 的虚拟机。
* 无公共 IP 且无标准负载均衡器的虚拟机。

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>具有公共 IP 的虚拟机

| 关联 | 方法 | IP 协议 |
| ---------- | ------ | ------------ |
| 公共负载均衡器或独立 | [SNAT (源网络地址转换) ](#snat) </br> [PAT (未使用端口伪装) ](#pat) 。 | TCP (传输控制协议)  </br> UDP (用户数据报协议)  </br> ICMP (Internet 控制消息协议)  </br> ESP (封装安全负载)  |

#### <a name="description"></a>说明

Azure 将分配给实例 NIC 的 IP 配置的公共 IP 用于所有出站流。 此实例具有所有可用的临时端口。 VM 是否负载均衡无关紧要。 此方案优先于其他方案。 

分配到 VM 的公共 IP 属于 1 对 1 关系（而不是 1 对多关系），并实现为无状态的 1 对 1 NAT。

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>无公共 IP 的虚拟机

| 关联 | 方法 | IP 协议 |
| ------------ | ------ | ------------ |
| 公共负载均衡器 | 使用具有 PAT 的适用于 [SNAT](#snat) 的负载均衡器前端 [ (端口伪装) ](#pat)。| TCP </br> UDP |

#### <a name="description"></a>说明

负载均衡器资源是使用负载均衡器规则配置的。 此规则用于创建公共 IP 前端与后端池之间的链接。 

如果未完成此规则配置，则行为将如方案3中所述。 

要使运行状况探测成功，不需要使用带有侦听器的规则。

当 VM 创建出站流时，Azure 会将源 IP 地址转换为公共负载均衡器前端的公共 IP 地址。 此转换通过 [SNAT](#snat)完成。 

负载均衡器前端公共 IP 地址的临时端口用于区分 VM 产生的各个流。 创建出站流后，SNAT 动态使用[预先分配的临时端口](#preallocatedports)。 

在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。 将预分配 SNAT 端口，如 [默认的 SNAT 端口分配表](#snatporttable)中所述。

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>无公共 IP 且无标准负载均衡器的虚拟机

| 关联 | 方法 | IP 协议 |
| ------------ | ------ | ------------ |
|无 </br> 基本负载均衡器 | 具有[端口伪装 (PAT) ](#pat)的[SNAT](#snat)| TCP </br> UDP | 

#### <a name="description"></a>说明

当 VM 创建出站流时，Azure 会将源 IP 地址转换为公共源 IP 地址。 此公共 IP 地址 **是不可配置** 的，无法保留。 此地址不会根据订阅的公共 IP 资源限制进行计数。 

如果重新部署，将会释放公共 IP 地址，并请求新的公共 IP： 

* 虚拟机
* 可用性集
* 虚拟机规模集  

不要使用此方案将 Ip 添加到允许列表。 使用方案1或2，显式声明出站行为。 将预分配[snat](#snat)端口，如[默认的 SNAT 端口分配表](#snatporttable)中所述。



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>端口分配算法

Azure 使用算法来确定可用的预分配 [SNAT](#snat) 端口数。 算法基于后端池大小的端口数。 

对于与负载均衡器关联的每个公共 IP 地址，64000端口都作为 [SNAT](#snat) 端口提供。 为 UDP 和 TCP 预先分配了相同数量的 [SNAT](#snat) 端口。 端口按 IP 协议单独使用。 

如果流是 UDP 或 TCP，则 [SNAT](#snat) 端口使用情况有所不同。 

端口会动态地消耗到预先分配的限制。  当流关闭或发生空闲超时时，将释放这些端口。

有关空闲超时的详细信息，请参阅 [对 Azure 负载均衡器中的出站连接进行故障排除](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

仅当需要使流保持唯一时，才使用端口。

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> 动态 SNAT 端口预先分配

下表显示后端池大小层的 [SNAT](#snat) 端口针对：

| 池大小（VM 实例） | 每个 IP 配置的预先分配 SNAT 端口 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

更改后端池大小可能会影响建立的某些流：

- 后端池大小会将触发器转换增加到下一层。 在转换到下一层期间，将回收一半的预分配 [SNAT](#snat) 端口。 

- 与回收的 [SNAT](#snat) 端口超时和关闭关联的流。 必须重新建立这些流。 如果尝试新流，则只要预先分配的端口可用，则该流就能立即成功。

- 如果后端池大小降低并过渡到较低的层，可用的 [SNAT](#snat) 端口数会增加。 现有的给定 [SNAT](#snat) 端口及其各自的流不会受到影响。

## <a name="outbound-rules"></a><a name="outboundrules"></a>出站规则

 出站规则允许配置公共 [标准负载均衡器](load-balancer-standard-overview.md)的出站网络地址转换。  

> [!NOTE]
> **Azure 虚拟网络 NAT** 可以为虚拟网络中的虚拟机提供出站连接。  有关详细信息，请参阅 [什么是 Azure 虚拟网络 NAT？](../virtual-network/nat-overview.md) 。

对出站连接具有完全声明性控制，可根据需要缩放和调整此功能。

![负载均衡器出站规则](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

使用出站规则时，可以使用负载均衡器从头开始定义出站 NAT。 你还可以缩放和优化现有出站 NAT 的行为。

使用出站规则可以控制：

- 应将哪些虚拟机转换为哪些公共 IP 地址。
- 如何指定出站 [SNAT](#snat) 端口。
- 为其提供出站转换的协议。
- 要用于出站连接空闲超时的持续时间 (4-100 分钟) 。
- 是否在空闲超时后发送 TCP 重置
- TCP 和 UDP 传输协议与单个规则

### <a name="outbound-rule-definition"></a>出站规则定义

出站规则遵循与负载均衡和入站 NAT 规则相同的熟悉语法：**前端**  +  **参数**  +  **后端池**。 出站规则为后端池识别的、要转换为前端的所有虚拟机配置出站 NAT。   参数针对出站 NAT 算法提供更精细的控制。

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>使用多个 IP 地址缩放出站 NAT

前端提供的每个附加 IP 地址都为负载均衡器提供附加的64000临时端口，用作 SNAT 端口。 

使用多个 IP 地址来规划大规模方案。 使用出站规则可减轻 [SNAT 消耗](troubleshoot-outbound-connection.md#snatexhaust)。 

你还可以直接在出站规则中使用[公共 IP 前缀](https://aka.ms/lbpublicipprefix)。 

公共 IP 前缀增加了部署规模。 可以将前缀添加到来源于 Azure 资源的流的允许列表中。 可以在负载均衡器中配置前端 IP 配置，以引用公共 IP 地址前缀。  

负载均衡器控制公共 IP 前缀。 出站规则将自动使用公共 IP 前缀中包含的所有公共 IP 地址进行出站连接。 

公共 IP 前缀中的每个 IP 地址都为负载均衡器的每个 IP 地址提供了额外的64000个临时端口，用作 SNAT 端口。

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 出站流空闲超时和 TCP 重置

出站规则提供一个配置参数用于控制出站流空闲超时，并使该超时符合应用程序的需求。 出站空闲超时默认为 4 分钟。 有关详细信息，请参阅 [配置空闲超时](load-balancer-tcp-idle-timeout.md)。 

负载均衡器的默认行为是在达到出站空闲超时时，无提示地删除流。 `enableTCPReset`参数实现可预测的应用程序行为和控制。 参数指示是否将双向 TCP 重置 (TCP RST) 发送到出站空闲超时的超时。 

查看[在空闲超时时 TCP 重置](https://aka.ms/lbtcpreset)，了解详细信息，包括区域可用性。

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>阻止出站连接

负载均衡规则提供出站 NAT 的自动编程。 某些方案受益或要求您禁用负载均衡规则对出站 NAT 的自动编程。 通过规则禁用可允许您控制或修改行为。  

可通过两种方式使用此参数：

1. 阻止出站 SNAT 的入站 IP 地址。 禁用负载均衡规则中的出站 SNAT。
  
2. 同时调整用于入站和出站的 IP 地址的出站 [SNAT](#snat) 参数。 必须禁用自动出站 NAT 才能控制出站规则。 若要更改用于入站的地址的 SNAT 端口分配，则 `disableOutboundSnat` 必须将参数设置为 true。 

如果尝试重新定义用于入站的 IP 地址，则配置出站规则的操作将失败。  首先禁用负载均衡规则的出站 NAT。

>[!IMPORTANT]
> 如果将此参数设置为 true，但没有任何出站规则来定义出站连接，则虚拟机将不会建立出站连接。  VM或应用程序的某些操作可能依赖于公网连接。 请务必了解方案的依赖关系，并考虑此项更改造成的影响。

有时，VM 创建出站流是不必要的。 可能需要管理哪些目标会接收出站流，或哪些目标开始入站流。 使用 [网络安全组](../virtual-network/security-overview.md) 来管理 VM 达到的目标。 使用 Nsg 管理要启动入站流的公共目标。

将 NSG 应用于负载均衡的 VM 时，需要注意[服务标记](../virtual-network/security-overview.md#service-tags)和[默认安全规则](../virtual-network/security-overview.md#default-security-rules)。 确保 VM 可以接收来自 Azure 负载均衡器的运行状况探测请求。

如果 NSG 阻止来自 AZURE_LOADBALANCER 默认标记的运行状况探测请求，则 VM 运行状况探测失败，VM 被标记为不可用。 负载均衡器停止向此 VM 发送新流。

## <a name="scenarios-with-outbound-rules"></a>具有出站规则的方案

### <a name="outbound-rules-scenarios"></a>出站规则方案

* 配置与一组特定公共 Ip 或前缀的出站连接。
* 修改 [SNAT](#snat) 端口分配。
* 仅启用出站。
* 仅 (没有入站) 的 Vm 的出站 NAT。
* 内部标准负载均衡器的出站 NAT。
* 为带有公共标准负载均衡器的出站 NAT 启用 TCP & UDP 协议。

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>配置与一组特定公共 Ip 或前缀的出站连接

#### <a name="details"></a>详细信息

使用此方案可将出站连接定制为源自一组公共 IP 地址。 基于源向允许或拒绝列表添加公共 Ip 或前缀。

此公共 IP 或前缀可以与负载平衡规则使用的相同。 

使用不同于负载均衡规则使用的公共 IP 或前缀：  

1. 创建公共 IP 前缀或公共 IP 地址。
2. 创建公共标准负载均衡器 
3. 创建一个前端，引用要使用的公共 IP 前缀或公共 IP 地址。 
4. 重复使用后端池，或创建后端池，并将 Vm 放置在公共负载均衡器的后端池中
5. 配置公共负载均衡器上的出站规则，以便使用前端为 Vm 启用出站 NAT。 如果不想要将负载均衡规则用于出站，请在负载均衡规则上禁用出站 SNAT。

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>修改 [SNAT](#snat) 端口分配

#### <a name="details"></a>详细信息

可以使用出站规则[基于后端池大小优化自动 SNAT 端口分配](load-balancer-outbound-connections.md#preallocatedports)。 

如果遇到 SNAT 消耗情况，请增加从默认值1024开始提供的 [snat](#snat) 端口数。 

每个公共 IP 地址最多可分配64000个临时端口。 后端池中的 Vm 数决定分配给每个 VM 的端口数。 后端池中的一个 VM 可以访问最多64000个端口。 对于两个 Vm，最多可为32000个 [SNAT](#snat) 端口提供一个出站规则 (2x 32000 = 64000) 。 

可以使用出站规则来优化默认情况下给定的 SNAT 端口。 提供的默认 [SNAT](#snat) 端口分配数量大于或小于默认值。 来自出站规则前端的每个公共 IP 地址都可提供多达64000个临时端口，用作 [SNAT](#snat) 端口。  

负载均衡器提供8个倍数的 [SNAT](#snat) 端口。 如果提供的值不能被 8 整除，则会拒绝配置操作。 

如果尝试根据公共 IP 地址数提供超过可用的 [SNAT](#snat) 端口，则配置操作将被拒绝。

如果为每个 VM 分配10000个端口，而后端池中有7个 Vm 共享单个公共 IP，则配置将被拒绝。 7乘以10000超过64000端口限制。 将更多公共 IP 地址添加到出站规则的前端，以启用该方案。 

通过为端口数指定0，恢复为 [默认端口分配](load-balancer-outbound-connections.md#preallocatedports) 。 第一个 50 VM 实例将获取1024端口，51-100 VM 实例的最大实例数为512。  有关默认 SNAT 端口分配的详细信息，请参阅 [SNAT 端口分配表](#snatporttable)。

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>仅启用出站连接

#### <a name="details"></a>详细信息

使用公共标准负载均衡器为一组 Vm 提供出站 NAT。 在这种情况下，只需使用出站规则，而无需任何其他规则。

> [!NOTE]
> **Azure 虚拟网络 NAT** 可以为虚拟机提供出站连接，而无需负载均衡器。  有关详细信息，请参阅 [什么是 Azure 虚拟网络 NAT？](../virtual-network/nat-overview.md) 。

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>仅对 VM 使用出站 NAT（无入站连接）

> [!NOTE]
> **Azure 虚拟网络 NAT** 可以为虚拟机提供出站连接，而无需负载均衡器。  有关详细信息，请参阅 [什么是 Azure 虚拟网络 NAT？](../virtual-network/nat-overview.md) 。

#### <a name="details"></a>详细信息

对于此方案：

1. 创建公共 IP 或前缀。
2. 创建公共标准负载均衡器。 
3. 创建与公共 IP 关联的前端或专用于出站的前缀。
4. 为 Vm 创建后端池。
5. 将 Vm 放入后端池。
6. 配置出站规则以启用出站 NAT。

使用前缀或公共 IP 来缩放 [SNAT](#snat) 端口。 将出站连接源添加到允许或拒绝列表。

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>内部标准负载均衡器的出站 NAT

> [!NOTE]
> **Azure 虚拟网络 NAT** 可以使用内部标准负载均衡器为虚拟机提供出站连接。  有关详细信息，请参阅 [什么是 Azure 虚拟网络 NAT？](../virtual-network/nat-overview.md) 。

#### <a name="details"></a>详细信息

对于内部标准负载均衡器，出站连接不可用，除非已显式声明。 

有关详细信息，请参阅 [仅出站负载平衡器配置](https://docs.microsoft.com/azure/load-balancer/egress-only)。


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>启用使用公共标准负载均衡器的出站 NAT 的 TCP & UDP 协议

#### <a name="details"></a>详细信息

使用公共标准负载均衡器时，所提供的自动出站 NAT 与负载均衡规则的传输协议相匹配。 

1. 禁用负载均衡规则上的出站 [SNAT](#snat) 。 
2. 在相同的负载均衡器上配置出站规则。
3. 重复使用 VM 已用的后端池。 
4. 指定“协议”：“所有”作为出站规则的一部分。 

只使用入站 NAT 规则时，不会提供出站 NAT。 

1. 将 VM 放入后端池。
2. 定义一个或多个具有公共 IP 地址的前端 IP 配置 (es) 或公共 IP 前缀 
3. 在相同的负载均衡器上配置出站规则。 
4. 指定“协议”：“所有”作为出站规则的一部分

## <a name="terminology"></a><a name="terms"></a> 规范

### <a name="source-network-address-translation"></a><a name="snat"></a>源网络地址转换

| 适用的协议 |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>详细信息

Azure 中的部署可与 Azure 外部的公共 IP 地址空间中的终结点进行通信。

当实例启动到具有公共 ip 地址的目标的出站流量时，Azure 会动态地将该资源的专用 IP 地址映射到公共 IP 地址。 

创建此映射后，此出站发起流的返回流量将达到流的起始专用 IP 地址。 

Azure ** (SNAT) 使用源网络地址转换 ** 来执行此功能。

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>端口伪装 SNAT (PAT)

| 适用的协议 |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>详细信息

当专用 ip 位于单个公共 IP 地址后面时，Azure 使用 **端口地址转换 (PAT) ** 来隐藏专用 IP 地址。 

临时端口用于 PAT，是基于池大小[预先分配](#preallocatedports)的。 

如果公共负载均衡器与没有公共 Ip 的 Vm 关联，则会重写每个出站连接源。 

将源从虚拟网络专用 IP 地址重新写入负载均衡器的前端公共 IP 地址。 

在公共 IP 地址空间中，流的五个元组必须是唯一的：

* 源 IP 地址
* Source Port
* IP 传输协议
* 目标 IP 地址
* Destination Port 

端口伪装 SNAT 可以与 TCP 或 UDP 协议一起使用。 重写专用源 IP 地址后使用 SNAT 端口，因为多个流源自单个公共 IP 地址。 端口伪装 SNAT 算法为 UDP 与 TCP 提供不同的 SNAT 端口。

### <a name="snat-ports-tcp"></a>SNAT 端口 (TCP) 

| 适用的协议 |
|------------------------|
| TCP |

#### <a name="details"></a>详细信息

SNAT 端口是可用于公共 IP 源地址的临时端口。 每个到单个目标 IP 地址的流和端口使用一个 SNAT 端口。 

对于到相同的目标 IP 地址、端口和协议的多个 TCP 流，每个 TCP 流使用一个 SNAT 端口。 

此使用情况可确保流在源自相同公共 IP 地址并传送到时是唯一的：

* 同一目标 IP 地址
* 端口
* 协议 

多个流共用一个 SNAT 端口。 

目标 IP 地址、端口和协议使流保持唯一，无需使用其他源端口来区分公共 IP 地址空间中的流。


### <a name="snat-ports-udp"></a> (UDP) 的 SNAT 端口

| 适用的协议 |
|------------------------|
| UDP |

#### <a name="details"></a>详细信息

UDP SNAT 端口由与 TCP SNAT 端口不同的算法管理。  负载均衡器使用一个名为端口限制的针对 UDP 的圆锥 NAT 的算法。

对于每个流的目标 IP 地址和端口，将使用一个 SNAT 端口。


### <a name="exhaustion"></a><a name="exhaustion"></a>耗尽

| 适用的协议 |
|------------------------|
| 空值 |

#### <a name="details"></a>详细信息

如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 负载均衡器在流关闭时回收 SNAT 端口。

负载均衡器使用的 [空闲超时值](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 为4分钟，以回收 SNAT 端口。

UDP SNAT 端口通常比 TCP SNAT 端口快得多，因为使用了不同的算法。 由于这种差异而设计和缩放测试。

### <a name="snat-port-release-behavior-tcp"></a>SNAT 端口 release 行为 (TCP) 

| 适用的协议 |
|------------------------|
| TCP |

#### <a name="details"></a>详细信息

当服务器或客户端发送 FINACK 时，将在240秒后释放 SNAT 端口。 发生 RST 时，将在15秒后释放 SNAT 端口。 如果已达到空闲超时，则释放端口。

### <a name="snat-port-release-behavior-udp"></a>UDP) 的 SNAT 端口 release 行为 (

| 适用的协议 |
|------------------------|
| TCP |

#### <a name="details"></a>详细信息

如果已达到空闲超时，则会释放端口。

### <a name="snat-port-reuse"></a>SNAT 端口重用

| 适用的协议 |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>详细信息

端口释放后，可重复使用该端口。 SNAT 端口是给定方案的从低到高的顺序，第一个可用的 SNAT 端口用于新连接。

## <a name="limitations"></a>限制

- 每个前端 IP 地址的最大可用临时端口数为 64,000。
- 可配置的出站空闲超时范围为 4 到 120 分钟（240 到 7200 秒）。
- 负载均衡器不支持对出站 NAT 进行 ICMP。
- 出站规则只能应用于 NIC 的主 IP 配置。  不能为 VM 或 NVA 的辅助 IP 创建出站规则。 支持多个 NIC。
- 使用内部标准负载均衡器时，无需虚拟网络和其他 Microsoft 平台服务的 Web 辅助角色可以访问。 此可访问性的原因是 VNet 服务和其他平台服务的运行方式有副作用。 请勿依赖于这一副作用，因为各自的服务本身或基础平台可能会更改，恕不另行通知。 如果只需要使用内部标准负载均衡器，则应始终假设你需要显式创建出站连接。 本文中所述的方案3不可用。

## <a name="next-steps"></a>后续步骤

如果在通过 Azure 负载均衡器进行出站连接时遇到问题，请参阅 [出站连接故障排除指南](../load-balancer/troubleshoot-outbound-connection.md)。

- 了解有关 [标准负载均衡器](load-balancer-standard-overview.md)的详细信息。
- 请参阅 [Azure 负载均衡器常见问题解答](load-balancer-faqs.md)。
- 详细了解标准公共负载均衡器的 [出站规则](load-balancer-outbound-rules-overview.md) 。

