---
title: 配置 Global Reach - ExpressRoute：Azure | Microsoft Docs
description: 本文介绍了如何将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络并启用 Global Reach。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ab1098ca65ad92cffdbe1dfb24fd43fcc8f10eae
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431665"
---
# <a name="configure-expressroute-global-reach-preview"></a>配置 ExpressRoute Global Reach（预览版）
本文帮助你使用 PowerShell 配置 ExpressRoute Global Reach。 有关详细信息，请参阅 [ExpressRouteRoute Global Reach](expressroute-global-reach.md)。
 
## <a name="before-you-begin"></a>开始之前
> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应将其用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 


在开始配置之前，请确认以下事项：

* 已安装了最新版本的 Azure PowerShell。 有关详细信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps)。
* 你了解 ExpressRoute 线路预配[工作流](expressroute-workflows.md)。
* ExpressRoute 线路处于“已预配”状态。
* 在 ExpressRoute 线路上配置了 Azure 专用对等互连。  

### <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户
若要开始配置，请登录到 Azure 帐户。 

使用提升的权限打开 PowerShell 控制台，并连接到你的帐户。 该命令会提示你提供 Azure 帐户的登录凭据。  

```powershell
Connect-AzureRmAccount
```

如果有多个 Azure 订阅，请查看该帐户的订阅。

```powershell
Get-AzureRmSubscription
```

指定要使用的订阅。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>确定用于配置的 ExpressRoute 线路
可以在任意两条 ExpressRoute 线路之间启用 ExpressRoute Global Reach，只要它们位于受支持的国家/地区并且是在不同的对等互连位置创建的。 如果你的订阅同时拥有这两条线路，则可以选择其中任一条线路来运行以下各部分中的配置。 

如果两个线路位于不同的 Azure 订阅中，则你需要获得一个 Azure 订阅的授权。 然后，你在另一个 Azure 订阅中运行配置命令时传入授权密钥。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>启用本地网络之间的连接

使用以下命令来获取线路 1 和线路 2。 这两条线路位于同一订阅中。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

针对线路 1 运行以下命令并传入线路 2 的专用对等互连 ID。 在运行命令时，请注意以下事项：

* 专用对等互连 ID 看起来类似于以下示例： 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* 必须是一个 /29 IPv4 子网，例如“10.0.0.0/29”。 我们使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 不应当在 Azure 虚拟网络或本地网络中使用此子网中的地址。

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

如下所示保存线路 1 上的配置：
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

当上面的操作完成后，应当已通过两条 ExpressRoute 线路在两端的本地网络之间创建了连接。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>不同 Azure 订阅中的 ExpressRoute 线路

如果两条线路不在同一 Azure 订阅中，则需要获得授权。 在下面的配置中，将在线路 2 订阅中生成授权并将授权密钥传递给线路 1。

生成授权密钥。 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
记下线路 2 的专用对等互连 ID 以及授权密钥。

针对线路 1 运行以下命令。 传入线路 2 的专用对等互连 ID 以及授权密钥。
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

在线路 1 上保存配置。
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

当上面的操作完成后，应当已通过两条 ExpressRoute 线路在两端的本地网络之间创建了连接。

## <a name="get-and-verify-the-configuration"></a>获取并验证配置

使用以下命令验证在其中进行了配置的线路（例如，上例中的线路 1）的配置。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

如果直接在 PowerShell 中运行 *$ckt1*，则会在输出中看到 *CircuitConnectionStatus*。 它指明连接已建立（“Connected”）还是未建立（“Disconnected”）。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>禁用本地网络之间的连接

若要禁用连接，请针对在其中进行了配置的线路（例如，上例中的线路 1）运行以下命令。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

可以运行 Get 操作来验证状态。 

当上面的操作完成后，在本地网络之间将不再具有通过 ExpressRoute 线路建立的连接。 


## <a name="next-steps"></a>后续步骤
1. [详细了解 ExpressRoute Global Reach](expressroute-global-reach.md)
2. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
3. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)


