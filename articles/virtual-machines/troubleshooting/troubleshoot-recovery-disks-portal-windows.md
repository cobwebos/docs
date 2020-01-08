---
title: 在 Azure 门户中使用 Windows 故障排除 VM | Microsoft Docs
description: 了解如何通过使用 Azure 门户将 OS 磁盘连接到恢复 VM，对 Azure 中的 Windows 虚拟机问题进行故障排除
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374550"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>通过使用 Azure 门户将 OS 磁盘附加到恢复 VM，对 Windows VM 进行故障排除
如果 Windows 虚拟机 (VM) 在 Azure 中遇到启动或磁盘错误，可能需要对虚拟硬盘本身执行故障排除步骤。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文详细介绍如何使用 Azure 门户将虚拟硬盘连接到另一个 Windows VM 来修复所有错误，然后重新创建原始 VM。 

## <a name="recovery-process-overview"></a>恢复过程概述
故障排除过程如下：

1. 停止受影响的 VM。
1. 为 VM 的 OS 磁盘创建快照。
1. 从快照创建虚拟硬盘。
1. 将虚拟硬盘附加并装入到另一个 Windows VM，以便进行故障排除。
1. 连接到故障排除 VM。 编辑文件或运行任何工具以修复原始虚拟硬盘上的问题。
1. 从故障排除 VM 卸载并分离虚拟硬盘。
1. 交换 VM 的 OS 磁盘。

> [!NOTE]
> 本文不适用于包含非托管磁盘的 VM。

## <a name="take-a-snapshot-of-the-os-disk"></a>拍摄 OS 磁盘快照
快照是虚拟硬盘 (VHD) 的完整只读副本。 建议在拍摄快照前完全关闭 VM，以清除正在进行的任何过程。 若要拍摄 OS 磁盘快照，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。 从边栏中选择 "**虚拟机**"，并选择有问题的 VM。
1. 在左侧窗格中，选择 "**磁盘**"，然后选择 OS 磁盘的名称。
    有关 OS 磁盘名称的 ![映像](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. 在 OS 磁盘的 "**概述**" 页上，选择 "**创建快照**"。
1. 在 OS 磁盘所在的同一位置创建快照。

## <a name="create-a-disk-from-the-snapshot"></a>从快照创建磁盘
若要从快照创建磁盘，请执行以下步骤：

1. 从 Azure 门户中选择**Cloud Shell** 。

    ![有关打开 Cloud Shell 的图像](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 运行以下 PowerShell 命令，从快照创建托管磁盘。 应将这些示例名称替换为相应的名称。

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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
3. 如果命令运行成功，你将在提供的资源组中看到新磁盘。

## <a name="attach-the-disk-to-another-vm"></a>将磁盘附加到另一个 VM
在后续几个步骤中，将使用另一个 VM 进行故障排除。 将磁盘附加到故障排除 VM 后，你可以浏览和编辑磁盘的内容。 此过程允许您更正任何配置错误或者查看其他应用程序或系统日志文件。 若要将磁盘附加到另一个 VM，请遵循以下步骤：

1. 在门户中选择资源组，并选择故障排除 VM。 选择 "**磁盘**"，选择 "**编辑**"，然后单击 "**添加数据磁盘**"：

    ![在门户中附加现有磁盘](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 在 "**数据磁盘**" 列表中，选择所标识 VM 的 OS 磁盘。 如果看不到 OS 磁盘，请确保故障排除 VM 和操作系统磁盘位于同一区域（位置）。 
3. 选择 "**保存**" 应用更改。

## <a name="mount-the-attached-data-disk-to-the-vm"></a>向 VM 装载附加的数据磁盘

1. 与故障排除 VM 建立远程桌面连接。 
2. 在 "故障排除 VM" 中，打开**服务器管理器**，然后选择 "**文件和存储服务**"。 

    ![在“服务器管理器”中选择“文件和存储服务”](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. 系统会自动检测并附加数据磁盘。 若要查看已连接磁盘的列表，请选择“磁盘”。 可选择要查看卷信息（包括驱动器号）的数据磁盘。 以下示例显示了使用 **F:** 的附加数据磁盘：

    ![“服务器管理器”中的附加磁盘和卷信息](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>修复原始虚拟硬盘上的问题
装载现有虚拟硬盘后，可以根据需要执行任何维护和故障排除步骤。 解决问题后，请继续执行以下步骤。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸载并分离原始虚拟硬盘
解决错误后，可从故障排除 VM 中分离现有虚拟硬盘。 在将虚拟硬盘附加到故障排除 VM 的租约释放前，不能将该虚拟硬盘用于任何其他 VM。

1. 在到 VM 的 RDP 会话中，打开“服务器管理器”，并选择“文件和存储服务”：

    ![在“服务器管理器”中选择“文件和存储服务”](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. 选择“磁盘”，并选择数据磁盘。 右键单击数据磁盘，并选择“脱机”：

    ![在“服务器管理器”中将数据磁盘设置为脱机](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 现在从 VM 中分离虚拟硬盘。 在 Azure 门户中选择 VM，并单击“磁盘”。 
4. 选择 "**编辑**"，选择附加的 OS 磁盘，然后单击 "**分离**"：

    ![分离现有虚拟硬盘](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    等到 VM 成功分离数据磁盘，并继续操作。

## <a name="swap-the-os-disk-for-the-vm"></a>交换 VM 的 OS 磁盘

Azure 门户现在支持更改 VM 的 OS 磁盘。 为此，请按照下列步骤进行操作：

1. 转到 [Azure 门户](https://portal.azure.com)。 从边栏中选择 "**虚拟机**"，并选择有问题的 VM。
1. 在左侧窗格中，选择 "**磁盘**"，然后选择 "**交换操作系统磁盘**"。
        ![Azure 门户中有关交换 OS 磁盘的映像](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 选择修复的新磁盘，然后键入 VM 的名称以确认更改。 如果在列表中看不到该磁盘，请在从故障排除 VM 中分离磁盘后等待10到15分钟。 另外，请确保该磁盘与 VM 位于同一位置。
1. 选择“确定”。

## <a name="next-steps"></a>后续步骤
如果在连接到 VM 时遇到问题，请参阅[对 Azure VM 的 RDP 连接进行故障排除](troubleshoot-rdp-connection.md)。 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[对 Windows VM 上的应用程序连接问题进行故障排除](troubleshoot-app-connection.md)。

有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。


