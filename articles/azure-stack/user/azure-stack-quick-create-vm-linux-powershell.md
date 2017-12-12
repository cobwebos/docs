---
title: "在 Azure 堆栈中使用 PowerShell 创建 Linux 虚拟机 |Microsoft 文档"
description: "使用 Azure 堆栈中的 PowerShell 创建 Linux 虚拟机。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 97847c0c2fcc4ab7d5fe8e4e5badd5809b7e2363
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-powershell-in-azure-stack"></a>在 Azure 堆栈中使用 PowerShell 创建 Linux 虚拟机 

*适用范围： Azure 堆栈集成系统*

Azure PowerShell 用于创建和管理在 Azure 堆栈从命令行或脚本中的资源。  此指南详细介绍如何使用 PowerShell 创建运行 Ubuntu server Azure 堆栈中的虚拟机。

## <a name="prerequisites"></a>必备组件 

* 请确保 Azure 堆栈运算符具有"Ubuntu Server 16.04 LTS"将图像添加到 Azure 堆栈市场。  

* Azure 堆栈需要特定版本的 Azure PowerShell 创建和管理的资源。 如果你没有配置为 Azure 堆栈的 PowerShell，登录到[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)并遵循相关步骤，为[安装](azure-stack-powershell-install.md)和[配置](azure-stack-powershell-configure-user.md)PowerShell。    

* 应在 Windows 用户配置文件的.ssh 目录中创建与名称 id_rsa.pub SSH 公钥。 有关创建 SSH 密钥的详细信息，请参阅[创建 SSH 密钥在 Windows 上的](../../virtual-machines/linux/ssh-from-windows.md)。  

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

创建存储帐户和一个存储容器来存储 Ubuntu Server 16.04 LTS 映像。

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

网络安全组使用入站和出站规则保护虚拟机。 让我们创建针对端口 3389，以允许传入远程桌面连接的入站的规则，并为端口 80 以允许传入的 web 流量的入站的规则。

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
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
创建虚拟机配置。 此配置包括部署虚拟机时使用的设置，例如虚拟机映像、大小和身份验证配置。

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize 

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred 

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
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

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

#Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine 
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

完成部署后，请与虚拟机建立 SSH 连接。 使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1) 命令返回虚拟机的公共 IP 地址。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

从使用 SSH 安装系统，使用以下命令以连接到虚拟机。 如果你正在 Windows 上，则可以使用[Putty](http://www.putty.org/)才能创建连接。

```
ssh <Public IP Address>
```

出现提示时，登录用户名为 azureuser。 如果在创建 SSH 密钥时输入了通行短语，还需要输入此通行短语。

## <a name="clean-up-resources"></a>清理资源
当不再需要可以使用[Remove-azurermresourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1)命令删除资源组，VM，以及所有相关资源：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门教程，你已部署简单的 Linux 虚拟机。 若要了解有关 Azure 堆栈的虚拟机的详细信息，继续到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。
