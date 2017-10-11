### <a name="noconnection"></a>若要修改本地网络网关 IP 地址前缀的没有网关连接

若要添加其他地址前缀：

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
```

若要删除地址前缀：<br>
省去你不再需要的前缀。 在此示例中，我们不再需要前缀 20.0.0.0/24 （从前面的示例），因此我们更新本地网络网关，排除该前缀。

```powershell
$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')
```

### <a name="withconnection"></a>若要修改本地网络网关 IP 地址前缀的现有网关连接

如果你具有网关连接，并想要添加或删除包含在本地网络网关的 IP 地址前缀，你需要执行以下步骤，按顺序。 这会导致你的 VPN 连接的一些停机时间。 在修改 IP 地址前缀，你不需要删除 VPN 网关。 只需删除的连接。


1. 删除连接。

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName
  ```
2. 修改你本地网络网关的地址前缀。
   
  为 LocalNetworkGateway 设置变量。

  ```powershell
  $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName
  ```
   
  修改前缀。
   
  ```powershell
  Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
  -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
  ```
3. 创建连接。 在此示例中，我们配置 IPsec 连接类型。 在重新创建你的连接，使用你的配置指定的连接类型。 有关其他连接类型，请参见[PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx)页。
   
  为 VirtualNetworkGateway 设置变量。

  ```powershell
  $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName
  ```
   
  创建连接。 此示例使用变量 $local 步骤 2 中设置。

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName -Location 'West US' `
  -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
  -ConnectionType IPsec `
  -RoutingWeight 10 -SharedKey 'abc123'
  ```