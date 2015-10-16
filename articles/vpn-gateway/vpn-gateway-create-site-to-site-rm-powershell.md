<properties
   pageTitle="使用 Azure 资源管理器和 PowerShell 创建具有站点到站点 VPN 连接的虚拟网络 | Microsoft Azure"
   description="本文将指导你完成使用资源管理器模型创建 VNet 并使用站点到站点 VPN 网关连接将其连接到你的本地网络。包括用于修改现有本地站点 IP 地址前缀的其他步骤。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.date="09/14/2015"
   wacn.date=""/>

# 使用 Azure 资源管理器和 PowerShell 创建具有站点到站点 VPN 连接的虚拟网络

> [AZURE.SELECTOR]
- [Azure 门户](/documentation/articles/vpn-gateway-site-to-site-create)
- [PowerShell - 资源管理器](/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell)


本主题将指导你创建一个 Azure 资源管理器虚拟网络和一个到本地网络的站点到站点 VPN 连接。

>[AZURE.IMPORTANT]请务必了解 Azure 当前使用两种部署模型：资源管理器和经典部署模型。在开始你的配置之前，请确保你了解部署模型和工具。有关部署模型的信息，请参阅 [Azure 部署模型](../azure-classic-rm.md)。

你可以通过使用上面的选项卡选择有关部署模型和部署工具的文章。例如，如果你想要使用 Azure 门户中的典型部署模型，而不是使用资源管理器模型，创建站点到站点 VPN 网关连接，请单击“Azure 门户”选项卡（上方）以打开“在 Azure 门户中创建站点到站点 VPN 连接”。[](vpn-gateway-site-to-site-create.md)


## 开始之前

在开始配置之前，请确认具有以下各项：

- 一台兼容 VPN 设备（和能够对其进行配置的人员）。请参阅[关于 VPN 设备](/documentation/articles/vpn-gateway-about-vpn-devices)。
- 一个用于 VPN 设备的面向外部的公共 IP 地址。此 IP 地址不得位于 NAT 之后。
- 最新版本的 Azure PowerShell cmdlet。可以从[下载页面](http://azure.microsoft.com/downloads/)的 Windows PowerShell 部分下载并安装最新版本。 
- Azure 订阅。如果你还没有 Azure 订阅，可以注册一个 [Azure 试用版](http://www.windowsazure.cn/pricing/1rmb-trial/)。
	
>[AZURE.IMPORTANT]如果你不熟悉 VPN 设备的配置，或者不熟悉本地网络配置中的 IP 地址范围，则需咨询能够为你提供此类详细信息的人员。

## 连接到订阅 


打开 PowerShell 控制台并连接到你的帐户。使用下面的示例来帮助你连接：

		Add-AzureAccount

如果你的订阅超过一个，请使用 *Select-AzureSubscription* 连接到你要使用的订阅。

		Select-AzureSubscription "yoursubscription"

接下来，切换到 Azure 资源管理器模式。在不久的将来，将不需要更改为此模式，并且用于这两种模式的 cmdlet 将自动可用。

		Switch-AzureMode -Name AzureResourceManager


## 创建虚拟网络和网关子网

- 如果你已经拥有一个包含网关子网的虚拟网络，则可以跳转到[添加你的本地站点](#add-your-local-site)。 
- 如果你有一个虚拟网络并且希望向 VNet 添加一个网关子网，请参阅[向 VNet 添加网关子网](#gatewaysubnet)。

### 若要创建虚拟网络和网关子网

使用下面的示例创建一个虚拟网络和网关子网。替换为你自己的值。

首先，创建一个资源组：

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

接下来，创建你的虚拟网络。请验证你指定的地址空间不与本地网络的任一个地址空间相重叠。

下面的示例创建一个名为 *testvnet* 的虚拟网络和两个子网，其中一个名为 *GatewaySubnet*，另一个名为 *Subnet1*。特意创建一个名为 *GatewaySubnet* 的子网非常重要。如果你以其他名称为其命名，则你的连接配置将会失败。

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>向 VNet 添加网关子网（可选）

只有你需要向 VNet 添加网关子网，才需要使用此步骤。

如果你已经有一个现有的虚拟网络并且希望在其中添加一个网关子网，可以通过使用下面的示例来创建网关子网。请务必将网关子网命名为“GatewaySubnet”。如果你将其命名其他内容，你将创建一个子网，但它不会被 Azure 视为网关子网。

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

现在，设置配置。

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 添加你的本地站点

在虚拟网络中，*本地站点*通常指你的本地位置。你将为该站点命名，而 Azure 可通过该名称引用该站点。

你还将指定本地站点的地址空间前缀。Azure 将使用你指定的 IP 地址前缀来识别要发送到本地站点的流量。这意味着你必须指定要与本地站点关联的各个地址前缀。如果本地网络出现变化，你可以轻松更新这些前缀。使用下面的 PowerShell 示例来指定你的本地站点。

在使用 PowerShell 示例时，请注意下列事项：
	
- *GatewayIPAddress* 是本地 VPN 设备的 IP 地址。VPN 设备不能位于 NAT 之后。 
- *AddressPrefix* 是本地地址空间。

若要添加具有单个地址前缀的本地站点：

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

若要添加具有多个地址前缀的本地站点：

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')


### 若要为你的本地网站修改的 IP 地址前缀：

有时你的本地站点前缀会有变化。修改你的 IP 地址前缀时采取的步骤取决于是否已创建 VPN 网关连接。请参阅[修改本地网站的 IP 地址前缀](#modify-ip-address-prefixes-for-a-local-site)。



## 为网关请求一个公共 IP 地址

接下来，你需要请求一个公共 IP 地址并将其分配到 Azure VNet VPN 网关。这个地址与分配到 VPN 设备的 IP 地址不同，而是分配到 Azure VPN 网关本身。你无法指定要使用的 IP 地址；它动态分配到你的网关。在配置本地 VPN 设备连接到网关时，你将使用这个 IP 地址。

使用下面的 PowerShell 示例。此地址的分配方法必须是“动态”。

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 创建网关 IP 寻址配置

网关配置定义要使用的子网和公共 IP 地址。使用下面的示例创建你的网关配置。


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## 创建网关

在此步骤中，你将创建虚拟网络网关。请注意，创建网关需要一段时间才能完成。

使用以下值：

- 网关类型为 *Vpn*。
- VpnType 可以是 RouteBased\*（在某些文档中称为动态网关）或 *Policy Based*（在某些文档中称为静态网关）。有关 VPN 网关类型的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 配置 VPN 设备

此时，你需要使用虚拟网络网关的公共 IP 地址来配置本地 VPN 设备。请联系你的设备制造商以获得具体的配置信息。此外，请参阅 [VPN 设备](http://go.microsoft.com/fwlink/p/?linkid=615099)以获得更多信息。

若要查找虚拟网络网关的公共 IP 地址，请使用下面的示例：

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 创建 VPN 连接

接下来，你将在虚拟网络网关和 VPN 设备之间创建站点到站点 VPN 连接。请务必替换为你自己的值。共享密钥必须与你用于 VPN 设备配置的值匹配。

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

在一小段时间后，将建立该连接。

## 验证 VPN 连接

此时，通过资源管理器创建的站点到站点 VPN 连接不在预览门户中显示。但是，可以通过使用 *get azurevirtualnetworkgatewayconnection ¨CDebug* 验证你的连接是否成功。将来，我们将为此提供一个 cmdlet 以及在预览门户中查看你的连接的功能。

你可以使用下面的 cmdlet 示例，配置符合自己需要的值。出现提示时，选择 *A* 以运行所有。

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 cmdlet 运行完毕后，通过滚动查看该值。在下面的示例中，连接状态显示为“已连接”，且你可以看到入口和出口字节数。

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## 修改本地网站的 IP 地址前缀

如果需要更改你的本地网站的前缀，请使用下面的说明。提供两个指令集，但取决于你是否已创建 VPN 网关连接。

### 添加或删除不具有 VPN 网关连接的前缀


- 若要将其他地址前缀添加到你已创建的但尚无 VPN 网关连接的本地站点，请使用下面的示例。

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')



- 若要从没有 VPN 连接的本地站点删除地址前缀，使用下面的示例。省去你不再需要的前缀。在此示例中，我们不再需要前缀 20.0.0.0/24（来自前面的示例），因此我们将更新本地站点并排除该前缀。

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### 添加或删除具有 VPN 网关连接的前缀

如果你已创建 VPN 连接并且想要添加或删除包含在你的本地网站中的 IP 地址前缀，你将需要按顺序执行以下步骤。这将导致你的 VPN 连接中断一段时间，因为你需要删除并重建网关。但是，由于你已为该连接请求一个 IP 地址，因此不需要重新配置你的本地 VPN 路由器，除非你决定要更改你以前使用过的值。

 
1. 删除网关连接。 
2. 修改你本地网站的前缀。 
3. 创建新的网关连接。 

你可以使用下面的示例作为指导原则。


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



## 后续步骤

将虚拟机添加到虚拟网络。[创建虚拟机](/documentation/articles/virtual-machines-windows-tutorial)。

<!---HONumber=74-->