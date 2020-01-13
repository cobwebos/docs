---
title: Azure 跨网络连接
description: 本页介绍跨网络连接和基于 Azure 网络功能的解决方案的应用程序方案。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644250"
---
# <a name="cross-network-connectivity"></a>跨网连接

Fabrikam Inc. 的实际经营场所众多，并在美国东部部署了 Azure。 Fabrikam 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。 同样，Contoso 公司在美国西部有一个存在和 Azure 部署。 Contoso 通过 ExpressRoute 在其本地与 Azure 部署之间建立了后端连接。  

Fabrikam Inc. 获取 Contoso 有限公司。在合并后，Fabrikam 想要将网络互连起来。 下图演示了该场景：

 [![1]][1]

上图中间的虚线箭头表示所需的网络互连。 具体而言，需要三种类型的交叉连接：1） Fabrikam 和 Contoso Vnet 交叉连接，2）跨区域本地和 Vnet 交叉连接（也就是说，将 Fabrikam 本地网络连接到 Contoso VNet 并连接 Contoso）本地网络到 Fabrikam VNet）和3） Fabrikam 和 Contoso 本地网络交叉连接。 

下表显示了在合并之前 Contoso 有限公司的 ExpressRoute 的专用对等互连的路由表。

[![2]][2]

下表显示了在合并之前 Contoso 订阅中的 VM 的有效路由。 根据表，VNet 中的 VM 知道 VNet 地址空间和 Contoso 本地网络，而不是默认网络。 

[![4]][4]

下表显示了在合并之前 Fabrikam Inc. 的 ExpressRoute 的专用对等互连的路由表。

[![3]][3]

下表显示了在合并之前 Fabrikam 订阅中的 VM 的有效路由。 根据表，VNet 中的 VM 知道 VNet 地址空间和 Fabrikam 本地网络，而不是默认网络。

[![5]][5]

在本文中，让我们逐步介绍如何使用以下 Azure 网络功能实现所需的交叉连接：

* [虚拟网络对等互连][Virtual network peering] 
* [虚拟网络 ExpressRoute 连接][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>交叉连接 Vnet

连接两个虚拟网络时，虚拟网络对等互连（VNet 对等互连）提供最佳的最佳网络性能。 VNet 对等互连支持两个 Vnet 在同一 Azure 区域（通常称为 VNet 对等互连）和两个不同的 Azure 区域（通常称为全局 VNet 对等互连）中。 

让我们在 Contoso 和 Fabrikam Azure 订阅中的 Vnet 之间配置全局 VNet 对等互连。 有关如何在两个虚拟网络之间创建虚拟网络对等互连的详细说明，请参阅[创建虚拟网络对等互连一][Configure VNet peering]文。

下图显示了配置全局 VNet 对等互连后的网络体系结构。

[![6]][6]

下表显示了 Contoso 订阅 VM 已知的路由。 注意表的最后一项。 此条目是将虚拟网络连接在一起的结果。

[![7]][7]

下表显示了 Fabrikam 订阅 VM 已知的路由。 注意表的最后一项。 此条目是将虚拟网络连接在一起的结果。

[![8]][8]

VNet 对等互连直接链接两个虚拟网络（请参阅上面两个表中的*VNetGlobalPeering*条目没有下一个跃点）

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>将 Vnet 连接到本地网络

我们可以将 ExpressRoute 线路连接到多个虚拟网络。 请参阅[订阅和服务限制][Subscription limits]，了解可连接到 ExpressRoute 线路的虚拟网络的最大数量。 

让我们将 Fabrikam ExpressRoute 线路连接到 Contoso 订阅 VNet，并将类似的 Contoso ExpressRoute 线路连接到 Fabrikam 订阅 VNet，以便在虚拟网络和本地网络之间实现跨连接。 若要将虚拟网络连接到其他订阅中的 ExpressRoute 线路，需要创建并使用授权。  请参阅文章：[将虚拟网络连接到 ExpressRoute 线路][Connect-ER-VNet]。

下图显示了配置与虚拟网络的 ExpressRoute 交叉连接后的网络体系结构。

[![9]][9]

下表显示了在通过 ExpressRoute 将虚拟网络连接到本地网络后，Contoso 公司的 ExpressRoute 的专用对等互连的路由表。 查看路由表中是否有属于这两个虚拟网络的路由。

[![10]][10]

下表显示了 Fabrikam Inc. 的 ExpressRoute 的专用对等互连的路由表，并将虚拟网络通过 ExpressRoute 连接到本地网络后。 查看路由表中是否有属于这两个虚拟网络的路由。

[![11x17]][11]

下表显示了 Contoso 订阅 VM 已知的路由。 请注意表的*虚拟网络网关*条目。 VM 会看到本地网络的路由。

[![10]][12]

下表显示了 Fabrikam 订阅 VM 已知的路由。 请注意表的*虚拟网络网关*条目。 VM 会看到本地网络的路由。

[![9]][13]

>[!NOTE]
>在 Fabrikam 和/或 Contoso 订阅中，还可以将分支 Vnet 到相应的中心 VNet （本文的体系结构关系图中未阐释中心和辐射设计）。 中心 VNet 网关与 ExpressRoute 之间的交叉连接还允许在东与西中心与轮辐之间进行通信。
>

## <a name="cross-connecting-on-premises-networks"></a>跨本地网络连接

ExpressRoute Global Reach 提供连接到不同 ExpressRoute 线路的本地网络之间的连接。 接下来，配置 Contoso 和 Fabrikam ExpressRoute 线路之间的 Global Reach。 由于 ExpressRoute 线路位于不同的订阅中，因此我们需要创建并使用授权。 有关分步指南，请参阅[配置 ExpressRoute Global Reach][Configure Global Reach]一文。

下图显示了配置 Global Reach 后的网络体系结构。

[![毫米]][14]

下表显示了 Contoso 有限公司的 ExpressRoute 的专用对等互连的路由表 Global Reach。 查看路由表中是否有属于本地网络的路由。 

[![15]][15]

下表显示了 Fabrikam Inc. 的 ExpressRoute 的专用对等互连的路由表，配置后 Global Reach。 查看路由表中是否有属于本地网络的路由。

[![超过]][16]

## <a name="next-steps"></a>后续步骤

有关 VNet 和 VNet 对等互连的任何其他问题，请参阅[虚拟网络常见问题解答][VNet-FAQ]。 有关 ExpressRoute 和虚拟网络连接的其他问题，请参阅[EXPRESSROUTE 常见问题][ER-FAQ]。

Global Reach 在国家/地区按国家/地区推出。 若要查看 Global Reach 是否在所需的国家/地区可用，请参阅[ExpressRoute Global Reach][Global Reach]。

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "应用场景"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "Contoso ExpressRoute 路由之前的合并"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "Fabrikam 之前的 Fabrikam ExpressRoute 路由表"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM 在合并前的路由"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "合并前的 Fabrikam VM 路由"
[6]: ./media/cross-network-connectivity/vnet-peering.png "与 VNet 对等互连后的体系结构"
[]: ./media/cross-network-connectivity/contosovm-routes-peering.png "在 VNet 对等互连后 7 Contoso VM 路由"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "在 VNet 对等互连后8个 Fabrikam VM 路由"
[]: ./media/cross-network-connectivity/exr-x-connect.png "ExpressRoutes 交叉连接后的体系结构"9
[]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "在交叉连接 .exr 和 vnet 之后10个 Contoso ExpressRoute 路由表"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "在交叉连接 .exr 和 vnet 之后的 Fabrikam ExpressRoute 路由表"
[]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "在交叉连接 .exr 和 vnet 后，为12个 Contoso VM 路由"
[]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "交叉连接 .exr 和 vnet 之后的13个 Fabrikam VM 路由"
[14]: ./media/cross-network-connectivity/globalreach.png "配置 Global Reach 的体系结构"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "Global Reach 之后的 Contoso ExpressRoute 路由表"
Global Reach 后的[16 个]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "Fabrikam ExpressRoute 路由表"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq