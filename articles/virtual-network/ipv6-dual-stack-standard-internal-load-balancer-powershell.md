---
title: 使用 Azure 中的标准内部负载均衡器部署 IPv6 双堆栈应用程序-PowerShell
titlesuffix: Azure Virtual Network
description: 本文介绍如何使用 Azure Powershell 在 Azure 虚拟网络中部署具有标准内部负载均衡器的 IPv6 双重堆栈应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2019
ms.author: kumud
ms.openlocfilehash: fdf726fd31e8b92a04a1c136eb5cd7110e0c6d5a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333360"
---
# <a name="deploy-an-ipv6-dual-stack-application-using-standard-internal-load-balancer-in-azure---powershell-preview"></a>使用 Azure 中的标准内部负载均衡器部署 IPv6 双堆栈应用程序-PowerShell （预览版）

本文介绍如何在 Azure 中部署包含双堆栈虚拟网络和子网的双堆栈（IPv4 + IPv6）应用程序、带有双重（IPv4 + IPv6）前端配置的标准内部负载均衡器、具有双 IP 的 Nic 的 Vm配置、网络安全组和公共 Ip。

> [!Important]
> Azure 虚拟网络的 IPv6 支持当前提供公共预览版。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

创建支持 IPv6 的内部负载均衡器的过程与创建面向 Internet 的 IPv6 负载均衡器的过程几乎完全[相同。](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 创建内部负载均衡器的唯一区别在于前端配置，如下面的 PowerShell 示例中所示：

```azurepowershell
 $frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
 -Name "dsLbFrontEnd_v6" `
 -PrivateIpAddress "ace:cab:deca:deed::100" `
 -PrivateIpAddressVersion "IPv6" `
 -Subnet $DsSubnet
```

使上述内部负载均衡器前端配置的更改如下所示：
- @No__t 指定为 "IPv6"
- 已省略 @no__t 参数 `-PrivateIpAddress`，或已将其替换为。 请注意，专用地址必须在将部署内部负载均衡器的子网 IP 空间的范围内。 如果省略静态 `-PrivateIpAddress`，将从部署内部负载均衡器的子网中选择下一个可用的 IPv6 地址。
- 将在其中部署内部负载均衡器的双堆栈子网使用 @no__t 0 或 `-SubnetId` 参数指定。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块版本6.9.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="prerequisites"></a>必备组件
在 Azure 中部署双堆栈应用程序之前，必须使用以下 Azure PowerShell 来配置此预览功能的订阅：

按如下所示进行注册：
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
功能注册最多需要 30 分钟才能完成。 可以通过运行以下 Azure PowerShell 命令来检查注册状态：按如下所示检查注册：
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
注册完成后，运行以下命令：

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>创建资源组

必须先使用[AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)创建资源组，然后才能创建双堆栈虚拟网络。 以下示例在 "*美国东部*" 位置创建名为 " *dsStd_ILB_RG* " 的资源组：

```azurepowershell
$rg = New-AzResourceGroup `
  -ResourceGroupName "dsStd_ILB_RG"  `
  -Location "east us"
```

## <a name="create-ipv4-and-ipv6-public-ip-addresses"></a>创建 IPv4 和 IPv6 公共 IP 地址
若要从 Internet 访问虚拟机，需要 Vm 的 IPv4 和 IPv6 公共 IP 地址。 通过[AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)创建公共 IP 地址。 以下示例在*dsStd_ILB_RG*资源组中创建名为*RdpPublicIP_1*和*RdpPublicIP_2*的 IPv4 和 IPv6 公共 IP 地址：

```azurepowershell
$RdpPublicIP_1 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
  
$RdpPublicIP_2 = New-AzPublicIpAddress `
  -Name "RdpPublicIP_2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -AllocationMethod Static `
  -IpAddressVersion IPv4  `
  -sku Standard
```
## <a name="create-the-virtual-network-and-the-subnet"></a>创建虚拟网络和子网

使用[AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)通过双堆栈创建虚拟网络使用[AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)的子网配置。 以下示例使用*dsSubnet*创建名为*dsVnet*的虚拟网络。

```azurepowershell
# Create dual stack subnet config
$DsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name "dsSubnet" `
  -AddressPrefix "10.0.0.0/24","ace:cab:deca:deed::/64"

# Create the virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsVnet" `
  -AddressPrefix "10.0.0.0/16","ace:cab:deca::/48"  `
  -Subnet $DsSubnet

#Refresh the fully populated subnet for use in load balancer frontend configuration
$DsSubnet = get-AzVirtualNetworkSubnetconfig -name dsSubnet -VirtualNetwork $vnet
```
## <a name="create-standard-load-balancer"></a>创建标准负载均衡器

在本部分中，将配置负载均衡器的前端 IP （IPv4 和 IPv6）和后端地址池，然后创建标准负载均衡器。

### <a name="create-front-end-ip"></a>创建前端 IP

使用 [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) 创建一个前端 IP。 以下示例创建名为 " *dsLbFrontEnd_v4* " 和 " *DsLbFrontEnd_v6*" 的 IPV4 和 IPv6 前端 IP 配置：

```azurepowershell
$frontendIPv4 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v4" `
  -PrivateIpAddress "10.0.0.100"  `
  -PrivateIpAddressVersion "IPv4"   `
  -Subnet $DsSubnet

$frontendIPv6 = New-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PrivateIpAddress "ace:cab:deca:deed::100"  `
  -PrivateIpAddressVersion "IPv6"   `
  -Subnet $DsSubnet

```

### <a name="configure-back-end-address-pool"></a>配置后端地址池

使用 [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) 创建一个后端地址池。 在剩余步骤中，VM 将连接到此后端池。 以下示例创建名为*dsLbBackEndPool_v4*的后端地址池和*dsLbBackEndPool_v6* ，以包括同时具有 IPV4 和 IPv6 NIC 配置的 vm：

```azurepowershell
$backendPoolv4 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v4"

$backendPoolv6 = New-AzLoadBalancerBackendAddressPoolConfig -Name "dsLbBackEndPool_v6"
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需源和目标端口。 若要确保仅正常运行的 Vm 接收流量，可以选择定义运行状况探测。 基本负载均衡器使用 IPv4 探测来评估 Vm 上 IPv4 和 IPv6 终结点的运行状况。 标准负载均衡器支持显式 IPv6 运行状况探测。

使用 [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) 创建一个负载均衡器规则。 以下示例创建名为 " *dsLBrule_v4* " 和 " *dsLBrule_v6* " 的负载均衡器规则，并将*TCP*端口*80*上的流量平衡到 IPv4 和 IPv6 前端 IP 配置：

```azurepowershell
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

使用[AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)创建标准负载均衡器。 下面的示例使用在前面的步骤中创建的 IPv4 和 IPv6 前端 IP 配置、后端池和负载均衡规则，创建名为*myInternalLoadBalancer*的公共标准负载均衡器：

```azurepowershell
$lb = New-AzLoadBalancer  `
  -ResourceGroupName $rg.ResourceGroupName  `
  -Location $rg.Location  `
  -Name  "MyInternalLoadBalancer"  `
  -Sku "Standard"  `
  -FrontendIpConfiguration  $frontendIPv4,$frontendIPv6  `
  -BackendAddressPool  $backendPoolv4,$backendPoolv6  `
  -LoadBalancingRule  $lbrule_v4,$lbrule_v6

```

## <a name="create-network-resources"></a>创建网络资源
在部署某些 Vm 并可以测试均衡器之前，必须创建支持网络资源-可用性集、网络安全组和虚拟 Nic。 

### <a name="create-an-availability-set"></a>创建可用性集
若要提高应用程序的高可用性，请将 Vm 放置在可用性集中。

使用 [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) 创建一个可用性集。 以下示例创建名为*dsAVset*的可用性集：

```azurepowershell
$avset = New-AzAvailabilitySet `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsAVset" `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2 `
  -Sku aligned
```

### <a name="create-network-security-group"></a>创建网络安全组

为将在 VNet 中控制入站和出站通信的规则创建网络安全组。

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>为端口 3389 创建网络安全组规则

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建网络安全组规则以允许通过端口 3389 创建 RDP 连接。

```azurepowershell
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

创建网络安全组规则，以允许通过[AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)的端口80连接 internet。

```azurepowershell
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

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Name "dsNSG1"  `
  -SecurityRules $rule1,$rule2
```
### <a name="create-nics"></a>创建 NIC

创建具有[AzNetworkInterface 的](/powershell/module/az.network/new-aznetworkinterface)虚拟 nic。 下面的示例创建两个虚拟 Nic，它们都是 IPv4 和 IPv6 配置。 （在以下步骤中为应用创建的每个 VM 各使用一个虚拟 NIC）。

```azurepowershell

# Create the IPv4 configuration for NIC 1
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_1

# Create the IPv6 configuration
$Ip6Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp6Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv6 `
  -LoadBalancerBackendAddressPool $backendPoolv6

# Create NIC 1
$NIC_1 = New-AzNetworkInterface `
  -Name "dsNIC1" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

# Create the IPv4 configuration for NIC 2
$Ip4Config=New-AzNetworkInterfaceIpConfig `
  -Name dsIp4Config `
  -Subnet $vnet.subnets[0] `
  -PrivateIpAddressVersion IPv4 `
  -LoadBalancerBackendAddressPool $backendPoolv4 `
  -PublicIpAddress  $RdpPublicIP_2

# Create NIC 2 reusing the IPv6 configuration from NIC 1
$NIC_2 = New-AzNetworkInterface `
  -Name "dsNIC2" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -NetworkSecurityGroupId $nsg.Id `
  -IpConfiguration $Ip4Config,$Ip6Config

```

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [New-AzureRmNetworkInterface](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell
$cred = get-credential -Message "DUAL STACK VNET SAMPLE:  Please enter the Administrator credential to log into the VM's"
```

现在，可使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建 VM。 以下示例创建两台 VM 和所需的虚拟网络组件（如果它们尚不存在）。

```azurepowershell
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
## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>在 Azure 门户中查看 IPv6 双堆栈虚拟网络
可以在 Azure 门户中查看 IPv6 双堆栈虚拟网络，如下所示：
1. 在门户的搜索栏中，输入 " *dsVnet*"。
2. 当 " **dsVnet** " 出现在搜索结果中时，请选择它。 这将启动名为*dsVnet*的双堆栈虚拟网络的 "**概述**" 页。 双堆栈虚拟网络将显示两个 Nic，它们都位于名为*dsSubnet*的双堆栈子网中。

![具有标准内部负载均衡器的 IPv6 双堆栈虚拟网络](./media/ipv6-dual-stack-standard-internal-load-balancer-powershell/ipv6-dual-stack-virtual-network.png)


> [!NOTE]
> 适用于 Azure 虚拟网络的 IPv6 可在此预览版本的只读 Azure 门户中使用。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、VM 和所有相关的资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell
Remove-AzResourceGroup -Name dsStd_ILB_RG
```

## <a name="next-steps"></a>后续步骤

本文介绍如何使用双前端 IP 配置（IPv4 和 IPv6）创建标准负载均衡器。 还创建了两个虚拟机，其中包含已添加到负载均衡器后端池的双 IP 配置（IPV4 + IPv6）的 Nic。 若要了解有关 Azure 虚拟网络中 IPv6 支持的详细信息，请参阅[什么是适用于 Azure 虚拟网络的 ipv6？](ipv6-overview.md)