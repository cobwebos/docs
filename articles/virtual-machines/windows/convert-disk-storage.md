---
title: 转换 Azure 托管磁盘存储的标准到高级或标准到高级版 |Microsoft Docs
description: 如何将 Azure 托管磁盘从标准到高级或标准到高级版使用 Azure PowerShell。
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: f97140ffeed9115a0308215ea082baee611501fb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009886"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>更新托管磁盘的存储类型

有四个选项适用于 Azure 托管磁盘：Azure 超高磁盘存储、 高级 SSD、 标准 SSD 和标准 HDD。 您可以根据性能需求很少停机的情况下这些存储类型之间进行切换。 非托管磁盘不支持此功能。 但您可以轻松地[将非托管的磁盘转换为托管磁盘](convert-unmanaged-to-managed-disks.md)能够磁盘类型之间切换。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>必备组件

* 由于转换需要重启虚拟机 (VM)，您应计划预先存在的维护时段内的磁盘存储迁移。
* 如果您的磁盘不受管理，首先[将其转换为托管磁盘](convert-unmanaged-to-managed-disks.md)以便可以存储选项之间切换。

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>切换高级和标准版之间将 VM 的所有托管的磁盘

此示例演示如何将转换的所有虚拟机的磁盘从标准到高级存储或从 premium 升级到标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还切换到了支持高级存储的大小：

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

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>切换标准和高级版之间的各个托管的磁盘

针对开发/测试工作负荷，你可能想混合使用标准和高级磁盘，来降低成本。 您可以选择升级仅需要更好的性能这些磁盘。 此示例演示如何将单个 VM 磁盘从标准到高级存储或从 premium 升级到标准存储。 若要使用高级托管磁盘，VM 必须使用支持高级存储的 [VM 大小](sizes.md)。 此示例还演示如何切换到了支持高级存储的大小：

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

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>将托管的磁盘从标准转换为高级，在 Azure 门户中

执行以下步骤:

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从列表中选择的 VM**虚拟机**在门户中。
3. 如果 VM 未停止，则选择**停止**VM 顶部**概述**窗格中，并等待要停止的 VM。
3. 在 vm 窗格中选择**磁盘**菜单中。
4. 选择你想要转换的磁盘。
5. 选择**配置**菜单中。
6. 更改**帐户类型**从**标准 HDD**到**高级 SSD**。
7. 单击**保存**，并关闭磁盘窗格。

磁盘类型转换是瞬间完成的。 在转换后，可以重新启动 VM。

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>切换标准 HDD 和 SSD 标准之间的托管的磁盘 

此示例演示如何将单个 VM 磁盘从标准 hdd 升级到标准的 SSD，或者到标准 HDD 标准 SSD:

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
