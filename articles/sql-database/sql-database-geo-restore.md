---
title: "从异地冗余备份还原 Azure SQL 数据库 | Microsoft Docs"
description: "使用 Azure 门户或 PowerShell 从异地冗余备份中将 Azure SQL 数据库还原到新的服务器中"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>从异地冗余备份还原 Azure SQL 数据库

本文演示了如何使用异地还原将数据库还原到新的服务器中。 这可以通过 Azure 门户或使用 PowerShell 完成。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>使用 Azure 门户从异地冗余备份还原 Azure SQL 数据库

要在 Azure 门户中异地还原数据库，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在屏幕左侧选择“**+新建**” > “**数据库**” > “**SQL 数据库**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 选择“**备份**”作为源，然后选择要从中进行恢复的备份。 指定数据库名称、你想要恢复的数据库，然后单击“**创建**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 通过单击页面右上方的通知图标监视恢复操作的状态。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>使用 PowerShell 从异地冗余备份还原 Azure SQL 数据库

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>将数据库异地还原到独立的数据库中
1. 使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) cmdlet 获取要还原的数据库的异地冗余备份。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 借助 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet，从异地冗余备份中开始还原。
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>将数据库异地还原到弹性池中
1. 使用 [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) cmdlet 获取要还原的数据库的异地冗余备份。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. 借助 [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet，从异地冗余备份中开始还原。 指定要将数据库还原到其中的池的名称。
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)。
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)。  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)。




<!--HONumber=Dec16_HO3-->


