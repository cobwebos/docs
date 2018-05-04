---
title: 在 Azure Stack 中使用 PowerShell 创建 Linux 虚拟机 | Microsoft Docs
description: 在 Azure Stack 中使用 PowerShell 创建 Linux 虚拟机。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 86597defad7c76d41065270030a4c77ee901b014
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>快速入门： Azure 堆栈中使用 PowerShell 创建 Linux 服务器虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以通过 Azure 堆栈 PowerShell 创建 Ubuntu Server 16.04 LTS 虚拟机。 按照这篇文章创建和使用虚拟机中的步骤。  这篇文章还为你提供的步骤：

* 连接到远程客户端的虚拟机。
* 清理未使用的资源。

## <a name="prerequisites"></a>必备组件

* **Azure Stack Marketplace 中的 Linux 映像**

   默认情况下，Azure Stack Marketplace 不包含 Linux 映像。 获取要提供的 Azure 堆栈运算符**Ubuntu Server 16.04 LTS**所需的映像。 运算符可以使用中所述的步骤[从 Azure 应用商店项下载到 Azure 堆栈](../azure-stack-download-azure-marketplace-item.md)文章。

* Azure 堆栈需要特定版本的 Azure PowerShell 创建和管理资源。 如果尚未针对 Azure Stack 配置 PowerShell，请登录到[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或登录到基于 Windows 的外部客户端（如果[通过 VPN 建立了连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)），遵循相应的步骤[安装](azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-user.md) PowerShell。

* 保存你的 Windows 用户配置文件的.ssh 目录中名称 id_rsa.pub 公共 SSH 密钥。 有关创建 SSH 密钥的详细信息，请参阅[创建 SSH 密钥在 Windows 上的](../../virtual-machines/linux/ssh-from-windows.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是你可以在其中部署和管理 Azure 堆栈资源的逻辑容器。 在开发工具包或 Azure Stack 集成系统中，运行以下代码块创建资源组。 本文档中的所有变量分配值，你可以使用这些值或指定新值。

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>创建存储资源

创建存储帐户，然后创建 Ubuntu Server 16.04 LTS 映像的存储容器。

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

创建虚拟网络、子网和公共 IP 地址。 这些资源用来与虚拟机建立网络连接。

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

网络安全组使用入站和出站规则保护虚拟机。 创建针对端口 3389，以允许传入远程桌面连接的入站的规则，并为端口 80 以允许传入的 web 流量的入站的规则。

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

创建虚拟机配置。 此配置操作包括将虚拟机部署时使用的设置。 例如： 用户凭据、 大小和虚拟机映像。

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

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

部署虚拟机后，配置虚拟机建立 SSH 连接。 使用 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1) 命令返回虚拟机的公共 IP 地址。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

从使用安装的 SSH 客户端系统，使用以下命令以连接到虚拟机。 如果在 Windows 上操作，可以使用 [Putty](http://www.putty.org/) 创建连接。

```
ssh <Public IP Address>
```

出现提示时，输入 azureuser 作为登录用户。 如果你在创建 SSH 密钥时，你会使用通行短语，你将需要提供的通行短语。

## <a name="clean-up-resources"></a>清理资源

清理不不再需要的资源。 你可以使用[Remove-azurermresourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1)命令，以删除这些资源。 若要删除资源组及其所有资源，请运行以下命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门教程，你将部署基本的 Linux 服务器虚拟机。 若要了解有关 Azure 堆栈的虚拟机的详细信息，请转到[Azure 堆栈中的虚拟机的注意事项](azure-stack-vm-considerations.md)。
