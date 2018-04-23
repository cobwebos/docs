---
title: include 文件
description: include 文件
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/22/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7e33d4ed7100287ef6b22aa4c90fd52671363902
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
此任务的步骤使用的 VNet 基于以下配置参考列表中的值。 此列表中也概述了其他设置和名称。 尽管我们确实基于此列表中的值添加变量，但是我们在任何步骤中不会直接使用此列表。 可以复制列表作为参考，并将列表中的值替换为自己的值。

**配置参考列表**

* 虚拟网络名称 = “TestVNet”
* 虚拟网络地址空间 = 192.168.0.0/16
* 资源组 = “TestRG”
* Subnet1 名称 = “FrontEnd” 
* Subnet1 地址空间 = “192.168.1.0/24”
* 网关子网名称：“GatewaySubnet”必须始终将网关子网命名为 *GatewaySubnet*。
* 网关子网地址空间 = “192.168.200.0/26”
* 区域 =“美国东部”
* 网关名称 = “GW”
* 网关 IP 名称 = “GWIP”
* 网关 IP 配置名称 = “gwipconf”
* 类型 =“ExpressRoute” ExpressRoute 配置需要此类型。
* 网关公共 IP 名称 = “gwpip”

## <a name="add-a-gateway"></a>添加网关
1. 连接到 Azure 订阅。

  ```powershell 
  Connect-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. 声明此练习的变量。 请务必编辑此示例，使之反映想要使用的设置。

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. 将虚拟网络对象存储为变量。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. 将网关子网添加到虚拟网络中。 网关子网必须命名为“GatewaySubnet”。 应创建 /27 或更大（/26、/25 等）的网关子网。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. 设置配置。

  ```powershell
  $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. 将网关子网存储为变量。

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. 请求公共 IP 地址。 创建网关之前请求 IP 地址。 无法指定要使用的 IP 地址；它会进行动态分配。 后面的配置部分将使用此 IP 地址。 AllocationMethod 必须是动态的。

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. 创建网关配置。 网关配置定义要使用的子网和公共 IP 地址。 在此步骤中，将指定创建网关时使用的配置。 此步骤不会实际创建网关对象。 使用下面的示例创建网关配置。

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. 创建网关。 在此步骤中，**-GatewayType** 尤其重要。 必须使用值 **ExpressRoute**。 运行这些 cmdlet 后，可能需要 45 分钟或更长时间才能创建好网关。

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>验证是否已创建网关
使用以下命令来验证是否已创建网关：

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>重设网关大小
有许多[网关 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。 可以使用以下命令随时更改网关 SKU。

> [!IMPORTANT]
> 此命令对 UltraPerformance 网关不起作用。 要将网关更改为 UltraPerformance 网关，首先要删除现有的 ExpressRoute 网关，然后创建新的 UltraPerformance 网关。 要将网关从 UltraPerformance 网关降级，首先要删除 UltraPerformance 网关，然后创建新网关。
> 
> 

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>删除网关
使用以下命令删除网关：

```powershell
Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
