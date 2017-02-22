---
title: "还原已删除的 Azure SQL 数据库（Azure 门户）| Microsoft 文档"
description: "还原已删除的 Azure SQL 数据库（Azure 门户）。"
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
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 9ea5287884cd7f1eb7314002b3ae98c9259686c3


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>使用 Azure 门户还原已删除的 Azure SQL 数据库

## <a name="select-the-database-to-restore"></a>选择要还原的数据库
在 Azure 门户中还原已删除的数据库：

1. 在 [Azure 门户](https://portal.azure.com)中，单击“**更多服务**” > “**SQL 服务器**”。
2. 选择包含要还原的数据库的服务器。
3. 向下滚动到服务器边栏选项卡的“**操作**”部分并选择“**已删除的数据库**”：![还原 Azure SQL 数据库](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. 选择要还原的数据库。
5. 指定数据库名称，然后单击“**确定**”：
   
   ![还原 Azure SQL 数据库](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>后续步骤
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](sql-database-business-continuity.md)
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)
* 若要了解更快的恢复选项，请参阅[活动异地复制](sql-database-geo-replication-overview.md)  
* 若要了解如何使用自动备份进行存档，请参阅[数据库复制](sql-database-copy.md)




<!--HONumber=Feb17_HO3-->


