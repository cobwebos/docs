---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6505b12b35ee436930ba6571c27db30c12030041
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419115"
---
### <a name="gwipnoconnection"></a> 修改本地网关的“GatewayIpAddress”- 无网关连接

如果要连接的 VPN 设备已更改其公共 IP 地址，则需根据该更改修改本地网关。 请使用此示例修改没有网关连接的本地网关。

修改此值时，还可同时修改地址前缀。 请务必使用本地网关的现有名称来覆盖当前设置。 如果使用其他名称，请创建一个新的本地网关，而不是覆盖现有的。

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a> 修改本地网关的“GatewayIpAddress”- 现有网关连接

如果要连接的 VPN 设备已更改其公共 IP 地址，则需根据该更改修改本地网关。 如果网关连接已存在，首先需要删除该连接。 删除连接后，可修改网关 IP 地址并重新创建一个新的连接。 此外可同时修改地址前缀。 这会导致 VPN 连接中断一段时间。 修改网关 IP 地址时，不需删除 VPN 网关。 只需删除连接。
 

1. 删除连接。 可以使用“Get-AzVirtualNetworkGatewayConnection”cmdlet 查找连接的名称。

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. 修改“GatewayIpAddress”值。 此外可同时修改地址前缀。 请务必使用本地网关的现有名称来覆盖当前设置。 如果不这样做，请创建一个新的本地网关，而不是覆盖现有的。

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. 创建连接。 在此示例中，我们配置 IPsec 连接类型。 重新创建连接时，请使用针对配置指定的连接类型。 有关其他连接类型，请参阅 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 页面。  若要获取 VirtualNetworkGateway 名称，可运行“Get-AzVirtualNetworkGateway”cmdlet。
   
    设置变量。

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    创建连接。

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```