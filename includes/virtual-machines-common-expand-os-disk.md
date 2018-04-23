## <a name="overview"></a>概述
在资源组中通过从 [Azure Marketplace](https://azure.microsoft.com/marketplace/) 部署映像来创建新的虚拟机 (VM) 时，默认的 OS 驱动器通常为 127 GB（默认情况下，某些映像的 OS 磁盘大小更小）。 尽管用户可以将数据磁盘添加到 VM（数量取决于所选择的 SKU），并且我们建议将应用程序和需要大量 CPU 的工作负荷安装在这些附加的磁盘上，但客户有时候还是必须扩展 OS 驱动器以支持特定的方案，例如：

1. 支持将组件安装在 OS 驱动器上的传统应用程序。
2. 从本地迁移具有较大 OS 驱动器的物理电脑或虚拟机。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：Resource Manager 和经典。 本文介绍如何使用 Resource Manager 模型。 Microsoft 建议大多数新部署使用资源管理器模型。
> 
> 
> [!WARNING]
> 调整 Azure 虚拟机的 OS 磁盘大小会导致其重启。
>

## <a name="resize-the-os-drive"></a>调整 OS 驱动器的大小
在本文中，我们将使用 [Azure Powershell](/powershell/azureps-cmdlets-docs) 的 Resource Manager 模块，完成调整 OS 驱动器大小的任务。 我们将演示如何为非托管磁盘和托管磁盘调整 OS 驱动器大小，因为调整磁盘大小的方法因这两种磁盘类型而异。

### <a name="for-resizing-unmanaged-disks"></a>若要调整非托管磁盘大小：

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
5. 接下来就是我们期待已久的时刻！ 将非托管 OS 磁盘的大小设置为所需值，并更新 VM，如下所示：
   
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

### <a name="for-resizing-managed-disks"></a>若要调整托管磁盘大小：

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

## <a name="summary"></a>摘要
在本文中，我们已使用 Powershell 的 Azure 资源管理器模块扩展 IaaS 虚拟机的 OS 驱动器。 下面重新生成了同时适用于非托管磁盘和托管磁盘的完整脚本，供你参考：

非托管磁盘：

```Powershell
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
托管磁盘：

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

## <a name="next-steps"></a>后续步骤
虽然在本文中，我们着重介绍扩展 VM 的非托管/托管 OS 磁盘，但开发的脚本也可用于扩展附加到 VM 的数据磁盘。 例如，要扩展附加到 VM 的第一个数据磁盘，请将 ```StorageProfile``` 的 ```OSDisk``` 对象替换为 ```DataDisks``` 数组，并使用数字索引获取对第一个附加的数据磁盘的引用，如下所示：

非托管磁盘：
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
托管磁盘：
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

同样，用户可以使用如上所示的索引，或如下所示的磁盘 ```Name``` 属性，引用附加到 VM 的其他数据磁盘：

非托管磁盘：
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
托管磁盘：
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

如果想要了解如何将磁盘附加到 Azure 资源管理器 VM，请参阅[此文](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

