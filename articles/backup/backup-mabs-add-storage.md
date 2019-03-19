---
title: 将新式备份存储与 Azure 备份服务器一起使用
description: 了解 Azure 备份服务器中的新功能。 本文介绍如何升级备份服务器安装。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 621d071f98701ff3a949f4172fef1d13819d7192
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57852847"
---
# <a name="add-storage-to-azure-backup-server"></a>将存储添加到 Azure 备份服务器

Azure 备份服务器 V2 及更高版本支持新式备份存储，后者可提供节省 50% 的存储、快三倍的备份以及更高效的存储。 它还提供可感知工作负荷的存储。

> [!NOTE]
> 若要使用新式备份存储，必须在 Windows Server 2016 上运行备份服务器 V2 或 V3，或者在 Windows Server 2019 上运行备份服务器 V3。
> 如果在早期版本的 Windows Server 上运行备份服务器 V2，则 Azure 备份服务器无法利用新式备份存储。 而是采用与备份服务器 V1 相同的方式来保护工作负载。 有关详细信息，请参阅备份服务器版本[保护矩阵](backup-mabs-protection-matrix.md)。

## <a name="volumes-in-backup-server"></a>备份服务器中的卷

备份服务器 V2 或更高版本可接受存储卷。 添加卷时，备份服务器会将卷格式化为新式备份存储所需的复原文件系统 (ReFS)。 若要添加卷以及以后在需要时扩展它，建议使用以下工作流：

1.  在 VM 上设置备份服务器。
2.  在存储池中的虚拟磁盘上创建卷：
    1.  将磁盘添加到存储池，然后使用简单布局创建虚拟磁盘。
    2.  添加任何其他磁盘，然后扩展虚拟磁盘。
    3.  在虚拟磁盘上创建卷。
3.  将卷添加到备份服务器。
4.  配置可感知工作负载的存储。

## <a name="create-a-volume-for-modern-backup-storage"></a>为新式备份存储创建卷

使用具有卷的备份服务器 V2 或更高版本作为磁盘存储可以帮助维护对存储的控制权。 卷可以是单个磁盘。 但是，如果要在将来扩展存储，请通过使用存储空间创建的磁盘来创建卷。 如果要为备份存储扩展卷，这可能有所帮助。 本部分提供有关使用此设置创建卷的最佳做法。

1. 在“服务器管理器”中，然后选择“文件和存储服务” > “卷” > “存储池”。 在“物理磁盘”，选择“新建存储池”。

    ![创建新存储池](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. 在“任务”下拉框中，选择“新建虚拟磁盘”。

    ![添加虚拟磁盘](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. 选择存储池，然后选择“添加物理磁盘”。

    ![添加物理磁盘](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. 选择物理磁盘，然后选择“扩展虚拟磁盘”。

    ![扩展虚拟磁盘](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. 选择虚拟磁盘，然后选择“新建卷”。

    ![创建新卷](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. 在“选择服务器和磁盘”对话框中，选择服务器和新磁盘。 然后，选择“下一步”。

    ![选择服务器和磁盘](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>将卷添加到备份服务器磁盘存储

若要将卷添加到备份服务器，请在“管理”窗格中，重新扫描存储，然后选择“添加”。 可用于为备份服务器存储添加的所有卷的列表随即出现。 将可用卷添加到所选卷的列表之后，可以为它们提供友好名称以帮助管理它们。 若要将这些卷格式化为 ReFS 以便备份服务器可以利用新式备份存储的优点，请选择“确定”。

![添加可用卷](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>设置可感知工作负载的存储

借助可感知工作负载的存储，可以选择优先存储某些类型的工作负载的卷。 例如，可以将支持较高每秒输入/输出操作次数 (IOPS) 的昂贵卷设置为仅存储需要频繁的高容量备份的工作负载。 一个示例是具有事务日志的 SQL Server。 可以将备份频率较低的其他工作负载（如 VM）备份到低成本卷。

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

可以使用 PowerShell cmdlet Update-dpmdiskstorage，更新 Azure 备份服务器上存储池中卷的属性来设置负载感知存储。 

语法：

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
下面的屏幕截图显示 PowerShell 窗口中的 Update-DPMDiskStorage cmdlet。

![PowerShell 窗口中的 Update-DPMDiskStorage 命令](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

使用 PowerShell 进行的更改会在备份服务器管理员控制台中得到反映。

![管理员控制台中的磁盘和卷](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>将旧存储迁移到 Modern Backup Storage
升级到或安装备份服务器 V2 并将操作系统升级到 Windows Server 2016 之后，可更新保护组以使用新式备份存储。 默认情况下，保护组不会进行更改。 它们会继续按照初始设置运行。

可以选择更新保护组以使用新式备份存储。 若要更新保护组，请使用保留数据选项停止所有数据源的保护。 然后，将数据源添加到新保护组。

1. 在管理员控制台中，选择“保护”功能。 在“保护组成员”列表中，右键单击成员，然后选择“停止保护成员”。

   ![停止保护成员](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. 在“从组中删除”对话框中，检查存储池的已用磁盘空间和可用空闲空间。 默认设置是在磁盘上保留恢复点，并让它们可以按照关联保留策略过期。 单击“确定”。

   如果要立即将已用磁盘空间返回到可用存储池，则选中“删除磁盘上的副本”复选框以删除与成员关联的备份数据（和恢复点）。

   ![“从组中删除”对话框](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. 创建一个使用新式备份存储的保护组。 包括未受保护的数据源。

## <a name="add-disks-to-increase-legacy-storage"></a>添加磁盘以增大旧存储

如果要将旧存储与备份服务器一起使用，则可能需要添加磁盘以增大旧存储。

添加磁盘存储：

1. 在管理员控制台中，选择“管理” > “磁盘存储” > “添加”。

    ![“添加磁盘存储”对话框](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. 在“添加磁盘存储”对话框中，选择“添加磁盘”。

5. 在可用磁盘的列表中，选择要添加的磁盘，选择“添加”，然后选择“确定”。

## <a name="next-steps"></a>后续步骤
安装备份服务器之后，了解如何准备服务器或开始保护工作负载。

- [准备备份服务器工作负载](backup-azure-microsoft-azure-backup.md)
- [使用备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)
- [使用备份服务器备份 SQL Server](backup-azure-sql-mabs.md)
