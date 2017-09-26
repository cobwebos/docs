---
title: "使用 DMV 监视工作负荷 | Microsoft 文档"
description: "了解如何使用 DMV 监视工作负荷。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 69ecd479-0941-48df-b3d0-cf54c79e6549
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 7ce6c2cdf1e28852da536414533ccdcdaeb437e5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/14/2017

---
# <a name="monitor-your-workload-using-dmvs"></a>使用 DMV 监视工作负荷
本文介绍如何使用动态管理视图 (DMV) 在 Azure SQL 数据仓库中监视工作负荷及调查查询执行情况。

## <a name="permissions"></a>权限
若要查询本文中的 DMV，需具有 VIEW DATABASE STATE 或 CONTROL 权限。 通常情况下，首选授予 VIEW DATABASE STATE 权限，因为该权限的限制要大得多。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>监视连接
所有登录到 SQL 数据仓库的操作都记录到 [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions]。  此 DMV 包含最后 10,000 个登录。  session_id 是主键，每次进行新的登录时按顺序分配。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>监视查询执行
在 SQL 数据仓库上执行的所有查询都记录到 [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests]。  此 DMV 包含最后 10,000 个执行的查询。  request_id 对每个查询进行唯一标识，并且为此 DMV 的主键。  request_id 在每次进行新的查询时按顺序分配，并会加上前缀 QID，代表查询 ID。  针对给定 session_id 查询此 DMV 将显示给定登录的所有查询。

> [!NOTE]
> 存储过程使用多个请求 ID。  按先后顺序分配请求 ID。 
> 
> 

以下是调查特定查询的查询执行计划和时间所要遵循的步骤。

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>步骤 1：确定想要调查的查询
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

从前面的查询结果中，记下想要调查的查询的**请求 ID**。

处于**已暂停**状态的查询是指因并发限制而排队的查询。 这些查询也出现在类型为 UserConcurrencyResourceType 的 sys.dm_pdw_waits 等待查询中。 有关并发限制的详细信息，请参阅[并发和工作负荷管理][Concurrency and workload management]。 查询也可能因其他原因（如对象锁定）处于等待状态。  如果查询正在等待资源，请参阅本文后面的[调查等待资源的查询][Investigating queries waiting for resources]。

为了简化在 sys.dm_pdw_exec_requests 表中查找查询的过程，请使用 [LABEL][LABEL] 将注释分配给可在 sys.dm_pdw_exec_requests 视图中查找的查询。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>步骤 2：调查查询计划
使用请求 ID 从 [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps] 检索查询的分布式 SQL (DSQL) 计划。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

当 DSQL 计划的执行时间超出预期时，原因可能是计划很复杂，包含许多 DSQL 步骤，也可能是一个步骤占用很长的时间。  如果计划有很多步骤，包含多个移动操作，可考虑优化表分布，减少数据移动。 [表分布][Table distribution]一文说明了为何必须移动数据才能解决查询问题，并说明了如何使用某些分布策略，尽量减少数据移动。

若要进一步调查单个步骤的详细信息，可检查长时间运行的查询步骤的 *operation_type* 列并记下**步骤索引**：

* 针对以下 **SQL 操作**继续执行步骤 3a：OnOperation、RemoteOperation、ReturnOperation。
* 针对以下**数据移动操作**继续执行步骤 3b：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>步骤 3a：查看分布式数据库上的 SQL
使用请求 ID 和步骤索引从 [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests] 中检索详细信息，其中包含所有分布式数据库上的查询步骤的执行信息。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

当查询步骤正在运行时，可以使用 [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] 从 SQL Server 计划缓存中检索 SQL Server 估计计划，了解在特定分布基础上运行的步骤。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>步骤 3b：查看在分布式数据库上进行的数据移动
使用请求 ID 和步骤索引检索在 [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers] 中的每个分布上运行的数据移动步骤的相关信息。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* 检查 *total_elapsed_time* 列，以查看是否有特定分布在数据移动上比其他分布花费了更多时间。
* 对于长时间运行的分布，请检查 *rows_processed* 列，以查看从该分布移动的行数是否远远多于其他分布。 如果是这样，这可能表示底层数据的偏斜。

如果查询正在运行，则可以使用 [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] 检索特定分发中当前正在运行的 SQL 步骤的 SQL Server 计划高速缓存中的 SQL Server 估计计划。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>监视正在等待的查询
如果查询未取得进展（因其正在等待资源），下面是显示查询正在等待的所有资源的查询。

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

如果查询正在主动等待另一个查询中的资源，则状态将为 **AcquireResources**。  如果查询具有全部所需资源，则状态将为 **Granted**。

## <a name="monitor-tempdb"></a>监视 tempdb
高 tempdb 使用率可能是性能不佳和内存不足问题的根本原因。 首先请检查是否存在数据倾斜或行组性能不佳的情况，然后采取相应措施。 如果发现 tempdb 在查询执行期间达到其限制，请考虑缩放数据仓库。 下面介绍如何确定每个节点上每个查询的 tempdb 使用情况。 

创建以下视图，为 sys.dm_pdw_sql_requests 关联相应的节点 ID。 如此，即可通过 sys.dm_pdw_sql_requests 利用其他直通 DMV 并联接这些表。

```sql
-- sys.dm_pdw_sql_requests with the correct node id
CREATE VIEW sql_requests AS
(SELECT
       sr.request_id,
       sr.step_index,
       (CASE 
              WHEN (sr.distribution_id = -1 ) THEN 
              (SELECT pdw_node_id FROM sys.dm_pdw_nodes WHERE type = 'CONTROL') 
              ELSE d.pdw_node_id END) AS pdw_node_id,
       sr.distribution_id,
       sr.status,
       sr.error_id,
       sr.start_time,
       sr.end_time,
       sr.total_elapsed_time,
       sr.row_count,
       sr.spid,
       sr.command
FROM sys.pdw_distributions AS d
RIGHT JOIN sys.dm_pdw_sql_requests AS sr ON d.distribution_id = sr.distribution_id)
```
运行以下查询来监视 tempdb：

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```
## <a name="monitor-memory"></a>监视内存

内存可能是性能不佳和内存不足问题的根本原因。 首先请检查是否存在数据倾斜或行组性能不佳的情况，然后采取相应措施。 如果发现 SQL Server 内存用量在查询执行期间达到其限制，请考虑缩放数据仓库。

下面的查询将返回每个节点的 SQL Server 内存使用情况和内存压力：   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>监视事务日志大小
下面的查询将返回每个分布区的事务日志大小。 请检查是否存在数据倾斜或行组性能不佳的情况，然后采取相应措施。 如果其中一个日志文件将达到 160 GB，则应考虑扩展实例或限制事务大小。 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
AND counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>监视事务日志回滚
如果查询失败或运行时间较长，可检查并监视是否存在事务回退。
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="next-steps"></a>后续步骤
请参阅[系统视图][System views]，了解 DMV 的详细信息。
有关最佳实践的详细信息，请参阅 [SQL 数据仓库最佳实践][SQL Data Warehouse best practices]

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Concurrency and workload management]: ./sql-data-warehouse-develop-concurrency.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

