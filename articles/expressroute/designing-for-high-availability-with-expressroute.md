---
title: 使用 Azure ExpressRoute 进行高可用性设计 | Microsoft Docs
description: 本页提供有关使用 Azure ExpressRoute 实现高可用性的体系结构建议。
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67466652"
---
# <a name="designing-for-high-availability-with-expressroute"></a>使用 ExpressRoute 进行高可用性设计

ExpressRoute 旨在实现高可用性，以便与 Microsoft 资源建立运营商级专用网络连接。 换句话说，Microsoft 网络中的 ExpressRoute 路径不存在单一故障点。 为了最大限度地提高可用性，还应该规划好 ExpressRoute 线路的客户与服务提供商细分。 本文先探讨使用 ExpressRoute 构建可靠网络连接时的网络体系结构注意事项，然后探讨如何微调功能，以帮助改善 ExpressRoute 线路的高可用性。


## <a name="architecture-considerations"></a>体系结构注意事项

下图演示了使用 ExpressRoute 线路进行连接，以最大程度提高 ExpressRoute 线路可用性的建议方法。

 [![1]][1]

为实现高可用性，在整个端到端网络中保持 ExpressRoute 线路的冗余至关重要。 换而言之，需要在本地网络中保持冗余，且不能破坏服务提供商网络中的冗余。 保持最低程度的冗余意味着可以避免网络中出现单一故障点。 为网络设备提供冗余的电源和散热装置可进一步改善高可用性。

### <a name="first-mile-physical-layer-design-considerations"></a>第一英里物理层设计注意事项

 如果在同一客户本地设备 (CPE) 上终止 ExpressRoute 线路的主要连接和辅助连接，将会降低你本地网络中的高可用性。 此外，如果你通过 CPE 的同一端口配置主要连接和辅助连接（通过终止不同子接口下的两个连接，或者在合作伙伴网络中合并两个连接），则合作伙伴也会被迫降低其网段中的高可用性。 下图演示了这种损害。

[![2]][2]

另一方面，如果在不同的地理位置终止 ExpressRoute 线路的主要连接和辅助连接，可能会降低连接的网络性能。 如果流量在不同地理位置终止的主要副本与辅助连接之间主动进行负载均衡，这两条路径之间可能存在的明显网络延迟差会导致网络性能不佳。 

有关异地冗余设计注意事项，请参阅[使用 ExpressRoute 进行灾难恢复设计][DR]。

### <a name="active-active-connections"></a>主动-主动连接

Microsoft 网络配置为以主动-主动模式运行 ExpressRoute 线路的主要连接和辅助连接。 但是，通过路由播发，可以强制在主动-被动模式下运行 ExpressRoute 线路的冗余连接。 播发更具体的路由和 BGP AS 路径预置是优先使用某一条路径的常用方法。

为了改善高可用性，建议在主动-主动模式下运行 ExpressRoute 线路的主要连接和辅助连接。 如果让连接以主动-主动模式运行，Microsoft 网络将会基于流对不同连接之间的流量进行负载均衡。

在主动-被动模式下运行 ExpressRoute 线路的主要连接和辅助连接会带来以下风险：连接失败，随后主动路径中发生故障。 切换时发生故障的常见原因是被动连接缺少主动管理，并且被动连接播发过时的路由。

或者，在主动-主动模式下运行 ExpressRoute 线路的主要连接和辅助连接会导致仅有大约一半的流量失败并重新路由，随后 ExpressRoute 连接也会失败。 因此，主动-主动模式明显有助于改善平均恢复时间 (MTTR)。

### <a name="nat-for-microsoft-peering"></a>Microsoft 对等互连的 NAT 

Microsoft 对等互连旨在实现公共终结点之间的通信。 因此，本地专用终结点在通过 Microsoft 对等互连通信之前，通常会使用客户或合作伙伴网络上的公共 IP 进行网络地址转换 (NAT)。 假设你在主动-主动模式下使用主要连接和辅助连接，执行 NAT 的位置和方式会影响到在某个 ExpressRoute 连接失败后进行恢复的速度。 下图演示了两个不同的 NAT 选项：

[![3]][3]

在选项 1 中，拆分 ExpressRoute 主要连接与辅助连接之间的流量之后应用了 NAT。 为了满足 NAT 的有状态要求，将在主要设备与辅助设备之间使用独立的 NAT 池，使返回流量抵达用于传出流量的同一边缘设备。

在选项 2 中，拆分 ExpressRoute 主要连接与辅助连接之间的流量之前使用了一个通用 NAT 池。 必须认识到，在拆分流量之前使用通用 NAT 池并不意味着会造成单一故障点，进而影响高可用性。

如果使用选项 1，发生 ExpressRoute 连接失败后，将无法访问相应的 NAT 池。 因此，在相应的期限超时后，TCP 或应用层必须重建所有已中断的流。 如果使用任一 NAT 池作为任何本地服务器的前端，而相应的连接失败，则在修复连接之前，无法从 Azure 访问本地服务器。

而使用选项 2 时，即使主要连接或辅助连接失败，也仍可访问 NAT。 因此，在发生故障后，网络层本身可以重新路由数据包，帮助更快进行恢复。 

> [!NOTE]
> 如果使用 NAT 选项 1（对 ExpressRoute 主要连接和辅助连接使用独立的 NAT 池），并将 IP 地址的端口从一个 NAT 池映射到本地服务器，则在相应的连接失败时，无法通过 ExpressRoute 线路访问服务器。
> 

## <a name="fine-tuning-features-for-private-peering"></a>微调专用对等互连的功能

本部分介绍可帮助改善 ExpressRoute 线路高可用性的可选功能（根据 Azure 部署以及 MTTR 重要性选择使用）。 具体而言，本部分将介绍区域感知的 ExpressRoute 虚拟网络网关部署和双向转发检测 (BFD)。

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>可用性区域感知的 ExpressRoute 虚拟网络网关

Azure 区域中的可用性区域是容错域和更新域的组合。 如果你选择进行区域冗余的 Azure IaaS 部署，则还可能需要配置用于终止 ExpressRoute 专用对等互连的区域冗余虚拟网络网关。 有关更多信息，请参阅[关于 Azure 可用性区域中的区域冗余虚拟网络网关][zone redundant vgw]。 若要配置区域冗余的虚拟网络网关，请参阅[在 Azure 可用性区域中创建区域冗余的虚拟网络网关][conf zone redundant vgw]。

### <a name="improving-failure-detection-time"></a>改善故障检测时间

ExpressRoute 支持通过专用对等互连执行的 BFD。 BFD 可以通过 Microsoft 企业边缘 (MSEE) 及其本地端的 BGP 邻居之间的第 2 层网络，将故障检测时间从大约 3 分钟（默认值）减少到 1 秒以下。 缩减故障检测时间有助于加速故障恢复。 有关更多信息，请参阅[配置基于 ExpressRoute 的 BFD][BFD]。

## <a name="next-steps"></a>后续步骤

本文介绍了如何设计 ExpressRoute 线路连接的高可用性。 一个 ExpressRoute 线路对等互连点固定为一个地理位置，因此，影响整个位置的灾难性故障可能会影响该连接点。 

有关与 Microsoft 主干网络建立可承受灾难性故障（会影响整个区域）的异地冗余网络连接的设计注意事项，请参阅[使用 ExpressRoute 专用对等互连进行灾难恢复设计][DR]。

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "使用 ExpressRoute 进行连接的建议方法"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "最后一英里连接欠佳"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT 选项"


<!--Link References-->
[zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways
[conf zone redundant vgw]: https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[BFD]: https://docs.microsoft.com/azure/expressroute/expressroute-bfd
[DR]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering




