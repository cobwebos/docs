---
title: "还原 Azure SQL 数据仓库（门户）| Microsoft 文档"
description: "用于还原 SQL 数据仓库的 Azure 门户任务。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/21/2016
ms.author: lakshmir;barbkess;sonyama
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cb8cb2b58df5cc209b1f966c792ca0f4082e652


---
# <a name="restore-an-azure-sql-data-warehouse-portal"></a>还原 Azure SQL 数据仓库（门户）
> [!div class="op_single_selector"]
> * [概述][概述]
> * [门户][门户]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

在本文中，你会学习如何使用 Azure 门户还原 Azure SQL 数据仓库。

## <a name="before-you-begin"></a>开始之前
**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 DTU 配额的 SQL 服务器（例如 myserver.database.windows.net）托管。  在还原 SQL 数据仓库之前，请确保 SQL Server 的剩余 DTU 配额足够进行数据库还原。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改][请求 DTU 配额更改]。

## <a name="restore-an-active-or-paused-database"></a>还原活动或暂停的数据库
还原数据库：

1. 登录到[Azure 门户][Azure 门户]
2. 在屏幕左侧选择“浏览”，然后选择“SQL Sever”
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 导航到你的服务器并选择它
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. 查找要从中还原的 SQL 数据仓库，然后选择它
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. 在数据仓库边栏选项卡顶部，单击“还原”
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. 指定新的“数据库名称”
7. 选择最新“还原点”
   
   1. 请确保选择最新还原点。  由于还原点以 UTC 进行显示，因为有时显示的默认选项不是最新还原点。
      
      ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. 单击 **“确定”**
9. 数据库还原过程将随即开始，并且可以使用“通知”监视还原进度

> [!NOTE]
> 完成还原后，即可按[恢复后配置数据库][恢复后配置数据库]中的说明配置恢复的数据库。
> 
> 

## <a name="restore-a-deleted-database"></a>还原已删除的数据库
还原已删除的数据库：

1. 登录到[Azure 门户][Azure 门户]
2. 在屏幕左侧选择“浏览”，然后选择“SQL Sever”
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 导航到你的服务器并选择它
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. 向下滚动到服务器边栏选项卡上的“操作”部分
5. 单击“已删除的数据库”磁贴
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. 选择要还原的已删除数据库
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. 指定新的“数据库名称”
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. 单击 **“确定”**
9. 数据库还原过程将随即开始，并且可以使用“通知”监视还原进度

> [!NOTE]
> 若要在完成还原后配置数据库，请参阅[恢复后配置数据库][恢复后配置数据库]。
> 
> 

## <a name="next-steps"></a>后续步骤
若要了解 Azure SQL 数据库版本的业务连续性功能，请阅读 [Azure SQL 数据库业务连续性概述][Azure SQL 数据库业务连续性概述]。

<!--Image references-->

<!--Article references-->
[Azure SQL 数据库业务连续性概述]: ../sql-database/sql-database-business-continuity.md
[概述]: ./sql-data-warehouse-restore-database-overview.md
[门户]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[恢复后配置数据库]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[请求 DTU 配额更改]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure 门户]: https://portal.azure.com/



<!--HONumber=Nov16_HO3-->


