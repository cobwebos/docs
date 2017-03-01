---
title: "PowerShell：将 Azure SQL 数据库还原到时间点 | Microsoft Docs"
description: "使用 PowerShell 将 Azure SQL 数据库还原到之前的时间点"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 5e5f0a474b6a34581be804b5d18d6ae03c99c14d
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>使用 PowerShell 将 Azure SQL 数据库还原到之前的时间点

本文介绍如何使用 PowerShell 将数据库从 [SQL 数据库自动备份](sql-database-automated-backups.md)还原到以前的时间点。 也可以[使用 Azure 门户](sql-database-point-in-time-restore-portal.md)执行此任务。  

## <a name="restore-to-a-previous-point-in-time"></a>还原到之前的时间点 

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery-powershell.md)
>


## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>使用 PowerShell 还原到上一个时间点

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>将数据库还原到作为单一数据库的某个时间点
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet 获取要还原的数据库。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet 将数据库还原到某个时间点。
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>将数据库还原到某个时间点的弹性池中
1. 使用 [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet 获取要还原的数据库。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 使用 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet 将数据库还原到某个时间点。
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>后续步骤
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)


