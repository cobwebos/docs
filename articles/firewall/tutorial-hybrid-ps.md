---
title: 教程：使用 Azure PowerShell 在混合网络中部署和配置 Azure 防火墙
description: 本教程介绍如何使用 Azure 门户部署和配置 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 12/14/2018
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 511fb425c6b31d204c88094ec5b1a49316495a0a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431514"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>教程：使用 Azure PowerShell 在混合网络中部署和配置 Azure 防火墙

将本地网络连接到 Azure 虚拟网络以创建混合网络时，必须能够控制对 Azure 网络资源的访问，这是整体安全计划的重要部分。

可以使用 Azure 防火墙通过规则来定义允许的和拒绝的网络流量，以便控制混合网络中的网络访问。

在本教程中，请创建三个虚拟网络：

- **VNet-Hub** - 防火墙在此虚拟网络中。
- **VNet-Spoke** - 分支虚拟网络代表 Azure 中的工作负荷。
- **VNet-Onprem** - 本地虚拟网络代表本地网络。 在实际部署中，可以使用 VPN 或 Route 连接来连接它。 为简单起见，本教程将使用 VPN 网关连接，并使用 Azure 中的某个虚拟网络来代表本地网络。

![混合网络中的防火墙](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 声明变量
> * 创建防火墙中心虚拟网络
> * 创建分支虚拟网络
> * 创建本地虚拟网络
> * 配置和部署防火墙
> * 创建并连接 VPN 网关
> * 将中心和分支虚拟网络对等互连
> * 创建路由
> * 创建虚拟机
> * 测试防火墙

## <a name="prerequisites"></a>先决条件

本教程要求在本地运行 PowerShell。 必须安装 Azure PowerShell 模块 6.12.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)。 验证 PowerShell 版本以后，请运行 `Login-AzureRmAccount`，以便创建与 Azure 的连接。

若要正常开展此方案，必须符合三项关键要求：

- 分支子网中有一个指向 Azure 防火墙 IP 地址（用作默认网关）的用户定义的路由 (UDR)。 必须在此路由表中**禁用** BGP 路由传播。
- 中心网关子网中的 UDR 必须指向用作分支网络下一跃点的防火墙 IP 地址。
- 无需在 Azure 防火墙子网中创建 UDR，因为它会从 BGP 探测路由。
- 在 VNet-Hub 与 VNet-Spoke 之间建立对等互连时，请务必设置 **AllowGatewayTransit**；在 VNet-Spoke 与 VNet-Hub 之间建立对等互连时，请务必设置 **UseRemoteGateways**。

请参阅本教程的[创建路由](#create-routes)部分了解如何创建这些路由。

>[!NOTE]
>Azure 防火墙必须具有直接的 Internet 连接。 如果已通过 ExpressRoute 或应用程序网关启用到本地的强制隧道，则需要配置 UDR 0.0.0.0/0，并将 **NextHopType** 值设置为 **Internet**，然后将其分配到 **AzureFirewallSubnet**。

>[!NOTE]
>即使 UDR 指向作为默认网关的 Azure 防火墙，也会直接路由直接对等互连 VNet 之间的流量。 若要在此方案中将子网到子网流量发送到防火墙，UDR 必须在这两个子网上显式地包含目标子网网络前缀。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="declare-the-variables"></a>声明变量

以下示例使用本教程中的值来声明变量。 在某些情况下，可能需要根据订阅情况将某些值替换为你自己的值。 根据需要修改变量，然后将变量复制并粘贴到 PowerShell 控制台中。

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>创建防火墙中心虚拟网络

首先，创建用于存储本教程资源的资源组：

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

定义要包含在虚拟网络中的子网：

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

现在，创建防火墙中心虚拟网络：

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

请求一个要分配到为虚拟网络创建的 VPN 网关的公共 IP 地址。 请注意，*AllocationMethod* 为 **Dynamic**（动态）。 无法指定要使用的 IP 地址。 该 IP 地址会动态分配到 VPN 网关。 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>创建分支虚拟网络

定义要包含在分支虚拟网络中的子网：

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

创建分支虚拟网络：

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>创建本地虚拟网络

定义要包含在虚拟网络中的子网：

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

现在，创建本地虚拟网络：

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

请求一个要分配到为虚拟网络创建的网关的公共 IP 地址。 请注意，*AllocationMethod* 为 **Dynamic**（动态）。 无法指定要使用的 IP 地址。 它动态分配到网关。 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>配置和部署防火墙

现在，将防火墙部署到中心虚拟网络。

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>配置网络规则

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>创建并连接 VPN 网关

通过 VPN 网关连接中心和本地虚拟网络。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>为中心虚拟网络创建 VPN 网关

创建 VPN 网关配置。 VPN 网关配置定义要使用的子网和公共 IP 地址。

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

现在，为中心虚拟网络创建 VPN 网关。 网络到网络配置需要 RouteBased VpnType。 创建 VPN 网关通常需要 45 分钟或更长时间，具体取决于所选 VPN 网关的 SKU。

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>为本地虚拟网络创建 VPN 网关

创建 VPN 网关配置。 VPN 网关配置定义要使用的子网和公共 IP 地址。

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

现在，为本地虚拟网络创建 VPN 网关。 网络到网络配置需要 RouteBased VpnType。 创建 VPN 网关通常需要 45 分钟或更长时间，具体取决于所选 VPN 网关的 SKU。

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>创建 VPN 连接

现在，可在中心与本地网关之间创建 VPN 连接

#### <a name="get-the-vpn-gateways"></a>获取 VPN 网关

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>创建连接

此步骤创建从中心虚拟网络到本地虚拟网络的连接。 示例中引用了共享密钥。 可以对共享密钥使用自己的值。 共享密钥必须与两个连接匹配，这一点非常重要。 创建连接可能需要简短的一段时间才能完成。

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
创建本地到中心虚拟网络连接。 此步骤类似于前一步骤，但这次是创建从 VNet-Onprem 到 VNet-hub 的连接。 确保共享密钥匹配。 几分钟后会建立连接。

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>验证连接

可以验证连接是否成功，方法是使用 *Get-AzureRmVirtualNetworkGatewayConnection* cmdlet，带或不带 *-Debug*。 使用以下 cmdlet 示例，配置符合自己需要的值。 如果出现提示，请选择“A”（表示“所有”）。 在此示例中，*-Name* 是指要测试的连接的名称。

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

cmdlet 运行完毕后，查看值。 在以下示例中，连接状态显示为 *Connected*，且可以看到入口和出口字节数。

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>将中心和分支虚拟网络对等互连

现在，将中心和分支虚拟网络对等互连。

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>创建路由

接下来创建一对路由：

- 通过防火墙 IP 地址从中心网关子网连接到分支子网的路由
- 通过防火墙 IP 地址从分支子网连接的默认路由

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>创建虚拟机

现在，创建分支工作负荷与本地虚拟机，并将其放入相应的子网。

### <a name="create-the-workload-virtual-machine"></a>创建工作负荷虚拟机

在分支虚拟网络中，创建运行 IIS 且不使用公共 IP 地址的虚拟机，并允许 ping 它。
出现提示时，请键入该虚拟机的用户名和密码。

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>创建本地虚拟机

这是一个简单的虚拟机，用于通过远程桌面连接到公共 IP 地址。 然后，请在该虚拟机中通过防火墙连接到本地服务器。 出现提示时，请键入该虚拟机的用户名和密码。

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>测试防火墙

首先，获取并记下 **VM-spoke-01** 虚拟机的专用 IP 地址。

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

在 Azure 门户中，连接到 **VM-Onprem** 虚拟机。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
在 **VM-Onprem** 上打开 Web 浏览器并浏览到 http://\<VM-spoke-01 的专用 IP\>。

应会看到 Internet Information Services 的默认页。

在 **VM-Onprem** 中，打开远程桌面并连接到 **VM-spoke-01** 的专用 IP 地址。

连接应会成功，并且应该可以使用所选的用户名和密码登录。

现已验证防火墙规则可正常工作：

<!---- You can ping the server on the spoke VNet.--->
- 可以浏览分支虚拟网络中的 Web 服务器。
- 可以使用 RDP 连接到分支虚拟网络中的服务器。

接下来，将防火墙网络规则集合操作更改为“拒绝”，以验证防火墙规则是否按预期工作。 运行以下脚本，将规则集合操作更改为“拒绝”。

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

现在再次运行测试。 这一次，这些规则应该全部失败。 在测试更改的规则之前，请关闭所有现有的远程桌面。

## <a name="clean-up-resources"></a>清理资源

可以保留防火墙资源以便在下一篇教程中使用。不再需要时，请删除 **FW-Hybrid-Test** 资源组，以删除与防火墙相关的所有资源。

## <a name="next-steps"></a>后续步骤

接下来，可以监视 Azure 防火墙日志。

> [!div class="nextstepaction"]
> [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
