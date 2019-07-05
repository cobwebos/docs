---
title: 设计使用 Azure ExpressRoute 的高可用性 |Microsoft Docs
description: 使用 Azure ExpressRoute 时，此页提供以实现高可用性体系结构的建议。
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
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466652"
---
# <a name="designing-for-high-availability-with-expressroute"></a>设计使用 ExpressRoute 的高可用性

ExpressRoute 专为高可用性来提供运营商级 Microsoft 资源的专用网络连接。 换而言之，没有任何单点故障，Microsoft 网络中的 ExpressRoute 路径中。 若要最大化可用性，客户和 ExpressRoute 线路的服务提供程序段应还进行设计，以实现高可用性。 在此文章中，首先让我们深入了解有关构建使用 ExpressRoute，可靠的网络连接的网络体系结构注意事项，然后让我们深入了解可帮助你提高 ExpressRoute 线路的高可用性的微调功能。


## <a name="architecture-considerations"></a>体系结构注意事项

下图说明了建议使用最大程度提高可用性的 ExpressRoute 线路的 ExpressRoute 线路进行连接。

 [![1]][1]

以实现高可用性，很重要，可以确保在整个端到端网络的 ExpressRoute 线路的冗余。 换而言之，您需要维护的本地网络中的冗余，并且不应在服务提供程序网络中破坏冗余。 保持在最低级别的冗余意味着避免单点网络故障。 具有冗余电源和冷却设备将更多的网络改进的高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一个英里物理层设计注意事项

 如果同时在同一个客户本地设备 (CPE) ExpressRoute 线路的主要和辅助连接终止时，要在本地网络中来破坏的高可用性。 此外，如果配置两个主要和辅助连接通过同一个端口的 CPE （不管是通过终止下不同的子接口的两个连接或通过合并两个连接合作伙伴网络中的），您在强制合作伙伴若要降低其网段上的高可用性。 下图说明了受损坏。

[![2]][2]

另一方面，如果您终止主数据库和在不同地理位置的 ExpressRoute 线路的辅助连接，然后您可以在危害的连接的网络性能。 如果流量主动进行负载平衡和终止在不同地理位置的辅助连接，两个路径之间的网络延迟的潜在重大差异会导致并非最佳的网络性能。 

异地冗余的设计注意事项，请参阅[使用 ExpressRoute 进行灾难恢复设计][DR]。

### <a name="active-active-connections"></a>主动-主动连接

Microsoft 网络配置为运行在主动-主动模式下的 ExpressRoute 线路的主要和辅助连接。 但是，通过将路由播发，可以强制在主动-被动模式下操作的 ExpressRoute 线路的冗余连接。 播发更具体的路由和 BGP 路径前追加以及用于生成优先于另一个路径的常见技术。

若要提高高可用性，建议运行这两个主动-主动模式中的 ExpressRoute 线路的连接。 如果让在主动-主动模式下操作的连接，Microsoft 网络将流量进行负载均衡跨每个流的基础上的连接。

在主动-被动模式人脸中运行这两个连接中活跃的路径失败后失败的风险的 ExpressRoute 线路的主要和辅助连接。 切换失败的常见原因是缺少的被动连接和播发过期的路由的被动连接的活动管理。

或者，导致只能实现大约一半的流故障并且获取在主动-主动模式下运行一条 ExpressRoute 线路的主要和辅助连接重新路由，ExpressRoute 连接发生故障之后。 因此，主动-主动模式将显著帮助提高时间到恢复 (MTTR)。

### <a name="nat-for-microsoft-peering"></a>用于 Microsoft 对等互连的 NAT 

Microsoft 对等互连专为公共终结点之间的通信。 因此情况下，在本地专用终结点是网络地址转换 （nat 型） 上的客户或合作伙伴网络的公共 ip 之前它们通过 Microsoft 对等互连进行通信。 假设在主动-主动模式下使用主要和辅助连接，位置和方式，NAT 会影响以下中的多个 ExpressRoute 连接的故障恢复速度。 在下图说明了两个不同的 NAT 选项：

[![3]][3]

选项 1，在 NAT 拆分 ExpressRoute 的主要和辅助连接之间的流量后应用。 为了满足 NAT 的有状态要求，独立的 NAT 池使用主站点和辅助设备之间，以便返回流量将到达流 egressed 通过其在同一边缘设备。

在选项 2 中，拆分 ExpressRoute 的主要和辅助连接之间的流量前使用了常见的 NAT 池。 务必使常见的 NAT 池，然后拆分流量并不意味着这种差异引入单点故障，从而危及高可用性。

使用选项 1，ExpressRoute 连接在发生故障之后，能够访问相应的 NAT 池已断开。 因此，所有中断的流一定要重新建立此连接通过 TCP 或遵循相应的窗口超时的应用程序层。 如果任何一种 NAT 池的本地服务器的任何使用前端和修复连接之前，如果相应的连接出现故障，不能从 Azure 中访问的本地服务器。

而使用选项 2，NAT 后可访问甚至主密钥或辅助连接失败。 因此，网络层本身可以重新出现故障之后的数据包和帮助更快地恢复路由。 

> [!NOTE]
> 如果使用 NAT 选项 1 （即独立 NAT 池，为主要和辅助 ExpressRoute 连接），并在本地服务器从一个 NAT 池的 IP 地址的端口映射，将无法访问服务器通过 ExpressRoute 线路时相应连接将失败。
> 

## <a name="fine-tuning-features-for-private-peering"></a>微调专用对等互连的功能

在本部分中，让我们查看 （具体取决于你的 Azure 部署和敏感程度，将为您 MTTR） 可选功能，帮助提高 ExpressRoute 线路的高可用性。 具体而言，让我们查看时区识别部署 ExpressRoute 虚拟网络网关和双向转发检测 (BFD)。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性区域注意 ExpressRoute 虚拟网络网关

Azure 区域中的可用性区域是容错域和更新域的组合。 如果您选择区域冗余的 Azure IaaS 部署，可能想要配置终止 ExpressRoute 专用对等互连的区域冗余的虚拟网络网关。 若要进一步了解，请参阅[有关 Azure 可用性区域中的区域冗余的虚拟网络网关][zone redundant vgw]. To configure zone-redundant virtual network gateway, see [Create a zone-redundant virtual network gateway in Azure Availability Zones][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>提高了故障检测时间

ExpressRoute 通过专用对等互连支持 BFD。 BFD 故障检测时间通过 Microsoft 企业边缘 (Msee) 和在本地端其 BGP 邻居之间的第 2 层网络从大约 3 分钟 （默认值） 到减少少于一秒。 可帮助快速故障检测时间，从而加快了故障恢复。 若要进一步了解，请参阅[通过 ExpressRoute 配置 BFD][BFD]。

## <a name="next-steps"></a>后续步骤

在本文中，我们讨论了如何为 ExpressRoute 线路连接的高可用性设计。 ExpressRoute 线路对等点固定到的地理位置，因此可能受影响位置的完整路径的灾难性故障。 

设计注意事项，以构建地域冗余的网络连接到 Microsoft 主干可以抵御灾难性故障，因为它们会影响整个区域，请参阅[使用 ExpressRoute 专用对等互连进行灾难恢复设计][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "推荐使用 ExpressRoute 进行连接的方法"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "Suboptimal 最后一英里连接"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 选项"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




