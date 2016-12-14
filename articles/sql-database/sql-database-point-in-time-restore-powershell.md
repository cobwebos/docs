---
title: "将 Azure SQL 数据库还原到之前的时间点 (PowerShell) | Microsoft 文档"
description: "将 Azure SQL 数据库还原到之前的时间点"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 3d1ecd68-5e78-4e8d-9347-b37b5fa15f36
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4b70d51cbd4b245605173ee596fc0ea587668b92


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>使用 PowerShell 将 Azure SQL 数据库还原到之前的时间点
> [!div class="op_single_selector"]
> * [概述](sql-database-recovery-using-backups.md)
> * [时间点还原：Azure 门户](sql-database-point-in-time-restore-portal.md)
> 
> 

本文介绍如何将数据库从 [SQL 数据库自动备份](sql-database-automated-backups.md)还原到以前的时间点。 可使用 PowerShell 实现此目的。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>将数据库还原到作为独立数据库的某个时间点
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet 获取要还原的数据库。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet 将数据库还原到某个时间点。
   
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>将数据库还原到某个时间点的弹性数据库池中
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet 获取要还原的数据库。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet 将数据库还原到某个时间点。
   
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


