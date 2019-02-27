---
title: 管理和监视 Azure VM 上通过 Azure 备份进行备份的 SQL Server 数据库 | Microsoft Docs
description: 本文介绍了如何使用 Azure 备份还原在 Azure VM 上运行的 SQL Server 数据库
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430951"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>管理和监视已备份的 SQL Server 数据库 


本文介绍了对在 Azure VM 上运行且通过 [Azure 备份](backup-overview.md)服务备份到 Azure 备份恢复服务保管库的 SQL Server 数据库进行管理和监视时的常见任务。 这些任务包括监视作业和警报，停止和恢复数据库保护，运行备份作业，以及从备份中取消注册 VM。


> [!NOTE]
> 目前可以使用 Azure 备份（公共预览版）来备份在 Azure VM 上运行的 SQl Server 数据库。


如果尚未配置 SQL Server 数据库的备份，请按[此文章](backup-azure-sql-database.md)中的说明进行操作

## <a name="monitor-backup-jobs"></a>监视备份作业

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>在门户中监视即席作业

Azure 备份会在**备份作业**门户中显示所有手动触发的作业，包括发现和注册数据库，以及备份和还原操作。

![“备份作业”门户](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> 计划的备份作业不会显示在**备份作业**门户中。 可按下一部分所述，使用 SQL Server Management Studio 来监视计划的备份作业。
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>使用 SQL Server Management Studio 监视备份作业 

Azure 备份使用 SQL 本机 API 执行所有备份操作。

使用本机 API 可从 msdb 数据库中的 [SQL backupset 表](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017)提取所有作业信息。

以下示例是用于获取名为 **DB1** 的数据库的所有备份作业的查询。 自定义查询以进行高级监视。

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>查看备份警报

由于日志备份每隔 15 分钟就会发生一次，因此，监视备份作业可能很繁琐。 Azure 备份通过电子邮件警报简化了监视工作。

- 会针对所有备份失败触发警报。
- 警报按错误代码在数据库级别合并。
- 只针对数据库的首次备份失败发送电子邮件警报。 

监视备份警报：

1. 在 [Azure 门户](https://portal.azure.com)中登录到你的 Azure 订阅来监视数据库警报。

2. 在保管库仪表板中，选择“警报和事件”。

   ![选择“警报和事件”](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. 在“警报和事件”中，选择“备份警报”。

   ![选择“备份警报”](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>停止 SQL Server 数据库的保护

可以通过两种方式来停止备份 SQL Server 数据库：

* 停止所有将来的备份作业，并删除所有恢复点。
* 停止所有将来的备份作业，但将恢复点保留不变。

请注意：

如果保留恢复点，则将根据备份策略清理恢复点。 在清理所有恢复点之前，你需要为受保护的实例和消耗的存储支付费用。 [详细了解](https://azure.microsoft.com/pricing/details/backup/)定价情况。
- 将恢复点保留不变时，尽管它们根据保留策略过期，但 Azure 备份始终会保留一个最后的恢复点，直至你显式删除备份数据。
- 如果在不停止备份的情况下删除数据源，则新备份将会失败。 同样，旧恢复点将根据策略过期，但始终会保留一个最后的恢复点，直至你显式停止备份并删除数据。
- 在禁用自动保护之前，无法为启用了自动保护的数据库停止备份。

停止数据库的保护：

1. 在保管库仪表板中，在“使用情况”下，选择“备份项”。

    ![打开“备份项”菜单](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. 在“备份管理类型”中，选择“Azure VM 中的 SQL”。

    ![选择“Azure VM 中 SQL”](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. 选择要停止保护的数据库。

    ![选择要停止保护的数据库](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. 在数据库菜单中，选择“停止备份”。

    ![选择“停止备份”](./media/backup-azure-sql-database/stop-db-button.png)


6. 在“停止备份”菜单中，选择是保留还是删除数据。 （可选）提供原因和注释。

    ![“停止备份”菜单](./media/backup-azure-sql-database/stop-backup-button.png)

7. 单击“停止备份”。

  

### <a name="resume-protection-for-a-sql-database"></a>恢复 SQL 数据库的保护

如果在停止 SQL 数据库的保护时选择了“保留备份数据”选项，则可以恢复保护。 如果未保留备份数据，则无法恢复保护。

1. 若要恢复 SQL 数据库的保护，请打开备份项并选择“恢复备份”。

    ![选择“恢复备份”以恢复数据库保护](./media/backup-azure-sql-database/resume-backup-button.png)

2. 在“备份策略”菜单中选择策略，然后选择“保存”。

## <a name="run-an-on-demand-backup"></a>运行按需备份

你可以运行不同类型的按需备份：

* 完整备份
* 仅复制完整备份
* 差异备份
* 日志备份

[详细了解](backup-architecture.md#sql-server-backup-types) SQL Server 备份类型。

## <a name="unregister-a-sql-server-instance"></a>取消注册 SQL Server 实例

在禁用保护之后但删除保管库之前取消注册 SQL Server 实例：

1. 在保管库仪表板上，在“管理”下，选择“备份基础结构”。  

   ![选择“备份基础结构”。](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. 在“管理服务器”下，选择“受保护的服务器”。

   ![选择“受保护的服务器”](./media/backup-azure-sql-database/protected-servers.png)


3. 在“受保护的服务器”中，选择要取消注册的服务器。 若要删除保管库，必须取消注册所有服务器。

4. 右键单击受保护的服务器，然后单击“删除”。

   ![选择“删除”](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>后续步骤

[查看](backup-sql-server-azure-troubleshoot.md) SQL Server 数据库备份的故障排除信息。
