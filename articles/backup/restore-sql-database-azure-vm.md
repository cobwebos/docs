---
title: 使用 Azure 备份在 Azure VM 上还原备份的 SQL Server 数据库 | Microsoft Docs
description: 本文介绍如何使用 Azure 备份还原在 Azure VM 上运行的 SQL Server 数据库
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445434"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>还原 Azure VM 上的 SQL Server 数据库 


本文介绍如何使用 [Azure 备份](backup-overview.md)服务还原在 Azure VM 上运行且已备份到 Azure 备份恢复服务保管库的 SQL Server 数据库。


> [!NOTE]
> 目前可以使用 Azure 备份（公共预览版）来备份在 Azure VM 上运行的 SQl Server 数据库。


本文介绍如何还原 SQL Server 数据库。 如果尚未配置数据库的备份，请按[此文](backup-azure-sql-database.md)中的说明操作



## <a name="about-restoring-databases"></a>关于如何还原数据库

Azure 备份可以还原在 Azure VM 上运行的 SQL Server 数据库，如下所示：

- 使用事务日志备份还原到特定的日期或时间（精确到秒）。 Azure 备份可自动确定相应的完整备份、差异备份和日志链备份，这些是根据所选时间进行还原所必需的。
- 还原特定的完整备份或差异备份，这样就可以还原到特定的恢复点而不是特定的时间。


### <a name="prerequisites"></a>先决条件

在还原数据库之前，请注意以下事项：

- 可将数据库还原到同一 Azure 区域中的 SQL Server 实例。
- 目标服务器必须注册到与源服务器相同的保管库。
- 若要将 TDE 加密的数据库还原到另一个 SQL Server，需先[将证书还原到目标服务器](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。
- 在触发还原“master”数据库的操作之前，请使用启动选项 **-m AzureWorkloadBackup** 在单用户模式下启动 SQL Server 实例。
    - **-m** 的值是客户端的名称。
    - 仅允许使用指定的客户端名称来打开连接。
- 对于所有系统数据库（模型数据库、master 数据库、msdb 数据库），请在触发还原操作之前停止 SQL 代理服务。
- 关闭任何可能尝试窃取到其中任何数据库的连接的应用程序。

## <a name="restore-a-database"></a>还原数据库

若要进行还原，你需要以下权限：

* 针对保管库（在其中进行还原）的**备份操作员**权限。
* 对已备份的源 VM 的**参与者（写入）** 访问权限。
* 对目标 VM 的**参与者（写入）** 访问权限：
    - 若要还原到同一 VM，则此项将是源 VM。
    - 若要还原到备用位置，则此项将是新的目标 VM。 

按如下所述进行还原：
1. 打开在其中注册 SQL Server VM 的保管库。
2. 在保管库仪表板的“使用情况”下，选择“备份项”。

    ![打开“备份项”菜单](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. 在“备份项”中的“备份管理类型”下，选择“Azure VM 中的 SQL”。

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 选择要还原的数据库。

    ![选择要还原的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. 查看数据库菜单。 它提供有关数据库备份的信息，其中包括： 

    * 最旧和最新的还原点。
    * 过去 24 小时的日志备份状态，适用于完整和批量日志记录恢复模型中的数据库，前提是针对事务日志备份进行了相应配置。

6. 单击“还原 DB”。 

    ![选择“还原数据库”](./media/backup-azure-sql-database/restore-db-button.png)

7. 在“还原配置”中，指定要将数据还原到哪个位置：
    - **备用位置**：将数据库还原到备用位置，同时保留原始源数据库。
    - **覆盖 DB**：将数据还原到原始源所在的同一 SQL Server 实例。 此选项会覆盖原始数据库。

    > [!Important]
    > 如果选定的数据库属于 Always On 可用性组，则 SQL Server 不允许覆盖数据库。 仅“备用位置”可用。
    >

    ![“还原配置”菜单](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>还原到备用位置

1. 在“还原配置”菜单中的“还原位置”下，选择“备用位置”。
2. 选择要将数据库还原到其中的 SQL Server 名称和实例。
3. 在“已还原 DB 的名称”框中，输入目标数据库的名称。
4. 如果适用，请选择“当选定的 SQL 实例上已存在同名的 DB 时覆盖”。
5. 单击“确定”。

    ![为“还原配置”菜单提供值](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. 在“选择还原点”中，选择是[还原到特定时间点](#restore-to-a-specific-point-in-time)，还是还原到[特定恢复点](#restore-to-a-specific-restore-point)。

    > [!NOTE]
    > 时间点还原仅适用于采用完整和批量日志记录恢复模式的数据库日志备份。 


### <a name="restore-and-overwrite"></a>还原并覆盖

1. 在“还原配置”菜单中的“还原位置”下，选择“覆盖 DB” > “确定”。

    ![选择“覆盖数据库”](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. 在“选择还原点”中，选择“日志(时间点)”以[还原到特定时间点](#restore-to-a-specific-point-in-time)，或者选择“完整和差异”以还原到[特定恢复点](#restore-to-a-specific-restore-point)。**

    > [!NOTE]
    > 时间点还原仅适用于采用完整和批量日志记录恢复模型的数据库日志备份。 




    
### <a name="restore-to-a-specific-point-in-time"></a>还原到特定时间点

如果已选择“日志(时间点)”作为还原类型，请执行以下操作：

1.  在“还原日期/时间”下，选择微型日历。 在“日历”中，以粗体显示的日期包含恢复点，当前日期已突出显示。
2. 选择包含恢复点的日期。 不能选择不包含恢复点的日期。

    ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. 选择日期后，时间线图会显示连续范围内的可用恢复点。
4. 使用时间线图指定恢复时间，或者选择一个时间。 然后单击“确定”。

    ![打开日历](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. 在“高级配置”菜单上，若要使数据库在还原后保持不可运行状态，请启用“使用 NORECOVERY 还原”。
5. 若要更改目标服务器上的还原位置，请输入新的目标路径。
6. 单击“确定”。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. 在“还原”菜单中，选择“还原”启动还原作业。
8. 在“通知”区域跟踪还原进度，或者在数据库菜单中选择“还原作业”。

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>还原到特定还原点

如果已选择“完整和差异”作为还原类型，请执行以下操作：


1. 在列表中选择一个恢复点，然后单击“确定”完成还原点过程。

    ![选择完整恢复点](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. 在“高级配置”菜单上，若要使数据库在还原后保持不可运行状态，请启用“使用 NORECOVERY 还原”。
3. 若要更改目标服务器上的还原位置，请输入新的目标路径。 
4. 单击“确定”。

    ![高级配置菜单](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. 在“还原”菜单中，选择“还原”启动还原作业。
8. 在“通知”区域跟踪还原进度，或者在数据库菜单中选择“还原作业”。

    ![还原作业的进度](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>后续步骤

[管理和监视](manage-monitor-sql-database-backup.md)通过 Azure 备份进行备份的 SQL Server 数据库。
