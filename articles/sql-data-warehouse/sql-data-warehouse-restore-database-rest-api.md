---
title: "还原 Azure SQL 数据仓库 (REST API) | Microsoft 文档"
description: "用于还原 SQL 数据仓库的 REST API 任务。"
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eed4442c5bd7a955e8d380bcb84cd62cd07eca05


---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>还原 Azure SQL 数据仓库 (REST API)
> [!div class="op_single_selector"]
> * [概述][概述]
> * [门户][门户]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

在本文中，你将学习如何使用 REST API 还原 Azure SQL 数据仓库。

## <a name="before-you-begin"></a>开始之前
**验证 DTU 容量。** 每个 SQL 数据仓库都由一个具有默认 DTU 配额的 SQL 服务器（例如 myserver.database.windows.net）托管。  在还原 SQL 数据仓库之前，请确保 SQL Server 的剩余 DTU 配额足够进行数据库还原。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改][请求 DTU 配额更改]。

## <a name="restore-an-active-or-paused-database"></a>还原活动或暂停的数据库
还原数据库：

1. 使用“获取数据库还原点”操作获取数据库还原点列表。
2. 使用[创建数据库还原请求][创建数据库还原请求]操作开始还原。
3. 使用[数据库操作状态][数据库操作状态]操作跟踪还原的状态。

> [!NOTE]
> 完成还原后，即可按[恢复后配置数据库][恢复后配置数据库]中的说明配置恢复的数据库。
> 
> 

## <a name="restore-a-deleted-database"></a>还原已删除的数据库
还原已删除的数据库：

1. 使用[列出可还原的已删除数据库][列出可还原的已删除数据库]操作列出所有可还原的已删除数据库。
2. 使用[获取可还原的已删除数据库][获取可还原的已删除数据库]操作获取要还原的已删除数据库的详细信息。
3. 使用[创建数据库还原请求][创建数据库还原请求]操作开始还原。
4. 使用[数据库操作状态][数据库操作状态]操作跟踪还原的状态。

> [!NOTE]
> 若要在完成还原后配置数据库，请参阅[恢复后配置数据库][恢复后配置数据库]。
> 
> 

## <a name="next-steps"></a>后续步骤
若要了解 Azure SQL 数据库版本的业务连续性功能，请阅读 [Azure SQL 数据库业务连续性概述][Azure SQL 数据库业务连续性概述]。

<!--Image references-->

<!--Article references-->
[Azure SQL 数据库业务连续性概述]: ../sql-database/sql-database-business-continuity.md
[请求 DTU 配额更改]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[恢复后配置数据库]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[如何安装和配置 Azure PowerShell]: ./powershell-install-configure.md
[概述]: ./sql-data-warehouse-restore-database-overview.md
[门户]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[创建数据库还原请求]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[数据库操作状态]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[获取可还原的已删除数据库]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[列出可还原的已删除数据库]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure 门户]: https://portal.azure.com/
[Microsoft Web 平台安装程序]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


