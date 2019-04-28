---
title: 转换 Azure 托管磁盘存储的标准到高级或标准到高级版 |Microsoft Docs
description: 如何使用 Azure PowerShell 将 Azure 托管磁盘从标准类型转换为高级类型，或者从高级类型转换为标准类型。
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: abd893c68f2e9cac713e09dd0bdafb7f277ae889
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766095"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新托管磁盘的存储类型

有四个选项适用于 Azure 托管磁盘：Azure 超高磁盘存储、 高级 SSD、 标准 SSD 和标准 HDD。 可以根据性能需求在这些存储类型之间切换，切换过程只会造成极短暂的停机。 非托管磁盘不支持此功能。 但是，可以轻松[将非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，然后即可切换磁盘类型。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

* 由于转换需要重启虚拟机 (VM)，因此请在预先存在的维护时段内计划磁盘存储迁移。
* 对于非托管磁盘，请先[将其转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便可以在存储选项之间切换。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>将 VM 的所有托管磁盘在高级类型与标准类型之间切换

此示例演示如何将 VM 的所有磁盘从标准存储转换为高级存储，或者从高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小：

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在标准类型与高级类型之间切换单个托管磁盘

对于开发/测试工作负荷，可以混合使用标准磁盘和高级磁盘来降低成本。 可以选择仅升级需要更高性能的磁盘。 此示例演示如何将单个 VM 磁盘从标准存储转换为高级存储，或者从高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还展示了如何切换到支持高级存储的大小：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>在 Azure 门户中将托管磁盘从标准类型转换为高级类型

执行以下步骤:

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在门户上的“虚拟机”列表中选择 VM。
3. 如果该 VM 未停止，请在 VM“概述”窗格的顶部选择“停止”，然后等待该 VM 停止。
3. 在 VM 对应的窗格中，从菜单中选择“磁盘”。
4. 选择要转换的磁盘。
5. 在菜单中选择“配置”。
6. 将“帐户类型”从“标准 HDD”更改为“高级 SSD”。
7. 单击“保存”并关闭磁盘窗格。

磁盘类型转换会瞬间完成。 转换后，可以重启 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在标准 HDD 与标准 SSD 之间切换托管磁盘 

此示例演示如何将单个 VM 磁盘从标准 HDD 转换为标准 SSD，或者从标准 SSD 转换为标准 HDD：

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)创建 VM 的只读副本。
