---
title: 教程：配置 ExpressRoute 线路的对等互连 - Azure PowerShell
description: 本教程介绍如何使用 PowerShell 在资源管理器部署模型中创建和管理 ExpressRoute 线路的路由配置。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 641d7eeef96af84f0f058aebd19d795083e3567f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855338"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>教程：使用 PowerShell 创建和修改 ExpressRoute 线路的对等互连

本教程帮助你使用 PowerShell 在资源管理器部署模型中创建和管理 ExpressRoute 线路的路由配置。 还可以检查 ExpressRoute 线路的状态，更新、删除和取消预配其对等互连。 如果想使用不同的方法处理线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [公共对等互连](about-public-peering.md)
> * [视频 - 专用对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [视频 - Microsoft 对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-routing-classic.md)
> 

这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果所使用的服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商会配置和管理路由。

> [!IMPORTANT]
> 我们目前无法通过服务管理门户播发服务提供商配置的对等互连。 我们正在努力不久就实现这一功能。 请在配置 BGP 对等互连之前与服务提供商协商。
> 

可以配置 ExpressRoute 线路的专用对等互连和 Microsoft 对等互连（对于新线路，Azure 公共对等互连已弃用）。 可以按所选的任意顺序配置对等互连。 但是，必须确保一次只完成一个对等互连的配置。 有关路由域和对等互连的详细信息，请参阅 [ExpressRoute 路由域](expressroute-circuit-peerings.md)。 有关公共对等互连的信息，请参阅 [ExpressRoute 公共对等互连](about-public-peering.md)。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 配置、更新和删除线路的 Microsoft 对等互连
> - 配置、更新和删除线路的 Azure 专用对等互连

## <a name="prerequisites"></a>必备条件

* 在开始配置之前，确保已查看以下页面：
    * [先决条件](expressroute-prerequisites.md) 
    * [路由要求](expressroute-routing.md)
    * [工作流](expressroute-workflows.md)
* 必须有一个活动的 ExpressRoute 线路。 在继续之前，请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配和已启用状态，以运行本文中的 cmdlet。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft 对等互连

本文介绍如何为 ExpressRoute 线路创建、获取、更新和删除 Microsoft 对等互连配置。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。 有关详细信息，请参阅[配置用于 Microsoft 对等互连的路由筛选器](how-to-routefilter-powershell.md)。
> 

### <a name="to-create-microsoft-peering"></a>创建 Microsoft 对等互连

1. 登录并选择订阅。

   如果在本地安装了 PowerShell，请登录。 如果使用 Azure Cloud Shell，则可以跳过此步骤。

   ```azurepowershell
   Connect-AzAccount
   ```

   选择要创建 ExpressRoute 线路的订阅。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. 创建 ExpressRoute 线路。

   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-arm.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Microsoft 对等互连。 无需遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后按照后续步骤继续配置。 

3. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   其响应类似于如下示例：

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. 配置线路的 Microsoft 对等互连。 在继续之前，请确保已准备好以下信息。

   * 主链路的 /30 或 /126 子网。 地址块必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 或 IPv6 前缀。
   * 辅助链路的 /30 或 /126 子网。 地址块必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 或 IPv6 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * 播发的前缀：提供你计划要通过 BGP 会话播发的所有前缀的列表。 只接受公共 IP 地址前缀。 如果打算发送一组前缀，可以发送逗号分隔列表。 这些前缀必须已在 RIR/IRR 中注册。 IPv4 BGP 会话需要 IPv4 播发前缀，而 IPv6 BGP 会话需要 IPv6 播发前缀。 
   * 路由注册表名称：可以指定 AS 编号和前缀要注册到的 RIR/IRR。
   * 可选：
     * 客户 ASN：如果要播发的前缀未注册到对等互连 AS 编号，可以指定要注册到的 AS 编号。
     * MD5 哈希（如果选择使用）。

> [!IMPORTANT]
> Microsoft 会验证指定的“播发的公用前缀”和“对等 ASN”（或“客户 ASN”）是否已在 Internet 路由注册表中分配给你。 如果要从其他实体获取公用前缀，并且没有在路由注册表中记录分配，则自动验证不会完成，需要手动验证。 如果自动验证失败，你将在“Get-AzExpressRouteCircuitPeeringConfig”的输出中看到“AdvertisedPublicPrefixesState”为“需要验证”（请参阅以下部分中的“获取 Microsoft 对等互连详细信息”）。 
> 
> 如果看到消息“需要验证”，请收集相关文档，它们显示公用前缀已由在路由注册表中作为前缀所有者列出的实体分配给你的组织，然后通过开具支持票证来提交这些文档以进行手动验证。 
> 

   使用以下示例为线路配置 Microsoft 对等互连：

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>获取 Microsoft 对等互连详细信息

可以使用以下示例来获取配置详细信息：

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>更新 Microsoft 对等互连配置

可以使用以下示例来更新配置的任何部分：

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Azure 专用对等互连

本文介绍了如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连

1. 为 ExpressRoute 导入 PowerShell 模块。

   从 [PowerShell 库](https://www.powershellgallery.com/)安装最新的 PowerShell 安装程序。 然后，将 Azure 资源管理器模块导入 PowerShell 会话，以便开始使用 ExpressRoute cmdlet。 你将需要以管理员身份运行 PowerShell。

   ```azurepowershell-interactive
   Install-Module Az
   ```

   导入已知语义版本范围内的所有 Az.\* 模块。

   ```azurepowershell-interactive
   Import-Module Az
   ```

   也可以只导入已知语义版本范围内的 select 模块。

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   登录到帐户。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   选择要创建 ExpressRoute 线路的订阅。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. 创建 ExpressRoute 线路。

   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-arm.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 无需遵循后续部分中所列的说明。 但是，如果连接服务提供商不为你管理路由，请在创建线路后按照后续步骤继续配置。

3. 检查 ExpressRoute 线路以确保它已预配并已启用。 使用以下示例：

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   其响应类似于如下示例：

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
   ProvisioningState                : Succeeded
   Sku                              : {
                                       "Name": "Standard_MeteredData",
                                       "Tier": "Standard",
                                       "Family": "MeteredData"
                                     }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             : 
   ServiceProviderProperties        : {
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. 配置线路的 Azure 专用对等互连。 在继续执行后续步骤之前，确保已准备好以下各项：

   * 主链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 辅助链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以将专用 AS 编号用于此对等互连。 确保你没有使用 65515。
   * 可选：
     * MD5 哈希（如果选择使用）。

   使用以下示例为线路配置 Azure 专用对等互连：

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   如果选择使用 MD5 哈希，请使用以下示例：

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN，而不是客户 ASN。
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>获取 Azure 专用对等互连详细信息

可以使用以下示例来获取配置详细信息：

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>更新 Azure 专用对等互连配置

可以使用以下示例来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 100 更新为 500。

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>清理资源

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>删除 Microsoft 对等互连

可以运行以下 cmdlet 来删除对等互连配置：

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>删除 Azure 专用对等互连

可以运行以下示例来删除对等互连配置：

> [!WARNING]
> 运行此示例前，必须确保已删除所有虚拟网络和 ExpressRoute Global Reach 连接。 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>后续步骤

配置 Azure 专用对等互连后，可以创建 ExpressRoute 网关，以将虚拟网络链接到线路。 

> [!div class="nextstepaction"]
> [配置 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)
