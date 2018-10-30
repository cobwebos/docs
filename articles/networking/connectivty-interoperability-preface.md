---
title: ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 测试设置：Azure 后端连接功能的互操作性 | Microsoft Docs
description: 本页提供用于分析 ExpressRoute、站点到站点 VPN 和 VNet 对等互连功能互操作性的测试设置。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946949"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>ExpressRoute、站点到站点 VPN 和 VNet 对等互连的互操作性 - 测试设置
在本文中，我们将确定一种用于分析不同的功能如何在控制平面和数据平面级别互操作的测试设置。 在讨论测试设置之前，让我们简单了解一下这些不同的 Azure 网络功能指的是什么。

ExpressRoute：使用 ExpressRoute 专用对等互连可将本地网络的专用 IP 空间直接连接到 Azure VNet 部署。  使用 ExpressRoute 可以实现更高的带宽和专用连接。 有许多 ExpressRoute 生态合作伙伴可以提供 ExpressRoute 连接并以 SLA 作为保障。 若要详细了解 ExpressRoute 以及如何对其进行配置，请参阅 [ExpressRoute 简介][ExpressRoute]

站点到站点 VPN：若要通过 Internet 或 ExpressRoute 将本地网络安全连接到 Azure，可以使用站点到站点 (S2S) VPN 选项。 若要详细了解如何配置用于连接到 Azure 的 S2S VPN，请参阅[配置 VPN 网关][VPN]

VNet 对等互连：使用 VNet 对等互连可在虚拟网络 (VNet) 之间建立连接。 若要详细了解 VNet 对等互连，请参阅[有关 VNet 对等互连的教程][VNet]。

##<a name="test-setup"></a>测试设置

下图演示了测试设置。

[![1]][1]

测试设置的中心部分是 Azure 区域 1 中的中心 VNet。 中心 VNet 按如下所述连接到不同的网络：

1.  通过 VNet 对等互连连接到辐射 VNet。 辐射 VNet 可远程访问中心 VNet 中的两个网关。
2.  通过站点到站点 VPN 连接到分支 VNet。 该连接使用 eBGP 来交换路由。
3.  通过用作主要路径的 ExpressRoute 专用对等互连和用作备用路径的站点到站点 VPN 连接到位置 1 本地网络。 在本文档的余下部分，此 ExpressRoute 线路称作 ExpressRoute1。 默认情况下，ExpressRoute 线路提供冗余连接以实现高可用性。 在 ExpressRoute1 上，已禁用面向辅助 MSEE 的辅助 CE 路由器子接口。 在上图中，在双线箭头上使用一根红线指示了此状态。
4.  通过另一个 ExpressRoute 专用对等互连连接到位置 2 本地网络。 在本文档的余下部分，这第二个 ExpressRoute 线路称作 ExpressRoute2。
5.  ExpressRoute1 也将中心 VNet 和本地位置 1 连接到 Azure 区域 2 中的远程 VNet。

## <a name="further-reading"></a>延伸阅读

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>串联使用 ExpressRoute 和站点到站点 VPN 连接

#### <a name="site-to-site-vpn-over-expressroute"></a>基于 ExpressRoute 的站点到站点 VPN 

可以基于 ExpressRoute Microsoft 对等互连配置站点到站点 VPN，专门用于在本地网络与 Azure VNet 之间交换数据，同时确保保密性、防重播能力、真实性和完整性。 有关如何基于 ExpressRoute Microsoft 对等互连以隧道模式配置站点到站点 IPSec VPN 的详细信息，请参阅[基于 ExpressRoute Microsoft 对等互连的站点到站点 VPN][S2S-Over-ExR]。 

基于 Microsoft 对等互连配置 S2S VPN 的主要限制是吞吐量。 基于 IPSec 隧道的吞吐量受限于 VPN 网关容量。 与 ExpressRoute 吞吐量相比，VPN 网关吞吐量更低。 在这种情况下，对高安全性流量使用 IPSec 隧道，并对其他所有流量使用专用对等互连，将有助于优化 ExpressRoute 带宽利用率。

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>将站点到站点 VPN 用作 ExpressRoute 的安全故障转移路径
ExpressRoute 以冗余线路对的形式提供，以确保高可用性。 可在不同的 Azure 区域配置异地冗余的 ExpressRoute 连接。 如同在测试设置中所做的那样，在给定的 Azure 区域中，若要对 ExpressRoute 连接使用故障转移路径，可以使用站点到站点 VPN 实现此目的。 通过 ExpressRoute 和 S2S VPN 播发相同的前缀时，Azure 会优先使用 ExpressRoute 而不是 S2S VPN。 为了避免 ExpressRoute 与 S2S VPN 之间的非对称路由，本地网络配置同样应该优先使用 ExpressRoute 而不是 S2S VPN 连接。

有关如何配置 ExpressRoute 和站点到站点 VPN 共存连接的详细信息，请参阅 [ExpressRoute 和站点到站点共存][ExR-S2S-CoEx]。

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>将后端连接扩展到辐射 VNet 和分支位置

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>使用 VNet 对等互连建立辐射 VNet 连接

中心辐射型 VNet 体系结构的使用非常广泛。 中心是 Azure 中的一个虚拟网络 (VNet)，充当辐射 VNet 与本地网络之间的连接中心点。 辐射是与中心对等互连的 VNet，可用于隔离工作负荷。 流量通过 ExpressRoute 或 VPN 连接在本地数据中心与中心之间流动。 有关体系结构的详细信息，请参阅[中心辐射型体系结构][Hub-n-Spoke]

区域中的 VNet 对等互连可让辐射 VNet 使用中心 VNet 网关（VPN 和 ExpressRoute 网关）来与远程网络通信。

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>使用站点到站点 VPN 建立分支 VNet 连接

如果希望分支 VNet（在不同区域中）和本地网络通过中心 VNet 相互通信，原生的 Azure 解决方案是使用 VPN 建立站点到站点 VPN 连接。 替代做法是对中心内部的路由使用 NVA。

有关如何配置 VPN 网关，请参阅[配置 VPN 网关][VPN]。 有关如何部署可用性 NVA，请参阅[部署高可用性 NVA][Deploy-NVA]。

## <a name="next-steps"></a>后续步骤

有关测试拓扑的配置详细信息，请参阅[配置详细信息][Configuration]。

有关测试设置的控制平面分析以及拓扑的不同 VNet/VLAN 视图，请参阅[控制平面分析][Control-Analysis]。

有关测试设置的数据平面分析以及 Azure 网络监视功能视图，请参阅[数据平面分析][Data-Analysis]。

若要了解可将多少个 ExpressRoute 线路连接到一个 ExpressRoute 网关，或者可将多少个 ExpressRoute 网关连接到一个 ExpressRoute 线路，或者要了解 ExpressRoute 的其他缩放限制，请参阅 [ExpressRoute 常见问题解答][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "测试拓扑"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




