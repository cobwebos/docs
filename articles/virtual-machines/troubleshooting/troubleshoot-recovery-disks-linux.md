---
title: 将 Linux 故障排除 VM 与 Azure CLI 配合使用 | Microsoft Docs
description: 了解如何通过使用 Azure CLI 将 OS 磁盘连接到恢复 VM 来排查 Linux VM 问题
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 49ee83e451e9d555a7fe5fca57bc58d6616334da
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641054"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>通过使用 Azure CLI 将 OS 磁盘附加到恢复 VM 来对 Linux VM 进行故障排除
如果 Linux 虚拟机 (VM) 遇到启动或磁盘错误，则可能需要对虚拟硬盘本身执行故障排除步骤。 一个常见示例是 `/etc/fstab` 中存在无效条目，使 VM 无法成功启动。 本文详细介绍如何使用 Azure CLI 将虚拟硬盘连接到另一个 Linux VM，以修复任何错误，然后重新创建原始 VM。 

## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 停止受影响的 VM。
1. 从 VM 的 OS 磁盘拍摄快照。
1. 从 OS 磁盘快照创建磁盘。
1. 出于故障排除目的, 将新的 OS 磁盘附加并装载到另一个 Linux VM。
1. 连接到故障排除 VM。 编辑文件或运行任何工具以修复新 OS 磁盘上的问题。
1. 从故障排除 VM 中卸载并分离新的 OS 磁盘。
1. 更改受影响 VM 的 OS 磁盘。

若要执行这些故障排除步骤，需要安装最新的 [Azure CLI](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。

> [!Important]
> 本文中的脚本仅适用于使用[托管磁盘](../linux/managed-disks-overview.md)的 VM。 

在以下示例中，请将参数名称替换成自己的值。 示例参数名称包括 `myResourceGroup` 和 `myVM`。

## <a name="determine-boot-issues"></a>确定启动问题
检查串行输出以确定 VM 不能正常启动的原因。 一个常见示例是 `/etc/fstab`中存在无效条目，或底层虚拟硬盘已删除或移动。

使用 [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics) 获取启动日志。 以下示例从名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 获取串行输出：

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

检查串行输出，以确定 VM 无法启动的原因。 如果串行输出未提供任何指示，则在将虚拟硬盘连接到故障排除 VM 后，可能需要查看 `/var/log` 中的日志文件。

## <a name="stop-the-vm"></a>停止 VM

以下示例在名为 `myResourceGroup` 的资源组中停止名为 `myVM` 的 VM：

```powershell
Stop-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

等到 VM 删除完成，然后继续下一步。

## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>从 VM 的 OS 磁盘创建快照

快照是 VHD 的完整只读副本。 不能将其附加到 VM。 下一步骤将从此快照创建磁盘。 以下示例从 VM“myVM”的 OS 磁盘创建名为 `mySnapshot` 的快照。 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```
## <a name="create-a-disk-from-the-snapshot"></a>从快照创建磁盘

此脚本从名为 `mysnapshot` 的快照创建名为 `newOSDisk` 的托管磁盘。  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzSubscription -SubscriptionId $SubscriptionId

#Provide the name of your resource group
$resourceGroupName ='myResourceGroup'

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshotName = 'mySnapshot' 

#Provide the name of the Managed Disk
$diskName = 'newOSDisk'

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '128'

#Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
$storageType = 'StandardLRS'

#Provide the Azure region (e.g. westus) where Managed Disks will be located.
#This location should be same as the snapshot location
#Get all the Azure location using command below:
#Get-AzLocation
$location = 'eastus'

$snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
现在，可以创建原始 OS 磁盘的副本。 可以将此新磁盘安装到另一个 Windows VM, 以便进行故障排除。

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>将新的虚拟硬盘附加到另一个 VM
在后续几个步骤中，将使用另一个 VM 进行故障排除。 将磁盘附加到此故障排除 VM, 以浏览和编辑磁盘的内容。 例如，此过程允许用户更正任何配置错误或者查看其他应用程序或系统日志文件。 选择或创建另一个 VM 用于故障排除。

使用 [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk) 附加现有的虚拟硬盘。 附加现有的虚拟硬盘时，请指定在前面的 `az vm show` 命令中获取的磁盘 URI。 以下示例将现有的虚拟硬盘附加到名为 `myResourceGroup` 的资源组中名为 `myVMRecovery` 的故障排除 VM：

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```
## <a name="mount-the-attached-data-disk"></a>装载附加的数据磁盘

> [!NOTE]
> 以下示例详细说明了在 Ubuntu VM 上需要执行的步骤。 如果使用不同的 Linux 分发版（如 Red Hat Enterprise Linux 或 SUSE）日志文件位置和 `mount` 命令可能稍有不同。 请参阅具体分发版的文档，了解命令中有哪些相应的变化。

1. 使用适当的凭据通过 SSH 登录到故障排除 VM。 如果此磁盘是附加到故障排除 VM 的第一个数据磁盘，则此磁盘可能已连接到 `/dev/sdc`。 使用 `dmseg` 查看附加的磁盘：

    ```bash
    dmesg | grep SCSI
    ```

    输出类似于以下示例：

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    在前面的示例中，OS 磁盘位于 `/dev/sda`，为每个 VM 提供的临时磁盘位于 `/dev/sdb`。 如果有多个数据磁盘，它们应位于 `/dev/sdd`、`/dev/sde`，依次类推。

2. 创建一个目录来装载现有的虚拟硬盘。 以下示例创建一个名为 `troubleshootingdisk` 的目录：

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 如果现有的虚拟硬盘上有多个分区，则装载所需的分区。 以下示例在 `/dev/sdc1` 中装载第一个主分区：

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 最佳做法是使用虚拟硬盘的全局唯一标识符 (UUID) 装载 Azure 中 VM 上的数据磁盘。 对于此简短的故障排除方案，不必要使用 UUID 装载虚拟硬盘。 但是，在正常使用时，编辑 `/etc/fstab` 以使用设备名称（而不是 UUID）装载虚拟硬盘可能会导致 VM 无法启动。


## <a name="fix-issues-on-the-new-os-disk"></a>解决新 OS 磁盘上的问题
装载现有虚拟硬盘后，可以根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。


## <a name="unmount-and-detach-the-new-os-disk"></a>卸载并分离新的 OS 磁盘
解决错误后，可从故障排除 VM 中卸载并分离现有虚拟硬盘。 在将虚拟硬盘附加到故障排除 VM 的租约释放前，不能将该虚拟硬盘用于任何其他 VM。

1. 通过 SSH 会话登录到故障排除 VM 中，卸载现有的虚拟硬盘。 首先更改出装入点的父目录：

    ```bash
    cd /
    ```

    现在卸载现有的虚拟硬盘。 以下示例卸载 `/dev/sdc1` 中的设备：

    ```bash
    sudo umount /dev/sdc1
    ```

2. 现在从 VM 中分离虚拟硬盘。 退出故障排除 VM 的 SSH 会话。 使用 [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk) 列出附加到故障排除 VM 的数据磁盘。 以下示例列出附加到名为 `myResourceGroup` 的资源组中名为 `myVMRecovery` 的 VM 的数据磁盘：

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    记下现有虚拟硬盘的名称。 例如，URI 为 **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** 的磁盘的名称为 **myVHD**。 

    使用 [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk) 从 VM 分离数据磁盘。 以下示例将名为 `myVHD` 的磁盘从 `myResourceGroup` 资源组中名为 `myVMRecovery` 的 VM 中分离：

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>更改受影响 VM 的 OS 磁盘

可以使用 Azure PowerShell 来交换 OS 磁盘。 无需删除和重新创建 VM。

此示例停止名为 `myVM` 的 VM，并将名为 `newOSDisk` 的磁盘分配为新的 OS 磁盘。 

```powershell
# Get the VM 
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name  -sto

# Update the VM with the new OS disk. Possible values of StorageAccountType include: 'Standard_LRS' and 'Premium_LRS'
Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -StorageAccountType <Type of the storage account >

# Start the VM
Start-AzVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[排查 Azure VM 的 SSH 连接问题](troubleshoot-ssh-connection.md)。 如果在访问 VM 上运行的应用时遇到问题，请参阅[排查 Linux VM 上的应用程序连接问题](troubleshoot-app-connection.md)。

