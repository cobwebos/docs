---
title: "Azure 门户：从异地冗余备份还原 SQL 数据库 | Microsoft Docs"
description: "使用 Azure 门户从异地冗余备份将 Azure SQL 数据库还原到新的服务器中"
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
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>使用 Azure 门户从异地冗余备份还原 Azure SQL 数据库

本文演示了如何使用 Azure 门户通过异地还原将数据库还原到新服务器中。 也可以[使用 PowerShell](sql-database-geo-restore-powershell.md) 执行此任务。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>使用 Azure 门户从异地冗余备份还原 Azure SQL 数据库

要在 Azure 门户中异地还原数据库，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在屏幕左侧选择“**+新建**” > “**数据库**” > “**SQL 数据库**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 选择“**备份**”作为源，然后选择要从中进行恢复的备份。 指定数据库名称、你想要恢复的数据库，然后单击“**创建**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/geo-restore.png)

4. 通过单击页面右上方的通知图标监视恢复操作的状态。


## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)。
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)。
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)。  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)。


