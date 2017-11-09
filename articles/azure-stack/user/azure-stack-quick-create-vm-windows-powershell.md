---
title: "在 Azure 堆栈使用 PowerShell 创建 Windows 虚拟机 |Microsoft 文档"
description: "使用 Azure 堆栈中的 PowerShell 创建 Windows 虚拟机。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 42e126ffefd75669d90ea3ff1d3939028f71159c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-by-using-powershell-in-azure-stack"></a>在 Azure 堆栈使用 PowerShell 创建 Windows 虚拟机

*适用范围： Azure 堆栈集成系统*

此指南详细介绍如何使用 PowerShell 在 Azure 堆栈中创建 Windows Server 2016 的虚拟机。 你可以运行如果你通过 VPN 连接从 Azure 堆栈开发工具包中，或从基于 Windows 的外部客户端此文章中所述的步骤。 

## <a name="prerequisites"></a>必备组件 

* 请确保 Azure 堆栈运算符已添加到 Azure 堆栈应用商店的"Windows Server 2016"映像。  

* Azure 堆栈需要特定版本的 Azure PowerShell 创建和管理的资源。 如果你没有配置为 Azure 堆栈的 PowerShell，请按照步骤[安装](azure-stack-powershell-install.md)和[配置](azure-stack-powershell-configure-user.md)PowerShell。    

## <a name="create-a-resource-group"></a>创建资源组

资源组是到哪些 Azure 堆栈部署和管理资源的逻辑容器。 从开发工具包或集成的 Azure 堆栈系统中，运行下面的代码块，来创建资源组。 我们已分配了本文档中的所有变量的值，你可以使用它们也将分配一个不同的值。  

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>创建存储资源 

创建存储帐户和一个存储容器来存储 Windows Server 2016 的映像。

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>创建网络资源

创建虚拟网络、子网和公共 IP 地址。 这些资源用于提供网络连接到虚拟机。  

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>创建网络安全组和网络安全组规则

网络安全组使用入站和出站规则保护虚拟机。 我们来创建针对端口 3389，以允许传入远程桌面连接的入站的规则，并为端口 80 以允许传入的 web 流量的入站的规则。

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb 
```
 
### <a name="create-a-network-card-for-the-virtual-machine"></a>为虚拟机创建网卡

网卡将虚拟机连接到子网、网络安全组和公共 IP 地址。

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id 
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建虚拟机配置。 配置包括部署虚拟机的虚拟机映像、 大小、 凭据如时使用的设置。

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize 

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential 

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine. 
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id 

#Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

到远程连接到你在上一步中创建虚拟机，你需要其公有 IP 地址。 运行以下命令以获取虚拟机的公共 IP 地址： 

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```
 
使用以下命令以创建与虚拟机的远程桌面会话。 将替换为你的虚拟机的 publicIPAddress 的 IP 地址。 出现提示时，输入的用户名和密码创建虚拟机时使用。

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>通过 PowerShell 安装 IIS

现在，已登录到 Azure VM，可以使用单行 PowerShell 安装 IIS，并启用本地防火墙规则以允许 Web 流量。 打开 PowerShell 提示符并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

IIS 已安装，并且现在已从 Internet 打开 VM 上的端口 80 - 可以使用所选的 Web 浏览器查看默认的 IIS 欢迎页。 请务必使用前面记录的 *publicIpAddress* 访问默认页面。 

![IIS 默认站点](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png) 


## <a name="delete-the-virtual-machine"></a>删除虚拟机

当不再需要使用以下命令删除包含虚拟机和其相关的资源的资源组：

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

在此快速入门中，你已部署简单的 Windows 虚拟机。 若要了解有关 Azure 堆栈的虚拟机的详细信息，继续到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。

