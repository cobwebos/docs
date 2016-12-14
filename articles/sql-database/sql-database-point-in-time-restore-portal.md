---
title: "将 Azure SQL 数据库还原到之前的时间点（Azure 门户）| Microsoft 文档"
description: "将 Azure SQL 数据库还原到之前的时间点。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>使用 Azure 门户将 Azure SQL 数据库还原到之前的时间点
> [!div class="op_single_selector"]
> * [概述](sql-database-recovery-using-backups.md)
> * [时间点还原：PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

本文介绍如何使用 Azure 门户将数据库从 [SQL 数据库自动备份](sql-database-automated-backups.md)还原到以前的时间点。

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>将 SQL 数据库还原到之前的时间点
在 Azure 门户中选择要还原的数据库：

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 在屏幕左侧选择“**更多服务**” > “**SQL 数据库**”。
3. 选择要还原的数据库。
4. 在数据库页的顶部，选择“**还原**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/restore.png)
5. 在“**还原**”页上，选择要将数据库还原到的日期和时间（UTC 时间），然后单击“**确定**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>监视还原操作
1. 在上一个步骤中单击“**确定**”后，单击页面右上方的通知图标，然后单击“**正在还原 SQL 数据库**”通知了解详细信息。
   
    ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. “正在还原 SQL 数据库”页面打开，其中显示还原状态的相关信息。 可以单击行项目查看更多详细信息：
   
    ![还原 Azure SQL 数据库](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


