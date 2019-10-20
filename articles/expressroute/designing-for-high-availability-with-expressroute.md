---
title: 用 Azure ExpressRoute 设计高可用性 |Microsoft Docs
description: 本页提供有关使用 Azure ExpressRoute 时的高可用性体系结构建议。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: rambala
ms.openlocfilehash: 4984b30daf6170873cad9472bfed2d879af57efe
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "67466652"
---
# <a name="designing-for-high-availability-with-expressroute"></a>通过 ExpressRoute 进行高可用性设计

ExpressRoute 旨在提供高可用性，以提供与 Microsoft 资源的专用网络连接。 换句话说，Microsoft 网络内 ExpressRoute 路径中没有单点故障。 为了最大限度地提高可用性，还应为 ExpressRoute 线路的客户和服务提供商分段设计高可用性。 在本文中，我们将首先了解网络体系结构注意事项，以便使用 ExpressRoute 构建强大的网络连接，然后查看微调功能，以帮助提高 ExpressRoute 线路的高可用性。


## <a name="architecture-considerations"></a>体系结构注意事项

下图说明了使用 ExpressRoute 线路进行连接以最大程度地提高 ExpressRoute 线路的可用性的建议方法。

 [![1]][1]

为实现高可用性，在端到端网络中维护 ExpressRoute 线路的冗余至关重要。 换句话说，你需要在本地网络中维护冗余，而不应在服务提供商网络内损害冗余。 以最小值保持冗余意味着避免单点网络故障。 网络设备具有冗余的电源和冷却将进一步提高高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一英里物理层设计注意事项

 如果在相同的客户端设备（CPE）上终止 ExpressRoute 线路的主连接和辅助连接，则会危及本地网络中的高可用性。 此外，如果通过一个 CPE 的相同端口配置主连接和辅助连接（通过在不同 subinterfaces 中终止两个连接或通过合并合作伙伴网络中的两个连接），则你将强制实施合作伙伴还会损害其网络网段上的高可用性。 下图说明了此折衷。

[![2]][2]

另一方面，如果在不同的地理位置终止 ExpressRoute 线路的主要和辅助连接，则可能会危及连接的网络性能。 如果流量在主副本和在不同地理位置终止的辅助连接之间主动进行负载平衡，则这两个路径之间的网络延迟可能会造成很大的影响，从而导致网络不理想性能. 

有关异地冗余设计注意事项，请参阅[针对 ExpressRoute 进行灾难恢复设计][DR]。

### <a name="active-active-connections"></a>主动-主动连接

Microsoft 网络配置为在主动-主动模式下操作 ExpressRoute 线路的主要和辅助连接。 但是，通过路由播发，可以强制 ExpressRoute 线路的冗余连接在主动-被动模式下运行。 将更具体的路由和 BGP 作为路径预置进行播发是用于使一个路径优先于其他路径的常用方法。

若要提高高可用性，建议在主动-主动模式下操作 ExpressRoute 线路的连接。 如果让连接在主动-主动模式下运行，则 Microsoft 网络会按流量对流量进行负载均衡。

在主动-被动模式下运行 ExpressRoute 线路的主连接和辅助连接，这会导致在活动路径中出现故障后连接失败的风险。 切换失败的常见原因是缺少被动连接的主动管理，并且被动连接公布了陈旧的路由。

或者，在主动-主动模式下运行 ExpressRoute 线路的主连接和辅助连接时，如果出现 ExpressRoute 连接故障，则只会产生大约一半的流失败和重新路由。 因此，主动-主动模式将明显有助于改进平均恢复时间（MTTR）。

### <a name="nat-for-microsoft-peering"></a>用于 Microsoft 对等互连的 NAT 

Microsoft 对等互连旨在实现公共终结点之间的通信。 因此，在通过 Microsoft 对等互连通信之前，本地专用终结点通常是在客户或合作伙伴网络上通过公共 IP 进行的网络地址转换（/Nsg/acl）。 假设你在主动-主动模式下使用主连接和辅助连接，并且 NAT 对你在其中一个 ExpressRoute 连接中出现故障后的恢复速度产生了影响。 下图演示了两种不同的 NAT 选项：

[![3]][3]

在选项1中，在拆分 ExpressRoute 的主连接和辅助连接之间的流量后应用 NAT。 为了满足 NAT 的有状态要求，将在主设备和辅助设备之间使用独立的 NAT 池，以便将返回流量发送到流出口的同一边缘设备。

在选项2中，在将 ExpressRoute 的主连接与辅助连接拆分之前，将使用一个通用的 NAT 池。 在拆分流量之前，请务必区分常见的 NAT 池，这并不意味着单点故障，进而影响高可用性。

如果选择了 "1"，则在 ExpressRoute 连接失败后，能够访问相应的 NAT 池。 因此，所有损坏的流都必须在相应的窗口超时后由 TCP 或应用程序层重新建立。 如果任意一个 NAT 池用于本地服务器的前端，并且相应的连接失败，则在连接固定之前，无法从 Azure 访问本地服务器。

使用选项2时，即使在主连接或辅助连接失败后，也可以访问 NAT。 因此，网络层本身可以重路由数据包，并在故障后帮助更快地恢复。 

> [!NOTE]
> 如果使用 NAT 选项1（适用于主 ExpressRoute 连接和辅助 ExpressRoute 连接的独立 NAT 池），并将 IP 地址的端口从一个 NAT 池映射到本地服务器，则在相应的连接失败。
> 

## <a name="fine-tuning-features-for-private-peering"></a>专用对等互连的微调功能

在本部分中，我们将查看可帮助提高 ExpressRoute 线路高可用性的功能（具体取决于你的 Azure 部署和你对 MTTR 的敏感程度）功能。 具体而言，我们来看一下 ExpressRoute 虚拟网络网关和双向转发检测（BFD）的区域感知部署。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性区域感知 ExpressRoute 虚拟网络网关

Azure 区域中的可用性区域是容错域和更新域的组合。 如果选择进行区域冗余的 Azure IaaS 部署，则可能还需要配置用于终止 ExpressRoute 专用对等互连的区域冗余虚拟网络网关。 若要进一步了解，请参阅[关于 Azure 可用性区域中的区域冗余虚拟网络网关][zone redundant vgw]。 若要配置区域冗余虚拟网络网关，请参阅[在 Azure 可用性区域中创建区域冗余虚拟网络网关][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>提高故障检测时间

ExpressRoute 支持通过专用对等互连的 BFD。 BFD 可将 Microsoft 企业边缘（Msee）和其 BGP 邻居在本地端的第2层网络中的故障检测时间减少到3分钟（默认值）到小于一秒。 快速故障检测时间有助于 hastening 故障恢复。 若要进一步了解，请参阅[CONFIGURE BFD Over ExpressRoute][BFD]。

## <a name="next-steps"></a>后续步骤

本文介绍了如何设计 ExpressRoute 线路连接的高可用性。 ExpressRoute 线路对等互连点固定到地理位置，因此可能会受到影响整个位置的灾难性故障的影响。 

有关构建与 Microsoft 骨干的异地冗余网络连接的设计注意事项，这些连接可能会影响整个区域的灾难性故障，请参阅[使用 ExpressRoute 专用对等互连进行灾难恢复设计][DR]。

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "建议使用 ExpressRoute 进行连接"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "最近一次英里连接"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 选项"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




