---
title: 创建一个 Azure 虚拟 WAN 虚拟中心路由表来引导到 NVA | Microsoft Docs
description: 虚拟 WAN 虚拟中心路由表，用于将流量引导到网络虚拟设备。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 821aecf5549548365d95ef83ea1fcdeb017a4a21
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321437"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>创建一个虚拟中心路由表来将流量引导到网络虚拟设备。

本文展示了如何将流量从虚拟中心引导到网络虚拟设备。 

![虚拟 WAN 示意图](./media/virtual-wan-route-table/vwanroute.png)

在本文中，你将学习如何：

* 创建 WAN
* 创建中心
* 创建中心虚拟网络连接
* 创建中心路由
* 创建路由表
* 应用路由表

## <a name="before-you-begin"></a>开始之前

验证是否符合以下条件：

1. 你有一个网络虚拟设备 (NVA)，它是你选择的第三方软件，通常是通过虚拟网络中的 Azure 市场（链接）预配的。
2. 你向 NVA 网络接口分配了一个专用 IP。 
3. NVA 不能部署在虚拟中心内。 它必须部署在单独的 VNet 中。 在本文中，此 VNet 称为“DMZ VNet”。
4. “DMZ VNet”可以有一个或多个虚拟网络连接到它。 在本文中，此 VNet 称为“间接辐射 VNet”。 这些 VNet 可以使用 VNet 对等互连连接到 DMZ VNet。
5. 验证已创建了 2 个 VNet。 它们将用作辐射 VNet。 对于本文，VNet 辐射地址空间是 10.0.2.0/24 和 10.0.3.0/24。 如果需要有关如何创建 VNet 的信息，请参阅[使用 PowerShell 创建虚拟网络](../virtual-network/quick-create-powershell.md)。
6. 请确保任何 VNet 中都没有虚拟网络网关。

## <a name="signin"></a>1.登录

确保安装最新版本的资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 这很重要，因为早期版本的 cmdlet 不包含本练习所需的最新值。

1. 使用提升的权限打开 PowerShell 控制台，并登录到你的 Azure 帐户。 该 cmdlet 会提示你输入登录凭据。 登录后，它会下载帐户设置，供 Azure PowerShell 使用。

  ```powershell
  Connect-AzureRmAccount
  ```
2. 获取 Azure 订阅的列表。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 指定要使用的订阅。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2.创建资源

1. 创建资源组。

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. 创建虚拟 WAN。

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. 创建虚拟中心。

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3.创建连接

创建从间接辐射 VNet 和 DMZ VNet 到虚拟中心的中心虚拟网络连接。

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4.创建虚拟中心路由

对于本文，间接辐射 VNet 地址空间是 10.0.2.0/24 和 10.0.3.0/24，DMZ NVA 网络接口专用 IP 地址是 10.0.4.5。

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5.创建虚拟中心路由表

创建一个虚拟中心路由表，然后将所创建的路由应用于它。
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6.提交更改

将更改提交到虚拟中心。

```powershell
Update-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。
