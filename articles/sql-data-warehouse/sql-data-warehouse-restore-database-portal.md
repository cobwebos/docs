---
title: 还原 Azure SQL 数据仓库（Azure 门户）| Microsoft Docs
description: 用于还原 Azure SQL 数据仓库的 Azure 门户任务。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6268f500d44bf7ceaaccbcc8e2c2134b42032197
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>还原 Azure SQL 数据仓库（门户）
> [!div class="op_single_selector"]
> * [概述][Overview]
> * [门户][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
在本文中，用户将学习如何使用 Azure 门户还原 Azure SQL 数据仓库。

## <a name="before-you-begin"></a>开始之前
**验证 DTU 容量。** 每个 SQL 数据仓库实例都由一个具有默认数据吞吐量单位 (DTU) 配额的 SQL 服务器（例如 myserver.database.windows.net）托管。 在还原 SQL 数据仓库之前，请验证 SQL Server 剩余 DTU 配额是否足够进行数据库还原。 若要了解如何计算 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改][Request a DTU quota change]。

## <a name="restore-an-active-or-paused-database"></a>还原活动或暂停的数据库
还原数据库：

1. 登录到 [Azure 门户][Azure portal]。
2. 在左侧面板中选择“浏览”，并选择“SQL Server”。

    ![依次选择“浏览”>“SQL Server”](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 查找服务器，并选择它。

    ![选择服务器](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. 查找要从中还原的 SQL 数据仓库实例，并选择它。

    ![选择要还原的 SQL 数据仓库实例](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. 在数据仓库边栏选项卡顶部，选择“还原”。

    ![选择“还原”](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. 指定新**数据库名称**。
7. 选择最新的**还原点**。

   请确保选择最新还原点。 因为还原点按照协调世界时(UTC) 显示，所以默认选项可能不是最新还原点。

      ![选择还原点](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. 选择“确定”。
9. 数据库还原过程将开始，可使用“通知”监视该过程。

> [!NOTE]
> 完成还原后，即可按[在恢复后配置数据库][Configure your database after recovery]中的说明配置恢复的数据库。
>
>

## <a name="restore-a-deleted-database"></a>还原已删除的数据库
还原已删除的数据库：

1. 登录到 [Azure 门户][Azure portal]。
2. 在左侧面板中选择“浏览”，并选择“SQL Server”。

    ![依次选择“浏览”>“SQL Server”](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 查找服务器，并选择它。

    ![选择服务器](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. 向下滚动到服务器边栏选项卡上的“操作”部分。
5. 选择“已删除的数据库”磁贴。

    ![选择“已删除的数据库”磁贴](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. 选择要还原的已删除数据库。

    ![选择要还原的数据库](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. 指定新**数据库名称**。

    ![添加数据库的名称](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. 选择“确定”。
9. 数据库还原过程将开始，可使用“通知”监视该过程。

> [!NOTE]
> 若要在完成还原后配置数据库，请参阅[在恢复后配置数据库][Configure your database after recovery]。
>
>

## <a name="next-steps"></a>后续步骤
若要了解 Azure SQL 数据库版本的业务连续性功能，请阅读 [Azure SQL 数据库业务连续性概述][Azure SQL Database business continuity overview]。

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
