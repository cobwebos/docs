---
title: "Azure 门户：将 Azure SQL 数据库还原到时间点 | Microsoft Docs"
description: "使用 Azure 门户将 Azure SQL 数据库还原到之前的时间点"
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
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>使用 Azure 门户将 Azure SQL 数据库还原到之前的时间点

本文介绍如何将数据库从 [SQL 数据库自动备份](sql-database-automated-backups.md)还原到之前的时间点。 使用 PowerShell。 也可以[使用 PowerShell](sql-database-point-in-time-restore-powershell.md) 执行此任务。

## <a name="restore-to-a-previous-point-in-time"></a>还原到之前的时间点 

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

## <a name="next-steps"></a>后续步骤
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)


