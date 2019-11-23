---
title: 管理长期备份保留
description: 了解如何在 SQL Azure 存储中存储自动备份，以及如何还原它们
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: a560f4f1399792a7b150b37c3c048ccc0079b98d
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420791"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>管理 Azure SQL 数据库长期备份保留

In Azure SQL Database, you can configure a single or a pooled database with a [long-term backup retention](sql-database-long-term-retention.md) policy (LTR) to automatically retain the database backups in separate Azure Blob storage containers for up to 10 years. 然后，可以通过 Azure 门户或 PowerShell 使用这些备份来恢复数据库。

> [!IMPORTANT]
> [Azure SQL database managed instance](sql-database-managed-instance.md) does not currently support long-term backup retention.

## <a name="using-azure-portal"></a>使用 Azure 门户

以下各部分展示了如何使用 Azure 门户配置长期保留、查看长期保留的备份，以及还原长期保留的备份。

### <a name="configure-long-term-retention-policies"></a>配置长期保留策略

可以对 SQL 数据库进行配置，使其[保留自动备份](sql-database-long-term-retention.md)的时间长于你的服务层级的保留期。

1. 在 Azure 门户中，选择你的 SQL Server，然后单击“管理备份”。 在“配置策略”选项卡上，选中要为其设置或修改长期备份保留策略的数据库所对应的复选框。 如果未选中数据库旁边的复选框，则策略的更改将不会应用于该数据库。  

   ![管理备份链接](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. 在“配置策略”窗格中，选择是要保留每周、每月还是每年备份，并指定各自的保留期。

   ![配置策略](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. 完成后，单击“应用”。

> [!IMPORTANT]
> When you enable a long-term backup retention policy, it may take up to 7 days for the first backup to become visible and available to restore. For details of the LTR backup cadance, see [long-term backup retention](sql-database-long-term-retention.md).

### <a name="view-backups-and-restore-from-a-backup"></a>View backups and restore from a backup

查看通过 LTR 策略为特定数据库保留的备份，并从这些备份进行还原。

1. 在 Azure 门户中，选择你的 SQL Server，然后单击“管理备份”。 在“可用备份”选项卡上，选择要查看其可用备份的数据库。

   ![选择数据库](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. 在“可用备份”窗格中，查看可用备份。

   ![查看备份](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. 选择要从中进行还原的备份，然后指定新的数据库名称。

   ![还原](./media/sql-database-long-term-retention/ltr-restore.png)

1. 单击“确定”将数据库从 Azure SQL 存储中的备份还原到新数据库。

1. 在工具栏上，单击通知图标以查看还原作业的状态。

   ![检查作业进度](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. 完成还原作业后，打开“SQL 数据库”页面以查看新还原的数据库。

> [!NOTE]
> 从此处，可使用 SQL Server Management Studio 连接到已还原的数据库，以执行所需任务，例如[从恢复的数据库中提取一部分数据，复制到现有数据库或删除现有数据库，并将已还原数据库的名称重命名为现有数据库名称](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="using-powershell"></a>使用 PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

以下各部分展示了如何使用 PowerShell 配置长期备份保留、查看 Azure SQL 存储中的备份，以及从 Azure SQL 存储中的备份进行还原。

### <a name="rbac-roles-to-manage-long-term-retention"></a>用于管理长期保留的 RBAC 角色

For **Get-AzSqlDatabaseLongTermRetentionBackup** and **Restore-AzSqlDatabase**, you will need to have one of the following roles:

- Subscription Owner role or
- SQL Server Contributor role or
- Custom role with the following permissions:

   Microsoft.Sql/locations/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read  Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

For **Remove-AzSqlDatabaseLongTermRetentionBackup**, you will need to have one of the following roles:

- Subscription Owner role or
- Custom role with the following permission:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> The SQL Server Contributor role does not have permission to delete LTR backups.

RBAC permissions could be granted in either *subscription* or *resource group* scope. However, to access LTR backups that belong to a dropped server, the permission must be granted in the *subscription* scope of that server.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>创建 LTR 策略

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>查看 LTR 策略

此示例展示了如何列出服务器内的 LTR 策略

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>清除 LTR 策略

此示例展示了如何从数据库中清除 LTR 策略

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>查看 LTR 备份

此示例展示了如何列出服务器内的 LTR 备份。

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>删除 LTR 备份

此示例展示了如何从备份列表中删除 LTR 备份。

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> 删除 LTR 备份操作是不可逆的。 To delete an LTR backup after the server has been deleted you must have Subscription scope permission. 可以通过筛选“删除长期保留备份”操作来在 Azure Monitor 中设置有关每次删除的通知。 活动日志包含有关发出请求的人员和时间的信息。 有关详细说明，请参阅[创建活动日志警报](../azure-monitor/platform/alerts-activity-log.md)。

### <a name="restore-from-ltr-backups"></a>从 LTR 备份进行还原

此示例展示了如何从 LTR 备份进行还原。 请注意，此接口没有更改，但是资源 ID 参数现在要求提供 LTR 备份资源 ID。

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> To restore from an LTR backup after the server has been deleted, you must have permissions scoped to the server's subscription and that subscription must be active. You must also omit the optional -ResourceGroupName parameter.

> [!NOTE]
> 从此处，可使用 SQL Server Management Studio 连接到已还原的数据库，执行所需任务，例如从恢复的数据库中提取一部分数据，复制到现有数据库或删除现有数据库，并将已还原的数据库重命名为现有数据库名。 请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。

## <a name="next-steps"></a>后续步骤

- 若要了解服务生成的自动备份，请参阅[自动备份](sql-database-automated-backups.md)
- 若要了解长期备份保留，请参阅[长期备份保留](sql-database-long-term-retention.md)
