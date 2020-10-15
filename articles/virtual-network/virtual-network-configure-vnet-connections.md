---
title: 配置和验证虚拟网络或 VPN 连接
description: 有关配置和验证各种 Azure VPN 与虚拟网络部署的分步指导
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: d6532747c50311ada4df6a0038bd0e05f4d9ce31
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089683"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>配置和验证虚拟网络或 VPN 连接

本演练提供有关配置和验证各种 Azure VPN 与虚拟网络部署的分步指导。 方案包括传输路由、网络间的连接、边界网关协议 (BGP)、多站点连接和点到站点连接。

借助 Azure VPN 网关的灵活性，可以在 Azure 中排列几乎所有类型的互连式虚拟网络拓扑。 例如，可以建立如下所述的虚拟网络连接：

- 跨区域。
- 在不同的虚拟网络类型（Azure 资源管理器与经典模型）之间。
- 在 Azure 内部，或者在本地混合环境内部。
- 在不同的订阅中。 

## <a name="network-to-network-vpn-connection"></a>网络间的 VPN 连接

通过 VPN 将一个虚拟网络连接到另一个虚拟网络（网络间连接）类似于将虚拟网络连接到本地站点位置。 这两种连接类型都使用 VPN 网关通过 IPsec 和 IKE 提供安全隧道。 虚拟网络可位于相同或不同的区域，来自相同或不同的订阅。

![使用 IPsec 的网络间连接](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
如果虚拟网络位于同一区域，你可能会考虑使用虚拟网络对等互连来连接这些虚拟网络。 虚拟网络对等互连不使用 VPN 网关。 它可以提高吞吐量并降低延迟。 若要配置虚拟网络对等互连，请选择“配置并验证 VNet 对等互连”。 

如果虚拟网络是通过 Azure 资源管理器部署模型创建的，请选择“配置并验证资源管理器 VNet 到资源管理器 VNet 的连接”来配置 VPN 连接。 

如果一个虚拟网络是通过 Azure 经典部署模型创建的，另一个虚拟网络是通过资源管理器创建的，请选择“配置并验证经典 VNet 到资源管理器 VNet 的连接”来配置 VPN 连接。 

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>为同一区域中的两个虚拟网络配置虚拟网络对等互连

在开始实施并配置 Azure 虚拟网络对等互连之前，请确保满足以下先决条件：

* 对等虚拟网络必须位于同一 Azure 区域。
* 对等虚拟网络必须使用不重叠的 IP 地址空间。
* 虚拟网络对等互连在两个虚拟网络之间进行。 对等互连之间没有任何派生的可传递关系。 例如，如果 VNetA 与 VNetB 对等互连，VNetB 与 VNetC 对等互连，但 VNetA 不与 VNetC 对等互连。 

满足要求后，可以根据[教程：使用 Azure 门户通过虚拟网络对等互连来连接虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)创建并配置对等互连。

若要检查对等互连配置，请使用以下方法：

1. 使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 在门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。   当“虚拟网络”出现在搜索结果中时，请选择它。 
3. 在显示的“虚拟网络”边栏选项卡中，选择想要为其创建对等互连的虚拟网络。 
4. 在针对所选虚拟网络显示的窗格中，选择“设置”部分中的“对等互连”。  
5. 选择一个对等互连并查看配置结果。

![用于检查虚拟网络对等互连配置的选项](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
在 Azure PowerShell 中，运行命令 [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) 获取虚拟网络对等互连。 下面是一个示例：

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>将资源管理器虚拟网络连接到另一个资源管理器虚拟网络

可以直接配置从一个资源管理器虚拟网络到另一个资源管理器虚拟网络的连接。 或者，可以配置使用 IPsec 的连接。

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>在资源管理器虚拟网络之间配置 VPN 连接

若要在资源管理器虚拟网络之间配置不使用 IPsec 的连接，请参阅[使用 Azure 门户配置网络间的 VPN 网关连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)。

若要在两个资源管理器虚拟网络之间配置使用 IPsec 的连接，请针对每个虚拟网络遵循[在 Azure 门户中创建站点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)中的步骤 1-5。

> [!Note]
> 这些步骤仅适用于同一订阅中的虚拟网络。 如果虚拟网络在不同的订阅中，则必须使用 PowerShell 进行连接。 请参阅 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 一文。

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>验证资源管理器虚拟网络之间的 VPN 连接

![经典虚拟网络到 Azure 资源管理器虚拟网络的连接](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

若要检查是否已正确配置 VPN 连接，请按以下说明操作。

> [!Note] 
> 在这些步骤中，虚拟网络组件后面的编号对应于上图中的编号。

1. 请确保连接的虚拟网络中不存在重叠的地址空间。
2. 验证是否在“连接对象”实例 (4) 中准确定义了 Azure 资源管理器虚拟网络的地址范围 (1)。 
3. 验证是否在“连接对象”实例 (3) 中准确定义了 Azure 资源管理器虚拟网络的地址范围 (6)。 
4. 验证连接对象中的预共享密钥是否匹配。
5. 验证是否在“连接对象”实例 (4) 中准确定义了 Azure 资源管理器虚拟网络网关 VIP (2)。 
6. 验证是否在“连接对象”实例 (3) 中准确定义了 Azure 资源管理器虚拟网络网关 VIP (5)。 

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>将经典虚拟网络连接到资源管理器虚拟网络

可以在位于不同订阅、不同区域中的虚拟网络之间创建连接。 还可以连接已连接到本地网络的虚拟网络，前提是已将网关类型配置为基于路由。

若要在经典虚拟网络与资源管理器虚拟网络之间配置连接，请参阅[使用 Azure 门户从不同的部署模型连接虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)。

![显示与 Azure 资源管理器虚拟网络的经典虚拟网络连接的关系图。](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

若要检查将经典虚拟网络连接到 Azure 资源管理器虚拟网络时的配置，请按以下说明操作。

> [!Note] 
> 在这些步骤中，虚拟网络组件后面的编号对应于上图中的编号。 

1. 请确保连接的虚拟网络中不存在重叠的地址空间。
2. 验证是否在经典本地网络定义 (3) 中准确定义了 Azure 资源管理器虚拟网络的地址范围 (6)。
3. 验证是否在 Azure 资源管理器“连接对象”实例 (4) 中准确定义了经典虚拟网络的地址范围 (1)。 
4. 验证是否在 Azure 资源管理器“连接对象”实例 (4) 中准确定义了经典虚拟网络网关 VIP (2)。 
5. 验证是否在经典“本地网络定义”实例 (3) 中准确定义了 Azure 资源管理器虚拟网络网关 (5)。 
6. 验证两个连接的虚拟网络上的预共享密钥是否匹配：
   - 经典虚拟网络：**本地网络定义** (3)
   - Azure 资源管理器虚拟网络：**连接对象** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>创建点到站点 VPN 连接

使用点到站点（下图中的 *P2S*）配置可以创建从单个客户端计算机到虚拟网络的安全连接。 如果要从远程位置（例如，从家里或会议室）连接到虚拟网络，点到站点连接会很有用。 只有少数几个客户端需要连接到虚拟网络时，这种连接也很有用。 

点到站点 VPN 连接通过本机 Windows VPN 客户端从客户端计算机启动。 连接客户端使用证书进行身份验证。

![点到站点连接](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

点到站点连接不需要 VPN 设备。 它们创建基于安全套接字隧道协议 (SSTP) 的 VPN 连接。 可以使用不同的部署工具和部署模型来与虚拟网络建立点到站点连接：

* [使用 Azure 门户配置与虚拟网络的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [使用 Azure 门户（经典）配置与虚拟网络的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [使用 PowerShell 配置与虚拟网络的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>验证点到站点连接

[故障排除：Azure 点到站点连接问题](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)一文逐步讲解了如何排查点到站点连接的常见问题。

## <a name="create-a-multisite-vpn-connection"></a>创建多站点 VPN 连接

可将站点到站点（下图中的 *S2S*）连接添加到已建立站点到站点连接、点到站点连接或网络间连接的虚拟网络。 这种连接通常称为多站点配置。  

![多站点连接](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure 当前使用两种部署模型：资源管理器部署模型和经典部署模型。 这两种模型彼此不完全兼容。 若要使用不同的模型配置多站点连接，请参阅以下文章：

* [将站点到站点连接添加到使用现有 VPN 网关连接的虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [将站点到站点连接添加到使用现有 VPN 网关连接的虚拟网络（经典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 这些文章中的步骤不适用于 Azure ExpressRoute 和站点到站点共存连接配置。 有关详细信息，请参阅 [ExpressRoute 和站点到站点共存连接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

## <a name="configure-transit-routing"></a>配置传输路由

传输路由是一种特定的路由方案，在其中可以“菊花链”拓扑的形式连接多个网络。 此路由方式可让链两端的虚拟网络中的资源通过其间的虚拟网络相互通信。 如果没有传输路由，通过中心对等互连的网络或设备无法相互访问。

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>在点到站点连接中配置传输路由

假设你要在 VNetA 与 VNetB 之间配置站点到站点 VPN 连接。 此外，你还想要配置点到站点 VPN，使客户端能够连接到 VNetA 的网关。 然后希望启用传输路由，使点到站点客户端能够通过 VNetA 连接到 VNetB。 

在 VNetA 与 VNetB 之间的站点到站点 VPN 上启用 BGP 后，将会支持此方案。 有关详细信息，请参阅[关于点到站点 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)。

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>在 ExpressRoute 连接中配置传输路由

使用 Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Microsoft 365 和 Dynamics 365 等 Microsoft 云服务建立连接。 有关详细信息，请参阅 [ExpressRoute 概述](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

![与 Azure虚拟网络建立 ExpressRoute 专用对等互连](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> 我们建议，如果 VNetA 和 VNetB 位于同一地缘政治区域，则[将两个虚拟网络都链接到 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)，而不要配置传输路由。 如果虚拟网络位于不同的地缘政治区域，并且你已获得 [ExpressRoute 高级版](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)，则也可以直接将虚拟网络链接到自己的线路。 

如果 ExpressRoute 和站点到站点连接共存，则不支持传输路由。 有关详细信息，请参阅[使用 PowerShell 配置 ExpressRoute 和站点到站点连接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

如果已启用 ExpressRoute 以将本地网络连接到 Azure 虚拟网络，则可以在要使用传输路由的虚拟网络之间启用对等互连。 要使本地网络能够连接到远程虚拟网络，必须配置[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)。 

> [!Note]
> 虚拟网络对等互连仅适用于同一区域中的虚拟网络。

若要检查是否为虚拟网络对等互连配置了传输路由，请按以下说明操作：

1. 使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 按前面的示意图所示，[在 VNetA 与 VNetB 之间创建对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)。 
3. 在针对所选虚拟网络显示的窗格中，选择“设置”部分中的“对等互连”。  
4. 选择要查看的对等互连。 然后选择“配置”，验证是否已在连接到 ExpressRoute 线路的 VNetA 网络上启用了“允许网关传输”，并在未连接到 ExpressRoute 线路的远程 VNetB 网络上启用了“使用远程网关”。   

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>在虚拟网络对等互连中配置传输路由

将虚拟网络对等互连后，还可以将对等虚拟网络中的网关配置为本地网络的传输点。 若要在虚拟网络对等互连中配置传输路由，请参阅[网络间的连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)。

> [!Note]
> 通过不同部署模型创建的虚拟网络之间的对等互连关系不支持网关传输。 若要使用网关传输，对等互连关系中的两个虚拟网络都必须通过资源管理器创建。

若要检查是否为虚拟网络对等互连配置了传输路由，请按以下说明操作：

1. 使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 在门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。   当“虚拟网络”出现在搜索结果中时，请选择它。 
3. 在显示的“虚拟网络”边栏选项卡中，选择要检查其对等互连设置的虚拟网络。 
4. 在针对所选虚拟网络显示的窗格中，选择“设置”部分中的“对等互连”。  
5. 选择要查看的对等互连。 在“配置”下验证是否已启用“允许网关传输”和“使用远程网关”。   

![用于检查是否已为虚拟网络对等互连配置传输路由的选项](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>在网络间连接中配置传输路由

若要在虚拟网络之间配置传输路由，必须使用资源管理器部署模型和 PowerShell 在所有中间网络间连接上启用 BGP。 有关说明，请参阅[如何使用 PowerShell 在 Azure VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

使用经典部署模型通过 Azure VPN 网关传输流量是可行的，但依赖于网络配置文件中静态定义的地址空间。 尚不支持通过经典部署模型使用 Azure 虚拟网络和 VPN 网关配置 BGP。 如果没有 BGP，手动定义传输地址空间很容易出错，因此不建议这样做。

> [!Note]
> 使用 Azure 经典门户或在经典门户中使用网络配置文件来配置经典网络到网络连接。 无法通过 Azure 资源管理器部署模型或 Azure 门户来创建或修改经典虚拟网络。 有关经典虚拟网络传输路由的详细信息，请参阅 [Microsoft 开发人员博客](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>在站点到站点连接中配置传输路由

若要在使用站点到站点连接的本地网络与虚拟网络之间配置传输路由，必须使用资源管理器部署模型和 PowerShell 在所有中间站点到站点连接上启用 BGP。 有关说明，请参阅[如何使用 PowerShell 在 Azure VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

使用经典部署模型通过 Azure VPN 网关传输流量是可行的，但依赖于网络配置文件中静态定义的地址空间。 尚不支持通过经典部署模型使用 Azure 虚拟网络和 VPN 网关配置 BGP。 如果没有 BGP，手动定义传输地址空间很容易出错，因此不建议这样做。

> [!Note]
> 使用 Azure 经典门户或在经典门户中使用网络配置文件来配置经典站点到站点连接。 无法通过 Azure 资源管理器部署模型或 Azure 门户来创建或修改经典虚拟网络。 有关经典虚拟网络传输路由的详细信息，请参阅 [Microsoft 开发人员博客](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

## <a name="configure-bgp-for-a-vpn-gateway"></a>为 VPN 网关配置 BGP

BGP 是在 Internet 上使用的，用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 在 Azure 虚拟网络的上下文中使用 BGP 时，BGP 支持 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）。 这些设备会向这两个网关提供有关前缀可用性和可访问性的信息，以便通过所涉及的网关或路由器。 

此外，BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点，以实现在多个网络之间传输路由。 有关详细信息，请参阅[使用 Azure VPN 网关的 BGP 概述](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。

### <a name="configure-bgp-for-a-vpn-connection"></a>为 VPN 连接配置 BGP

若要配置使用 BGP 的 VPN 连接，请参阅[使用 PowerShell 在 Azure VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

通过为虚拟网络网关创建自治系统 (AS) 编号在虚拟网络网关上启用 BGP。 基本网关不支持 BGP。 若要检查网关的 SKU，请在 Azure 门户中转到“VPN 网关”边栏选项卡的“概述”部分。   如果 SKU 为“基本”，则必须将 SKU（请参阅[调整网关大小](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)）更改为“VpnGw1”。   

检查 SKU 会导致 20 到 30 分钟的停机时间。 网关获得正确的 SKU 后，你可以使用 [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell cmdlet 添加 AS 编号。 配置 AS 编号后，系统会自动提供网关的 BGP 对等互连 IP。

必须使用 AS 编号和 BGP 对等互连地址手动提供 `LocalNetworkGateway`。 可以使用 [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) 或 [Set-AzureRmLocalNetworkGateway PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) cmdlet 设置 `ASN` 和 `-BgpPeeringAddress` 值。 某些 AS 编号是为 Azure 保留的，不能按[关于使用 Azure VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md#faq) 中所述使用这些编号。

必须为连接对象启用 BGP。 可以通过 [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) 或 [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) 将 `-EnableBGP` 值设置为 `$True`。

### <a name="validate-the-bgp-configuration"></a>验证 BGP 配置

若要检查是否正确配置了 BGP，可以运行 `get-AzureRmVirtualNetworkGateway` 和 `get-AzureRmLocalNetworkGateway` cmdlet。 然后，可以在 `BgpSettingsText` 部分看到 BGP 相关的输出。 例如：

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>创建高可用性主动/主动 VPN 连接

主动/主动与主动/待机网关之间的重要差异：

* 必须使用两个公共 IP 地址创建两个网关 IP 配置。
* 必须设置 **EnableActiveActiveFeature** 标志。
* 网关 SKU 必须是 **VpnGw1**、**VpnGw2** 或 **VpnGw3**。

若要实现跨界连接和网络间连接的高可用性，应该部署多个 VPN 网关，在网络与 Azure 之间建立多个并行连接。 有关连接选项和拓扑的概述，请参阅[高可用性跨界连接和网络间连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。

若要创建主动/主动跨界连接和网络间连接，请按照[配置与 Azure VPN 网关的主动/主动 S2S VPN 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)中的说明，以主动/主动模式配置 Azure VPN 网关。

> [!Note]  
> * 将地址添加到启用了 BGP 的主动/主动模式的本地网络网关时，只会添加 BGP 对等互连的 /32 地址。  如果添加更多地址，这些地址将被视为静态路由，并优先于 BGP 路由。
> * 对于连接到 Azure 的本地网络，必须使用不同的 BGP AS 编号。 （如果它们是相同的，并且本地 VPN 设备已使用 ASN 与其他 BGP 邻居建立对等互连，则必须更改虚拟网络的 AS 编号。）

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>部署后更改 Azure VPN 网关类型

无法将 Azure 虚拟网络网关类型从基于策略更改为基于路由，反之亦然。 必须先删除网关。 之后，IP 地址和预共享密钥不会保留。 然后可以创建所需类型的新网关。 

若要删除并创建网关，请执行以下步骤：

1. 删除与原始网关相关联的所有连接。
2. 使用 Azure 门户、PowerShell 或经典 PowerShell 删除网关： 
   * [使用 Azure 门户删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [使用 PowerShell 删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [使用 PowerShell（经典）删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 遵循[创建 VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)中的步骤创建所需类型的新网关，并完成 VPN 设置。

> [!Note]
> 此过程将花费大约 60 分钟时间。

## <a name="next-steps"></a>后续步骤

* [排查 Azure VM 间的连接问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

