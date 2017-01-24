---
title: "将 Azure SQL 数据库还原到之前的时间点 | Microsoft Docs"
description: "将 Azure SQL 数据库还原到之前的时间点"
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
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>将 Azure SQL 数据库还原到之前的时间点 

本文介绍如何将数据库从 [SQL 数据库自动备份](sql-database-automated-backups.md)还原到以前的时间点。 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>使用 Azure 门户还原到上一个时间点

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md)
>

在 Azure 门户中选择要还原的数据库：

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 在屏幕左侧选择“**更多服务**” > “**SQL 数据库**”。
3. 选择要还原的数据库。
4. 在数据库页的顶部，选择“**还原**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/restore.png)
5. 在“**还原**”页上，选择要将数据库还原到的日期和时间（UTC 时间），然后单击“**确定**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. 在上一个步骤中单击“**确定**”后，单击页面右上方的通知图标，然后单击“**正在还原 SQL 数据库**”通知了解详细信息。
   
    ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. “正在还原 SQL 数据库”页面打开，其中显示还原状态的相关信息。 可以单击行项目查看更多详细信息：
   
    ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/inprogress.png)

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
* 若要从数据库的服务生成备份查看最早的还原点，请参阅[查看最早的还原点](sql-database-view-oldest-restore-point.md)
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


