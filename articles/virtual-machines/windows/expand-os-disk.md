---
title: 在 Azure 中扩展 Windows VM 的 OS 驱动器 | Microsoft Docs
description: 在 Resource Manager 部署模型中，使用 Azure Powershell 增加虚拟机的 OS 驱动器大小。
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.openlocfilehash: f7ff1f2f27806c92c1ac887ce3c3343b96339745
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888943"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>如何扩展虚拟机的 OS 驱动器

在资源组中通过从 [Azure 市场](https://azure.microsoft.com/marketplace/)部署映像来创建新的虚拟机 (VM) 时，默认的 OS 驱动器通常为 127 GB（默认情况下，某些映像的 OS 磁盘大小更小）。 尽管用户可以将数据磁盘添加到 VM（数量取决于所选择的 SKU），并且我们建议将应用程序和需要大量 CPU 的工作负荷安装在这些附加的磁盘上，但客户有时候还是必须扩展 OS 驱动器以支持特定的方案，例如：

- 支持将组件安装在 OS 驱动器上的传统应用程序。
- 从本地迁移具有较大 OS 驱动器的物理电脑或虚拟机。


> [!IMPORTANT]
> 调整 Azure 虚拟机的 OS 磁盘大小会导致其重启。
>
> 扩展磁盘后，需要[扩展 OS 中的卷](#expand-the-volume-within-the-os)才能使用更大的磁盘。
> 

## <a name="resize-a-managed-disk"></a>调整托管磁盘的大小

在管理模式下打开 Powershell ISE 或 Powershell 窗口，并遵循以下步骤：

1. 在资源管理模式下登录 Microsoft Azure 帐户，并选择订阅，如下所示：
   
   ```powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. 设置资源组名称和 VM 名称，如下所示：
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 获取对 VM 的引用，如下所示：
   
   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 在调整磁盘大小之前停止 VM，如下所示：
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 获取对托管 OS 磁盘的引用。 将托管 OS 磁盘的大小设置为所需值，并更新磁盘，如下所示：
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > 新大小应该大于现有磁盘大小。 对于 OS 磁盘来说，允许的最大值为 2048 GB。 （可以扩展 VHD Blob，使之超出该大小，但 OS 只能使用空间的头 2048 GB。）
   > 
   > 
6. 更新 VM 可能需要几秒钟时间。 命令完成执行后，请重新启动 VM，如下所示：
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

这就是所有的操作！ 现在，请通过 RDP 访问 VM，打开“计算机管理”（或“磁盘管理”），并使用刚刚分配的空间扩展驱动器。

## <a name="resize-an-unmanaged-disk"></a>调整费托管磁盘的大小

在管理模式下打开 Powershell ISE 或 Powershell 窗口，并遵循以下步骤：

1. 在资源管理模式下登录 Microsoft Azure 帐户，并选择订阅，如下所示：
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. 设置资源组名称和 VM 名称，如下所示：
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 获取对 VM 的引用，如下所示：
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 在调整磁盘大小之前停止 VM，如下所示：
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 将非托管 OS 磁盘的大小设置为所需值，并更新 VM，如下所示：
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > 新大小应该大于现有磁盘大小。 对于 OS 磁盘来说，允许的最大值为 2048 GB。 （可以扩展 VHD Blob，使之超出该大小，但 OS 只能使用空间的头 2048 GB。）
   > 
   > 
   
6. 更新 VM 可能需要几秒钟时间。 命令完成执行后，请重新启动 VM，如下所示：
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>OS 磁盘的脚本

以下是同时适用于托管磁盘和非托管磁盘的引用的完整脚本：


**托管磁盘**

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

**非托管磁盘**

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>调整数据磁盘的大小

虽然本文重介绍扩展 VM 的 OS 磁盘，但该脚本也可用于扩展附加到 VM 的数据磁盘。 例如，要扩展附加到 VM 的第一个数据磁盘，请将 `StorageProfile` 的 `OSDisk` 对象替换为 `DataDisks` 数组，并使用数字索引获取对第一个附加的数据磁盘的引用，如下所示：

**托管磁盘**

```powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**非托管磁盘**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



同样，如上所示使用索引或磁盘的“名称属性”可以引用附加到 VM 的其他数据磁盘：


**托管磁盘**

```powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**非托管磁盘**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>扩展 OS 中的卷

展开 VM 的磁盘后，需要转到 OS 并展开卷以包含新的空间。 以下几种方法可用于扩展分区。 本部分介绍了如何使用 RDP 连接来连接 VM 以使用 DiskPart 展开分区。

1. 打开与 VM 的 RDP 连接。

2.  打开命令提示符并键入 diskpart

2.  在 DISKPART 提示符处，键入 `list volume`。 记下要扩展的卷。

3.  在 DISKPART 提示符处，键入 `select volume <volumenumber>`。 这将选择将扩展到同一磁盘上的连续可用空间的卷 volumenumber。

4.  在 DISKPART 提示符处，键入 `extend [size=<size>]`。 这将按大小（MB）扩展所选的卷。


##<a name="next-steps"></a>后续步骤

还可以使用 [Azure 门户](attach-managed-disk-portal.md)来附加磁盘。



