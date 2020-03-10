---
title: 使用 Dmv 监视 SQL 池工作负荷
description: 了解如何使用 Dmv 监视 Azure Synapse Analytics SQL 池工作负荷和查询执行情况。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/23/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: f00ab883f9e2b1365c4e7486d61b55157cecb2a7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383727"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>使用 Dmv 监视 Azure Synapse Analytics SQL 池工作负荷

本文介绍如何使用动态管理视图（Dmv）监视工作负荷，包括调查 SQL 池中的查询执行情况。

## <a name="permissions"></a>权限

若要查询本文中的 Dmv，你需要 "**查看数据库状态**" 或 "**控制**" 权限。 通常，授予**VIEW DATABASE 状态**是首选权限，因为它的限制更多。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>监视连接

数据仓库的所有登录名都将记录到[sys.databases. dm_pdw_exec_sessions](https://msdn.microsoft.com/library/mt203883.aspx)。  此 DMV 包含最后 10,000 个登录。  session_id 是主键，每次进行新的登录时按顺序分配。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>监视查询执行

在 SQL 池上执行的所有查询都记录到[sys.databases. dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx)。  此 DMV 包含最后 10,000 个执行的查询。  request_id 对每个查询进行唯一标识，并且为此 DMV 的主键。  request_id 在每次进行新的查询时按顺序分配，并会加上前缀 QID，代表查询 ID。  针对给定 session_id 查询此 DMV 会显示给定登录的所有查询。

> [!NOTE]
> 存储过程使用多个请求 ID。  按先后顺序分配请求 ID。

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

```

从前面的查询结果中，记下想要调查的查询的**请求 ID**。

处于**挂起**状态的查询可以排队，因为有大量活动运行的查询。 这些查询还会出现在 UserConcurrencyResourceType 类型的 " [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)等待查询中。 有关并发限制的信息，请参阅[内存和并发限制](memory-concurrency-limits.md)或[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。 查询也可能因其他原因（如对象锁定）处于等待状态。  如果查询正在等待资源，请参阅本文后面的[调查等待资源的查询](#monitor-waiting-queries)。

若要简化对[sys.databases. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)表中的查询的查找，请使用 "[标签](https://msdn.microsoft.com/library/ms190322.aspx)" 为查询指定注释，该注释可以在 sys.databases. dm_pdw_exec_requests 视图中查找。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>步骤 2：调查查询计划

使用请求 ID 从 [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx) 检索查询的分布式 SQL (DSQL) 计划。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

当 DSQL 计划的执行时间超出预期时，原因可能是计划很复杂，包含许多 DSQL 步骤，也可能是一个步骤占用很长的时间。  如果计划有很多步骤，包含多个移动操作，可考虑优化表分布，减少数据移动。 [表分布](sql-data-warehouse-tables-distribute.md)一文说明了为何必须移动数据才能解决查询。 本文还介绍了一些用于最大程度减少数据移动的分发策略。

若要进一步调查单个步骤的详细信息，可检查长时间运行的查询步骤的 *operation_type* 列并记下**步骤索引**：

* 针对以下 **SQL 操作**继续执行步骤 3a：OnOperation、RemoteOperation、ReturnOperation。
* 针对以下**数据移动操作**继续执行步骤 3b：ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation。

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>步骤3：调查分布式数据库上的 SQL

使用请求 ID 和步骤索引从 [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx) 中检索详细信息，其中包含所有分布式数据库上的查询步骤的执行信息。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

当查询步骤正在运行时，可以使用 [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx) 从 SQL Server 计划缓存中检索 SQL Server 估计计划，了解在特定分布基础上运行的步骤。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>步骤4：调查分布式数据库上的数据移动
使用请求 ID 和步骤索引检索在 [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx) 中的每个分布上运行的数据移动步骤的相关信息。

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* 检查 *total_elapsed_time* 列，以查看是否有特定分布在数据移动上比其他分布花费了更多时间。
* 对于长时间运行的分布，请检查 *rows_processed* 列，以查看从该分布移动的行数是否远远多于其他分布。 如果是这样，此发现可能指示基础数据倾斜。

如果查询正在运行，则可以使用[DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)从 SQL Server 计划缓存中检索特定分发中当前正在运行的 SQL 步骤的 SQL Server 估算计划。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
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

Tempdb 用于在执行查询时保存中间结果。 Tempdb 数据库的使用率很高可能导致查询性能下降。 SQL 池中的每个节点对于 tempdb 有大约 1 TB 的原始空间。 下面是用于监视 tempdb 使用情况以及在查询中减小 tempdb 使用情况的提示。 

### <a name="monitoring-tempdb-with-views"></a>用视图监视 tempdb

若要监视 tempdb 使用情况，请首先从适用于 SQL 的[Microsoft 工具包](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)安装[vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql)视图。 然后，可以执行以下查询来查看所有已执行查询的每个节点的 tempdb 使用情况：

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
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```

如果查询正在消耗大量内存或收到一条与 tempdb 的分配相关的错误消息，则可能是[CREATE TABLE 由于 select （CTAS）](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)或[INSERT select](/sql/t-sql/statements/insert-transact-sql)语句正在运行，但在最终的数据移动操作中失败。 这通常可以在分布式查询计划中标识为最后一个 INSERT SELECT 之前的 ShuffleMove 操作。  [Dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)使用 ShuffleMove 监视操作。 

最常见的缓解措施是将 CTAS 或 INSERT SELECT 语句分割为多个 load 语句，以便数据量不会超过每节点 tempdb 的最大限制。 你还可以将群集缩放到更大的大小，这会将 tempdb 大小分散到多个节点上，从而减少每个节点上的 tempdb。

除了 CTAS 和 INSERT SELECT 语句外，运行内存不足的大型复杂查询也可能溢出到 tempdb 中，导致查询失败。  请考虑使用较大的[资源类](resource-classes-for-workload-management.md)运行，以避免溢出到 tempdb 中。

## <a name="monitor-memory"></a>监视内存

内存可能是性能不佳和内存不足问题的根本原因。 如果发现 SQL Server 内存用量在查询执行期间达到其限制，请考虑缩放数据仓库。

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
下面的查询将返回每个分布区的事务日志大小。 如果其中一个日志文件将达到 160 GB，则应考虑纵向扩展实例或限制事务大小。

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

## <a name="monitor-polybase-load"></a>监视 PolyBase 负载

下面的查询对负载进度进行了大致估计。 查询仅显示当前正在处理的文件。 

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>后续步骤

有关 Dmv 的详细信息，请参阅[系统视图](sql-data-warehouse-reference-tsql-system-views.md)。
