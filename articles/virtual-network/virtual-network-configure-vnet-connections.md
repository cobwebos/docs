---
title: 配置和验证 VNet 或 VPN 连接
description: 配置和验证各种 Azure VPN 和 VNet 部署的循序渐进指南
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
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901888"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>配置和验证 VNet 或 VPN 连接

本引导式演练提供分步指南，在诸如、传输路由、VNet 到 VNet、BGP、多站点、点到站点等方案中配置和验证各种 Azure VPN 和 VNet 部署。

通过 azure VPN 网关，可以灵活地在 Azure 中排列几乎任何类型的连接的虚拟网络（VNet）拓扑：可以跨区域、VNet 类型之间连接 Vnet （Azure 资源管理器与经典），在 Azure 中或者在本地混合环境中、在不同的订阅中，等等。 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>方案 1：VNet 到 VNet VPN 连接

通过 VPN 将虚拟网络连接到另一个虚拟网络（VNet 到 VNet）类似于将 VNet 连接到本地站点位置。 这两种连接类型都使用 VPN 网关来提供使用**IPsec/IKE**的安全隧道。 虚拟网络可以位于相同或不同的区域，也可以来自相同或不同的订阅。

![影像](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
图 1-具有 IPsec 的 VNet 到 VNet

如果你的 Vnet 位于同一区域，你可能需要考虑使用 VNet 对等互连来连接它们，这不使用 VPN 网关，增加吞吐量并减少延迟，请选择 "**配置并验证 vnet 对等互连**" 以配置 vnet 对等互连连接.

如果 Vnet 是通过使用 Azure 资源管理器部署模型创建的，请选择 "**配置" 并验证资源管理器 vnet 到资源管理器 vnet 连接**，以配置 VPN 连接。

如果 Vnet 是使用 Azure 经典部署模型创建的，另一个是资源管理器创建的，请选择 "配置"，**并验证经典 vnet 到资源管理器 VNet 连接**以配置 VPN 连接。

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>配置1：为同一区域中的两个 Vnet 配置 VNet 对等互连

在开始实现 Azure VNet 对等互连之前，请确保满足以下先决条件配置 VNet 对等互连：

* 对等虚拟网络必须位于同一 Azure 区域。
* 对等互连虚拟网络必须具有不重叠的 IP 地址空间。
* 虚拟网络对等互连在两个虚拟网络之间进行。 对等互连之间没有任何派生的可传递关系。 例如，如果 VNetA 与 VNetB 对等互连，VNetB 与 VNetC 对等互连，但 VNetA *不* 与 VNetC 对等互连。

满足要求后，可以遵循[创建虚拟网络对等互连教程](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)来创建和配置 VNet 对等互连。

若要查看 VNet 对等互连配置，请使用以下方法：

1. 使用具有必要[角色和权限](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请单击它。
3. 在显示的“虚拟网络”边栏选项卡中，单击想要为其创建对等互连的虚拟网络。
4. 在针对所选虚拟网络显示的窗格中，在 "**设置**" 部分中单击 "**对等互连**"。
5. 单击要检查配置的对等互连。

![影像](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
使用 Azure Powershell 运行命令[remove-azurermvirtualnetworkpeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0)获取虚拟网络对等互连，例如：

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

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>连接类型1：将资源管理器 VNet 连接到另一个台资源管理器 VNet （Azure 资源管理器资源管理器）

你可以直接配置从一个资源管理器 VNet 到另一个资源管理器 VNet 的连接，或配置与 IPsec 的连接。

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>配置2：在资源管理器 Vnet 之间配置 VPN 连接

若要在不包含 IPsec 的资源管理器 Vnet 之间配置连接，请参阅[使用 Azure 门户配置 vnet 到 VNET VPN 网关连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)。

若要在两个资源管理器 Vnet 之间配置与 IPsec 的连接，请按照在每个 VNet[的 Azure 门户中创建站点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)中的步骤1-5。

> [!Note]
> 这些步骤仅适用于同一订阅中的 VNet。 如果 VNet 属于不同的订阅，则必须使用 PowerShell 进行连接。 请参阅 [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps) 一文。

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>验证资源管理器 Vnet 之间的 VPN 连接

![影像](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

图 4-与 Azure 资源管理器 VNet 的经典 VNet 连接

若要检查是否已正确配置 VPN 连接，请按照说明进行操作：

> [!Note]
> 虚拟网络组件之后的数字，如以下步骤中的 "Vnet 1"，对应于图4中的数字。

1. 检查连接的 Vnet 中是否有重叠的地址空间。

   > [!Note]
   > Vnet 1 和 Vnet 6 中不能有重叠的地址空间。 

2. 验证 Azure 资源管理器 Vnet 1 地址范围是否在**连接对象**4 中正确定义。
3. 验证 Azure 资源管理器 Vnet 6 地址范围是否在**连接对象**3 中正确定义。
4. 验证在连接对象3和4上是否匹配预共享密钥。
5. 验证 Azure 资源管理器 Vnet 网关 2 VIP 是否准确地定义在**连接对象**4 中。
6. 验证 Azure 资源管理器 Vnet 网关 5 VIP 是否在**连接对象**3 中正确定义。

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>连接类型2：将经典 VNet 连接到资源管理器 VNet

可以在位于不同订阅、不同区域中的 VNet 之间创建连接。 你还可以将已经连接到本地网络的 Vnet 连接，前提是你已将网关类型配置为 "基于路由"。

若要在经典 VNet 与资源管理器 VNet 之间配置连接，请参阅[使用 Azure 门户将不同部署模型中的虚拟网络连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal)到详细信息。

![影像](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

图 5-与 Azure 资源管理器 VNet 的经典 VNet 连接

若要检查将经典 VNet 连接到 Azure 资源管理器 VNet 时的配置，请按照说明进行操作：

> [!Note]
> 以下步骤中的虚拟网络组件（如 "Vnet 1"）后面的数字与图5中的数字对应。

1. 检查连接的 Vnet 中是否有重叠的地址空间。

   > [!Note]
   > Vnet 1 和 Vnet 6 中不能有重叠的地址空间

2. 验证 Azure 资源管理器 VNet 6 地址范围是否在经典本地网络定义3中正确定义。
3. 验证经典 VNet 1 地址范围是否在 Azure 资源管理器**连接对象**4 中正确定义。
4. 验证经典 VNet 网关 2 VIP 是否在 Azure 资源管理器**连接对象**4 中正确定义。
5. 验证 Azure 资源管理器 VNet 网关 5 VIP 是否在经典**本地网络定义**3 中正确定义。
6. 验证两个连接的 Vnet 上的预共享密钥是否匹配：
   1. 经典 Vnet-本地网络定义3
   2. Azure 资源管理器 Vnet-连接对象4

## <a name="scenario-2-point-to-site-vpn-connection"></a>方案 2：点到站点 VPN 连接

通过点到站点 (P2S) 配置，可以使单台客户端计算机与虚拟网络建立安全的连接。 如果要从远程位置（例如，从家里或会议室）连接到 VNet，或者只有少数几台客户端计算机需要连接到虚拟网络，点到站点连接将非常有用。 P2S VPN 连接通过本机 Windows VPN 客户端从客户端计算机启动。 连接客户端使用证书进行身份验证。

![影像](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

图 2-点到站点连接

点到站点连接不需要 VPN 设备。 P2S 创建基于 SSTP（安全套接字隧道协议）的 VPN 连接。 可以通过使用不同的部署工具和部署模型，将点到站点连接连接到 VNet：

* [使用 Azure 门户配置与 VNet 的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [使用 Azure 门户（经典）配置与 VNet 的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [使用 PowerShell 配置与 VNet 的点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>验证点到站点连接

疑难解答文章[：Azure 点到站点连接问题](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems)介绍了点到站点连接的常见问题。

## <a name="scenario-3-multi-site-vpn-connection"></a>方案 3：多站点 VPN 连接

可以将站点到站点（S2S）连接添加到已有 S2S 连接、点到站点连接或 VNet 到 VNet 连接的 VNet，这种连接通常称为 "**多站点**配置"。 

![影像](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

图 3-多站点连接

Azure 当前使用两种部署模型：资源管理器和经典。 这两个模型并不完全兼容。 若要为**多站点**连接配置不同的模型，请查看以下文章：

* [向使用现有 VPN 网关连接的 VNet 添加站点到站点连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [向使用现有 VPN 网关连接的 VNet 添加站点到站点连接（经典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> 这些步骤不适用于 ExpressRoute 和站点到站点共存连接配置。 有关共存连接的详细信息，请参阅[ExpressRoute/S2S 共存连接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

## <a name="scenario-4-configure-transit-routing"></a>情景 4：配置传输路由

可传递路由是一种特定的路由方案，用于连接 "菊花链" 拓扑中的多个网络。 此路由启用 "链" 两端的 Vnet 中的资源，以通过 Vnet 在两者之间相互通信。 如果没有可传递的路由，通过集线器对等互连的网络或设备将无法相互连接。

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>配置1：在点到站点连接中配置传输路由

在此方案中，你将配置站点到站点 VPN 连接 VNetA 与 VNetB 之间的连接，还可以配置点到站点 VPN，以便客户端连接到 VNetA 的网关。 然后，你希望为点到站点客户端启用传输路由以连接到 VNetB，后者通过 VNetA。 如果在 VNetA 和 VNetB 之间的站点到站点 VPN 上启用了 BGP，则支持此方案。 有关详细信息，请参阅[关于点到站点 VPN 路由](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)。

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>配置2：在 ExpressRoute 连接中配置传输路由

Microsoft Azure ExpressRoute 可让你通过连接服务提供商所提供的专用连接，将本地网络扩展到 Microsoft 云。 使用 ExpressRoute 可与 Microsoft Azure、Office 365 和 Dynamics 365 等 Microsoft 云服务建立连接。  有关详细信息，请参阅[ExpressRoute 概述](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)。

![影像](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

图 6-ExpressRoute "专用对等互连" 与 Azure Vnet 的连接

> [!Note]
> 我们建议，如果 VNetA 和 VNetB 位于将[两个 vnet 链接到 ExpressRoute 线路](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm)的同一地缘政治区域，而不是配置可传递路由。 如果你的 Vnet 在不同的地缘政治区域中，则如果你有[ExpressRoute 高级版](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium)，还可以直接将它们链接到线路。 

如果有 ExpressRoute 和站点到站点共存，则不支持传输路由。 有关详细信息，请参阅[配置 ExpressRoute 和站点到站点共存连接](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager)。

如果已启用 ExpressRoute 以将本地网络连接到 Azure 虚拟网络，则可以在要建立可传递路由的 Vnet 之间启用 VNet 对等互连。 若要允许本地网络连接到远程 VNet，必须配置[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)。 

> [!Note]
> VNet 对等互连仅适用于同一区域中的 Vnet。

若要检查是否已为 VNet 对等互连配置了传输路由，请按照说明进行操作：

1. 使用具有必要[角色和权限](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 如前面的关系图中所示[，在 VNet a 和 B 之间创建对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering)（图6）。 
3. 在针对所选虚拟网络显示的窗格中，在 "**设置**" 部分中单击 "**对等互连**"。
4. 单击要查看和**配置**的对等互连，验证是否已启用 "允许在连接到 expressroute 线路的 VNetA 上进行**网关传输**" 和 "在未连接到 expressroute 的远程 VNetB 上**使用远程网关**"接通.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>配置3：在 VNet 对等互连连接中配置传输路由

虚拟网络对等时，用户还可以将对等虚拟网络中的网关配置为本地网络的传输点。 若要在 VNet 对等互连中配置传输路由，请检查[虚拟网络到虚拟网络连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)。

> [!Note]
> 通过不同部署模型创建的虚拟网络之间的对等互连关系不支持网关传输。 若要使用网关传输，对等互连关系中的两个虚拟网络都必须通过 Resource Manager 创建。

若要检查是否已为 VNet 对等互连配置了传输路由，请按照说明进行操作：

1. 使用具有必要[角色和权限](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions)的帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的顶部包含 "搜索资源" 文本的框中，键入 "*虚拟网络*"。 当“虚拟网络”出现在搜索结果中时，请单击它。
3. 在出现的 "**虚拟网络**" 边栏选项卡中，单击要检查对等互连设置的虚拟网络。
4. 在针对所选虚拟网络显示的窗格中，在 "**设置**" 部分中单击 "**对等互连**"。
5. 单击要查看的对等互连，并验证是否已启用 "**允许网关传输**" 和 "在**配置**下**使用远程网关**"。

![影像](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>配置4：在 VNet 到 VNet 连接中配置传输路由

若要配置 Vnet 之间的传输路由，必须在所有中间 VNet 到 VNet 连接上启用 BGP，方法是使用资源管理器部署模型和 PowerShell。 有关说明，请参阅[如何使用 PowerShell 在 AZURE VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

通过 Azure VPN 网关传输流量是可行的，但它依赖于网络配置文件中静态定义的地址空间。 Azure 虚拟网络和 VPN 网关尚不支持 BGP，因为使用的是经典部署模型。 如果没有 BGP，手动定义传输地址空间很容易出错，因此不建议使用。

> [!Note]
> 经典 VNet 到 VNet 连接是通过使用 Azure 门户（经典）或在经典门户中使用网络配置文件配置的。 不能通过 Azure 资源管理器部署模型或 Azure 门户来创建或修改经典虚拟网络。 有关经典 Vnet 传输路由的详细信息，请参阅[使用 VPN （V1）在 AZURE ARM 中使用集线器 & 辐射、菊花链和全网格 VNET 拓扑](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>配置5：在站点到站点连接中配置传输路由

若要配置本地网络和具有站点到站点连接的 VNet 之间的传输路由，必须使用资源管理器部署模型和 PowerShell 在所有中间站点到站点连接上启用 BGP，请参阅[如何配置Azure VPN 网关上的 BGP （使用 PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) ）说明。

通过 Azure VPN 网关传输流量是可行的，但它依赖于网络配置文件中静态定义的地址空间。 Azure 虚拟网络和 VPN 网关尚不支持 BGP，因为使用的是经典部署模型。 如果没有 BGP，手动定义传输地址空间很容易出错，因此不建议使用。

> [!Note]
> 经典站点到站点连接是使用 Azure 门户（经典）或在经典门户中使用网络配置文件配置的。 不能通过 Azure 资源管理器部署模型或 Azure 门户来创建或修改经典虚拟网络。 有关经典 Vnet 传输路由的详细信息，请参阅[使用 VPN （V1）在 AZURE ARM 中使用集线器 & 辐射、菊花链和全网格 VNET 拓扑](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/)。

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>情景 5：为 VPN 网关配置 BGP

BGP 是在 Internet 中用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 当 BGP 在 Azure 虚拟网络的上下文中使用时，BGP 将启用 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）。 它们交换 "路由"，这些路由将通知这两个网关的可用性和可访问性，以便这些前缀可通过涉及的网关或路由器。 BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点来允许在多个网络之间传输路由。 有关详细信息，请参阅[AZURE VPN 网关的 BGP 概述](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)。

### <a name="configure-bgp-for-a-vpn-connection"></a>为 VPN 连接配置 BGP

若要配置使用 BGP 的 VPN 连接，请参阅[如何使用 PowerShell 在 AZURE VPN 网关上配置 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps)。

> [!Note]
> 1. 通过为虚拟网络网关创建虚拟网络网关来启用 BGP。 基本网关不支持 BGP。 若要检查网关的 SKU，请前往 Azure 门户中 "VPN 网关" 边栏选项卡的 "概述" 部分。 如果你的 SKU 是**基本**的，则必须将 sku （重设[网关大小](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)）更改为**VpnGw1** SKU。 检查 SKU 会导致20-30 分钟的停机时间。 当网关有正确的 SKU 后，就可以通过[Get-azurermvirtualnetworkgateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell 命令来添加 as。 配置 AS 编号后，将自动提供网关的 BGP 对等 IP。
> 2. 必须使用 AS 编号和 BGP 对等地址**手动**提供 LocalNetworkGateway。 可以通过使用[AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0)或[AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell 命令来设置**ASN**和 **-BgpPeeringAddress**值。 某些 AS 编号是为 azure 保留的，不能使用[AZURE VPN 网关的 BGP](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq)中所述的。
> 3. 最后，连接对象必须已启用 BGP。 可以通过[get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)或[get-azurermvirtualnetworkgatewayconnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0)将 **-EnableBGP**值设置为`$True` 。

### <a name="validate-the-bgp-configuration"></a>验证 BGP 配置

若要检查是否已正确配置 BGP，你可以运行 cmdlet `get-AzureRmVirtualNetworkGateway`和`get-AzureRmLocalNetworkGateway`，然后你会在 BgpSettingsText 部分中看到与 BGP 相关的输出。 例如：BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>情景 6：高可用性主动-主动 VPN 连接

主动-主动与主机-待机网关之间的重要差异：

* 必须创建两个具有两个公共 IP 地址的网关 IP 配置
* 必须设置*EnableActiveActiveFeature*标志
* 网关 SKU 必须是 VpnGw1、VpnGw2、VpnGw3

若要实现跨界连接和 VNet 到 VNet 连接的高可用性，应该部署多个 VPN 网关，在网络与 Azure 之间建立多个并行连接。 有关连接选项和拓扑的概述，请参阅[高度可用的跨界连接和 vnet 到 Vnet 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)。

若要创建主动-主动跨界连接和 VNet 到 VNet 连接，请按照在[AZURE Vpn 网关上配置主动-主动 S2S VPN 连接](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell)中的说明，在主动/主动模式下配置 Azure vpn 网关。

> [!Note]  
> 1. 将地址添加到本地网络网关以启用 BGP 时，主动-主动模式*仅添加 bgp 对等节点的/32 地址*。 如果添加更多地址，它们将被视为静态路由，并优先于 BGP 路由。
> 2. 对于连接到 Azure 的本地网络，必须使用不同的 BGP Asn。 （如果两者相同，则必须更改 VNet ASN （如果本地 VPN 设备已使用 ASN 与其他 BGP 邻居对等）。）

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>方案7：部署后更改 Azure VPN 网关类型

不能从基于策略的 Azure VNet 网关类型更改为基于路由或其他方式。 必须先删除该网关，然后 won'tbe 保留 IP 地址和预共享密钥（PSK）。 然后可以创建所需类型的新网关。 若要删除并创建网关，请执行以下步骤：

1. 删除与原始网关关联的任何连接。
2. 使用 Azure 门户、PowerShell 或经典 PowerShell 删除网关： 
   * [使用 Azure 门户删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [使用 PowerShell 删除虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [使用 PowerShell 删除虚拟网络网关（经典）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. 按照[创建 vpn 网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway)中的步骤创建所需类型的新网关，并完成 VPN 设置。

> [!Note]
> 此过程大约需要60分钟。

## <a name="next-steps"></a>后续步骤

* [排查 Azure VM 间的连接问题](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

