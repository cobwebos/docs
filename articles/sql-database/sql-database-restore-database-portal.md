---
title: "Azure 门户：还原 Azure SQL 数据库 | Microsoft 文档"
description: "还原 Azure SQL 数据库（Azure 门户）。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>使用 Azure 门户还原 Azure SQL 数据库

以下步骤显示如何将 Azure SQL 数据库从已删除的数据库还原到某个时间点，以及如何从异地冗余备份还原。

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>将 Azure SQL 数据库还原到之前的时间点 

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery-portal.md)
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


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>从备份还原已删除的 Azure SQL 数据库
在 Azure 门户中还原已删除的数据库：

1. 在 [Azure 门户](https://portal.azure.com)中，单击“**更多服务**” > “**SQL 服务器**”。
2. 选择包含要还原的数据库的服务器。
3. 向下滚动到服务器边栏选项卡的“**操作**”部分并选择“**已删除的数据库**”：![还原 Azure SQL 数据库](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 选择要还原的数据库。
5. 指定数据库名称，然后单击“**确定**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>从异地冗余备份还原 Azure SQL 数据库

要在 Azure 门户中异地还原数据库，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在屏幕左侧选择“**+新建**” > “**数据库**” > “**SQL 数据库**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 选择“**备份**”作为源，然后选择要从中进行恢复的备份。 指定数据库名称、你想要恢复的数据库，然后单击“**创建**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 通过单击页面右上方的通知图标监视恢复操作的状态。

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)


