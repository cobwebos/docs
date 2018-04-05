---
title: 分析工作负荷 - Azure SQL 数据仓库 | Microsoft Docs
description: 分析针对 Azure SQL 数据仓库中工作负荷的查询优化的技巧。
services: sql-data-warehouse
author: sqlmojo
manager: jhubbard
ms.topic: conceptual
ms.component: manage
ms.date: 03/28/2018
ms.author: joeyong
ms.reviewer: jrj
ms.openlocfilehash: 7fa5bbd8d9a50bb1dcd1ab5be73f4e248cbbf8fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="analyze-your-workload"></a>分析工作负荷
分析针对 Azure SQL 数据仓库中工作负荷的查询优化的技巧。

## <a name="workload-groups"></a>工作负荷组 
SQL 数据仓库通过使用工作负荷组来实现资源类。 总共有八个工作负荷组用于控制不同 DWU 大小的资源类的行为。 对于任何 DWU，SQL 数据仓库只使用八个工作负荷组中的四个。 这样做是合理的，因为每个工作负荷组会被分配到以下四个资源类中的一个：smallrc、mediumrc、largerc 或 xlargerc。 了解工作负荷组的重要性在于其中一些工作负荷组已设置为较高的重要性。 重要性用于 CPU 计划。 重要性为高的查询在运行时所获得的 CPU 周期数将是重要性为中的查询的 3 倍。 因此，并发槽映射也决定了 CPU 优先级。 如果某个查询使用了至少 16 个槽，则该查询是作为重要性高的查询运行的。

下表显示了每个工作负荷组的重要性映射。

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>并发槽和重要性的工作负荷组映射

| 工作负荷组 | 并发槽映射 | MB/分布区（弹性） | MB/分布区（计算） | 重要性映射 |
|:---------------:|:------------------------:|:------------------------------:|:---------------------------:|:------------------:|
| SloDWGroupC00   | 1                        |    100                         | 250                         | 中型             |
| SloDWGroupC01   | 2                        |    200                         | 500                         | 中型             |
| SloDWGroupC02   | 4                        |    400                         | 1000                        | 中型             |
| SloDWGroupC03   | 8                        |    800                         | 2000                        | 中型             |
| SloDWGroupC04   | 16                       |  1,600                         | 4000                        | 高               |
| SloDWGroupC05   | 32                       |  3,200                         | 8000                        | 高               |
| SloDWGroupC06   | 64                       |  6,400                         | 16,000                      | 高               |
| SloDWGroupC07   | 128                      | 12,800                         | 32,000                      | 高               |
| SloDWGroupC08   | 256                      | 25,600                         | 64,000                      | 高               |

<!-- where are the allocation and consumption of concurrency slots charts? -->
从**分配和使用并发槽**图中可以看到，对于 smallrc、mediumrc、largerc 和 xlargerc，DW500 分别使用 1、4、8 和 16 个并发槽。 可以在上面的图中查找这些值，以找到每个资源类的重要性。

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 的资源类到重要性的映射
| 资源类 | 工作负荷组 | 使用的并发槽数 | MB / 分布区 | Importance |
|:-------------- |:-------------- |:----------------------:|:-----------------:|:---------- |
| smallrc        | SloDWGroupC00  | 1                      | 100               | 中型     |
| mediumrc       | SloDWGroupC02  | 4                      | 400               | 中型     |
| largerc        | SloDWGroupC03  | 8                      | 800               | 中型     |
| xlargerc       | SloDWGroupC04  | 16                     | 1,600             | 高       |
| staticrc10     | SloDWGroupC00  | 1                      | 100               | 中型     |
| staticrc20     | SloDWGroupC01  | 2                      | 200               | 中型     |
| staticrc30     | SloDWGroupC02  | 4                      | 400               | 中型     |
| staticrc40     | SloDWGroupC03  | 8                      | 800               | 中型     |
| staticrc50     | SloDWGroupC03  | 16                     | 1,600             | 高       |
| staticrc60     | SloDWGroupC03  | 16                     | 1,600             | 高       |
| staticrc70     | SloDWGroupC03  | 16                     | 1,600             | 高       |
| staticrc80     | SloDWGroupC03  | 16                     | 1,600             | 高       |

## <a name="view-workload-groups"></a>查看工作负荷组
在进行故障诊断时，可以使用以下 DMV 查询，从资源调控器的角度来详细查看内存资源分配的差异，或者分析工作负荷组目前的和历史上的使用情况：

```sql
WITH rg
AS
(   SELECT  
     pn.name                                AS node_name
    ,pn.[type]                              AS node_type
    ,pn.pdw_node_id                         AS node_id
    ,rp.name                                AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                                AS group_name
    ,wg.importance                          AS group_importance
    ,wg.request_max_memory_grant_percent    AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                             AS group_max_dop
    ,wg.effective_max_dop                   AS group_effective_max_dop
    ,wg.total_request_count                 AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
    AND     rp.name    = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
        node_name
,       group_request_max_memory_grant_pcnt
,       group_importance
;
```

## <a name="queued-query-detection-and-other-dmvs"></a>对排队的查询进行的检测，以及其他 DMV
可以使用 `sys.dm_pdw_exec_requests` DMV 来确定在并发队列中等待的查询。 正在等待并发槽的查询的状态为**挂起**。

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

可以使用 `sys.database_principals` 来查看工作负荷管理角色。

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

以下查询显示分配给每个用户的角色。

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL 数据仓库具有以下等待类型：

* **LocalQueriesConcurrencyResourceType**：位于并发槽框架外部的查询。 DMV 查询和 `SELECT @@VERSION` 等系统函数是本地查询的示例。
* **UserConcurrencyResourceType**：位于并发槽框架内部的查询。 针对最终用户表的查询代表使用此资源类型的示例。
* **DmsConcurrencyResourceType**：数据移动操作生成的等待。
* **BackupConcurrencyResourceType**：此等待表明正在备份数据库。 此资源类型的最大值为 1。 如果同时请求了多个备份，其他备份会排队。

可以使用 `sys.dm_pdw_waits` DMV 来查看请求所等待的具体资源。

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV 仅显示给定查询所占用的资源等待。 资源等待时间只度量等待提供资源的时间，与信号等待时间相反，后者是基础 SQL Server 将查询调度到 CPU 所需的时间。

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```
还可以使用 `sys.dm_pdw_resource_waits` DMV 计算已授予的并发槽数。

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots 
FROM    sys.[dm_pdw_resource_waits] 
WHERE   [state]           = 'Granted' 
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

可以使用 `sys.dm_pdw_wait_stats` DMV 对等待进行历史趋势分析。

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>后续步骤
有关如何管理数据库用户和安全性的详细信息，请参阅[保护 SQL 数据仓库中的数据库][Secure a database in SQL Data Warehouse]。 有关如何通过更大型资源类来改进聚集列存储索引质量的详细信息，请参阅[重新生成索引以提升段质量]。

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[重新生成索引以提升段质量]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
