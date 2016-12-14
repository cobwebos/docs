---
title: "还原已删除的 Azure SQL 数据库 (PowerShell) | Microsoft 文档"
description: "还原已删除的 Azure SQL 数据库 (PowerShell)。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: ce7f0f3f-47a6-42af-b8a9-4a34bbbd8966
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: fcaa796a9108700e34222eb12cc3c45d4d1b275a


---
# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>使用 PowerShell 还原已删除的 Azure SQL 数据库
> [!div class="op_single_selector"]
> * [概述](sql-database-recovery-using-backups.md)
> * [还原已删除的数据库：门户](sql-database-restore-deleted-database-portal.md)
> * [**还原已删除的数据库：PowerShell**](sql-database-restore-deleted-database-powershell.md)
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="get-a-list-of-deleted-databases"></a>获取已删除数据库的列表
```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-standalone-database"></a>将已删除的数据库还原到独立数据库
使用 [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx) cmdlet 获取要还原的已删除数据库备份。 然后，使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx) cmdlet 从已删除的数据库备份开始还原。

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-database-pool"></a>将已删除的数据库还原到弹性数据库池中
使用 [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx) cmdlet 获取要还原的已删除数据库备份。 然后，使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx) cmdlet 从已删除的数据库备份开始还原。

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


