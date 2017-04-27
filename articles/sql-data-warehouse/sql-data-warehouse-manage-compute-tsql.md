---
title: "在 Azure SQL 数据仓库中借助 T-SQL 进行暂停、恢复、缩放 | Microsoft Docs"
description: "可通过调整 DWU 来提高性能的 Transact-SQL (T-SQL) 任务。 通过在非高峰期缩减性能来节省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/30/2017
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e37a943c775261a6817169c95a931f1b268305d9
ms.lasthandoff: 03/31/2017


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>管理 Azure SQL 数据仓库中的计算能力 (T-SQL)
> [!div class="op_single_selector"]
> * [概述](sql-data-warehouse-manage-compute-overview.md)
> * [门户](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>查看当前的 DWU 设置
若要查看数据库的当前 DWU 设置，请执行以下操作：

1. 在 Visual Studio 中打开“SQL Server 对象资源管理器”。
2. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
3. 从 sys.database_service_objectives 动态管理视图中选择。 下面是一个示例： 

```sql
SELECT
    db.name [Database]
,    ds.edition [Edition]
,    ds.service_objective [Service Objective]
FROM
     sys.database_service_objectives ds
JOIN
    sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>缩放计算
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

若要更改 DWU，请执行以下操作：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 使用 [ALTER DATABASE][ALTER DATABASE] TSQL 语句。 以下示例将数据库 MySQLDW 的服务级别目标设置为 DW1000。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state-and-operation-progress"></a>查看数据库状态和操作进度

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 提交查询以查看数据库状态

```sql
SELECT *
FROM
sys.databases
```

3. 提交查询以查看操作状态

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```

此 DMV 将返回有关对 SQL 数据仓库的各种管理操作的信息，例如操作和操作状态（IN_PROGRESS 或 COMPLETED）。



<a name="next-steps-bk"></a>

## <a name="next-steps"></a>后续步骤
有关其他管理任务，请参阅[管理概述][Management overview]。

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

