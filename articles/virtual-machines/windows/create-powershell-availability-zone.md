---
title: "创建区域化 Windows VM - Azure PowerShell | Microsoft Docs"
description: "使用 Azure PowerShell 在可用性区域中创建 Windows 虚拟机"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: ada47536dbd736386a4efc76249f4ff3a1cfd527
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>使用 PowerShell 在可用性区域中创建 Windows 虚拟机

本文详细介绍如何使用 Azure PowerShell 在 Azure 可用性区域中创建运行 Windows Server 2016 的 Azure 虚拟机。 [可用性区域](../../availability-zones/az-overview.md)是 Azure 区域中物理上独立的区域。 使用可用性区域可以在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]

请确保已安装最新的 Azure PowerShell 模块。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="log-in-to-azure"></a>登录 Azure

使用 `Login-AzureRmAccount` 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Login-AzureRmAccount
```

## <a name="check-vm-sku-availability"></a>查看 VM SKU 可用性
VM 大小或 SKU 的可用性可能因地区和区域而异。 可以按 Azure 区域列出可用的 VM SKU，以便规划可用性区域的使用。 此功能可确保选择适当的 VM 大小，并跨区域获取所需的复原能力。 有关不同 VM 类型和大小的详细信息，请参阅 [VM 大小概述](sizes.md)。

可以使用 [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) 命令来查看可用的 VM SKU。 以下示例列出了 *eastus2* 区域中可用的 VM SKU：

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

输出类似于以下浓缩版示例，其中显示了每种 VM 大小都可用的可用性区域：

```powershell
ResourceType                Name  Location      Zones
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 本示例在 *eastus2* 区域中创建名为 *myResourceGroup* 的资源组。 “美国东部 2”是支持可用性区域预览版的 Azure 区域之一。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location eastus2
```

## <a name="create-networking-resources"></a>创建网络资源

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>创建虚拟网络、子网和公共 IP 地址 
这些资源用来与虚拟机建立网络连接，以及连接到 Internet。 在可用性区域（在本示例中为 *2*）中创建 IP 地址。 若要在可用性区域中创建 VM（后续步骤会介绍），请指定创建 IP 地址时所用的相同区域。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>创建网络安全组和网络安全组规则 
网络安全组使用入站和出站规则保护虚拟机。 在本例中，将为端口 3389 创建一个入站规则，该规则允许传入的远程桌面连接。 我们还需要为端口 80 创建入站规则，以允许传入的 Web 流量。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>为虚拟机创建网卡 
使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 为虚拟机创建网卡。 网卡将虚拟机连接到子网、网络安全组和公共 IP 地址。

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>创建虚拟机

创建虚拟机配置。 此配置包括部署虚拟机时使用的设置，例如虚拟机映像、大小和身份验证配置。 可用性区域预览版支持本示例中的 *Standard_DS1_v2* 大小。 此配置还指定了创建 IP 地址时所设置的可用性区域。 运行此步骤时，会提示输入凭据。 输入的值将配置为用于虚拟机的用户名和密码。

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
```

使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建虚拟机。

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="zone-for-ip-address-and-managed-disk"></a>IP 地址和托管磁盘的区域

已在与 VM 相同的可用性区域中创建了 VM 的 IP 地址资源。 此外，还在相同的可用性区域中创建了 VM 的托管磁盘资源。 可以使用 [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) 验证此结果：

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

输出显示，托管磁盘与 VM 位于相同的可用性区域中：

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```




## <a name="next-steps"></a>后续步骤

本文已介绍如何在可用性区域中创建 VM。 详细了解 Azure VM 的[区域和可用性](regions-and-availability.md)。