此任务的步骤使用 VNet 根据以下配置引用列表中的值。 此列表中，还概述了其他设置和名称。 尽管我们希望添加基于此列表中的值的变量，我们不直接在任何步骤，使用此列表。 你可以复制要用作参考的列表将替换为你自己的值。

**配置引用列表**

* 虚拟网络名称 ="TestVNet"
* 虚拟网络地址空间 = 192.168.0.0/16
* 资源组 ="TestRG"
* Subnet1 名称 ="FrontEnd" 
* Subnet1 地址空间 ="192.168.1.0/24"
* 网关的子网名称:"GatewaySubnet"必须始终命名网关子网*GatewaySubnet*。
* 网关子网地址空间 ="192.168.200.0/26"
* 区域 ="East US"
* 网关名称 ="GW"
* 网关 IP 名称 ="GWIP"
* 网关 IP 配置名称 ="gwipconf"
* 类型 ="ExpressRoute"此类型是必需的 ExpressRoute 配置。
* 网关的公共 IP 名称 ="gwpip"

## <a name="add-a-gateway"></a>添加网关
1. 连接到你的 Azure 订阅。

  ```powershell 
  Login-AzureRmAccount
  Get-AzureRmSubscription 
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. 声明你对于本练习的变量。 请务必编辑此示例以反映你想要使用的设置。

  ```powershell 
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "GW"
  $GWIPName = "GWIP"
  $GWIPconfName = "gwipconf"
  $VNetName = "TestVNet"
  ```
3. 将虚拟网络对象存储为一个变量。

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
  ```
4. 将网关子网添加到你的虚拟网络。 网关子网必须命名为"GatewaySubnet"。 你应创建网关子网是/27 或更大 (/ 26 日/月 25 日，等等。)。

  ```powershell
  Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
  ```
5. 设置的配置。

  ```powershell
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```
6. 存储变量的网关子网。

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
  ```
7. 请求一个公共 IP 地址。 创建网关之前请求的 IP 地址。 不能指定你想要使用; 的 IP 地址动态分配。 你将在下一步的配置部分中使用此 IP 地址。 将 AllocationMethod 必须是动态的。

  ```powershell
  $pip = New-AzureRmPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
  ```
8. 创建您的网关的配置。 网关配置定义子网以及要使用的公共 IP 地址。 在此步骤中，指定当创建网关时将使用的配置。 此步骤不实际创建的网关对象。 使用下面的示例创建你的网关配置。

  ```powershell
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
  ```
9. 创建网关。 在此步骤中， **-GatewayType**尤为重要。 你必须使用值**ExpressRoute**。 在运行这些 cmdlet 之后, 网关可能需要 45 分钟或更创建。

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
  ```

## <a name="verify-the-gateway-was-created"></a>验证已创建网关
使用以下命令来验证已创建网关：

```powershell
Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>调整大小网关
有大量的[网关 Sku](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。 可以使用以下命令以在任何时候更改网关 SKU。

> [!IMPORTANT]
> 此命令不起作用 UltraPerformance 网关。 若要更改 UltraPerformance 网关到网关，请首先删除现有的 ExpressRoute 网关，然后创建新的 UltraPerformance 网关。 若要降级从 UltraPerformance 网关将网关，首先删除 UltraPerformance 网关，然后再创建新的网关。
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