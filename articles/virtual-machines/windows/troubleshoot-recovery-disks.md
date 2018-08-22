---
title: 将 Windows 故障排除 VM 与 Azure PowerShell 配合使用 | Microsoft 文档
description: 了解如何使用 Azure PowerShell 将 OS 磁盘连接到恢复 VM，以便排查 Azure 中的 Windows VM 问题。
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 9845476e23396eecc4149f3e856c40b0f80f13cb
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004760"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>通过使用 Azure PowerShell 将 OS 磁盘附加到恢复 VM 来对 Windows VM 进行故障排除
如果 Windows 虚拟机 (VM) 在 Azure 中遇到启动或磁盘错误，可能需要对磁盘本身执行故障排除步骤。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文详细介绍如何使用 Azure PowerShell 将磁盘连接到另一个 Windows VM 来修复所有错误，然后修复原始 VM。 

> [!Important]
> 本文中的脚本仅适用于使用[托管磁盘](managed-disks-overview.md)的 VM。 


## <a name="recovery-process-overview"></a>恢复过程概述
现在，可以使用 Azure PowerShell 来更改 VM 的 OS 磁盘， 而不再需要删除并重新创建 VM。

故障排除过程如下：

1. 停止受影响的 VM。
2. 从 VM 的 OS 磁盘创建快照。
3. 从 OS 磁盘快照创建磁盘。
4. 将磁盘作为数据磁盘附加到恢复 VM。
5. 连接到恢复 VM。 编辑文件或运行任何工具，以修复复制的 OS 磁盘上的问题。
6. 从恢复 VM 卸载并分离磁盘。
7. 更改受影响 VM 的 OS 磁盘。

确保已安装[最新 Azure PowerShell](/powershell/azure/overview) 并登录到订阅：

```powershell
Connect-AzureRmAccount
```

在以下示例中，请将参数名称替换为自己的值。 

## <a name="determine-boot-issues"></a>确定启动问题
用户可以通过查看 Azure 中 VM 的屏幕快照来排查启动问题。 此屏幕快照有助于确定为何 VM 无法启动。 以下示例从名为 `myResourceGroup` 的资源组中名为 `myVM` 的 Windows VM 获取屏幕快照：

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

检查屏幕快照，确定 VM 无法启动的原因。 请注意所提供的任何具体错误消息或错误代码。

## <a name="stop-the-vm"></a>停止 VM

以下示例在名为 `myResourceGroup` 的资源组中停止名为 `myVM` 的 VM：

```powershell
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

等到 VM 删除完成，然后继续下一步。


## <a name="create-a-snapshot-from-the-os-disk-of-the-vm"></a>从 VM 的 OS 磁盘创建快照

以下示例从 VM“myVM”的 OS 磁盘创建名为 `mySnapshot` 的快照。 

```powershell
$resourceGroupName = 'myResourceGroup' 
$location = 'eastus' 
$vmName = 'myVM'
$snapshotName = 'mySnapshot'  

#Get the VM
$vm = get-azurermvm `
-ResourceGroupName $resourceGroupName `
-Name $vmName

#Create the snapshot configuration for the OS disk
$snapshot =  New-AzureRmSnapshotConfig `
-SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
-Location $location `
-CreateOption copy

#Take the snapshot
New-AzureRmSnapshot `
   -Snapshot $snapshot `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName 
```

快照是 VHD 的完整只读副本。 不能将其附加到 VM。 下一步骤将从此快照创建磁盘。

## <a name="create-a-disk-from-the-snapshot"></a>从快照创建磁盘

此脚本从名为 `mysnapshot` 的快照创建名为 `newOSDisk` 的托管磁盘。  

```powershell
#Set the context to the subscription Id where Managed Disk will be created
#You can skip this step if the subscription is already selected

$subscriptionId = 'yourSubscriptionId'

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
#Get-AzureRmLocation
$location = 'eastus'

$snapshot = Get-AzureRmSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
 
$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
 
New-AzureRmDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
```
现在，可以创建原始 OS 磁盘的副本。 可将此磁盘装载到另一个 Windows VM，以进行故障排除。

## <a name="attach-the-disk-to-another-windows-vm-for-troubleshooting"></a>将磁盘附加到另一个 Windows VM 以进行故障排除

现在，我们将原始 OS 磁盘的副本作为数据磁盘附加到 VM。 执行此过程可以更正配置错误，或者查看磁盘中的其他应用程序或系统日志文件。 以下示例将名为 `newOSDisk` 的磁盘附加到名为 `RecoveryVM` 的 VM。

> [!NOTE]
> 若要附加磁盘，原始 OS 磁盘的副本和恢复 VM 必须位于同一位置。

```powershell
$rgName = "myResourceGroup"
$vmName = "RecoveryVM"
$location = "eastus" 
$dataDiskName = "newOSDisk"
$disk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="connect-to-the-recovery-vm-and-fix-issues-on-the-attached-disk"></a>连接到恢复 VM，并修复所附加的磁盘上的问题

1. 使用相应的凭据通过 RDP 连接到恢复 VM。 以下示例为名为 `myResourceGroup` 的资源组中名为 `RecoveryVM` 的 VM 下载 RDP 连接文件，并将其下载到 `C:\Users\ops\Documents`。

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "RecoveryVM" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. 系统应会自动检测并附加数据磁盘。 查看已附加卷的列表以确定驱动器号，如下所示：

    ```powershell
    Get-Disk
    ```

    以下示例输出显示该磁盘连接了磁盘 **2**。 （也可使用 `Get-Volume` 查看驱动器号）：

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        newOSDisk                                  Healthy             Online       127 GB MBR
    ```

装载原始 OS 磁盘的副本后，可根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。

## <a name="unmount-and-detach-original-os-disk"></a>卸载并分离原始 OS 磁盘
解决错误后，可从恢复 VM 中卸载并分离现有磁盘。 在将磁盘附加到恢复 VM 的租约释放之前，不能将该磁盘用于其他任何 VM。

1. 从 RDP 会话卸载恢复 VM 上的数据磁盘。 需要使用前面的 `Get-Disk` cmdlet 中的磁盘编号。 然后，使用 `Set-Disk` 将磁盘设置为脱机：

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    再次使用 `Get-Disk` 确认磁盘现已设置为脱机。 下面的示例输出显示磁盘现已设置为脱机：

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. 退出 RDP 会话。 在 Azure PowerShell 会话中，从名为“RecoveryVM”的 VM 中删除名为 `newOSDisk` 的磁盘。

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "RecoveryVM"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "newOSDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>更改受影响 VM 的 OS 磁盘

可以使用 Azure PowerShell 来交换 OS 磁盘。 无需删除和重新创建 VM。

此示例停止名为 `myVM` 的 VM，并将名为 `newOSDisk` 的磁盘分配为新的 OS 磁盘。 

```powershell
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup
```

## <a name="verify-and-enable-boot-diagnostics"></a>验证和启用启动诊断

以下示例在名为 `myResourceGroup` 的资源组中名为 `myVMDeployed` 的 VM 上启用诊断扩展：

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[对 Azure VM 的 RDP 连接进行故障排除](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[对 Windows VM 上的应用程序连接问题进行故障排除](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
