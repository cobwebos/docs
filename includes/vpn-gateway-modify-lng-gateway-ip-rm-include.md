### <a name="gwipnoconnection"></a>若要修改本地网络网关 GatewayIpAddress 的任何网关连接

如果你想要连接到 VPN 设备已更改其公有 IP 地址，你需要修改本地网络网关，以反映所做的更改。 使用示例来修改没有网关连接的本地网络网关。

在修改此值时，你还可以修改在同一时间的地址前缀。 请务必使用你的本地网络网关的现有名称，若要覆盖当前的设置。 如果你使用不同的名称，则会创建新的本地网络网关，而不是覆盖现有的一个。

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>若要修改本地网络网关 GatewayIpAddress 的现有网关连接

如果你想要连接到 VPN 设备已更改其公有 IP 地址，你需要修改本地网络网关，以反映所做的更改。 如果网关已存在的连接，你首先需要删除的连接。 删除该连接后，您可以修改网关 IP 地址并重新创建一个新的连接。 你还可以在同一时间修改的地址前缀。 这会导致你的 VPN 连接的一些停机时间。 在修改网关 IP 地址，你不需要删除 VPN 网关。 只需删除的连接。
 

1. 删除连接。 可以通过使用 Get AzureRmVirtualNetworkGatewayConnection cmdlet 来查找你连接的名称。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. 修改 GatewayIpAddress 值。 你还可以在同一时间修改的地址前缀。 请务必使用你的本地网络网关的现有名称来覆盖当前的设置。 如果不这样做，则会创建新的本地网络网关，而不是覆盖现有的一个。

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. 创建连接。 在此示例中，我们配置 IPsec 连接类型。 在重新创建你的连接，使用你的配置指定的连接类型。 有关其他连接类型，请参见[PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx)页。  若要获取的 VirtualNetworkGateway 名称，你可以运行 Get AzureRmVirtualNetworkGateway cmdlet。
   
    设置变量。

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    创建连接。

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```