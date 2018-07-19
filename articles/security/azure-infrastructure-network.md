---
title: Azure 网络体系结构
description: 本文提供有关 Microsoft Azure 基础结构网络的一般说明。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101239"
---
# <a name="azure-network-architecture"></a>Azure 网络体系结构
Azure 网络体系结构遵循行业标准核心/分配/访问模型的修改版本，并使用不同的硬件层。 这些层包括：

- 核心（数据中心路由器）
- 分配（接入路由器和 L2 聚合）- 分配层将 L3 路由与 L2 交换隔离开来
- 访问（L2 主机交换机）

网络体系结构包含两个级别的第 2 层交换机、一个从其他层聚合流量的层，以及用于整合冗余的第 2 层环路。 此体系结构提供的优势包括更灵活的 VLAN 占用空间和端口缩放度的提升。 该体系结构保持 L2 和 L3 的独特性，允许在网络中的每个独特层上使用硬件，并尽量减少一个层中的故障影响到其他层。 使用中继可以实现资源共享，例如，与 L3 基础结构建立连接。

## <a name="network-configuration"></a>网络配置
数据中心内 Microsoft Azure 群集的网络体系结构包括以下设备：

- 路由器（数据中心、接入路由器和边界叶路由器）
- 交换机（聚合和架顶式交换机）
- Digi CM
- PDU or Nucleon

下图从较高层面提供了群集中 Azure 网络体系结构的概况。 Azure 有两个独立的体系结构。 某些现有 Azure 客户和共享服务驻留在默认 LAN 体系结构 (DLA) 中，可通过 Quantum 10 (Q10) 体系结构访问其中的新区域和虚拟客户。 DLA 体系结构采用传统的树形设计，其中包含主动/被动接入路由器，并向接入路由器应用安全性 ACL。 Quantum 10 体系结构采用 clos/mesh 路由器设计，它不是在路由器上应用 ACL，而是通过软件负载均衡 (SLB) 或软件定义的 VLAN 在路由下面的应用 ACL。

![Azure 网络][1]

### <a name="quantum-10-devices"></a>Quantum 10 设备
Quantum 10 设计通过 CLOS/mesh 设计中的多个设备执行第 3 层交换传播。 Q10 设计的优点包括容量更大，缩放现有网络基础结构的能力更高。 该设计采用边界叶路由器、骨干交换机和架顶式路由器，跨多个路由将流量传递到群集，以实现容错。 网络地址转换等安全服务通过软件负载均衡而不是硬件设备进行处理。

### <a name="access-routers"></a>接入路由器
分配/L3 接入路由器 (AR) 针对分配层和接入层执行主要路由功能。 这些设备以配对的形式部署，是子网的默认网关。 每个 AR 对可以支持多个 L2 聚合交换机对，具体取决于容量。 最大对数取决于设备的容量以及故障域。 基于预期容量计算出的典型数字是每个 AR 对支持三个 L2 聚合交换机对。

### <a name="l2-aggregation-switches"></a>L2 聚合交换机  
这些设备充当 L2 流量的聚合点。 它们是 L2 结构的分配层，可以处理大量的流量。 由于这些设备聚合流量，因此需要 802.1q 功能，并使用端口聚合和 10GE 等高带宽技术。

### <a name="l2-host-switches"></a>L2 主机交换机
主机直接连接到这些交换机。 它们可以是机架安装式交换机，也可以是机箱部署设备。 802.1q 标准允许将一个 VLAN 指定为本机 VLAN，并将该 VLAN 视为正常（未标记）以太网帧。 在正常情况下，将通过 802.1q 中继端口以未标记的形式传输和接收本机 VLAN 上的帧。 此功能可用于迁移到 802.1q，并与不支持 802.1q 的设备实现兼容。 在此体系结构中，只有网络基础结构利用本机 VLAN。

此体系结构指定了本机 VLAN 的选择标准，确保 AR 设备尽量为每个中继以及 L2Aggregation 到 L2Aggregation 的中继使用唯一的本机 VLAN。 L2Aggregation 到 L2Host 的交换中继采用非默认的本机 VLAN。

### <a name="link-aggregation-8023ad"></a>链路聚合 (802.3ad)
链路聚合 (LAG) 允许将多个独立的链路捆绑在一起，并将其视为一个逻辑链路。 用于指定端口通道接口的编号需要标准化，以简化操作调试，网络的剩余部分将在端口通道的两端使用相同的编号。 这需要采用某种标准确定要使用端口通道的哪一端来定义下一个可用编号。

为 L2Agg 到 L2Host 交换指定的编号是在 L2Agg 端使用的端口通道编号。 由于 L2Host 端的编号范围有更高的限制，因此，标准是使用在 L2Host 端使用编号 1 和 2 来分别表示连接到“a”端和“b”端的端口通道。

### <a name="vlans"></a>VLAN
网络体系结构使用 VLAN 将服务器统一分组成单个广播域。 VLAN 编号符合 802.1q 标准，该标准支持 VLAN 编号 1 - 4094。

### <a name="customer-vlans"></a>客户 VLAN
使用各种 VLAN 实施选项的客户可以通过 Azure 门户进行部署，以满足解决方案的隔离和体系结构需求。 这些解决方案通过虚拟机进行部署。 有关客户参考体系结构的示例，请访问 [Azure 参考体系结构](https://docs.microsoft.com/azure/architecture/reference-architectures/)。

### <a name="edge-architecture"></a>边缘体系结构
Azure 数据中心构建在高度冗余、适当预配的网络基础结构之上。 Azure 数据中心内的网络以“需求加一”(N+1) 冗余体系结构或更佳的方式实施。 数据中心内部和数据中心之间的完全故障转移功能有助于确保网络与服务的可用性。 从外部看，数据中心由专用的高带宽网络线路提供服务，这些线路以冗余方式将资产连接到全球 1200 多个 Internet 服务提供商的多个对等连接点，在整个网络中提供超过 2,000 Gbps 的潜在边缘容量。

位于 Microsoft Azure 网络边缘和访问层的筛选路由器在数据包级别提供十分可靠的安全性，防止有人在未经授权的情况下尝试连接到 Azure。 它们有助于确保数据包的实际内容包含预期格式的数据，并符合预期的客户端/服务器通信方案。 Azure 实施分层体系结构，其中包括以下网络隔离和访问控制组件：

- 边缘路由器 - 将应用程序环境与 Internet 隔离开来。 边缘路由器旨在提供反欺骗保护，并使用访问控制列表 (ACL) 限制访问。
- 分配（接入）路由器 - 接入路由器只允许 Microsoft 批准的 IP 地址，使用 ACL 提供已建立的反欺骗连接。

### <a name="a10-ddos-mitigation-architecture"></a>A10 DDOS 缓解体系结构
拒绝服务攻击持续对 Microsoft 联机服务的可靠性造成实际威胁。 随着攻击变得更有针对性且更复杂，以及 Microsoft 服务的地理分布越来越广泛，提供有效的机制来识别这些攻击并尽量降低其影响的能力就成了一项高优先级任务。

下面从网络体系结构的角度详细说明了如何实施 A10 DDOS 缓解系统。  
Microsoft Azure 在 DCR（数据中心路由器）上使用 A10 网络设备来提供自动检测和缓解。 A10 解决方案利用 Azure 网络监视功能对网络流数据包采样，并确定是否存在攻击。 检测到攻击后，A10 设备将用作清理设备来缓解攻击。 缓解后，允许进一步得到清理的流量直接从 DCR 进入 Azure 数据中心。 A10 解决方案用于保护 Azure 网络基础结构。

A10 解决方案中的 DDoS 防护措施包括：

- UDP IPv4 和 IPv6 泛洪防护
- ICMP IPv4 和 IPv6 泛洪防护
- TCP IPv4 和 IPv6 泛洪防护
- 针对 IPv4 和 IPv6 的 TCP SYN 攻击防护
- 碎片攻击

> [!NOTE]
> 默认为所有 Azure 客户提供 DDoS 防护。
>
>

## <a name="network-connection-rules"></a>网络连接规则
Azure 在其网络中部署边缘路由器，以便在数据包级别提供安全性，防止在未经授权的情况下尝试连接到 Microsoft Azure。 这些路由器可以确保数据包的实际内容包含预期格式的数据，并符合预期的客户端/服务器通信方案。

边缘路由器将应用程序环境与 Internet 隔离开来。 边缘路由器旨在提供反欺骗保护，并使用访问控制列表 (ACL) 限制访问。 这些边缘路由器已使用分层 ACL 方法进行配置，可以限制允许传输边缘路由器和接入路由器流量的网络协议。

网络设备定位在接入位置和边缘位置，充当应用入口和/或出口筛选器的边界点。

## <a name="next-steps"></a>后续步骤
若要详细了解 Microsoft 如何保护 Azure 基础结构，请参阅：

- [Azure 设施、场地和物理安全性](azure-physical-security.md)
- [Azure 基础结构可用性](azure-infrastructure-availability.md)
- [Azure 信息系统的组件和边界](azure-infrastructure-components.md)
- [Azure 生产网络](azure-production-network.md)
- [Microsoft Azure SQL 数据库安全功能](azure-infrastructure-sql.md)
- [Azure 生产运营和管理](azure-infrastructure-operations.md)
- [监视 Azure 基础结构](azure-infrastructure-monitoring.md)
- [Azure 基础结构的完整性](azure-infrastructure-integrity.md)
- [保护 Azure 中的客户数据](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
