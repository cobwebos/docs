---
title: 使用 Azure 备份来还原 Azure VM 上的备份 SQL Server 数据库 |Microsoft Docs
description: 本文介绍如何还原 Azure VM 上运行的和的备份与 Azure 备份的 SQL Server 数据库。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: d8ade598e4f1b6331367e8bd04ad59951ef5de8f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242366"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>还原 Azure VM 上的 SQL Server 数据库

本文介绍如何还原 Azure 虚拟机 (VM) 运行的 SQL Server 数据库的[Azure 备份](backup-overview.md)服务备份到 Azure 备份恢复服务保管库。

本文介绍如何还原 SQL Server 数据库。 有关详细信息，请参阅[备份 Azure Vm 上的 SQL Server 数据库](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>还原到时间或恢复点

Azure 备份可以还原 SQL Server 数据库上运行的 Azure Vm，如下所示：

- 使用事务日志备份还原到特定的日期或时间 （针对第二个）。 Azure 备份会自动确定适当的完整差异备份和还原所需的日志备份链基于所选的时间。
- 还原某个特定的完整或差异备份，以便还原到特定恢复点。


## <a name="prerequisites"></a>必备组件

在还原数据库之前，请注意以下各项：

- 可将数据库还原到同一 Azure 区域中的 SQL Server 实例。
- 目标服务器必须注册到与源服务器相同的保管库。
- 若要还原到另一台 SQL 服务器的 TDE 加密数据库，需要先[还原到目标服务器的证书](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 将"主"数据库还原之前，请启动 SQL Server 实例在单用户模式下使用启动选项 **-m AzureWorkloadBackup**。
    - 值 **-m**是客户端的名称。
    - 只有指定的客户端名称可以打开的连接。
- 所有系统数据库 (模型中，master、 msdb) 触发还原之前都停止 SQL Server 代理服务。
- 关闭任何可能会尝试将连接到这些数据库的应用程序。
- 如果有多个实例的服务器上运行的所有实例应启动并运行否则服务器不会显示，以便数据库还原到目标服务器的列表中。

## <a name="restore-a-database"></a>还原数据库

若要还原，需要以下权限：

* **备份操作员**中要在其中执行还原的保管库的权限。
* 对已备份的源 VM 的**参与者（写入）** 访问权限。
* 对目标 VM 的**参与者（写入）** 访问权限：
    - 如果要还原到同一个 VM，这是源 VM。
    - 如果要还原到备用位置，这是新的目标 VM。

按如下所述进行还原：
1. 打开在其中注册 SQL Server VM 的保管库。
2. 在保管库仪表板的“使用情况”下，选择“备份项”   。
3. 在“备份项”中的“备份管理类型”下，选择“Azure VM 中的 SQL”。   

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 选择要还原的数据库。

    ![选择要还原的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 查看数据库菜单。 它提供有关数据库备份的信息，其中包括：

    * 最旧和最新的还原点。
    * 为事务日志备份配置的数据库，在完整和大容量日志恢复模式，并在最后一个 24 小时内的日志备份状态。

6. 选择**还原 DB**。

    ![选择“还原数据库”](./media/backup-azure-sql-database/restore-db-button.png)

7. 在中**还原配置**，指定用于还原数据的位置：
   - **备用位置**：将数据库还原到备用位置，并保留原始的源数据库。
   - **覆盖 DB**：将数据还原到原始源所在的同一 SQL Server 实例。 此选项将覆盖原始数据库。

     > [!Important]
     > 如果选定的数据库属于 Always On 可用性组，则 SQL Server 不允许覆盖数据库。 仅“备用位置”可用。 
     >

     ![“还原配置”菜单](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 在中**还原配置**菜单下**还原到何处**，选择**备用位置**。
2. 选择要将数据库还原到其中的 SQL Server 名称和实例。
3. 在“还原数据库名称”框中，输入目标数据库的名称。 
4. 如果适用，请选择“当选定的 SQL 实例上已存在同名的 DB 时覆盖”。 
5. 选择“确定”  。

    ![为“还原配置”菜单提供值](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. 在中**选择还原点**，选择是否[还原到的特定点的时间](#restore-to-a-specific-point-in-time)或设置为[还原到特定恢复点](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 仅适用于在完整和大容量日志恢复模式下的数据库的日志备份时间点还原。

### <a name="restore-and-overwrite"></a>还原并覆盖

1. 在中**还原配置**菜单下**还原到何处**，选择**覆盖 DB** > **确定**。

    ![选择“覆盖数据库”](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在中**选择还原点**，选择**日志 （时间点）** 到[及时还原到的特定点](#restore-to-a-specific-point-in-time)。 或选择**完整和差异**还原到[特定的恢复点](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 仅适用于在完整和大容量日志恢复模式下的数据库的日志备份时间点还原。

### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作： 

1.  下**还原日期/时间**，打开日历。 在日历上以粗体类型显示的具有恢复点的日期和突出显示当前日期。
1. 选择具有恢复点的日期。 不能选择不具有任何恢复点的日期。

    ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 选择日期后，时间线图会显示连续范围内的可用恢复点。
1. 在时间线关系图中上, 指定的时间进行恢复，或选择一个时间。 然后选择“确定”。 

    ![选择还原时间](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. 上**高级配置**菜单中，如果你想要保留在数据库还原后，不再运行启用**使用 NORECOVERY 还原**。
1. 若要更改目标服务器上的还原位置，请输入新的目标路径。
1. 选择“确定”  。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。  
1. 跟踪中的还原进度**通知**区域中，或通过选择跟踪**还原作业**数据库菜单上。

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>还原到特定还原点

如果已选择“完整和差异”作为还原类型，请执行以下操作： 

1. 在列表中选择一个恢复点，然后选择“确定”完成还原点过程。 

    ![选择完整恢复点](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. 上**高级配置**菜单中，如果你想要保留在数据库还原后，不再运行启用**使用 NORECOVERY 还原**。
1. 若要更改目标服务器上的还原位置，请输入新的目标路径。
1. 选择“确定”  。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。  
1. 跟踪中的还原进度**通知**区域中，或通过选择跟踪**还原作业**数据库菜单上。

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>使用大量的文件还原数据库

如果在数据库中的文件的总字符串大小大于[特定限制](backup-sql-server-azure-troubleshoot.md#files-size-limit-beyond-which-restore-happens-to-default-path)，Azure 备份将数据库文件的列表存储在不同时间点组件，以便你将不能在还原过程中设置目标还原路径操作。 文件将改为还原到的 SQL 默认路径。

  ![使用大型文件还原数据库](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>后续步骤

[管理和监视](manage-monitor-sql-database-backup.md)备份的 Azure 备份的 SQL Server 数据库。
