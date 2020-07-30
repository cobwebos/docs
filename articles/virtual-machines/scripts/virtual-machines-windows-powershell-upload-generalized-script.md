---
title: 将通用 VHD 上传到 Azure PowerShell 脚本示例
description: PowerShell 示例脚本，用于将通用化 VHD 上传到 Azure 并使用 Resource Manager 部署模型和托管磁盘创建新的 VM。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 633db386804a59ca7e315135c1069b9ca278183d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082267"
---
# <a name="sample-script-to-upload-a-vhd-to-azure-and-create-a-new-vm"></a>将 VHD 上传到 Azure 并创建新的 VM 的示例脚本

此脚本从通用化 VM 获取本地 .vhd 文件，将其上传到 Azure，创建托管磁盘映像并用以创建新的 VM。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>示例脚本

```powershell
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'EastUS'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$containername = 'mycontainer'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vmName = 'myVM'
$imageName = 'myImage'
$vhdName = 'myUploadedVhd.vhd'
$diskSizeGB = '128'
$subnetName = 'mySubnet'
$vnetName = 'myVnet'
$ipName = 'myPip'
$nicName = 'myNic'
$nsgName = 'myNsg'
$ruleName = 'myRdpRule'
$computerName = 'myComputerName'
$vmSize = 'Standard_DS1_v2'

# Get the username and password to be used for the administrators account on the VM. 
# This is used when connecting to the VM using RDP.

$cred = Get-Credential

# Upload the VHD
New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
    -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ('https://' + $storageaccount + '.blob.core.windows.net/' + $containername + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath

# Note: Uploading the VHD may take awhile!

# Create a managed image from the uploaded VHD 
$imageConfig = New-AzImageConfig -Location $location
$imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized `
    -BlobUri $urlOfUploadedImageVhd
$image = New-AzImage -ImageName $imageName -ResourceGroupName $resourceGroup -Image $imageConfig
 
# Create the networking resources
$singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
$pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $resourceGroup -Location $location `
    -AllocationMethod Dynamic
$rdpRule = New-AzNetworkSecurityRuleConfig -Name $ruleName -Description 'Allow RDP' -Access Allow `
    -Protocol Tcp -Direction Inbound -Priority 110 -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
$nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $resourceGroup -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
$vnet = Get-AzVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName

# Start building the VM configuration
$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize

# Set the VM image as source image for the new VM
$vm = Set-AzVMSourceImage -VM $vm -Id $image.Id

# Finish the VM configuration and add the NIC.
$vm = Set-AzVMOSDisk -VM $vm  -DiskSizeInGB $diskSizeGB -CreateOption FromImage -Caching ReadWrite
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

# Create the VM
New-AzVM -VM $vm -ResourceGroupName $resourceGroup -Location $location

# Verify that the VM was created
$vmList = Get-AzVM -ResourceGroupName $resourceGroup
$vmList.Name


```


<!-- 
[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-iis/create-windows-vm-iis.ps1 "Create VM IIS")] -->

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令                                                                                                             | 注释                                                                                                                                                                                |
|---------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)                           | 创建用于存储所有资源的资源组。                                                                                                                          |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)                         | 创建存储帐户。                                                                                                                                                           |
| [Add-AzVhd](/powershell/module/az.compute/add-azvhd)                                               | 将虚拟硬盘从本地虚拟机上传到 Azure 云存储帐户中的 blob。                                                                       |
| [New-AzImageConfig](/powershell/module/az.compute/new-azimageconfig)                               | 创建一个可配置的映像对象。                                                                                                                                                 |
| [Set-AzImageOsDisk](/powershell/module/az.compute/set-azimageosdisk)                               | 设置映像对象的操作系统磁盘属性。                                                                                                                        |
| [New-AzImage](/powershell/module/az.compute/new-azimage)                                           | 创建新映像。                                                                                                                                                                 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 创建子网配置。 在虚拟网络创建过程中会使用此配置。                                                                                |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)                         | 创建虚拟网络。                                                                                                                                                           |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)                       | 创建公共 IP 地址。                                                                                                                                                         |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)                     | 创建网络接口。                                                                                                                                                         |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)   | 创建网络安全组规则配置。 创建 NSG 时会使用此配置创建 NSG 规则。                                                       |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)             | 创建网络安全组。                                                                                                                                                    |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)                         | 获取资源组中的虚拟网络。                                                                                                                                          |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)                                     | 创建 VM 配置。 此配置包括 VM 名称、操作系统和管理凭据等信息。 在创建 VM 期间使用此配置。 |
| [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)                           | 指定虚拟机的映像。                                                                                                                                            |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)                                     | 设置虚拟机的操作系统磁盘属性。                                                                                                                      |
| [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)                   | 设置虚拟机的操作系统磁盘属性。                                                                                                                      |
| [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)                 | 将网络接口添加到虚拟机。                                                                                                                                       |
| [New-AzVM](/powershell/module/az.compute/new-azvm)                                                 | 创建虚拟机。                                                                                                                                                            |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)                     | 删除资源组及其中包含的所有资源。                                                                                                                         |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure Windows VM 文档](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 PowerShell 脚本示例。
