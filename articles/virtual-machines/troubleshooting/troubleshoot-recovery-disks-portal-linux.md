---
title: 在 Azure 门户中使用 Linux 故障排除 VM | Microsoft Docs
description: 了解如何通过使用 Azure 门户将 OS 磁盘连接到恢复 VM 来排查 Linux 虚拟机问题
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: 2dcd2009d93fdf39d1221f2a2f5354fe68658077
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103363"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>通过使用 Azure 门户将 OS 磁盘附加到恢复 VM 来对 Linux VM 进行故障排除
如果 Linux 虚拟机 (VM) 遇到启动或磁盘错误，则可能需要对虚拟硬盘本身执行故障排除步骤。 一个常见示例是 `/etc/fstab` 中存在无效条目，使 VM 无法成功启动。 本文详细介绍如何使用 Azure 门户将虚拟硬盘连接到另一个 Linux VM 来修复所有错误，然后重新创建原始 VM。

## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 停止受影响的 VM。
1. 为 VM 的 OS 磁盘拍摄快照。
1. 从快照创建虚拟硬盘。
1. 将虚拟硬盘附加并装入到另一个 Windows VM，以便进行故障排除。
1. 连接到故障排除 VM。 编辑文件或运行任何工具以修复原始虚拟硬盘上的问题。
1. 从故障排除 VM 卸载并分离虚拟硬盘。
1. 交换 VM 的 OS 磁盘。

> [!NOTE]
> 本文不适用于包含非托管磁盘的 VM。

## <a name="determine-boot-issues"></a>确定启动问题
检查启动诊断信息和 VM 屏幕截图，确定 VM 不能正常启动的原因。 一个常见的例子是 `/etc/fstab` 中存在无效条目，或底层虚拟硬盘已删除或移动。

在门户中选择 VM，然后向下滚动到“支持 + 故障排除”部分。 单击“启动诊断”，查看从 VM 流式传输的控制台消息。 查看控制台日志，以便了解是否能够确定 VM 遇到问题的原因。 以下示例显示某个 VM 停滞在维护模式，需要人工干预：

![查看 VM 启动诊断控制台日志](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

也可以单击启动诊断日志顶部的“屏幕截图”，下载 VM 的屏幕截图。

## <a name="take-a-snapshot-of-the-os-disk"></a>拍摄 OS 磁盘快照
快照是虚拟硬盘 (VHD) 的完整只读副本。 建议在拍摄快照前完全关闭 VM, 以清除正在进行的任何过程。 若要拍摄 OS 磁盘快照, 请执行以下步骤:

1. 转到 [Azure 门户](https://portal.azure.com)。 从边栏中选择 "**虚拟机**", 并选择有问题的 VM。
1. 在左侧窗格中, 选择 "**磁盘**", 然后选择 OS 磁盘的名称。
    ![有关 OS 磁盘名称的图像](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. 在 OS 磁盘的 "**概述**" 页上, 选择 "**创建快照**"。
1. 在 OS 磁盘所在的同一位置创建快照。

## <a name="create-a-disk-from-the-snapshot"></a>从快照创建磁盘
若要从快照创建磁盘, 请执行以下步骤:

1. 从 Azure 门户中选择**Cloud Shell** 。

    ![有关打开 Cloud Shell 的图像](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 运行以下 PowerShell 命令, 从快照创建托管磁盘。 应将这些示例名称替换为相应的名称。

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. 如果命令运行成功, 你将在提供的资源组中看到新磁盘。

## <a name="attach-disk-to-another-vm"></a>将磁盘附加到另一个 VM
在后续几个步骤中，将使用另一个 VM 进行故障排除。 将磁盘附加到故障排除 VM 后, 你可以浏览和编辑磁盘的内容。 此过程允许您更正任何配置错误或者查看其他应用程序或系统日志文件。 若要将磁盘附加到另一个 VM, 请遵循以下步骤:

1. 在门户中选择资源组，并选择故障排除 VM。 依次选择“磁盘”、“编辑”，然后单击“添加数据磁盘”：

    ![在门户中附加现有磁盘](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 在“数据磁盘”列表中，选择所标识的 VM 的 OS 磁盘。 如果看不到 OS 磁盘，请确保故障排除 VM 和 OS 磁盘位于同一区域（位置）。 
3. 选择“保存”应用所做的更改。

## <a name="mount-the-attached-data-disk"></a>装载附加的数据磁盘

> [!NOTE]
> 以下示例详细说明了在 Ubuntu VM 上需要执行的步骤。 如果使用不同的 Linux 分发版（如 Red Hat Enterprise Linux 或 SUSE）日志文件位置和 `mount` 命令可能稍有不同。 请参阅具体分发版的文档，了解命令中有哪些相应的变化。

1. 使用相应的凭据通过 SSH 连接到故障排除 VM。 如果此磁盘是附加到故障排除 VM 的第一个数据磁盘，则它可能已连接到 `/dev/sdc`。 使用 `dmseg` 列出附加的磁盘：

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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>修复原始虚拟硬盘上的问题
装载现有虚拟硬盘后，可以根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
解决错误后，可从故障排除 VM 中分离现有虚拟硬盘。 在将虚拟硬盘附加到故障排除 VM 的租约释放前，不能将该虚拟硬盘用于任何其他 VM。

1. 通过 SSH 会话登录到故障排除 VM 中，卸载现有的虚拟硬盘。 首先更改出装入点的父目录：

    ```bash
    cd /
    ```

    现在卸载现有的虚拟硬盘。 以下示例卸载 `/dev/sdc1` 中的设备：

    ```bash
    sudo umount /dev/sdc1
    ```

2. 现在从 VM 中分离虚拟硬盘。 在门户中选择 VM，然后单击“磁盘”。 选择现有的虚拟硬盘，并单击“分离”：

    ![分离现有虚拟硬盘](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    等到 VM 成功分离数据磁盘，并继续操作。

## <a name="swap-the-os-disk-for-the-vm"></a>交换 VM 的 OS 磁盘

Azure 门户现在支持更改 VM 的 OS 磁盘。 为此，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。 从边栏中选择 "**虚拟机**", 并选择有问题的 VM。
1. 在左侧窗格中, 选择 "**磁盘**", 然后选择 "**交换操作系统磁盘**"。
        ![有关 Azure 门户中的交换 OS 磁盘的图像](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 选择修复的新磁盘, 然后键入 VM 的名称以确认更改。 如果在列表中看不到该磁盘, 请在从故障排除 VM 中分离磁盘后等待10到15分钟。 另外, 请确保该磁盘与 VM 位于同一位置。
1. 选择 "确定"。

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[排查 Azure VM 的 SSH 连接问题](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果在访问 VM 上运行的应用时遇到问题，请参阅 [Troubleshoot application connectivity issues on a Linux VM](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（排查 Linux VM 上的应用程序连接问题）。

有关使用 Resource Manager 的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
