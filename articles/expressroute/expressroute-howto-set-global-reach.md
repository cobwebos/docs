---
title: 配置 Global Reach - ExpressRoute：Azure | Microsoft Docs
description: 本文介绍了如何将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络并启用 Global Reach。
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: de9cbd9cfac766e2a67274684d3fb6b447e45200
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572752"
---
# <a name="configure-expressroute-global-reach"></a>配置 ExpressRoute Global Reach

本文帮助你使用 PowerShell 配置 ExpressRoute Global Reach。 有关详细信息，请参阅 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。

 ## <a name="before-you-begin"></a>开始之前

在开始配置之前，请确认以下事项：

* 了解 ExpressRoute 线路预配[工作流](expressroute-workflows.md)。
* 为 ExpressRoute 线路处于已预配状态。
* 为 ExpressRoute 线路上配置 azure 专用对等互连。
* 如果你想要在本地运行 PowerShell，请验证您的计算机上安装最新版本的 Azure PowerShell。

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>标识线路

1. 若要开始配置，登录到你的 Azure 帐户并选择你想要使用的订阅。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 确定要使用 ExpressRoute 线路。 您可以启用 ExpressRoute 全球覆盖任何两个 ExpressRoute 线路之间，只要它们位于受支持的国家/地区，并在不同对等互连位置创建。 

   * 如果你的订阅同时拥有这两条线路，则可以选择其中任一条线路来运行以下各部分中的配置。
   * 如果两个线路位于不同的 Azure 订阅中，则你需要获得一个 Azure 订阅的授权。 然后，你在另一个 Azure 订阅中运行配置命令时传入授权密钥。

## <a name="enable-connectivity"></a>启用连接

启用你的本地网络之间的连接。 有单独的组的同一个 Azure 订阅中的线路和不同的订阅的线路的说明。

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>在同一 Azure 订阅中的 ExpressRoute 线路

1. 使用以下命令来获取线路 1 和线路 2。 这两条线路位于同一订阅中。

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. 针对线路 1 运行以下命令并传入线路 2 的专用对等互连 ID。 在运行命令时，请注意以下事项：

   * 专用对等互连 ID 看起来类似于以下示例： 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* 必须是一个 /29 IPv4 子网，例如“10.0.0.0/29”。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 不应当在 Azure 虚拟网络或本地网络中使用此子网中的地址。

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. 如下所示保存线路 1 上的配置：

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

上一个操作完成后，你将通过两个 ExpressRoute 线路的两面上你的本地网络之间具有连接。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>不同 Azure 订阅中的 ExpressRoute 线路

如果两条线路不在同一 Azure 订阅中，则需要获得授权。 在下面的配置中，将在线路 2 订阅中生成授权并将授权密钥传递给线路 1。

1. 生成授权密钥。

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   记下线路 2 的专用对等互连 ID 以及授权密钥。
2. 针对线路 1 运行以下命令。 传入线路 2 的专用对等互连 ID 以及授权密钥。

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. 在线路 1 上保存配置。

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

上一个操作完成后，你将通过两个 ExpressRoute 线路的两面上你的本地网络之间具有连接。

## <a name="verify-the-configuration"></a>验证配置

使用以下命令验证在其中进行了配置的线路（例如，上例中的线路 1）的配置。
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

如果直接在 PowerShell 中运行 *$ckt1*，则会在输出中看到 *CircuitConnectionStatus*。 它指明连接已建立（“Connected”）还是未建立（“Disconnected”）。 

## <a name="disable-connectivity"></a>禁用连接

若要禁用你的本地网络，在其中生成配置的对线路运行命令 （例如，线路 1 上一示例中） 之间的连接。

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

可以运行 Get 操作来验证状态。

当上面的操作完成后，在本地网络之间将不再具有通过 ExpressRoute 线路建立的连接。

## <a name="next-steps"></a>后续步骤
1. [详细了解 ExpressRoute Global Reach](expressroute-global-reach.md)
2. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
3. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)
