---
title: Azure ExpressRoute Global Reach | Microsoft Docs
description: 本文介绍了如何将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用网络并启用 Global Reach。
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966806"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>将 ExpressRoute 线路链接起来以启用 ExpressRoute Global Reach（预览版）

ExpressRoute 是用来将本地网络连接到 Microsoft 云的一种专用且可复原的方式。 可以从专用数据中心或公司网络访问许多 Microsoft 云服务，例如 Azure、Office 365 和 Dynamics 365。 例如，你可能在旧金山有一家分公司，其 ExpressRoute线路位于硅谷，在巴尔的摩有另一家分公司，其 ExpressRoute 线路位于华盛顿特区。 

两家分公司都可以通过高速连接访问“美国东部”和“美国西部”中的 Azure 资源。但是，这两家分公司彼此之间无法直接交换数据。 换句话说，10.0.1.0/24 可以与 10.0.3.0/24 和 10.0.4.0/24 交换数据，但无法与 10.0.2.0/24 交换数据。

![不带时][1]

使用 **ExpressRoute Global Reach**，可以将 ExpressRoute 线路链接到一起，以在本地网络之间建立专用数据网络。 在上面的示例中，通过添加 ExpressRoute Global Reach，你的旧金山分公司 (10.0.1.0/24) 可以通过现有的 ExpressRoute 线路和 Microsoft 的全球网络直接与你的巴尔的摩分公司 (10.0.2.0/24) 交换数据。 

![带有时][2]

当前，以下国家/地区支持 ExpressRoute Global Reach：

* 美国
* 英国 
* 日本

在以上国家/地区中，必须在 [ExpressRoute 对等互连位置](expressroute-locations.md)创建 ExpressRoute 线路。 若要在[不同的地缘政治区域](expressroute-locations.md)之间启用 ExpressRoute Global Reach，你的线路必须是高级 SKU。


## <a name="before-you-begin"></a>开始之前

> [!IMPORTANT]
> 此公共预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

在开始配置之前，需要检查以下要求。

* 安装最新版本的 Azure PowerShell。 请参阅[安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。
* 了解 ExpressRoute 线路预配[工作流](expressroute-workflows.md)。
* 确保 ExpressRoute 线路处于“已预配”状态。
* 确保在 ExpressRoute 线路上配置了 Azure 专用对等互连。  

### <a name="log-into-your-azure-account"></a>登录到 Azure 帐户
若要开始配置，必须登录到 Azure 帐户。 

使用提升的权限打开 PowerShell 控制台，并连接到帐户。 该命令会提示你提供 Azure 帐户的登录凭据。  

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
可以在任意两条 ExpressRoute 线路之间启用 ExpressRoute Global Reach，只要它们位于受支持的国家/地区并且它们是在不同的对等互连位置创建的。 如果你的订阅同时拥有这两条线路，则可以选择其中任意一条来运行以下各部分中的配置。 如果两条线路位于不同的 Azure 订阅中，则你将需要来自一个 Azure 订阅的授权，并且当在另一个 Azure 订阅中运行配置命令时传入授权密钥。

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>启用本地网络之间的连接

使用以下命令来获取线路 1 和线路 2。 这两条线路位于同一订阅中。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

针对线路 1 运行以下命令并传入线路 2 的专用对等互连的 ID。

请注意，专用对等互连的 Id 如下所示：

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*-AddressPrefix* 必须是一个 /29 IPv4 子网，例如“10.0.0.0/29”。 我们将使用此子网中的 IP 地址在两条 ExpressRoute 线路之间建立连接。 不应当在 Azure VNet 或本地网络中使用此子网中的地址。 


在线路 1 上保存配置。

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

当上面的操作完成后，应当已通过两条 ExpressRoute 线路在两端的本地网络之间创建了连接。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>不同 Azure 订阅中的 ExpressRoute 线路

如果两条线路不在同一 Azure 订阅中，则需要授权。 在下面的配置中，将在线路 2 的订阅中生成授权并将授权密钥传递给线路 1。

生成授权密钥。 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
记下线路 2 的专用对等互连的 ID 以及授权密钥。

针对线路 1 运行以下命令。 传入线路 2 的专用对等互连的 ID 和授权密钥

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

在线路 1 上保存配置。

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

当上面的操作完成后，应当已通过两条 ExpressRoute 线路在两端的本地网络之间创建了连接。

## <a name="to-get-and-verify-the-configuration"></a>获取并验证配置

使用以下命令验证在其中进行了配置的线路上的配置。 此示例使用上一示例中的线路 1。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

如果只是在 PowerShell 中运行 *$ckt1*，则会在输出中看到 *CircuitConnectionStatus*。 它将指示连接已建立（“Connected”）还是未建立（“Disconnected”）。 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>禁用本地网络之间的连接

若要禁用，请针对在其中进行了配置的线路运行以下命令。 此示例使用前面示例中的线路 1。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

可以运行 Get 操作来验证状态。 

当上面的操作完成后，在本地网络之间将不再具有通过 ExpressRoute 线路建立的连接。 

## <a name="next-steps"></a>后续步骤
1. [详细了解 ExpressRoute Global Reach](expressroute-faqs.md#globalreach)
2. [验证 ExpressRoute 连接](expressroute-troubleshooting-expressroute-overview.md)
3. [将 ExpressRoute 线路链接到 Azure 虚拟网络](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "不带 Global Reach 时的示意图"
[2]: ./media/expressroute-global-reach/2.png "带有 Global Reach 时的示意图"