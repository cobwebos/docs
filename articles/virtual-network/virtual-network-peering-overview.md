---
title: Azure 虚拟网络对等互连
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的虚拟网络对等互连，其中包括如何使你能够在 Azure 虚拟网络中连接网络。
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 61617777efa8241c93b2b5ffe42fc1d914b6ebcf
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531864"
---
# <a name="virtual-network-peering"></a>虚拟网络对等互连

利用虚拟网络对等互连，可以在 Azure 中无缝连接两个或多个 [虚拟网络](virtual-networks-overview.md) 。 出于连接目的，两个虚拟网络会显示为一个。 对等互连虚拟网络中虚拟机之间的流量使用 Microsoft 主干基础结构。 类似于同一网络中的虚拟机之间的流量，仅通过 Microsoft 的 *专用* 网络路由流量。

Azure 支持以下类型的对等互连：

* 虚拟网络对等互连：连接同一 Azure 区域中的虚拟网络。
* 全局虚拟网络对等互连：跨 Azure 区域连接虚拟网络。

使用虚拟网络对等互连（无论本地还是全局）的优点包括：

* 不同虚拟网络中资源之间的连接延迟低且带宽高。
* 一个虚拟网络中的资源可与另一个虚拟网络中的资源通信。
* 可以在跨 Azure 订阅、Azure Active Directory 租户、部署模型和 Azure 区域的虚拟网络之间传输数据。
* 可以对等互连通过 Azure 资源管理器创建的虚拟网络。
* 可将通过资源管理器创建的虚拟网络对等互连到通过经典部署模型创建的虚拟网络。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 在创建对等互连之时或之后，虚拟网络中的资源不会出现停机的现象。

对等虚拟网络之间的网络流量是专用的。 虚拟网络之间的流量仅限于 Microsoft 主干网络。 在虚拟网络之间通信不需公共 Internet、网关或加密。

## <a name="connectivity"></a>连接

对于对等互连的虚拟网络，任一虚拟网络中的虚拟机资源可直接连接到对等互连虚拟网络中的资源。

同一区域中对等互连虚拟网络上的虚拟机之间的网络延迟与单个虚拟网络中的延迟相同。 网络吞吐量取决于可供虚拟机使用的与其大小成比例的带宽。 对等互连的带宽没有任何其他限制。

对等互连虚拟网络中虚拟机之间的流量直接通过 Microsoft 主干基础结构路由，而不通过网关或公共 Internet 路由。

可以在虚拟网络中应用网络安全组，以阻止访问其他虚拟网络或子网。
配置虚拟网络对等互连时，可以打开或关闭虚拟网络之间的网络安全组规则。 如果在对等互连的虚拟网络之间建立了完全连接，则可以应用网络安全组来阻止或拒绝特定的访问。 完全连接是默认选项。 若要详细了解网络安全组，请参阅[安全组](security-overview.md)。

## <a name="service-chaining"></a>服务链

使用服务链，可以通过用户定义的路由将流量从一个虚拟网络定向到对等互连网络中的虚拟设备或网关。

若要启用服务链，请将指向对等互连虚拟网络中虚拟机的用户定义的路由配置为下一跃点 IP 地址。  用户定义的路由还可以指向虚拟网络网关，以启用服务链。

可以部署中心辐射型网络，其中，中心虚拟网络托管网络虚拟设备或 VPN 网关等基础结构组件。 然后，可将所有分支虚拟网络对等互连到中心虚拟网络。 流量流经中心虚拟网络中的网络虚拟设备或 VPN 网关。

通过虚拟网络对等互连，用户定义的路由中的下一个跃点可以成为对等虚拟网络中虚拟机或 VPN 网关的 IP 地址。 在使用一个将 Azure ExpressRoute 网关指定为下一跃点类型的用户定义路由的虚拟网络之间不能路由。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined)。 若要了解如何创建中心和分支网络拓扑，请参阅 [Azure 中的中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>网关和本地连接

每个虚拟网络（包括对等互连的虚拟网络）都可以有自身的网关。 虚拟网络可以使用其网关连接到本地网络。 即使是对于对等互连的虚拟网络，也可以使用网关配置[虚拟网络到虚拟网络的连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

为虚拟网络互连配置这两个选项时，虚拟网络之间的流量将通过对等互连配置流动。 该流量使用 Azure 主干网络。

还可以将对等互连的虚拟网络中的网关配置为本地网络的传输点。 在这种情况下，使用远程网关的虚拟网络不能有自身的网关。 一个虚拟网络只能有一个网关。 网关是对等互连虚拟网络中的本地网关或远程网关，如下图所示：

![虚拟网络对等互连传输](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

虚拟网络对等互连和全局虚拟网络对等互连都支持网关传输。

支持在通过不同部署模型创建的虚拟网络之间进行网关传输。 网关必须位于资源管理器模型中的虚拟网络内。 若要了解有关使用网关进行传输的详细信息，请参阅[配置 VPN 网关以在虚拟网络对等互连中传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

对等互连共享单个 Azure ExpressRoute 连接的虚拟网络时，这些虚拟网络之间的流量将通过对等互连关系流动。 该流量使用 Azure 主干网络。 仍可在各个虚拟网络中使用本地网关连接到本地线路。 否则，可以使用共享网关，并为本地连接配置传输。

## <a name="troubleshoot"></a>故障排除

若要确认虚拟网络是否已对等互连，可以检查有效路由。 检查虚拟网络中任一子网内的某个网络接口的路由。 如果虚拟网络对等互连存在，则虚拟网络中的所有子网都会有下一跃点类型为“VNet 对等互连”的路由，这适用于每个对等互连的虚拟网络中的每个地址空间。  有关详细信息，请参阅[诊断虚拟机路由问题](diagnose-network-routing-problem.md)。

还可以使用 Azure 网络观察程序来排查与对等互连虚拟网络中某个虚拟机之间的连接问题。 可以通过连接性检查来确定流量如何从源虚拟机的网络接口路由到目标虚拟机的网络接口。 有关详细信息，请参阅[通过 Azure 门户使用 Azure 网络观察程序排查连接问题](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)。

也可尝试[排查虚拟网络对等互连问题](virtual-network-troubleshoot-peering-issues.md)。

## <a name="constraints-for-peered-virtual-networks"></a>对等互连虚拟网络的约束<a name="requirements-and-constraints"></a>

仅当虚拟网络全局对等互连时，以下约束适用：

* 一个虚拟网络中的资源无法与全局对等互连虚拟网络中基本内部负载均衡器 (ILB) 的前端 IP 地址通信。
* 使用基本负载均衡器的某些服务无法通过全局虚拟网络对等互连正常工作。 有关详细信息，请参阅[与全局 VNet 对等互连和负载均衡器相关的约束有哪些？](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

有关详细信息，请参阅[要求和约束](virtual-network-manage-peering.md#requirements-and-constraints)。 若要详细了解支持的对等互连数，请参阅[网络限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="permissions"></a>权限

若要了解创建虚拟网络对等互连所需的权限，请参阅[权限](virtual-network-manage-peering.md#permissions)。

## <a name="pricing"></a>定价

使用虚拟网络对等互连的传入和传出流量会产生少许费用。 有关详细信息，请参阅[虚拟网络定价](https://azure.microsoft.com/pricing/details/virtual-network)。

网关传输是一种对等互连属性，可以让虚拟网络利用对等互连虚拟网络中的 VPN/ExpressRoute 网关。 网关传输适用于跨界连接和网络间的连接。 传入或传出对等互连虚拟网络中的网关的流量会在分支 VNet（或非网关 VNet）上产生虚拟网络对等互连费用。 请参阅 [VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway/)来详细了解 VPN 网关费用，以及 ExpressRoute 网关的定价和费用。

>[!NOTE]
> 本文档的旧版中已指出，虚拟网络对等互连费用不适用于具有网关传输功能的分支 VNet（或非网关 VNet）。 本文档现在根据定价页反映准确的定价。

## <a name="next-steps"></a>后续步骤

* 可在两个虚拟网络之间创建对等互连。 网络可以属于同一订阅、同一订阅中的不同部署模型，或不同的订阅。 完成适用于以下方案之一的教程：

    |Azure 部署模型             | 订阅  |
    |---------                          |---------|
    |都是资源管理器模型              |[相同](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[不同](create-peering-different-subscriptions.md)|
    |一个是资源管理器模型，一个是经典模型  |[相同](create-peering-different-deployment-models.md)|
    |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 若要了解如何创建中心和分支网络拓扑，请参阅 [Azure 中的中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 若要了解所有虚拟网络对等互连设置，请参阅[创建、更改或删除虚拟网络对等互连](virtual-network-manage-peering.md)。
* 有关常见虚拟网络对等互连和全局虚拟网络对等互连问题的解答，请参阅 [VNet 对等互连](virtual-networks-faq.md#vnet-peering)。
