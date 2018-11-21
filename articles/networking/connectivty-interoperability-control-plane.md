---
title: Azure 后端连接性功能的互操作性：控制平面分析 | Microsoft Docs
description: 本文提供了可用于分析 Azure 中 ExpressRoute、站点到站点 VPN 和虚拟网络对等互连之间互操作性的测试设置的控制平面分析。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 37f5399426bebd375200bbc18dae7ed83f4fde3f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614665"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Azure 后端连接性功能的互操作性：控制平面分析

本文介绍了[测试设置][Setup]的控制分析。 你也可以查看测试设置的[测试设置配置][Configuration]和[数据分析][Data-Analysis]。

从本质上讲，控制平面分析的作用是检查拓扑中网络之间交换的路由。 控制平面分析有助于了解不同网络如何查看拓扑。

## <a name="hub-and-spoke-vnet-perspective"></a>中心和辐射 VNet 透视图

下图说明了中心虚拟网络 (VNet) 和辐射 VNet（以蓝色突出显示）中的网络。 该图还显示了不同网络的自治系统编号 (ASN)，以及在不同网络之间交换的路由： 

[![1]][1]

VNet 的 ExpressRoute 网关的 ASN 不同于 Microsoft Enterprise Edge 路由器 (MSEE) 的 ASN。 ExpressRoute 网关使用专用 ASN（值为 65515），而 MSEE 全局使用公共 ASN（值为 12076）。 配置 ExpressRoute 对等互连时，由于 MSEE 是对等方，因此，需要将 12076 用作对等 ASN。 在 Azure 端，MSEE 与 ExpressRoute 网关建立 eBGP 对等互连。 MSEE 为每个 ExpressRoute 对等互连建立的双重 eBGP 对等互连在控制平面级别是透明的。 因此，在查看 ExpressRoute 路由表时，会看到 VNet 的 ExpressRoute 网关 ASN 是 VNet 的前缀。 

下图显示了 ExpressRoute 路由表示例： 

[![5]][5]

在 Azure 中，仅从对等互连角度来看，ASN 才有意义。 默认情况下，Azure VPN 网关中 ExpressRoute 网关和 VPN 网关的 ASN 均为 65515。

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>本地位置 1 和远程 VNet 透视图（通过 ExpressRoute 1 建立连接）

本地位置 1 和远程 VNet 都通过 ExpressRoute 1 连接到中心 VNet。 它们共享同一个拓扑透视图，如下图所示：

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>本地位置 1 和分支 VNet 通过站点到站点 VPN 建立连接的透视图

本地位置 1 和分支 VNet 都通过站点到站点 VPN 连接连接到中心 VNet 的 VPN 网关。 它们共享同一个拓扑透视图，如下图所示：

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>本地位置 2 透视图

本地位置 2 通过 ExpressRoute 2 的专用对等互连连接到中心 VNet： 

[![4]][4]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联 ExpressRoute 和站点到站点 VPN 连接

###  <a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN

可以使用 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，在本地网络与 Azure VNet 之间以私密方式交换数据。 使用此配置可以在确保保密性、真实性和完整性的基础上交换数据。 这种数据交换还可以防重播。 有关如何使用 ExpressRoute Microsoft 对等互连以隧道模式配置站点到站点 IPsec VPN 的详细信息，请参阅[基于 ExpressRoute Microsoft 对等互连的站点到站点 VPN][S2S-Over-ExR]。 

配置使用 Microsoft 对等互连的站点到站点 VPN 的主要限制是吞吐量。 基于 IPsec 隧道的吞吐量受限于 VPN 网关容量。 VPN 网关吞吐量低于 ExpressRoute 吞吐量。 在这种情况下，对高安全性流量使用 IPsec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径

ExpressRoute 充当冗余的线路对，可确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 另外，如测试设置中所示，在 Azure 区域中，可以使用站点到站点 VPN 为 ExpressRoute 连接创建故障转移路径。 通过 ExpressRoute 和站点到站点 VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute。 为了避免 ExpressRoute 与站点到站点 VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 连接，然后再使用站点到站点 VPN 连接。

有关如何配置 ExpressRoute 和站点到站点 VPN 共存连接的详细信息，请参阅 [ExpressRoute 和站点到站点共存][ExR-S2S-CoEx]。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个 VNet，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的详细信息，请参阅[在 Azure 中实现中心辐射型网络拓扑][Hub-n-Spoke]。

在区域内的 VNet 对等互连中，辐射 VNet 可以使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

你可能想让位于不同区域中的分支 VNet 和本地网络通过中心 VNet 相互通信。 此配置的原生 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代方案是对中心内部的路由使用网络虚拟设备 (NVA)。

有关详细信息，请参阅[什么是 VPN 网关？][VPN]和[部署高度可用的 NVA][Deploy-NVA]。

## <a name="next-steps"></a>后续步骤

了解测试设置的[数据平面分析][Data-Analysis]，以及 Azure 网络监视功能视图。

请参阅 [ExpressRoute 常见问题解答][ExR-FAQ]：
-   了解可将多少条 ExpressRoute 线路连接到一个 ExpressRoute 网关。
-   了解可将多少个 ExpressRoute 网关连接到一条 ExpressRoute 线路。
-   了解 ExpressRoute 的其他缩放限制。


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "拓扑的中心和辐射 VNet 透视图"
[2]: ./media/backend-interoperability/Loc1ExRView.png "拓扑的本地位置 1 和远程 VNet 透视图（通过 ExpressRoute 1 建立连接）"
[3]: ./media/backend-interoperability/Loc1VPNView.png "拓扑的本地位置 1 和分支 VNet 透视图（通过站点到站点 VPN 建立连接）"
[4]: ./media/backend-interoperability/Loc2View.png "拓扑的位置 2 透视图"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "ExpressRoute 1 路由表"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


