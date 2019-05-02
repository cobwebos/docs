---
title: Azure 跨网络连接性 |Microsoft Docs
description: 此页介绍了跨网络连接和基于 Azure 网络功能的解决方案的应用程序方案。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 3bc189cf269084fdb26f141a36755c96554cad7b
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866007"
---
# <a name="cross-network-connectivity"></a>跨网连接

Fabrikam Inc. 的实际经营场所众多，并在美国东部部署了 Azure。 Fabrikam 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。 同样，Contoso Ltd.具有状态显示和美国西部的 Azure 部署。 Contoso 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。  

Fabrikam Inc. 并购了 Contoso ltd.并购后，Fabrikam 希望建立网络互连。 下图演示了该场景：

 [![1]][1]

上图的中间的虚线的箭头表示所需的网络互连。 具体而言，有三个交叉连接所需的类型：1） Fabrikam 和 Contoso 跨 Vnet 连接，2） 跨区域的本地和 Vnet 跨连接 （即，Fabrikam 的本地网络连接到 Contoso VNet 和 Contoso 的本地网络连接到 Fabrikam VNet），和 3) Fabrikam 和 Contoso跨本地网络连接。 

下表显示了 ExpressRoute 的 Contoso Ltd.，合并之前的专用对等互连的路由表。

[![2]][2]

下表显示在 Contoso 订阅中，在合并之前的 VM 的有效路由。 每个表，在 VNet 上的 VM 都知道 VNet 地址空间和 Contoso 的本地网络，除了默认值。 

[![4]][4]

下表显示了 ExpressRoute 的 Fabrikam Inc.，在合并之前的专用对等互连的路由表。

[![3]][3]

下表显示在 Fabrikam 订阅中，在合并之前的 VM 的有效路由。 每个表，在 VNet 上的 VM 都知道 VNet 地址空间和 Fabrikam 的本地网络，除了默认值。

[![5]][5]

在本文中，让我们看一下步骤并讨论如何实现所需的交叉连接，使用以下 Azure 网络功能：

* [虚拟网络对等互连][Virtual network peering] 
* [虚拟网络的 ExpressRoute 连接][connection]
* [全球覆盖][Global Reach] 

## <a name="cross-connecting-vnets"></a>跨将 Vnet 连接

连接两个虚拟网络时，虚拟网络对等互连 （VNet 对等互连） 可提供最优和最佳的网络性能。 VNet 对等互连支持 Vnet 对等互连两个同一 Azure 区域 （通常称为 VNet 对等互连） 和 （通常称为全局 VNet 对等互连） 的两个不同 Azure 区域中。 

让我们配置全局 VNet 对等互连之间 Contoso 和 Fabrikam Azure 订阅中的 Vnet。 有关如何创建虚拟网络对等互连两个虚拟网络，请参阅[创建虚拟网络对等互连][ Configure VNet peering]一文。

配置全局 VNet 对等互连后下, 图显示网络体系结构。

[![6]][6]

下表显示了路由到 Contoso 订阅 VM 已知。 特别注意的表的最后一个条目。 此项是跨虚拟网络连接的结果。

[![7]][7]

下表显示了路由到 Fabrikam 订阅 VM 已知。 特别注意的表的最后一个条目。 此项是跨虚拟网络连接的结果。

[![8]][8]

VNet 对等互连直接连接两个虚拟网络 (有无下一跃点，请参阅*VNetGlobalPeering*上述两个表中的条目)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>跨将 Vnet 连接到本地网络

我们可以连接到多个虚拟网络的 ExpressRoute 线路。 请参阅[订阅和服务限制][ Subscription limits]可以连接到 ExpressRoute 线路的虚拟网络的最大数目。 

让我们将为 Contoso 订阅 VNet Fabrikam ExpressRoute 线路和同样 Contoso ExpressRoute 线路连接到 Fabrikam 订阅以启用虚拟网络与本地网络之间的交叉连接的 VNet。 若要连接到其他订阅中的 ExpressRoute 线路的虚拟网络，我们需要创建和使用授权。  请参阅文章：[虚拟网络连接到 ExpressRoute 线路][Connect-ER-VNet]。

下图显示后配置 ExpressRoute 的网络体系结构跨连接到虚拟网络。

[![9]][9]

下表显示了专用对等互连的 ExpressRoute Contoso Ltd.之后, 交叉连接的本地网络通过 ExpressRoute 虚拟网络的路由表。 查看路由表具有属于两个虚拟网络的路由。

[![10]][10]

下表显示了专用对等互连的 ExpressRoute Fabrikam Inc.，在跨虚拟网络连接到本地网络通过 ExpressRoute 的路由表。 查看路由表具有属于两个虚拟网络的路由。

[![11]][11]

下表显示了路由到 Contoso 订阅 VM 已知。 注意*虚拟网络网关*表的条目。 VM 会看到这两个本地网络的路由。

[![12]][12]

下表显示了路由到 Fabrikam 订阅 VM 已知。 注意*虚拟网络网关*表的条目。 VM 会看到这两个本地网络的路由。

[![13]][13]

>[!NOTE]
>在 Fabrikam 和/或 Contoso 订阅还可以让辐射 Vnet 到各自的中心 VNet （中心辐射设计不在本文中的体系结构关系图中所示）。 中心 VNet 与 ExpressRoute 网关之间的交叉连接还将允许东部和西部中心和辐射之间的通信。
>

## <a name="cross-connecting-on-premises-networks"></a>交叉连接的本地网络

ExpressRoute 全局访问提供连接到不同的 ExpressRoute 线路的本地网络之间的连接。 让我们配置 Contoso 和 Fabrikam ExpressRoute 线路之间全球覆盖。 由于 ExpressRoute 线路位于不同的订阅，我们需要创建和使用授权。 请参阅[配置 ExpressRoute 全球覆盖][ Configure Global Reach]一文，了解分步指南。

配置全局到达后下, 图显示网络体系结构。

[![14]][14]

下表显示了专用对等互连的 ExpressRoute Contoso Ltd.之后配置全局访问, 的路由表。 查看路由表具有属于这两个本地网络的路由。 

[![15]][15]

下表显示了专用对等互连的 ExpressRoute Fabrikam Inc.之后配置全局访问, 的路由表。 查看路由表具有属于这两个本地网络的路由。

[![16]][16]

## <a name="next-steps"></a>后续步骤

请参阅[虚拟网络常见问题][VNet-FAQ]，对于任何进一步问题上 VNet 和 VNet 对等互连。 请参阅[ExpressRoute 常见问题][ ER-FAQ]适用于任何其他问题在 ExpressRoute 上和虚拟网络连接。

按国家/地区的国家/地区推出的全球性覆盖。 若要查看全球覆盖是否在所需国家/地区可用，请参阅[ExpressRoute 全球覆盖][Global Reach]。

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "应用场景"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute 路由表，然后合并器"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam ExpressRoute 路由表，然后合并器"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM 将路由之前合并器"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM 将路由之前合并器"
[6]: ./media/cross-network-connectivity/vnet-peering.png "后 VNet 对等互连的体系结构"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "VNet 对等互连后将路由 Contoso VM"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VNet 对等互连后将路由 Fabrikam VM"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "后 ExpressRoutes 交叉连接的体系结构"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "Contoso ExpressRoute 路由表后的，交叉连接 ExR 和 Vnet"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute 路由表后的，交叉连接 ExR 和 Vnet"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "后的 Contoso VM 路由交叉连接 ExR 和 Vnet"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "后的 Fabrikam VM 路由交叉连接 ExR 和 Vnet"
[14]: ./media/cross-network-connectivity/globalreach.png "后配置全球覆盖的体系结构"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Contoso ExpressRoute 路由表后全球覆盖"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute 路由表后全球覆盖"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq