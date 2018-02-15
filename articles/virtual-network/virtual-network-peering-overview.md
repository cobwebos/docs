---
title: "Azure 虚拟网络对等互连 | Microsoft 文档"
description: "了解 Azure 中的虚拟网络对等互连。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 7c384f07ec6b71596dcdbc5b7214fa7ce65d0b7d
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="virtual-network-peering"></a>虚拟网络对等互连

使用虚拟网络对等互连可以无缝连接两个 Azure [虚拟网络](virtual-networks-overview.md)。 建立对等互连后，出于连接目的，两个虚拟网络会显示为一个。 对等虚拟网络中虚拟机之间的流量通过 Microsoft 主干基础结构路由，非常类似于只通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由流量。 

使用虚拟网络对等互连的优点包括：

* 对等虚拟网络之间的网络流量是专用的。 虚拟网络之间的流量仅限于 Microsoft 主干网络。 在虚拟网络之间通信不需公共 Internet、网关或加密。
* 不同虚拟网络中资源之间的连接延迟低且带宽高。
* 在虚拟网络对等互连之后，一个虚拟网络中的资源与另一虚拟网络中的资源通信的功能。
* 跨 Azure 订阅、部署模型和 Azure 区域传输数据的功能（预览版）。
* 可将通过 Azure 资源管理器创建的虚拟网络对等互连，或者将一个通过资源管理器创建的虚拟网络对等互连到通过经典部署模型创建的虚拟网络。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 在创建对等互连之时或之后，虚拟网络中的资源不会出现停机的现象。

## <a name="requirements-constraints"></a>要求和约束

* 在同一区域中的虚拟网络之间建立对等互连的功能已推出正式版。 将不同区域中的虚拟网络对等互连的功能目前在以下区域中处于预览状态：美国中西部、加拿大中部、美国西部 2、韩国南部、英国南部、英国西部、加拿大东部、印度南部、印度中部和印度西部。 在不同区域中进行虚拟网络对等互连之前，必须先[注册订阅](virtual-network-create-peering.md#register)（针对预览版）。 如果尚未完成预览版的注册，则无法在不同区域创建虚拟网络之间的对等互连。
    > [!WARNING]
    > 跨区域创建的虚拟网络对等互连与正式版中的对等互连相比，可用性和可靠性级别可能不同。 虚拟网络对等互连的功能可能存在约束，不一定可在所有 Azure 区域中使用。 有关此功能可用性和状态方面的最新通知，请参阅 [Azure Virtual Network updates](https://azure.microsoft.com/updates/?product=virtual-network)（Azure 虚拟网络更新）页。

* 对等虚拟网络 的 IP 地址空间不得重叠。
* 虚拟网络与另一虚拟网络对等后，不能在虚拟网络的地址空间中添加或删除地址范围。 若需向对等互连的虚拟网络的地址空间添加地址范围，必须先删除对等互连，然后添加地址空间，最后再添加对等互连。
* 虚拟网络对等互连在两个虚拟网络之间进行。 对等互连之间没有任何派生的可传递关系。 例如，如果 virtualNetworkA 与 virtualNetworkB 对等互连，而 virtualNetworkB 与 virtualNetworkC 对等互连，则 virtualNetworkA 不会对等互连到 virtualNetworkC。
* 可将两个不同订阅中的虚拟网络对等互连，只要两个订阅的特权用户（请参阅[特定权限](create-peering-different-deployment-models-subscriptions.md#permissions)）授权对等互连，并且订阅与同一个 Azure Active Directory 租户关联即可。 可以使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)来连接关联到不同 Active Directory 租户的订阅中的虚拟网络。
* 如果两个虚拟网络都是通过 Resource Manager 部署模型创建的，或者其中一个虚拟网络是通过 Resource Manager 部署模型创建的，而另一个是通过经典部署模型创建的，则可以将这两个虚拟网络对等互连。 但是，都是通过经典部署模型创建的虚拟网络不能彼此对等互连。 可以使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)来连接通过经典部署模型创建的虚拟网络。
* 虽然在对等虚拟网络中进行虚拟机之间的通信没有其他带宽限制，但有一个最大网络带宽，具体取决于虚拟机大小（仍适用）。 若要深入了解不同 虚拟机大小的最大网络带宽，请参阅有关 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机大小的文章。

     ![基本虚拟网络对等互连](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>连接

将虚拟网络对等互连后，其中一个虚拟网络中的虚拟机资源可直接连接到对等互连虚拟网络中的资源。

同一区域中对等互连虚拟网络上的虚拟机之间的网络延迟与单个虚拟网络中的延迟相同。 网络吞吐量取决于可供虚拟机使用的与其大小成比例的带宽。 对等互连的带宽没有任何其他限制。

对等互连虚拟网络中虚拟机之间的流量直接通过 Microsoft 主干基础结构路由，而不通过网关或公共 Internet 路由。

虚拟网络中的虚拟机可以访问同一区域中对等互连虚拟网络上的内部负载均衡。 对内部负载均衡器的支持并未扩展到全局对等互连的虚拟网络（预览版）。 全局虚拟网络对等互连正式版会支持内部负载均衡器。

可根据需要将网络安全组应用于虚拟网络（阻止访问其他虚拟网络）或子网。
配置虚拟网络对等互连时，可打开或关闭虚拟网络之间的网络安全组规则。 如果打开对等虚拟网络之间的完全连接（这是默认选项），则可将网络安全组应用到特定子网或虚拟机，以便阻止或拒绝特定访问。 若要深入了解网络安全组，请参阅[网络安全组概述](virtual-networks-nsg.md)。

## <a name="service-chaining"></a>服务链

可将指向对等虚拟网络中虚拟机的用户定义的路由表配置为“下一跃点”IP 地址（也可配置指向虚拟网关的用户定义的路由），以便启用服务链。 使用服务链，可以通过用户定义的路由将流量从一个虚拟网络定向到对等虚拟网络中的虚拟设备（或虚拟网关）。

可以部署中心辐射型网络，允许中心虚拟网络在其中托管基础结构组件，如网络虚拟设备或 VPN 网关。 然后，可以将所有分散虚拟网络与中心虚拟网络对等。 流量可以流经中心虚拟网络中的网络虚拟设备或 VPN 网关。 

通过虚拟网络对等互连，用户定义的路由中的下一个跃点可以成为对等虚拟网络中虚拟机或 VNP 网关的 IP 地址。 但在虚拟网络之间进行路由时，不能通过用户定义的路由将 ExpressRoute 网关指定为下一跃点类型。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined)。 若要了解如何创建中心和分支网络拓扑，请参阅[中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)。

## <a name="gateways-and-on-premises-connectivity"></a>网关和本地连接

无论是否与另一个虚拟网络对等，每个虚拟网络仍可具有自己的网关，并使用它连接到本地网络。 即使虚拟网络对等，用户也可以使用网关配置[虚拟网络到虚拟网络连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

若已配置虚拟网络互连的两个选项，则虚拟网络之间的流量将通过对等配置（即通过 Azure 主干）流通。

在同一区域中建立虚拟网络对等互连后，还可将对等互连虚拟网络中的网关配置为本地网络的传输点。 在这种情况下，使用远程网关的虚拟网络没有自己的网关。 虚拟网络只能有一个网关。 网关可能是本地网关或远程网关（对等虚拟网络中），如下图所示：

![虚拟网络对等互连传输](./media/virtual-networks-peering-overview/figure04.png)

通过不同部署模型或不同区域创建的虚拟网络之间的对等互连关系不支持网关传输。 若要使网关传输正常工作，对等互连关系中的两个虚拟网络都必须通过资源管理器创建，并且必须在同一区域中。 全局对等互连的虚拟网络目前不支持网关传输。

正在共享单个 Azure ExpressRoute 连接的虚拟网络对等时，它们之间的流量会通过对等关系（即通过 Azure 主干网）流通。 仍可在各个虚拟网络中使用本地网关连接到本地线路。 也可使用共享网关，并为本地连接配置传输。

## <a name="permissions"></a>权限

虚拟网络对等互连是一项特权操作。 它是 VirtualNetworks 命名空间下的独立功能。 可授予用户特定权限来授权对等互连。 具有虚拟网络读写访问权限的用户自动继承这些权限。

管理员或具有对等互连能力的特权用户可在另一个虚拟网络上启动对等互连操作。 所需的最低权限级别为“网络参与者”。 如果另一端存在对等互连的匹配请求且也满足其他要求，则会建立对等互连。

例如，如果在名为 myvirtual networkA 和 myvirtual networkB 的虚拟网络之间建立对等互连，则必须为帐户分配针对每个虚拟网络的以下最低角色或权限：

|虚拟网络|部署模型|角色|权限|
|---|---|---|---|
|myVirtualNetworkA|资源管理器|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|不适用|
|myVirtualNetworkB|资源管理器|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||经典|[经典网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>监视

对等互连两个通过资源管理器创建的虚拟网络时，必须为对等互连中的每个虚拟网络都配置对等互连。 可以监视对等互连的状态。 对等互连处于以下状态之一：

* **已启动**：创建从第一个虚拟网络到第二个虚拟网络的对等互连时显示的状态。
* **已连接**：创建从第二个虚拟网络到第一个虚拟网络的对等互连后显示的状态。 第一个虚拟网络的对等互连状态从“已启动”更改为“已连接”。 直到两个虚拟网络对等互连的状态均为“已连接”时，虚拟网络对等互连才成功建立。
* **已断开连接**：如果在两个虚拟网络之间建立对等互连后删除了从一个虚拟网络到另一个虚拟网络的对等互连，则这种情况下显示的状态为“已断开连接”。

## <a name="troubleshoot"></a>故障排除

若要确认虚拟网络对等互连，可以[检查有效路由](virtual-network-routes-troubleshoot-portal.md)，看虚拟网络的任何子网中是否存在网络接口。 如果虚拟网络对等互连存在，则虚拟网络中的所有子网都会有下一跃点类型为“VNet 对等互连”的路由，这适用于每个对等互连的虚拟网络中的每个地址空间。

还可以使用网络观察程序的[连接性检查](../network-watcher/network-watcher-connectivity-portal.md)来排查与对等互连虚拟网络中某个虚拟机之间的连接问题。 可以通过连接性检查来确定流量如何从源虚拟机的网络接口路由到目标虚拟机的网络接口。

## <a name="limits"></a>限制

允许单个虚拟网络建立的对等互连数存在限制。 有关详细信息，请参阅 [Azure 网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="pricing"></a>定价

利用虚拟网络对等互连的入口和出口流量会产生少许费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="next-steps"></a>后续步骤

* 完成虚拟网络对等互连教程。 可在通过相同或不同订阅中的相同或不同部署模型创建的虚拟网络之间创建虚拟网络对等互连。 完成适用于以下方案之一的教程：

    |Azure 部署模型  | 订阅  |
    |---------|---------|
    |都是资源管理器模型 |[相同](virtual-network-create-peering.md)|
    | |[不同](create-peering-different-subscriptions.md)|
    |一个是资源管理器模型，一个是经典模型     |[相同](create-peering-different-deployment-models.md)|
    | |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 了解如何创建[中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)。
* 了解所有[虚拟网络对等互连设置以及如何对其进行更改](virtual-network-manage-peering.md)
