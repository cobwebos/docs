---
title: "如何在 Azure 中均衡 Windows 虚拟机负载 | Microsoft Docs"
description: "了解如何使用 Azure 负载均衡器在 3 个 Windows VM 之间创建高可用性和安全性的应用程序"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6738d88d5a0430abaf3855dbf97a618e4c83617f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>如何在 Azure 中均衡 Windows 虚拟机负载以创建高可用性应用程序
负载均衡通过将传入请求分布到多个虚拟机来提供更高级别的可用性。 本教程介绍 Azure 负载均衡器的不同组件，这些组件用于分发流量和提供高可用性。 学习如何：

> [!div class="checklist"]
> * 创建 Azure 负载均衡器
> * 创建负载均衡器运行状况探测
> * 创建负载均衡器流量规则
> * 使用自定义脚本扩展创建基本的 IIS 站点
> * 创建虚拟机并将其附加到负载均衡器
> * 查看运行中的负载均衡器
> * 从负载均衡器中添加和删除 VM

本教程需要 Azure PowerShell 模块 3.6 或更高版本。 可以运行 ` Get-Module -ListAvailable AzureRM` 来查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。


## <a name="azure-load-balancer-overview"></a>Azure 负载均衡器概述
Azure 负载均衡器是位于第 4 层（TCP、UDP）的负载均衡器，通过在正常运行的 VM 之间分发传入流量提供高可用性。 负载均衡器的运行状况探测监视每个 VM 上的给定端口，并仅将流量分发给可操作的 VM。

定义包含一个或多个公共 IP 地址的前端 IP 配置。 利用此前端 IP 配置，可通过 Internet 访问负载均衡器和应用程序。 

虚拟机使用其虚拟网络接口卡 (NIC) 连接到负载均衡器。 若要向 VM 分发流量，后端地址池需包含连接到负载均衡器的虚拟 NIC 的 IP 地址。

若要控制流量流，需为映射到 VM 的特定端口和协议定义负载均衡器规则。


## <a name="create-azure-load-balancer"></a>创建 Azure 负载均衡器
本部分详细介绍如何创建和配置负载均衡器的每个组件。 创建负载均衡器之前，需使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建资源组。 以下示例在“EastUS”位置创建名为“myResourceGroupLoadBalancer”的资源组：

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS
```

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址
若要通过 Internet 访问应用，需要负载均衡器的一个公共 IP 地址。 使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 创建一个公共 IP 地址。 以下示例在“myResourceGroupLoadBalancer”资源组中创建名为“myPublicIP”的公共 IP 地址：

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>创建负载均衡器
使用 [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) 创建一个前端 IP 地址。 以下示例创建名为“myFrontEndPool”的前端 IP 地址： 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

使用 [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) 创建一个后端地址池。 以下示例创建名为“myBackEndPool”的后端地址池：

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

现在，使用 [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) 创建负载均衡器。 以下示例使用“myPublicIP”地址创建名为“myLoadBalancer”的负载均衡器：

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location EastUS `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>创建运行状况探测器
若要允许负载均衡器监视应用的状态，可以使用运行状况探测器。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 默认情况下，在 15 秒时间间隔内发生两次连续的故障后，将从负载均衡器分布中删除 VM。 可以为应用创建基于协议或特定运行状况检查页面的运行状况探测器。 

以下示例创建一个 TCP 探测。 还可创建自定义 HTTP 探测，以便执行更精细的运行状况检查。 使用自定义 HTTP 探测时，必须创建运行状况检查页，例如 healthcheck.aspx。 探测必须为负载均衡器返回 HTTP 200 OK 响应，以保持主机处于旋转状态。

若要创建 TCP 运行状况探测，请使用 [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig)。 以下示例创建名为“myHealthProbe”的运行状况探测，用于监视每个 VM：

```powershell
Add-AzureRmLoadBalancerProbeConfig `
  -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

使用 [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) 更新负载均衡器：

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则
负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需源和目标端口。 若要确保仅正常运行的 VM 接收流量，还需定义要使用的运行状况探测。

使用 [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) 创建一个负载均衡器规则。 以下示例创建名为“myLoadBalancerRule”的负载均衡器规则并平衡端口 80 上的流量：

```powershell
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name myHealthProbe

Add-AzureRmLoadBalancerRuleConfig `
  -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

使用 [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) 更新负载均衡器：

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>配置虚拟网络
需要先创建支持的虚拟网络资源，才能部署某些 VM 和测试均衡器。 有关虚拟网络的详细信息，请参阅[管理 Azure 虚拟网络](tutorial-virtual-network.md)教程。

### <a name="create-network-resources"></a>创建网络资源
使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 创建虚拟网络。 以下示例创建具有“mySubnet”的名为“myVnet”的虚拟网络：

```powershell
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

使用 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) 创建网络安全组规则，并使用 [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) 创建网络安全组。 使用 [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) 将网络安全组添加到子网，然后使用 [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) 更新虚拟网络。 

以下示例创建一个名为“myNetworkSecurityGroup”的网络安全组规则，并将其应用于 mySubnet：

```powershell
# Create security rule config
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create the network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location EastUS `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule

# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 创建虚拟 NIC。 以下示例创建三个虚拟 NIC。 （在以下步骤中为应用创建的每个 VM 各使用一个虚拟 NIC）。 可随时创建其他虚拟 NIC 和 VM，并将其添加到负载均衡器：

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location EastUS `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>创建虚拟机
要提高应用的高可用性，请将 VM 放置在可用性集中。

使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 创建一个可用性集。 以下示例创建名为“myAvailabilitySet”的可用性集：

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location EastUS `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

使用 [New-AzureRmNetworkInterface](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```powershell
$cred = Get-Credential
```

现在，可使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 VM。 下例创建 3 个 VM：

```powershell
for ($i=1; $i -le 3; $i++)
{
  $vm = New-AzureRmVMConfig `
    -VMName myVM$i `
    -VMSize Standard_D1 `
    -AvailabilitySetId $availabilitySet.Id
  $vm = Set-AzureRmVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred `
    -ProvisionVMAgent `
    -EnableAutoUpdate
  $vm = Set-AzureRmVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  $vm = Set-AzureRmVMOSDisk `
    -VM $vm `
    -Name myOsDisk$i `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite
  $nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic$i
  $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
  New-AzureRmVM `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Location EastUS `
    -VM $vm
}
```

创建和配置所有三个 VM 需要几分钟时间。

### <a name="install-iis-with-custom-script-extension"></a>使用自定义脚本扩展安装 IIS
在有关[如何自定义 Windows 虚拟机](tutorial-automate-vm-deployment.md)的上一个教程中，已了解如何使用 Windows 的自定义脚本扩展自动执行 VM 自定义。 可使用相同的方法在 VM 上安装和配置 IIS。

使用 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 安装自定义脚本扩展。 该扩展运行 `powershell Add-WindowsFeature Web-Server` 以安装 IIS Web 服务器，并更新“Default.htm”页以显示 VM 的主机名：

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>测试负载均衡器
使用 [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) 获取负载均衡器的公共 IP 地址。 以下示例获取前面创建的“myPublicIP”的 IP 地址：

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

然后，可将公共 IP 地址输入 web 浏览器中。 随即显示网站，包括负载均衡器将流量分发到的 VM 的主机名，如下例所示：

![运行 IIS 网站](./media/tutorial-load-balancer/running-iis-website.png)

若要查看负载均衡器如何在运行应用的所有 3 个 VM 之间分配流量，可强制刷新 web 浏览器。


## <a name="add-and-remove-vms"></a>添加和删除 VM
建议对运行应用的 VM 执行维护，例如安装 OS 更新。 若要应对应用增加的流量，建议添加更多 VM。 本部分演示了如何在负载均衡器中删除或添加 VM。

### <a name="remove-a-vm-from-the-load-balancer"></a>从负载均衡器中删除 VM
使用 [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) 获取网络接口卡，然后将虚拟 NIC 的 LoadBalancerBackendAddressPools 属性设置为“$null”。 最后，更新虚拟 NIC。

```powershell
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

若要查看负载均衡器如何在运行应用的其余两个 VM 之间分发流量，可强制刷新 web 浏览器。 现在可以对 VM 执行维护，例如安装 OS 更新或执行 VM 重新启动。

### <a name="add-a-vm-to-the-load-balancer"></a>将 VM 添加到负载均衡器
执行 VM 维护后，或者如果需要扩展容量，请通过 [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) 将虚拟 NIC 的 LoadBalancerBackendAddressPools 属性设置为“BackendAddressPool”：

获取负载均衡器：

```powershell
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>后续步骤

在本教程中，已创建负载均衡器并已将 VM 附加到它。 你已了解如何：

> [!div class="checklist"]
> * 创建 Azure 负载均衡器
> * 创建负载均衡器运行状况探测
> * 创建负载均衡器流量规则
> * 使用自定义脚本扩展创建基本的 IIS 站点
> * 创建虚拟机并将其附加到负载均衡器
> * 查看运行中的负载均衡器
> * 从负载均衡器中添加和删除 VM

请转到下一教程，了解如何管理 VM 网络。

> [!div class="nextstepaction"]
> [管理 VM 和虚拟网络](./tutorial-virtual-network.md)
