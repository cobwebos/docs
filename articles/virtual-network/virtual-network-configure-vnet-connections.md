---
title: 配置和验证虚拟网络或 VPN 连接
description: 配置和验证各种 Azure VPN 和虚拟网络部署的分步指南
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
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71099058"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>配置和验证虚拟网络或 VPN 连接

本演练提供了配置和验证各种 Azure VPN 和虚拟网络部署的分步指导。 方案包括传输路由、网络到网络连接、边界网关协议（BGP）、多站点连接和点到站点连接。

通过 azure VPN 网关，可以灵活地在 Azure 中排列几乎任何类型的连接的虚拟网络拓扑。 例如，可以连接虚拟网络：

- 跨区域。
- 在虚拟网络类型之间（Azure 资源管理器与经典）。
- 在 Azure 中或本地混合环境中。
- 在不同的订阅中。 

## <a name="network-to-network-vpn-connection"></a>网络到网络 VPN 连接

通过 VPN 将虚拟网络连接到另一个虚拟网络（网络到网络）类似于将虚拟网络连接到本地站点位置。 这两种连接类型都使用 VPN 网关通过 IPsec 和 IKE 提供安全隧道。 虚拟网络可以位于相同或不同的区域，也可以来自相同或不同的订阅。

![与 IPsec 建立网络到网络的连接](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
如果你的虚拟网络位于同一区域，你可能需要考虑使用虚拟网络对等互连来连接它们。 虚拟网络对等互连不使用 VPN 网关。 它可增加吞吐量并降低延迟。 若要配置虚拟网络对等互连，请选择 "**配置并验证 VNet 对等互连**"。

如果你的虚拟网络是通过 Azure 资源管理器部署模型创建的，请选择 "**配置" 并验证资源管理器 vnet 到资源管理器 vnet 连接**，以配置 VPN 连接。

如果其中一个虚拟网络是通过 Azure 经典部署模型创建的，而另一个是通过资源管理器创建的，请选择 "**配置" 并验证经典 vnet 到资源管理器 VNet 连接**以配置 VPN 连接。

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>为同一区域中的两个虚拟网络配置虚拟网络对等互连

在开始实施和配置 Azure 虚拟网络对等互连之前，请确保满足以下先决条件：

* 对等虚拟网络必须位于同一 Azure 区域。
* 对等互连虚拟网络必须具有不重叠的 IP 地址空间。
* 虚拟网络对等互连在两个虚拟网络之间进行。 对等互连之间没有任何派生的可传递关系。 例如，如果 VNetA 为对等互连 with VNetB，而 VNetB 为对等互连 with 互连，则 VNetA*不*与对等互连互连。

满足要求后，可以按照[教程操作：使用 Azure 门户](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)将虚拟网络连接到虚拟网络对等互连，以创建和配置对等互连。

若要检查对等互连配置，请使用以下方法：

1. 使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 在门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请选择它。
3. 在出现的 "**虚拟网络**" 边栏选项卡中，选择要为其创建对等互连的虚拟网络。
4. 在为虚拟网络显示的窗格中，在 "**设置**" 部分中选择 "**对等互连**"。
5. 选择对等互连并查看配置结果。

![用于检查虚拟网络对等互连配置的选择](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
有关 Azure PowerShell，请运行命令[remove-azurermvirtualnetworkpeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0)以获取虚拟网络对等互连。 以下是一个示例：

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

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>将资源管理器的虚拟网络连接到另一个资源管理器虚拟网络

你可以直接配置从一个资源管理器虚拟网络到另一个资源管理器虚拟网络的连接。 或者，你可以通过使用 IPsec 来配置连接。

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>在资源管理器虚拟网络之间配置 VPN 连接

若要在不使用 IPsec 的资源管理器虚拟网络之间配置连接，请参阅[使用 Azure 门户配置网络到网络 VPN 网关连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)。

若要在两个资源管理器虚拟网络之间配置与 IPsec 的连接，请执行在每个虚拟网络[的 Azure 门户中创建站点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)中的步骤1至5。

> [!Note]
> 这些步骤仅适用于同一订阅中的虚拟网络。 如果虚拟网络属于不同的订阅，则必须使用 PowerShell 进行连接。 请参阅 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 一文。

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>验证资源管理器虚拟网络之间的 VPN 连接

![与 Azure 资源管理器虚拟网络的经典虚拟网络连接](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

若要检查是否已正确配置 VPN 连接，请遵循这些说明。

> [!Note] 
> 这些步骤中的虚拟网络组件之后的数字与上图中的数字相对应。

1. 请确保连接的虚拟网络中没有重叠的地址空间。
2. 验证 Azure 资源管理器虚拟网络的地址范围（1）是否正确地在**连接对象**实例（4）中定义。
3. 验证 Azure 资源管理器虚拟网络的地址范围（6）是否正确地定义在**连接对象**实例中（3）。
4. 验证 "预共享密钥" 是否与 "连接对象" 匹配。
5. 验证 Azure 资源管理器虚拟网络网关 VIP （2）是否在**连接对象**实例（4）中正确定义。
6. 验证是否在**连接对象**实例（3）中准确定义了 Azure 资源管理器虚拟网络网关 VIP （5）。

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>将经典虚拟网络连接到资源管理器虚拟网络

可以在不同订阅中的虚拟网络与不同区域之间创建连接。 你还可以将已经连接到本地网络的虚拟网络连接到已配置为基于路由的网关类型。

若要在经典虚拟网络与资源管理器虚拟网络之间配置连接，请参阅[使用 Azure 门户从不同的部署模型连接虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)。

![与 Azure 资源管理器虚拟网络的经典虚拟网络连接](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

若要在将经典虚拟网络连接到 Azure 资源管理器虚拟网络时检查配置，请按照以下说明操作。

> [!Note] 
> 这些步骤中的虚拟网络组件之后的数字与上图中的数字相对应。 

1. 请确保连接的虚拟网络中没有重叠的地址空间。
2. 验证 Azure 资源管理器虚拟网络的地址范围（6）是否在经典本地网络定义（3）中正确定义。
3. 验证经典虚拟网络的地址范围（1）是否在 Azure 资源管理器**连接对象**实例（4）中正确定义。
4. 验证是否在 Azure 资源管理器**连接对象**实例（4）中准确定义了经典虚拟网络网关 VIP （2）。
5. 验证是否在经典**本地网络定义**实例（3）中准确定义了 Azure 资源管理器虚拟网络网关（5）。
6. 验证在两个连接的虚拟网络上是否匹配预共享密钥：
   - 经典虚拟网络：**本地网络定义**三维空间
   - Azure 资源管理器虚拟网络：**Connection 对象**4

## <a name="create-a-point-to-site-vpn-connection"></a>创建点到站点 VPN 连接

点到站点（下图中的*P2S* ）配置使你能够创建从单个客户端计算机到虚拟网络的安全连接。 如果要从远程位置（例如从家里或会议）连接到虚拟网络，点到站点连接将非常有用。 当只有几个客户端需要连接到虚拟网络时，它们也非常有用。 

点到站点 VPN 连接通过本机 Windows VPN 客户端从客户端计算机启动。 连接客户端使用证书进行身份验证。

![点到站点连接](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

点到站点连接不需要 VPN 设备。 它们通过安全套接字隧道协议（SSTP）创建 VPN 连接。 可以通过使用各种部署工具和部署模型，将点到站点连接连接到虚拟网络：

* [使用 Azure 门户配置与虚拟网络的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [使用 Azure 门户（经典）配置与虚拟网络的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [使用 PowerShell 配置与虚拟网络的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>验证点到站点连接

疑难解答文章[：Azure 点到站点连接问题](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)介绍了点到站点连接的常见问题。

## <a name="create-a-multisite-vpn-connection"></a>创建多站点 VPN 连接

你可以将站点到站点（*S2S* ）添加到已具有站点到站点连接、点到站点连接或网络到网络连接的虚拟网络的连接上。 这种类型的连接通常称为 "*多站点*配置"。 

![多站点连接](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure 当前使用两种部署模型：资源管理器部署模型和经典部署模型。 这两个模型并不完全兼容。 若要使用不同的模型配置多站点连接，请参阅以下文章：

* [使用现有的 VPN 网关连接添加到虚拟网络的站点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [向使用现有 VPN 网关连接的虚拟网络添加站点到站点连接（经典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 这些文章中的步骤不适用于 Azure ExpressRoute 和站点到站点共存连接配置。 有关详细信息，请参阅[ExpressRoute 和站点到站点共存连接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

## <a name="configure-transit-routing"></a>配置传输路由

传输路由是一种特定的路由方案，在此方案中，可以连接到菊花链拓扑中的多个网络。 通过此路由，可以在链的任一端使用虚拟网络中的资源，通过虚拟网络在两者之间进行通信。 如果没有传输路由，则通过集线器对等互连的网络或设备将无法相互连接。

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>在点到站点连接中配置传输路由

假设你想要在 VNetA 与 VNetB 之间配置站点到站点 VPN 连接。 还需要为客户端配置点到站点 VPN，以便连接到 VNetA 的网关。 然后，你希望为点到站点客户端启用传输路由以连接到 VNetB，后者通过 VNetA。 

在 VNetA 和 VNetB 之间的站点到站点 VPN 上启用 BGP 时，支持此方案。 有关详细信息，请参阅[关于点到站点 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)。

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>在 ExpressRoute 连接中配置传输路由

使用 Azure ExpressRoute 可通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。 有关详细信息，请参阅[ExpressRoute 概述](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

![与 Azure 虚拟网络的 ExpressRoute 专用对等互连](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> 我们建议，如果 VNetA 和 VNetB 位于同一地缘政治区域，请将[这两个虚拟网络链接到 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)，而不是配置传输路由。 如果你的虚拟网络位于不同的地缘政治区域，则如果你有[ExpressRoute 高级版](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)，还可以直接将它们链接到线路。 

如果有 ExpressRoute 和站点到站点共存，则不支持传输路由。 有关详细信息，请参阅[使用 PowerShell 配置 ExpressRoute 和站点到站点](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

如果已启用 ExpressRoute 以将本地网络连接到 Azure 虚拟网络，则可以在想要进行传输路由的虚拟网络之间启用对等互连。 若要允许本地网络连接到远程虚拟网络，必须配置[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)。 

> [!Note]
> 虚拟网络对等互连仅适用于同一区域中的虚拟网络。

若要检查是否已为虚拟网络对等互连配置了传输路由，请按照以下说明进行操作：

1. 使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 在[VNetA 和 VNetB 之间创建对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)，如前面的关系图中所示。 
3. 在为虚拟网络显示的窗格中，在 "**设置**" 部分中选择 "**对等互连**"。
4. 选择要查看的对等互连。 然后，选择 "**配置**"，验证是否已启用 "允许在连接到 expressroute 线路的 VNetA 网络上进行**网关传输**" 和 "在未连接到 expressroute 的远程 VNetB 网络上**使用远程网关**"接通.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>在虚拟网络对等互连连接中配置传输路由

虚拟网络对等时，用户还可以将对等虚拟网络中的网关配置为本地网络的传输点。 若要在虚拟网络对等互连中配置传输路由，请参阅[网络到网络连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)。

> [!Note]
> 通过不同部署模型创建的虚拟网络之间的对等互连关系不支持网关传输。 对等互连关系中的两个虚拟网络都必须通过资源管理器创建，以便网关传输工作。

若要检查是否为虚拟网络对等互连配置了传输路由，请按照以下说明进行操作：

1. 使用具有必要[角色和权限](virtual-network-manage-peering.md#permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 在门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请选择它。
3. 在出现的 "**虚拟网络**" 边栏选项卡中，选择要检查对等互连设置的虚拟网络。
4. 在针对所选虚拟网络显示的窗格中，在 "**设置**" 部分中选择 "**对等互连**"。
5. 选择要查看的对等互连。 验证是否已启用 "**允许网关传输**" 并在 "**配置**" 下**使用远程网关**。

![用于检查是否已为虚拟网络对等互连配置传输路由的选项](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>在网络到网络连接中配置传输路由

若要配置虚拟网络之间的传输路由，必须使用资源管理器部署模型和 PowerShell 在所有中间网络到网络连接上启用 BGP。 有关说明，请参阅[如何使用 PowerShell 在 AZURE VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

通过 Azure VPN 网关传输流量是通过经典部署模型实现的，但它依赖于在网络配置文件中静态定义的地址空间。 Azure 虚拟网络和 VPN 网关尚不支持 BGP 通过经典部署模型。 如果没有 BGP，手动定义传输地址空间很容易出错，不建议这样做。

> [!Note]
> 使用 Azure 经典门户或在经典门户中使用网络配置文件来配置经典网络到网络连接。 不能通过 Azure 资源管理器部署模型或 Azure 门户来创建或修改经典虚拟网络。 有关经典虚拟网络传输路由的详细信息，请参阅[Microsoft 开发人员博客](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>在站点到站点连接中配置传输路由

若要通过站点到站点连接配置本地网络与虚拟网络之间的传输路由，必须使用资源管理器部署模型和 PowerShell 在所有中间站点到站点连接上启用 BGP。 有关说明，请参阅[如何使用 PowerShell 在 AZURE VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) 。

通过 Azure VPN 网关传输流量是通过经典部署模型实现的，但它依赖于在网络配置文件中静态定义的地址空间。 Azure 虚拟网络和 VPN 网关尚不支持 BGP 通过经典部署模型。 如果没有 BGP，手动定义传输地址空间很容易出错，不建议这样做。

> [!Note]
> 使用 Azure 经典门户或在经典门户中使用网络配置文件来配置经典站点到站点连接。 不能通过 Azure 资源管理器部署模型或 Azure 门户来创建或修改经典虚拟网络。 有关经典虚拟网络传输路由的详细信息，请参阅[Microsoft 开发人员博客](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

## <a name="configure-bgp-for-a-vpn-gateway"></a>为 VPN 网关配置 BGP

BGP 是在 internet 上用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 当在 Azure 虚拟网络的上下文中使用 BGP 时，它将启用 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）。 它们交换 "路由"，这些路由将通知这两个网关的可用性和可访问性，以便这些前缀可通过涉及的网关或路由器。 

BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点，在多个网络之间启用传输路由。 有关详细信息，请参阅[AZURE VPN 网关的 BGP 概述](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。

### <a name="configure-bgp-for-a-vpn-connection"></a>为 VPN 连接配置 BGP

若要配置使用 BGP 的 VPN 连接，请参阅[如何使用 PowerShell 在 AZURE VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

通过为该虚拟网络网关创建一个自治系统（AS）号来启用该虚拟网络网关的 BGP。 基本网关不支持 BGP。 若要检查网关的 SKU，请参阅 Azure 门户中 " **VPN 网关**" 边栏选项卡的 "**概述**" 部分。 如果你的 SKU 是**基本**的，则必须将 sku （请参阅[调整网关大小](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)）更改为**VpnGw1**。 

检查 SKU 将导致20到30分钟的停机时间。 一旦网关有正确的 SKU，就可以使用[Get-azurermvirtualnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell commandlet 来添加 as 编号。 配置 AS 编号后，将自动提供网关的 BGP 对等 IP。

必须手动提供`LocalNetworkGateway` AS 编号和 BGP 对等地址。 可以通过使用`ASN` [AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0)或`-BgpPeeringAddress` [AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell commandlet 来设置和值。 某些 AS 编号是为 Azure 保留的，不能如[关于使用 AZURE VPN 网关的 BGP](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)中所述。

连接对象必须已启用 BGP。 可以通过[get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)或`-EnableBGP` [get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)将`$True`值设置为。

### <a name="validate-the-bgp-configuration"></a>验证 BGP 配置

若要检查是否已正确配置 BGP，你可以运行`get-AzureRmVirtualNetworkGateway`和`get-AzureRmLocalNetworkGateway` commandlet。 然后，你会注意到此部分中的`BgpSettingsText`与 BGP 相关的输出。 例如：

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>创建高度可用的主动/主动 VPN 连接

主动/主动和主动/备用网关之间的主要区别是：

* 必须创建两个具有两个公共 IP 地址的网关 IP 配置。
* 必须设置**EnableActiveActiveFeature**标志。
* 网关 SKU 必须是**VpnGw1**、 **VpnGw2**或**VpnGw3**。

要实现跨界连接和网络到网络连接的高可用性，应该部署多个 VPN 网关，并在网络与 Azure 之间建立多个并行连接。 有关连接选项和拓扑的概述，请参阅[高度可用的跨界连接和网络到网络连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。

若要创建主动/主动跨界连接和网络到网络连接，请按照[使用 AZURE VPN 网关配置主动/主动 S2S VPN 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)中的说明，在主动/主动模式下配置 Azure VPN 网关。

> [!Note]  
> * 将地址添加到本地网络网关以启用启用 BGP 的主动/主动模式时，请*仅添加 bgp 对等节点的/32 地址*。 如果添加更多地址，它们将被视为静态路由，并优先于 BGP 路由。
> * 对于连接到 Azure 的本地网络，必须使用不同的 BGP 作为号码。 （如果两者相同，则必须将虚拟网络改为数字，前提是本地 VPN 设备已将 ASN 与其他 BGP 邻居一起使用。）

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>部署后更改 Azure VPN 网关类型

不能从基于策略的 Azure 虚拟网络网关类型更改为基于路由或其他方式。 必须先删除该网关。 之后，将不会保留 IP 地址和预共享密钥。 然后可以创建所需类型的新网关。 

若要删除并创建网关，请执行以下步骤：

1. 删除与原始网关关联的任何连接。
2. 使用 Azure 门户、PowerShell 或经典 PowerShell 删除网关： 
   * [使用 Azure 门户删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [使用 PowerShell 删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [使用 PowerShell 删除虚拟网络网关（经典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 按照[创建 vpn 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)中的步骤创建所需类型的新网关，并完成 VPN 设置。

> [!Note]
> 此过程大约需要60分钟。

## <a name="next-steps"></a>后续步骤

* [排查 Azure VM 间的连接问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

