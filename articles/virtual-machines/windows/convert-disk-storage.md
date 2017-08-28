---
title: "Azure 标准与高级托管磁盘存储的相互转换 | Microsoft Docs"
description: "如何使用 Azure PowerShell 实现 Azure 标准与高级托管磁盘的相互转换。"
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 9e5c73ceb0ff7d9c18c9cf7128b69e40b9796874
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---

# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure 标准与高级托管磁盘存储的相互转换

托管磁盘提供两种存储选项：[高级](../../storage/storage-premium-storage.md)（基于 SSD）和[标准](../../storage/storage-standard-storage.md)（基于 HDD）。 它可让你基于性能需求在这两个选项之间轻松切换，并保障最短停机时间。 非托管磁盘不具备此功能。 但可以轻松[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便在这两个选项之间轻松切换。

本文介绍了如何使用 Azure PowerShell 实现 Azure 标准与高级托管磁盘的相互转换。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps.md)。

## <a name="before-you-begin"></a>开始之前

* 该转换需要重启 VM，因此请在预先存在的维护时段内计划磁盘存储迁移。 
* 如果你使用非托管磁盘，请首先[转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便按照本文中的说明在两个存储选项之间切换。 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>实现 VM 所有标准与高级托管磁盘的相互转换

下方示例展示如何将 VM 的所有磁盘从标准存储切换到高级存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

```powershell
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>标准与高级托管磁盘的相互转换

对于开发/测试工作负荷，你可能想要同时具有标准磁盘和高级磁盘，以减少成本。 可通过仅将需要更佳性能的磁盘升级到高级存储来实现此目的。 下方示例展示如何将 VM 的单个磁盘在标准存储与高级存储之间相互切换。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小。

```powershell

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.OwnerId
$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)获取 VM 的只读副本。


