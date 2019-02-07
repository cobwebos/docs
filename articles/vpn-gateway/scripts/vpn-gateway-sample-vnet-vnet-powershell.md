---
title: Azure PowerShell 脚本示例 - 配置 VNet 到 VNet VPN | Microsoft Docs
description: 配置站点到站点 VPN。
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/30/2018
ms.author: alzam
ms.openlocfilehash: 8de152af72acaecd5bcd9c43b812394508c91eca
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507971"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>使用 PowerShell 配置 VNet 到 VNet VPN 网关连接

此脚本使用 VNet 到 VNet 连接类型连接两个虚拟网络。

```azurepowershell-interactive
# Declare variables for VNET 1
  $RG1 = "TestRG1"
  $VNetName1  = "VNet1"
  $FESubName1 = "FrontEnd"
  $GWSubName1 = "GatewaySubnet"
  $VNetPrefix11 = "10.1.0.0/16"
  $FESubPrefix1 = "10.1.0.0/24"
  $GWSubPrefix1 = "10.1.255.0/27"
  $Location1 = "EastUS"
  $GWName1 = "VNet1GW"
  $GWIPName1 = "VNet1GWIP"
  $GWIPconfName1 = "gwipconfig1"
  $Connection12 = "VNet1toVNet2"

# Declare variables for VNET 2  
  $RG2 = "TestRG2"
  $VNetName2  = "VNet2"
  $FESubName2 = "FrontEnd"
  $GWSubName2 = "GatewaySubnet"
  $VNetPrefix21 = "10.2.0.0/16"
  $FESubPrefix2 = "10.2.0.0/24"
  $GWSubPrefix2 = "10.2.255.0/27"
  $Location2 = "EastUS"
  $GWName2 = "VNet2GW"
  $GWIPName2 = "VNet2GWIP"
  $GWIPconfName2 = "gwipconfig2"
  $Connection21 = "VNet2toVNet1"

# Create first resource group
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create a virtual network 1
$virtualNetwork1 = New-AzureRmVirtualNetwork `
  -ResourceGroupName $RG1 `
  -Location $Location1 `
  -Name $VNetName1 `
  -AddressPrefix $VNetPrefix11

# Create a subnet configuration
Add-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 -VirtualNetwork $virtualNetwork1

# Set the subnet configuration for virtual network 1
$virtualNetwork1 | Set-AzureRmVirtualNetwork

# Add a gateway subnet
Add-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1 -VirtualNetwork $virtualNetwork1

# Set the subnet configuration for the virtual network
$virtualNetwork1 | Set-AzureRmVirtualNetwork

# Request a public IP address
$gwpip1= New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 `
 -AllocationMethod Dynamic

# Create the gateway IP address configuration
$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -VirtualNetwork $vnet1
$gwipconfig1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

# Create the VPN gateway (takes 20-40 minutes)
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
 -Location $Location1 -IpConfigurations $gwipconfig1 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1 

# Create the second resource group
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

# Create a virtual network 2
$virtualNetwork2 = New-AzureRmVirtualNetwork `
  -ResourceGroupName $RG2 `
  -Location $Location2 `
  -Name $VNetName2 `
  -AddressPrefix $VNetPrefix21

# Create a subnet configuration
Add-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2 -VirtualNetwork $virtualNetwork2

# Set the subnet configuration for virtual network 2
$virtualNetwork2 | Set-AzureRmVirtualNetwork

# Add a gateway subnet
Add-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2 -VirtualNetwork $virtualNetwork2

# Set the subnet configuration for the virtual network
$virtualNetwork2 | Set-AzureRmVirtualNetwork

# Request a public IP address
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 `
 -AllocationMethod Dynamic

# Create the gateway IP address configuration
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -VirtualNetwork $vnet2
$gwipconfig2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

# Create the VPN gateway (takes 20-40 minutes)
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 `
 -Location $Location2 -IpConfigurations $gwipconfig2 -GatewayType Vpn `
 -VpnType RouteBased -GatewaySku VpnGw1

# Create the connections
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 `
-VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
 ```
 
## <a name="clean-up-resources"></a>清理资源

如果不再需要所创建的资源，请使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令删除资源组。 这将删除资源组及其包含的所有资源。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
Remove-AzureRmResourceGroup -Name TestRG2
```
 
 
 ## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig) | 添加子网配置。 在虚拟网络创建过程中将使用此配置。 |
| [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) | 获取虚拟网络详细信息。 |
| [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway) | 获取虚拟网络网关详细信息。 |
| [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) | 获取虚拟网络子网配置详细信息。 |
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 创建子网配置。 在虚拟网络创建过程中将使用此配置。 |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 创建虚拟网络。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 创建公共 IP 地址。 |
| [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) | 新建网关 IP 配置。 |
| [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/new-azurermvirtualnetworkgateway) | 创建 VPN 网关。 |
| [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) | 创建 VNet 到 VNet 连接。 |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 删除资源组及其中包含的所有资源。 |
| [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) | 设置虚拟网络的子网配置。 |
| [Set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) | 设置 VPN 网关的配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。
