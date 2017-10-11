---
title: "将 Modern Backup Storage 与 Azure 备份服务器 v2 一起使用 | Microsoft Docs"
description: "了解 Azure 备份服务器 v2 中的新功能。 本文介绍如何升级备份服务器安装。"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>将存储添加到 Azure 备份服务器 v2

Azure 备份服务器 v2 附带 System Center 2016 Data Protection Manager 新式备份存储。 Modern Backup Storage 可提供节省 50% 的存储、快三倍的备份以及更高效的存储。 它还提供可感知工作负载的存储。 

> [!NOTE]
> 若要使用新式备份存储，必须在 Windows Server 2016 上运行备份服务器 v2。 如果在早期版本的 Windows Server 上运行备份服务器 v2，则 Azure 备份服务器无法利用新式备份存储。 而是采用与备份服务器 v1 相同的方式来保护工作负载。 有关详细信息，请参阅备份服务器版本[保护矩阵](backup-mabs-protection-matrix.md)。

## <a name="volumes-in-backup-server-v2"></a>备份服务器 v2 中的卷

备份服务器 v2 可接受存储卷。 添加卷时，备份服务器会将卷格式化为新式备份存储所需的复原文件系统 (ReFS)。 若要添加卷以及以后在需要时扩展它，建议使用以下工作流：

1.  在 VM 上设置备份服务器 v2。
2.  在存储池中的虚拟磁盘上创建卷：
    1.  将磁盘添加到存储池，然后使用简单布局创建虚拟磁盘。
    2.  添加任何其他磁盘，然后扩展虚拟磁盘。
    3.  在虚拟磁盘上创建卷。
3.  将卷添加到备份服务器。
4.  配置可感知工作负载的存储。

## <a name="create-a-volume-for-modern-backup-storage"></a>为新式备份存储创建卷

使用具有卷的备份服务器 v2 作为磁盘存储可以帮助维护对存储的控制权。 卷可以是单个磁盘。 但是，如果要在将来扩展存储，请通过使用存储空间创建的磁盘来创建卷。 如果要为备份存储扩展卷，这可能有所帮助。 本部分提供有关使用此设置创建卷的最佳做法。

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

可以使用 PowerShell cmdlet Update-DPMDiskStorage（它可更新 Data Protection Manager 服务器上存储池中的卷的属性）设置可感知工作负载的存储。

语法：

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
下面的屏幕截图显示 PowerShell 窗口中的 Update-DPMDiskStorage cmdlet。

![PowerShell 窗口中的 Update-DPMDiskStorage 命令](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

使用 PowerShell 进行的更改会在备份服务器管理员控制台中得到反映。

![管理员控制台中的磁盘和卷](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>后续步骤
安装备份服务器之后，了解如何准备服务器或开始保护工作负载。

- [准备备份服务器工作负载](backup-azure-microsoft-azure-backup.md)
- [使用备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)
- [使用备份服务器备份 SQL Server](backup-azure-sql-mabs.md)

