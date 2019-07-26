---
title: 使用 Azure 备份在 Azure VM 上还原已备份的 SQL Server 数据库 |Microsoft Docs
description: 本文介绍如何还原在 Azure VM 上运行并且使用 Azure 备份进行备份的 SQL Server 数据库。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: f6bd9ea70a3334fbf3843a6b5d44d10a929e20ae
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467214"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>还原 Azure VM 上的 SQL Server 数据库

本文介绍如何还原 azure[备份](backup-overview.md)服务已备份到 Azure 备份恢复服务保管库的 azure 虚拟机 (VM) 上运行的 SQL Server 数据库。

本文介绍如何还原 SQL Server 数据库。 有关详细信息, 请参阅[备份 Azure vm 上的 SQL Server 数据库](backup-azure-sql-database.md)。

## <a name="restore-to-a-time-or-a-recovery-point"></a>还原到时间或恢复点

Azure 备份可以还原在 Azure Vm 上运行 SQL Server 数据库, 如下所示:

- 使用事务日志备份还原到特定的日期或时间 (到第二个)。 Azure 备份会自动根据所选时间确定所需的完整差异备份和日志备份链。
- 还原特定的完整备份或差异备份以还原到特定恢复点。


## <a name="prerequisites"></a>先决条件

在还原数据库之前, 请注意以下事项:

- 可将数据库还原到同一 Azure 区域中的 SQL Server 实例。
- 目标服务器必须注册到与源服务器相同的保管库。
- 若要将 TDE 加密的数据库还原到另一个 SQL Server, 需要首先将[证书还原到目标服务器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 在还原 "master" 数据库之前, 请使用启动选项 **-m AzureWorkloadBackup**在单用户模式下启动 SQL Server 实例。
    - **-M**的值是客户端的名称。
    - 只有指定的客户端名称可以打开连接。
- 对于所有系统数据库 (model、master、msdb), 在触发还原之前, 停止 SQL Server 代理服务。
- 关闭可能会尝试连接到这些数据库的任何应用程序。
- 如果在服务器上运行多个实例, 则所有实例都应启动并运行, 否则, 服务器将不会出现在目标服务器列表中, 以便您可以将数据库还原到该列表中。

## <a name="restore-a-database"></a>还原数据库

若要还原, 需要具有以下权限:

* 要在其中执行还原操作的保管库中的**备份操作员**权限。
* 对已备份的源 VM 的**参与者（写入）** 访问权限。
* 对目标 VM 的**参与者（写入）** 访问权限：
    - 如果正在还原到同一个 VM, 则这是源 VM。
    - 如果要还原到备用位置, 则这是新的目标 VM。

按如下所述进行还原：
1. 打开在其中注册 SQL Server VM 的保管库。
2. 在保管库仪表板的“使用情况”下，选择“备份项”。
3. 在“备份项”中的“备份管理类型”下，选择“Azure VM 中的 SQL”。

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 选择要还原的数据库。

    ![选择要还原的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 查看数据库菜单。 它提供有关数据库备份的信息，其中包括：

    * 最旧和最新的还原点。
    * 在完整和大容量日志恢复模式下, 以及针对事务日志备份配置的数据库的最近24小时的日志备份状态。

6. 选择 "**还原数据库**"。

    ![选择“还原数据库”](./media/backup-azure-sql-database/restore-db-button.png)

7. 在 "**还原配置**" 中, 指定要将数据还原到的位置:
   - **备用位置**：将数据库还原到备用位置, 并保留原始源数据库。
   - **覆盖 DB**：将数据还原到原始源所在的同一 SQL Server 实例。 此选项将覆盖原始数据库。

     > [!Important]
     > 如果选定的数据库属于 Always On 可用性组，则 SQL Server 不允许覆盖数据库。 仅“备用位置”可用。
     >

     ![“还原配置”菜单](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 在 "**还原配置**" 菜单中的 "**还原位置**" 下, 选择 "**备用位置**"。
2. 选择要将数据库还原到其中的 SQL Server 名称和实例。
3. 在“还原数据库名称”框中，输入目标数据库的名称。
4. 如果适用，请选择“当选定的 SQL 实例上已存在同名的 DB 时覆盖”。
5. 选择“确定”。

    ![为“还原配置”菜单提供值](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. 在 "**选择还原点**" 中, 选择是[还原到特定的时间点](#restore-to-a-specific-point-in-time)还是[还原到特定的恢复点](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 时间点还原仅适用于处于完全恢复模式和大容量日志恢复模式的数据库的日志备份。

### <a name="restore-and-overwrite"></a>还原并覆盖

1. 在 "**还原配置**" 菜单中的 "**还原位置**" 下, 选择 "**覆盖 DB** >  **"** 。

    ![选择“覆盖数据库”](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在 "**选择还原点**" 中, 选择要[还原到特定时间点的](#restore-to-a-specific-point-in-time)**日志 (时间点)** 。 或者选择 "**完全 & 差异**" 以还原到[特定恢复点](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 时间点还原仅适用于处于完全恢复模式和大容量日志恢复模式的数据库的日志备份。

### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作：

1.  在 "**还原日期/时间**" 下, 打开日历。 在日历上, 具有恢复点的日期以粗体显示, 并突出显示当前日期。
1. 选择具有恢复点的日期。 不能选择没有恢复点的日期。

    ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 选择日期后，时间线图会显示连续范围内的可用恢复点。
1. 在时间线关系图上指定恢复时间, 或选择一个时间。 然后选择“确定”。

    ![选择还原时间](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. 在 "**高级配置**" 菜单上, 如果要在还原后保留数据库 nonoperational, 请启用 "**使用 NORECOVERY 还原**"。
1. 若要更改目标服务器上的还原位置，请输入新的目标路径。
1. 选择“确定”。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。
1. 通过选择 "数据库" 菜单上的 "**还原作业**", 在 "**通知**" 区域中跟踪还原进度。

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>还原到特定还原点

如果已选择“完整和差异”作为还原类型，请执行以下操作：

1. 在列表中选择一个恢复点，然后选择“确定”完成还原点过程。

    ![选择完整恢复点](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. 在 "**高级配置**" 菜单上, 如果要在还原后保留数据库 nonoperational, 请启用 "**使用 NORECOVERY 还原**"。
1. 若要更改目标服务器上的还原位置，请输入新的目标路径。
1. 选择“确定”。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. 在“还原”菜单中，选择“还原”启动还原作业。
1. 通过选择 "数据库" 菜单上的 "**还原作业**", 在 "**通知**" 区域中跟踪还原进度。

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>还原包含大量文件的数据库

如果数据库中文件的总字符串大小超过[特定限制](backup-sql-server-azure-troubleshoot.md#size-limit-for-files), Azure 备份会将数据库文件的列表存储在不同的 pit 组件中, 以便在还原操作期间不能设置目标还原路径。 文件将还原到 SQL 默认路径。

  ![还原包含大型文件的数据库](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>后续步骤

[管理和监视](manage-monitor-sql-database-backup.md)SQL Server 由 Azure 备份备份的数据库。
