---
title: ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 控制平面分析：Azure 后端连接功能的互操作性 | Microsoft Docs
description: 本页提供创建用来分析 ExpressRoute、站点到站点 VPN 和 VNet 对等互连功能互操作性的测试设置的控制平面分析。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946951"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 控制平面分析

本文介绍测试设置的控制平面分析。 若要了解测试设置，请参阅[测试设置][Setup]。 若要了解测试设置的配置详细信息，请参阅[测试设置配置][Configuration]。

从本质上讲，控制平面分析的作用是检查拓扑中网络之间交换的路由。 控制平面分析有助于了解拓扑中不同网络视图的外观。

##<a name="hub-and-spoke-vnet-perspective"></a>中心和辐射 VNet 角度

下图演示了从中心 VNet 和辐射 VNet（以蓝色突出显示）角度来看的网络外观。 该图还显示了不同网络的自治系统编号 (ASN)，以及在不同网络之间交换的路由。 

[![1]][1]

请注意，VNet 的 ExpressRoute 网关的 ASN 不同于 Microsoft 企业边缘路由器 (MSEE) 的 ASN。 ExpressRoute 网关使用专用 ASN (65515)，而 MSEE 全局使用公共 ASN (12076)。 配置 ExpressRoute 对等互连时，由于 MSEE 是对等方，因此，需将 12076 用作对等 ASN。 在 Azure 端，MSEE 与 ExpressRoute 网关建立 eBGP 对等互连。 MSEE 为每个 ExpressRoute 对等互连建立的双重 eBGP 对等互连在控制平面级别是透明的。 因此，在查看 ExpressRoute 路由表时，会看到 VNet 的 ExpressRoute 网关 ASN 是 VNet 的前缀。 下面显示了示例 ExpressRoute 路由表的屏幕截图： 

[![5]][5]

在 Azure 中，仅从对等互连的角度来看，ASN 才有意义。 默认情况下，ExpressRoute 网关和 VPN 网关的 ASN 都是 65515。

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>本地位置 1 和远程 VNet 通过 ExpressRoute 1 建立连接的透视图

本地位置 1 和远程 VNet 都通过 ExpressRoute 1 连接到中心 VNet，因此，它们共享相同的拓扑透视图，如下图所示。

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>本地位置 1 和分支 VNet 通过站点到站点 VPN 建立连接的透视图

本地位置 1 和分支 VNet 都通过站点到站点 VPN 连接到中心 VNet 的 VPN 网关，因此，它们共享相同的拓扑透视图，如下图所示。

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>本地位置 2 透视图

本地位置 2 通过 ExpressRoute 2 的专用对等互连连接到中心 VNet。 

[![4]][4]

## <a name="further-reading"></a>延伸阅读

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联使用 ExpressRoute 和站点到站点 VPN 连接

####  <a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN

可以基于 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，专门用于在本地网络与 Azure VNet 之间交换数据，同时确保保密性、防重播能力、真实性和完整性。 有关如何基于 ExpressRoute Microsoft 对等互连以隧道模式配置站点到站点 IPSec VPN 的详细信息，请参阅[基于 ExpressRoute Microsoft 对等互连的站点到站点 VPN][S2S-Over-ExR]。 

基于 Microsoft 对等互连配置 S2S VPN 的主要限制是吞吐量。 基于 IPSec 隧道的吞吐量受限于 VPN 网关容量。 与 ExpressRoute 吞吐量相比，VPN 网关吞吐量更低。 在这种情况下，对高安全性流量使用 IPSec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径
ExpressRoute 以冗余线路对的形式提供，以确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 如同在测试设置中所做的那样，在给定的 Azure 区域中，若要对 ExpressRoute 连接使用故障转移路径，可以使用站点到站点 VPN 实现此目的。 通过 ExpressRoute 和 S2S VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute 而不是 S2S VPN。 为了避免 ExpressRoute 与 S2S VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 而不是 S2S VPN 连接。

有关如何配置 ExpressRoute 和站点到站点 VPN 共存连接的详细信息，请参阅 [ExpressRoute 和站点到站点共存][ExR-S2S-CoEx]。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个虚拟网络 (VNet)，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的更多详细信息，请参阅[中心辐射型体系结构][Hub-n-Spoke]

区域中的 VNet 对等互连可让辐射 VNet 使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

如果希望分支 VNet（在不同区域中）和本地网络通过中心 VNet 相互通信，原生的 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代做法是对中心内部的路由使用 NVA。

有关如何配置 VPN 网关，请参阅[配置 VPN 网关][VPN]。 有关如何部署可用性 NVA，请参阅[部署高可用性 NVA][Deploy-NVA]。

## <a name="next-steps"></a>后续步骤

有关测试设置的数据平面分析以及 Azure 网络监视功能视图，请参阅[数据平面分析][Data-Analysis]。

若要了解可将多少个 ExpressRoute 线路连接到一个 ExpressRoute 网关，或者可将多少个 ExpressRoute 网关连接到一个 ExpressRoute 线路，或者要了解 ExpressRoute 的其他缩放限制，请参阅 [ExpressRoute 常见问题解答][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "拓扑的中心和辐射 VNet 透视图"
[2]: ./media/backend-interoperability/Loc1ExRView.png "本地位置 1 和远程 VNet 通过拓扑的 ExpressRoute 1 建立连接的透视图"
[3]: ./media/backend-interoperability/Loc1VPNView.png "位置 1 和分支 VNet 通过拓扑的 S2S VPN 建立连接的透视图"
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




