---
title: Azure 跨网络连接
description: 本页介绍跨网络连接和基于 Azure 网络功能的解决方案的应用场景。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: e503dc2b4ae8773ebfedc7a9b73bc5ea93dd9d5a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076746"
---
# <a name="cross-network-connectivity"></a>跨网连接

Fabrikam Inc. 的实际经营场所众多，并在美国东部部署了 Azure。 Fabrikam 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。 同样，Contoso Ltd. 也有一定数量的经营场所，并在美国西部部署了 Azure。 Contoso 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。  

Fabrikam Inc. 获取 Contoso 有限公司。在合并后，Fabrikam 想要将网络互连起来。 下图演示了该场景：

 [![1]][1]

上图中间的虚线箭头表示所需的网络互连。 具体而言，需要三种类型的交叉连接：1） Fabrikam 和 Contoso Vnet 交叉连接，2）跨区域本地和 Vnet 交叉连接（也就是说，将 Fabrikam 本地网络连接到 Contoso VNet 并连接 Contoso）本地网络到 Fabrikam VNet）和3） Fabrikam 和 Contoso 本地网络交叉连接。 

下表显示了并购之前，Contoso Ltd. 的 ExpressRoute 专用对等互连路由表。

[![2]][2]

下表显示了合并之前 Contoso 订阅中 VM 的有效路由。 根据该表，VNet 中的 VM 可以识别 VNet 地址空间和 Contoso 本地网络，此外还能识别默认网络。 

[![4]][4]

下表显示了并购之前，Fabrikam Inc. 的 ExpressRoute 专用对等互连路由表。

[![3]][3]

下表显示了合并之前 Fabrikam 订阅中 VM 的有效路由。 根据该表，VNet 中的 VM 可以识别 VNet 地址空间和 Fabrikam 本地网络，此外还能识别默认网络。

[![5]][5]

本文逐步介绍如何使用以下 Azure 网络功能实现所需的交叉连接：

* [虚拟网络对等互连][Virtual network peering] 
* [虚拟网络 ExpressRoute 连接][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>交叉连接 VNet

连接两个虚拟网络时，虚拟网络对等互连 (VNet 对等互连) 可提供最佳网络性能。 VNet 对等互连既支持同一 Azure 区域中的两个 VNet（通常称为 VNet 对等互连），也支持两个不同 Azure 区域中的 VNet（通常称为全局 VNet 对等互连）。 

让我们在 Contoso 和 Fabrikam Azure 订阅中的 VNet 之间配置全局 VNet 对等互连。 有关如何在两个虚拟网络之间创建虚拟网络对等互连，请参阅[创建虚拟网络对等互连][Configure VNet peering]一文。

下图显示了配置全局 VNet 对等互连后的网络体系结构。

[![6]][6]

下表显示了 Contoso 订阅 VM 已知的路由。 请注意表格中的最后一项。 此项是交叉连接虚拟网络后的结果。

[![7]][7]

下表显示了 Fabrikam 订阅 VM 已知的路由。 请注意表格中的最后一项。 此项是交叉连接虚拟网络后的结果。

[![8]][8]

VNet 对等互连直接将两个虚拟网络链接到一起（在上面的两个表格中可以看到，*VNetGlobalPeering* 项没有下一跃点）

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>将 VNet 交叉连接到本地网络

我们可以将 ExpressRoute 线路连接到多个虚拟网络。 有关可连接到 ExpressRoute 线路的最大虚拟网络数量，请[参阅订阅和服务限制][Subscription limits]。 

让我们将 Fabrikam ExpressRoute 线路连接到 Contoso 订阅 VNet，并将 Contoso ExpressRoute 线路连接到 Fabrikam 订阅 VNet，以便在虚拟网络与本地网络之间实现交叉连接。 若要将虚拟网络连接到其他订阅中的 ExpressRoute 线路，需要创建并使用授权。  请参阅文章：[将虚拟网络连接到 ExpressRoute 线路][Connect-ER-VNet]。

下图显示了配置 ExpressRoute 与虚拟网络之间的交叉连接后的网络体系结构。

[![9]][9]

下表显示了通过 ExpressRoute 将虚拟网络交叉连接到本地网络后，Contoso Ltd. 的 ExpressRoute 专用对等互连路由表。 可以看到，该路由表包含属于这两个虚拟网络的路由。

[![10]][10]

下表显示了通过 ExpressRoute 将虚拟网络交叉连接到本地网络后，Fabrikam Ltd. 的 ExpressRoute 专用对等互连路由表。 可以看到，该路由表包含属于这两个虚拟网络的路由。

[![11]][11]

下表显示了 Contoso 订阅 VM 已知的路由。 请注意表格中的“虚拟网络网关”项。 VM 会看到这两个本地网络的路由。

[![12]][12]

下表显示了 Fabrikam 订阅 VM 已知的路由。 请注意表格中的“虚拟网络网关”项。 VM 会看到这两个本地网络的路由。

[![13]][13]

>[!NOTE]
>在 Fabrikam 和/或 Contoso 订阅中，还可以将辐射 VNet 连接到相应的中心 VNet（本文中的体系结构图未演示中心辐射型设计）。 中心 VNet 网关与 ExpressRoute 之间的交叉连接还允许东部和西部中心与辐射之间的通信。
>

## <a name="cross-connecting-on-premises-networks"></a>交叉连接本地网络

ExpressRoute Global Reach 在已连接到不同 ExpressRoute 线路的本地网络之间提供连接。 让我们在 Contoso 与 Fabrikam ExpressRoute 线路之间配置 Global Reach。 由于 ExpressRoute 线路位于不同的订阅中，因此我们需要创建并使用授权。 有关分步指导，请参阅[配置 ExpressRoute Global Reach][Configure Global Reach] 一文。

下图显示了配置 Global Reach 后的网络体系结构。

[![14]][14]

下表显示了配置 Global Reach 之后，Contoso Ltd. 的 ExpressRoute 专用对等互连路由表。 可以看到，该路由表包含属于这两个本地网络的路由。 

[![15]][15]

下表显示了配置 Global Reach 之后，Fabrikam Inc. 的 ExpressRoute 专用对等互连路由表。 可以看到，该路由表包含属于这两个本地网络的路由。

[![16]][16]

## <a name="next-steps"></a>后续步骤

如果遇到 VNet 和 VNet 对等互连的其他任何问题，请参阅[虚拟网络常见问题解答][VNet-FAQ]。 如果遇到 ExpressRoute 和虚拟网络连接的其他任何问题，请参阅 [ExpressRoute 常见问题解答][ER-FAQ]。

Global Reach 是按国家/地区推出的。 若要查看 Global Reach 是否在所需的国家/地区推出，请参阅 [ExpressRoute Global Reach][Global Reach]。

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "应用场景"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "并购之前的 Contoso ExpressRoute 路由表"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "并购之前的 Fabrikam ExpressRoute 路由表"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "并购之前的 Contoso VM 路由"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "并购之前的 Fabrikam VM 路由"
[6]: ./media/cross-network-connectivity/vnet-peering.png "建立 VNet 对等互连后的体系结构"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "建立 VNet 对等互连后的 Contoso VM 路由"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "建立 VNet 对等互连后的 Fabrikam VM 路由"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "建立 ExpressRoute 交叉连接后的体系结构"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "交叉连接 ExpressRoute 和 VNet 后的 Contoso ExpressRoute 路由表"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "交叉连接 ExpressRoute 和 VNet 后的 Fabrikam ExpressRoute 路由表"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "交叉连接 ExpressRoute 和 VNet 后的 Contoso VM 路由"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "交叉连接 ExpressRoute 和 VNet 后的 Fabrikam VM 路由"
[14]: ./media/cross-network-connectivity/globalreach.png "配置 Global Reach 后的体系结构"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "配置 Global Reach 后的 Contoso ExpressRoute 路由表"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "配置 Global Reach 后的 Fabrikam ExpressRoute 路由表"

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