---
title: "从自动备份还原 Azure SQL 数据库（Azure 门户）| Microsoft 文档"
description: "从自动备份还原 Azure SQL 数据库（Azure 门户）。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
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
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>使用 Azure 门户从自动备份还原 Azure SQL 数据库
> [!div class="op_single_selector"]
> * [概述](sql-database-recovery-using-backups.md#geo-restore)
> * [异地还原：PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

本文演示了如何使用 Azure 门户通过[异地还原](sql-database-recovery-using-backups.md#geo-restore)将[自动备份](sql-database-automated-backups.md)中的数据库还原到新服务器中。

## <a name="select-a-database-to-restore"></a>选择要还原的数据库
要在 Azure 门户中还原数据库，请执行以下步骤：

1. 转到 [Azure 门户](https://portal.azure.com)。
2. 在屏幕左侧选择“**+新建**” > “**数据库**” > “**SQL 数据库**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. 选择“**备份**”作为源，然后选择要从中进行恢复的备份。 指定数据库名称、你想要恢复的数据库，然后单击“**创建**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-geo-restore-portal/geo-restore.png)

通过单击页面右上方的通知图标监视恢复操作的状态。

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


