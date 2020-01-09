---
title: Azure 虚拟网络对等互连
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的虚拟网络对等互连。
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 335b956701a87b6a0e5984ae50507a91d212ab67
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646738"
---
# <a name="virtual-network-peering"></a>虚拟网络对等互连

利用虚拟网络对等互连，可以在[Azure 虚拟网络](virtual-networks-overview.md)中无缝连接网络。 虚拟网络出于连接目的显示为一个虚拟网络。 虚拟机之间的流量使用 Microsoft 主干基础结构。 类似于同一网络中的虚拟机之间的流量，仅通过 Microsoft 的*专用*网络路由流量。

Azure 支持以下类型的对等互连：

* 虚拟网络对等互连：连接同一 Azure 区域内的虚拟网络。
* 全局虚拟网络对等互连：跨 Azure 区域连接虚拟网络。

使用虚拟网络对等互连（无论本地还是全局）的优点包括：

* 不同虚拟网络中资源之间的连接延迟低且带宽高。
* 一个虚拟网络中的资源与另一个虚拟网络中的资源进行通信的能力。
* 跨 Azure 订阅、Azure Active Directory 租户、部署模型和 Azure 区域之间的虚拟网络之间传输数据的功能。
* 通过 Azure 资源管理器对等互连虚拟网络的功能。
* 能够将通过资源管理器创建的虚拟网络对等互连到通过经典部署模型创建的虚拟网络。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 在创建对等互连之时或之后，虚拟网络中的资源不会出现停机的现象。

对等虚拟网络之间的网络流量是专用的。 虚拟网络之间的流量仅限于 Microsoft 主干网络。 在虚拟网络之间通信不需公共 Internet、网关或加密。

## <a name="connectivity"></a>连接性

对于对等互连虚拟网络，任一虚拟网络中的资源可以直接与对等互连虚拟网络中的资源进行连接。

同一区域中对等互连虚拟网络上的虚拟机之间的网络延迟与单个虚拟网络中的延迟相同。 网络吞吐量取决于可供虚拟机使用的与其大小成比例的带宽。 对等互连的带宽没有任何其他限制。

对等互连虚拟网络中虚拟机之间的流量直接通过 Microsoft 主干基础结构路由，而不通过网关或公共 Internet 路由。

可以在虚拟网络中应用网络安全组，阻止对其他虚拟网络或子网的访问。
配置虚拟网络对等互连时，可以打开或关闭虚拟网络之间的网络安全组规则。 如果打开对等互连虚拟网络之间的完全连接，则可以应用网络安全组来阻止或拒绝特定访问。 默认选项为 "完全连接"。 若要了解有关网络安全组的详细信息，请参阅[安全组](security-overview.md)。

## <a name="service-chaining"></a>服务链

使用服务链，可以通过用户定义的路由将流量从一个虚拟网络定向到对等互连网络中的虚拟设备或网关。

若要启用服务链接，请将指向对等互连虚拟网络中的虚拟机的用户定义路由配置为*下一个跃点*IP 地址。 用户定义的路由还可以指向虚拟网络网关，以启用服务链接。

你可以部署中心辐射型网络，其中，中心虚拟网络托管基础结构组件，如网络虚拟设备或 VPN 网关。 然后，可以将所有分散虚拟网络与中心虚拟网络对等。 流量流过中心虚拟网络中的网络虚拟设备或 VPN 网关。

通过虚拟网络对等互连，用户定义的路由中的下一个跃点可以成为对等虚拟网络中虚拟机或 VNP 网关的 IP 地址。 不能使用用户定义的路由（将 Azure ExpressRoute 网关指定为下一跃点类型）在虚拟网络之间进行路由。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined)。 若要了解如何创建中心和分支网络拓扑，请参阅[Azure 中的中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>网关和本地连接

每个虚拟网络（包括对等互连虚拟网络）都可以有自己的网关。 虚拟网络可以使用它的网关连接到本地网络。 你还可以使用网关配置[虚拟网络到虚拟网络连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，即使对于对等互连虚拟网络也是如此。

如果为虚拟网络互连配置这两个选项，则虚拟网络之间的流量将流经对等互连配置。 流量使用 Azure 主干。

你还可以在对等互连虚拟网络中将网关配置为本地网络的传输点。 在这种情况下，使用远程网关的虚拟网络不能有自己的网关。 虚拟网络只有一个网关。 网关是对等互连虚拟网络中的本地或远程网关，如下图所示：

![虚拟网络对等互连传输](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

虚拟网络对等互连和全局虚拟网络对等互连都支持网关传输。

支持在通过不同部署模型创建的虚拟网络之间进行网关传输。 网关必须位于资源管理器型号的虚拟网络中。 若要了解有关使用网关进行传输的详细信息，请参阅[配置 VPN 网关以在虚拟网络对等互连中传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

当对等互连共享单个 Azure ExpressRoute 连接的虚拟网络时，它们之间的流量会通过对等关系进行。 该流量使用 Azure 主干网络。 仍可在各个虚拟网络中使用本地网关连接到本地线路。 否则，可以使用共享网关，并为本地连接配置传输。

## <a name="troubleshoot"></a>故障排除

若要确认虚拟网络是对等互连的，可以检查有效路由。 检查虚拟网络中任何子网中的网络接口的路由。 如果虚拟网络对等互连存在，则虚拟网络中的所有子网都会有下一跃点类型为“VNet 对等互连”的路由，这适用于每个对等互连的虚拟网络中的每个地址空间。 有关详细信息，请参阅[诊断虚拟机路由问题](diagnose-network-routing-problem.md)。

还可以使用 Azure 网络观察程序对对等互连虚拟网络中虚拟机的连接进行故障排除。 连接性检查使你可以查看流量如何从源虚拟机的网络接口路由到目标虚拟机的网络接口。 有关详细信息，请参阅[使用 Azure 门户对 Azure 网络观察程序的连接进行故障排除](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)。

你还可以尝试[排查虚拟网络对等互连问题](virtual-network-troubleshoot-peering-issues.md)。

## 对等互连虚拟网络的约束<a name="requirements-and-constraints"></a>

仅当虚拟网络全局对等互连时，以下约束适用：

* 一个虚拟网络中的资源无法与全局对等互连虚拟网络中的基本内部负载均衡器（ILB）的前端 IP 地址通信。
* 使用基本负载均衡器的某些服务不能通过全局虚拟网络对等互连。 有关详细信息，请参阅与[全局 VNet 对等互连和负载均衡器相关的约束是什么？](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

有关详细信息，请参阅[要求和约束](virtual-network-manage-peering.md#requirements-and-constraints)。 若要了解有关支持的对等互连数量的详细信息，请参阅[网络限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="permissions"></a>权限

若要了解创建虚拟网络对等互连所需的权限，请参阅[权限](virtual-network-manage-peering.md#permissions)。

## <a name="pricing"></a>价格

使用虚拟网络对等互连连接的入口和出口流量需要支付极少的费用。 有关详细信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network)。

网关传输是一种对等互连属性，使虚拟网络能够在对等互连虚拟网络中使用 VPN/ExpressRoute 网关。 网关传输适用于跨界连接和网络到网络连接。 到对等互连虚拟网络中的网关（入站或出站）的流量产生虚拟网络对等互连费用。 有关详细信息，请参阅 vpn 网关[定价](https://azure.microsoft.com/pricing/details/vpn-gateway/)和 expressroute 网关定价以获得 expressroute 网关费用。

>[!NOTE]
> 本文档的以前版本指出，不会将虚拟网络对等互连费用用于网关传输。 它现在按定价页反映准确的定价。

## <a name="next-steps"></a>后续步骤

* 可以在两个虚拟网络之间创建对等互连。 网络可以属于同一订阅、同一订阅中的不同部署模型或不同的订阅。 完成适用于以下方案之一的教程：

    |Azure 部署模型             | 订阅  |
    |---------                          |---------|
    |都是资源管理器模型              |[相同](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[不同](create-peering-different-subscriptions.md)|
    |一个是资源管理器模型，一个是经典模型  |[相同](create-peering-different-deployment-models.md)|
    |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 若要了解如何创建中心和分支网络拓扑，请参阅[Azure 中的中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 若要了解有关所有虚拟网络对等互连设置的信息，请参阅[创建、更改或删除虚拟网络对等互连](virtual-network-manage-peering.md)。
* 有关常见虚拟网络对等互连和全局虚拟网络对等互连问题的解答，请参阅[VNet 对等互连](virtual-networks-faq.md#vnet-peering)。
