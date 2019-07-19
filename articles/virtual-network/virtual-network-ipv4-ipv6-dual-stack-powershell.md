---
title: 在 Azure 虚拟网络中部署 IPv6 双重堆栈应用程序-PowerShell
titlesuffix: Azure Virtual Network
description: 本文介绍如何使用 Azure Powershell 在 Azure 虚拟网络中部署 IPv6 双重堆栈应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: b9a6b0ee6796acc2b9adc88480f6933af413e4e6
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260852"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---powershell-preview"></a>在 Azure 中部署 IPv6 双重堆栈应用程序-PowerShell (预览版)

本文介绍如何在 Azure 中部署一个双堆栈 (IPv4 + IPv6) 应用程序, 该应用程序包括双重堆栈虚拟网络和子网、带有双重 (IPv4 + IPv6) 前端配置的负载均衡器、具有具有双 IP 配置的 Nic 的 Vm、网络安全组和公共 Ip。

> [!Important]
> Azure 虚拟网络的 IPv6 支持当前提供公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell, 则本文需要 Azure PowerShell 模块版本6.9.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="prerequisites"></a>系统必备
在 Azure 中部署双堆栈应用程序之前, 必须使用以下 Azure PowerShell 来配置此预览功能的订阅:

按如下所示进行注册:

```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
功能注册最多需要 30 分钟才能完成。 可以通过运行以下 Azure PowerShell 命令来检查注册状态:检查注册, 如下所示:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
注册完成后，运行以下命令：

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>创建资源组

必须先使用[AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)创建资源组, 然后才能创建双堆栈虚拟网络。 以下示例在 "*美国东部*" 位置创建名为 " *myRGDualStack* " 的资源组:

```azurepowershell-interactive
   $rg = New-AzResourceGroup `
  -ResourceGroupName "dsRG1"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>创建 IPv4 和 IPv6 公共 IP 地址
若要从 Internet 访问虚拟机, 需要负载均衡器的 IPv4 和 IPv6 公共 IP 地址。 通过[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)创建公共 IP 地址。 以下示例在*dsRG1*资源组中创建名为*dsPublicIP_v4*和*dsPublicIP_v6*的 IPv4 和 IPv6 公共 IP 地址:

```azurepowershell-interactive
$PublicIP_v4 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v4" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
$PublicIP_v6 = New-AzPublicIpAddress `
  -Name "dsPublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv6
```
若要使用 RDP 连接访问虚拟机, 请使用[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)为虚拟机创建 IPV4 公共 IP 地址。

```azurepowershell-interactive
  $RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Dynamic `
  -IpAddressVersion IPv4
  
  $RdpPublicIP_2 = New-AzPublicIpAddress `
   -Name "RdpPublicIP_2" `
   -ResourceGroupName $rg.ResourceGroupName `
   -Location $rg.Location  `
   -AllocationMethod Dynamic `
   -IpAddressVersion IPv4
```

## <a name="create-basic-load-balancer"></a>创建基本负载均衡器

在本部分中, 将配置负载均衡器的双前端 IP (IPv4 和 IPv6) 和后端地址池, 并创建基本的负载均衡器。

### <a name="create-front-end-ip"></a>创建前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 创建一个前端 IP。 以下示例创建名为 " *dsLbFrontEnd_v4* " 和 " *DsLbFrontEnd_v6*" 的 IPV4 和 IPv6 前端 IP 配置:

```azurepowershell-interactive
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PublicIpAddress $PublicIP_v4

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6

```

### <a name="configure-back-end-address-pool"></a>配置后端地址池

使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 创建一个后端地址池。 在剩余步骤中，VM 将连接到此后端池。 以下示例创建名为*dsLbBackEndPool_v4*的后端地址池和*dsLbBackEndPool_v6* , 以包括同时具有 IPV4 和 IPv6 NIC 配置的 vm:

```azurepowershell-interactive
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig `
-Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需的源端口和目标端口。 若要确保仅正常运行的 Vm 接收流量, 可以选择定义运行状况探测。 基本负载均衡器使用 IPv4 探测来评估 Vm 上 IPv4 和 IPv6 终结点的运行状况。 标准负载均衡器支持显式 IPv6 运行状况探测。

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 创建一个负载均衡器规则。 以下示例创建名为 " *dsLBrule_v4* " 和 " *dsLBrule_v6* " 的负载均衡器规则, 并将*TCP*端口*80*上的流量平衡到 IPv4 和 IPv6 前端 IP 配置:

```azurepowershell-interactive
$lbrule_v4 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v4" `
  -FrontendIpConfiguration $frontendIPv4 `
  -BackendAddressPool $backendPoolv4 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

$lbrule_v6 = New-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

### <a name="create-load-balancer"></a>创建负载均衡器

使用 [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) 创建基本负载均衡器。 以下示例使用在前面的步骤中创建的 IPv4 和 IPv6 前端 IP 配置、后端池和负载均衡规则, 创建名为*myLoadBalancer*的公共基本负载均衡器:

```azurepowershell-interactive
$lb = New-AzLoadBalancer `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "MyLoadBalancer" `
-Sku "Basic" `
-FrontendIpConfiguration $frontendIPv4,$frontendIPv6 `
-BackendAddressPool $backendPoolv4,$backendPoolv6 `
-LoadBalancingRule $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>创建网络资源
在部署某些 Vm 并可以测试均衡器之前, 必须创建支持网络资源-可用性集、网络安全组、虚拟网络和虚拟 Nic。 
### <a name="create-an-availability-set"></a>创建可用性集
要提高应用的高可用性，请将 VM 放置在可用性集中。

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) 创建一个可用性集。 以下示例创建名为“myAvailabilitySet”  的可用性集：

```azurepowershell-interactive
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>创建网络安全组

为将在 VNET 中控制入站和出站通信的规则创建网络安全组。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>为端口 3389 创建网络安全组规则

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建网络安全组规则以允许通过端口 3389 创建 RDP 连接。

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
-Name 'myNetworkSecurityGroupRuleRDP' `
-Description 'Allow RDP' `
-Access Allow `
-Protocol Tcp `
-Direction Inbound `
-Priority 100 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389
```
#### <a name="create-a-network-security-group-rule-for-port-80"></a>为端口 80 创建网络安全组规则

创建网络安全组规则, 以允许通过[AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)的端口80连接 internet。

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name 'myNetworkSecurityGroupRuleHTTP' `
  -Description 'Allow HTTP' `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange 80 `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80
```
#### <a name="create-a-network-security-group"></a>创建网络安全组

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 创建网络安全组。

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "dsNSG1"  `
-SecurityRules $rule1,$rule2
```
### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 创建虚拟网络。 以下示例创建具有“mySubnet”  的名为“myVnet”  的虚拟网络：

```azurepowershell-interactive
# Create dual stack subnet
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "dsSubnet" `
-AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $subnet
```

### <a name="create-nics"></a>创建 NIC

创建具有[AzNetworkInterface 的](/powershell/module/az.network/new-aznetworkinterface)虚拟 nic。 下面的示例创建两个虚拟 Nic, 它们都是 IPv4 和 IPv6 配置。 （在以下步骤中为应用创建的每个 VM 各使用一个虚拟 NIC）。

```azurepowershell-interactive
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_1
    
  $Ip6Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp6Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv6 `
    -LoadBalancerBackendAddressPool $backendPoolv6
    
  $NIC_1 = New-AzNetworkInterface `
    -Name "dsNIC1" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 
    
  $Ip4Config=New-AzNetworkInterfaceIpConfig `
    -Name dsIp4Config `
    -Subnet $vnet.subnets[0] `
    -PrivateIpAddressVersion IPv4 `
    -LoadBalancerBackendAddressPool $backendPoolv4 `
    -PublicIpAddress  $RdpPublicIP_2  

  $NIC_2 = New-AzNetworkInterface `
    -Name "dsNIC2" `
    -ResourceGroupName $rg.ResourceGroupName `
    -Location $rg.Location  `
    -NetworkSecurityGroupId $nsg.Id `
    -IpConfiguration $Ip4Config,$Ip6Config 

```

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [New-AzureRmNetworkInterface](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell-interactive
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VMs."
```

现在，可使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建 VM。 以下示例创建两台 VM 和所需的虚拟网络组件（如果它们尚不存在）。 

```azurepowershell-interactive
$vmsize = "Standard_A2"
$ImagePublisher = "MicrosoftWindowsServer"
$imageOffer = "WindowsServer"
$imageSKU = "2019-Datacenter"

$vmName= "dsVM1"
$VMconfig1 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_1.Id  3> $null 
$VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig1 

$vmName= "dsVM2"
$VMconfig2 = New-AzVMConfig -VMName $vmName -VMSize $vmsize -AvailabilitySetId $avset.Id 3> $null | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent 3> $null | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" 3> $null | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption fromImage  3> $null | Add-AzVMNetworkInterface -Id $NIC_2.Id  3> $null 
$VM2 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $VMconfig2
```

## <a name="determine-ip-addresses-of-the-ipv4-and-ipv6-endpoints"></a>确定 IPv4 和 IPv6 终结点的 IP 地址
获取资源组中的所有网络接口对象, 以汇总此部署`get-AzNetworkInterface`中使用的 IP。 此外, 还可以通过`get-AzpublicIpAddress`获取 IPv4 和 IPv6 终结点的负载均衡器的前端地址。

```azurepowershell-interactive
$rgName= "dsRG1"
$NICsInRG= get-AzNetworkInterface -resourceGroupName $rgName 
write-host `nSummary of IPs in this Deployment: 
write-host ******************************************
foreach ($NIC in $NICsInRG) {
 
    $VMid= $NIC.virtualmachine.id 
    $VMnamebits= $VMid.split("/") 
    $VMname= $VMnamebits[($VMnamebits.count-1)] 
    write-host `nPrivate IP addresses for $VMname 
    $IPconfigsInNIC= $NIC.IPconfigurations 
    foreach ($IPconfig in $IPconfigsInNIC) {
 
        $IPaddress= $IPconfig.privateipaddress 
        write-host "    "$IPaddress 
        IF ($IPconfig.PublicIpAddress.ID) {
 
            $IDbits= ($IPconfig.PublicIpAddress.ID).split("/")
            $PipName= $IDbits[($IDbits.count-1)]
            $PipObject= get-azPublicIpAddress -name $PipName -resourceGroup $rgName
            write-host "    "RDP address:  $PipObject.IpAddress
                 }
         }
 }
 
 
 
  write-host `nPublic IP addresses on Load Balancer:
 
  (get-AzpublicIpAddress -resourcegroupname $rgName | where { $_.name -notlike "RdpPublicIP*" }).IpAddress
```
下图显示了一个示例输出, 其中列出了两个 Vm 的专用 IPv4 和 IPv6 地址, 以及负载均衡器的前端 IPv4 和 IPv6 IP 地址。

![Azure 中的双堆栈 (IPv4/IPv6) 应用程序部署的 IP 摘要](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-application-summary.png)

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 门户中查看 IPv6 双堆栈虚拟网络
可以在 Azure 门户中查看 IPv6 双堆栈虚拟网络, 如下所示:
1. 在门户的搜索栏中, 输入 " *dsVnet*"。
2. 当“myVirtualNetwork”出现在搜索结果中时，将其选中。  这将启动名为*dsVnet*的双堆栈虚拟网络的 "**概述**" 页。 双堆栈虚拟网络将显示两个 Nic, 它们都位于名为*dsSubnet*的双堆栈子网中。

  ![Azure 中的 IPv6 双堆栈虚拟网络](./media/virtual-network-ipv4-ipv6-dual-stack-powershell/dual-stack-vnet.png)

> [!NOTE]
> 适用于 Azure 虚拟网络的 IPv6 可在此预览版本的只读 Azure 门户中使用。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name dsRG1
```

## <a name="next-steps"></a>后续步骤

在本文中, 你创建了一个具有双前端 IP 配置 (IPv4 和 IPv6) 的基本负载均衡器。 还创建了两个虚拟机, 其中包含已添加到负载均衡器后端池的双 IP 配置 (IPV4 + IPv6) 的 Nic。 若要了解有关 Azure 虚拟网络中 IPv6 支持的详细信息, 请参阅[什么是适用于 Azure 虚拟网络的 ipv6？](ipv6-overview.md)