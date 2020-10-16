---
title: 教程：将 VNet 链接到 ExpressRoute 线路 - Azure PowerShell
description: 本教程概述如何使用资源管理器部署模型和 Azure PowerShell 将虚拟网络 (VNet) 链接到 ExpressRoute 线路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 69067ca34b231f1b14f8cc854288c3ed4c4ac82a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855984"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>教程：将虚拟网络连接到 ExpressRoute 线路
> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
>

本文将帮助你使用 Resource Manager 部署模型和 PowerShell 将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 虚拟网络可以在同一个订阅中，也可以属于另一个订阅。 本文还介绍如何更新虚拟网络链接。

* 最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络必须都位于同一地缘政治区域。 

* 单个 VNet 可最多连接到 4 条 ExpressRoute 线路。 使用本文中的步骤为要连接的每条 ExpressRoute 线路创建新的连接对象。 ExpressRoute 线路可在同一订阅、不同订阅或两者兼有。

* 如果启用 ExpressRoute 高级版加载项，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络。 通过高级版加载项，你还可以根据所选带宽，将 10 个以上的虚拟网络连接到 ExpressRoute 线路。 有关高级外接程序的更多详细信息，请参阅[常见问题解答](expressroute-faqs.md)。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> - 将同一订阅中的虚拟网络连接到线路
> - 将另一订阅中的虚拟网络连接到线路
> - 修改虚拟网络连接
> - 配置 ExpressRoute FastPath

## <a name="prerequisites"></a>必备条件

* 在开始配置之前，请先查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。

* 必须有一个活动的 ExpressRoute 线路。 
  * 请按说明[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)，并通过连接提供商启用该线路。 
  * 请确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](expressroute-howto-routing-arm.md)一文。 
  * 确保已配置 Azure 专用对等互连，并建立网络和 Microsoft 之间的 BGP 对等互连，以便进行端到端连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 按照说明[创建 ExpressRoute 的虚拟网络网关](expressroute-howto-add-gateway-resource-manager.md)。 ExpressRoute 虚拟网络网关使用的 GatewayType 是“ExpressRoute”而非 VPN。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路
可以使用以下 cmdlet 将虚拟网络网关连接到 ExpressRoute 线路。 在运行 cmdlet 之前，请确保已创建虚拟网络网关并可将其用于进行链接：

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将另一订阅中的虚拟网络连接到线路
用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。 单个部门（在此示例中为 IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

> [!NOTE]
> 订阅所有者需要缴纳 ExpressRoute 线路的连接和带宽费用。 所有虚拟网络共享相同的带宽。
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="跨订阅连接":::

### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 线路所有者和线路用户

“线路所有者”是 ExpressRoute 线路资源的已授权超级用户。 线路所有者可以创建可由线路用户兑换的授权。 线路用户是虚拟网络网关的所有者（这些网关与 ExpressRoute 线路位于不同的订阅中）。 线路用户可以兑换授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤消授权。 撤消授权会导致从已撤消访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**若要创建授权**

线路所有者创建授权，这将创建授权密钥，供线路用户用于将其虚拟网络网关连接到 ExpressRoute 线路。 一个授权只可用于一个连接。

以下 cmdlet 代码段演示如何创建授权：

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

对上述命令的响应将包含授权密钥和状态：

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**若要查看授权**

线路所有者可以通过运行以下 cmdlet 来查看针对特定线路发出的所有授权：

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**若要添加授权**

线路所有者可以使用以下 cmdlet 添加授权：

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**若要删除授权**

线路所有者可以通过运行以下 cmdlet 来撤消/删除对用户的授权：

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>线路用户操作

线路用户需有对等 ID 以及线路所有者提供的授权密钥。 授权密钥是一个 GUID。

可以使用以下命令检查对等 ID：

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**若要兑换连接授权**

线路用户可以通过运行以下 cmdlet 来兑现链接授权：

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**若要释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接释放授权。

## <a name="modify-a-virtual-network-connection"></a>修改虚拟网络连接
可以更新虚拟网络连接的某些属性。 

**若要更新连接权重**

虚拟网络可以连接到多条 ExpressRoute 线路。 可以从多条 ExpressRoute 线路收到相同的前缀。 若要选择使用哪个连接发送目标为此前缀的流量，可以更改连接的 *RoutingWeight*。 将在具有最高 *RoutingWeight* 的连接上发送流量。

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

*RoutingWeight* 的范围是 0 到 32000。 默认值为 0。

## <a name="configure-expressroute-fastpath"></a>配置 ExpressRoute FastPath 
如果虚拟网络网关是超高性能网关或 ErGw3AZ，则可以启用 [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md)。 FastPath 可提高数据路径性能，例如本地网络和虚拟网络之间的每秒数据包数和每秒连接数。 

对新的连接配置 FastPath

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

更新现有连接以启用 FastPath

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>清理资源

如果不再需要 ExpressRoute 连接，请使用 `Remove-AzVirtualNetworkGatewayConnection` 命令，从该网关所在的订阅删除网关和线路之间的链接。

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>后续步骤
有关 ExpressRoute 的详细信息，请参阅 ExpressRoute 常见问题。

> [!div class="nextstepaction"]
> [ExpressRoute 常见问题](expressroute-faqs.md)
