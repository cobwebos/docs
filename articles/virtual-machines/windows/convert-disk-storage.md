---
title: 在标准和高级 SSD 之间转换托管磁盘存储
description: 如何使用 Azure PowerShell 将标准版或高级版中的 Azure 托管磁盘转换为标准版。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e339f0c7ca0807eec3e160eeb3464044c2ef29ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720939"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新托管磁盘的存储类型

有四种磁盘类型的 Azure 托管磁盘： Azure ultra Ssd （预览版）、高级 SSD、标准 SSD 和标准 HDD。 你可以根据性能需求在三个 GA 磁盘类型（高级 SSD、标准 SSD 和标准 HDD）之间进行切换。 你还不能从或切换到 ultra SSD，你必须部署一个新的。

非托管磁盘不支持此功能。 但可以轻松地[将非托管磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便在磁盘类型之间切换。

 

## <a name="prerequisites"></a>必备条件

* 由于转换需要重新启动虚拟机（VM），因此应在预先存在的维护时段内计划磁盘存储的迁移。
* 如果磁盘未受管理，请首先[将其转换为托管磁盘](convert-unmanaged-to-managed-disks.md)，以便可以在存储选项之间进行切换。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>在高级和标准版之间切换 VM 的所有托管磁盘

此示例演示如何将 VM 的所有磁盘从标准存储或高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小：

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
        $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
        $disk | Update-AzDisk
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>在标准和高级之间切换各个托管磁盘

对于开发/测试工作负荷，可能需要混合使用标准磁盘和高级磁盘，以减少成本。 你可以选择仅升级那些需要更好性能的磁盘。 此示例显示了如何将单个 VM 磁盘从标准存储或高级存储转换为标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还演示如何切换到支持高级存储的大小：

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>将 Azure 门户中的托管磁盘转换为标准版

执行以下步骤:

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 从门户中的**虚拟机**列表中选择 VM。
3. 如果未停止 VM，请选择 "VM**概述**" 窗格顶部的 "**停止**"，然后等待 VM 停止。
3. 在 VM 的窗格中，从菜单中选择 "**磁盘**"。
4. 选择要转换的磁盘。
5. 从菜单中选择 "**配置**"。
6. 将**帐户类型**从**标准 HDD**更改为**高级 SSD**。
7. 单击 "**保存**"，然后关闭 "磁盘" 窗格。

磁盘类型转换是即时的。 转换后，可以启动 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>在标准 HDD 和标准 SSD 之间切换托管磁盘 

此示例演示如何将单个 VM 磁盘从标准 HDD 转换为标准 SSD 或从标准 SSD 转换为标准 HDD：

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
$disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new($storageType)
$disk | Update-AzDisk

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>后续步骤

使用[快照](snapshot-copy-managed-disk.md)创建 VM 的只读副本。
