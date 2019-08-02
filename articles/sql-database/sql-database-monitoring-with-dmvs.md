---
title: 使用 DMV 监视性能 Azure SQL 数据库 | Microsoft Docs
description: 了解如何通过使用动态管理视图检测常见性能问题来监视 Microsoft Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 12/19/2018
ms.openlocfilehash: 5bddcb89d26566bd2024cbde086b6e35ddaf94ef
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567186"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>使用动态管理视图监视性能 Azure SQL 数据库

Microsoft Azure SQL 数据库支持通过一部分动态管理视图来诊断性能问题，这些问题可能由查询受阻或长时间运行、资源瓶颈、不良查询计划等原因造成。 本主题提供有关如何通过使用动态管理视图检测常见性能问题的信息。

SQL 数据库部分支持三种类别的动态管理视图：

- 与数据库相关的动态管理视图。
- 与执行相关的动态管理视图。
- 与事务相关的动态管理视图。

有关动态管理视图的详细信息，请参阅 SQL Server 联机丛书中的[动态管理视图和功能 (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx)。

## <a name="permissions"></a>权限

在 SQL 数据库中，查询动态管理视图需要 **VIEW DATABASE STATE** 权限。 **VIEW DATABASE STATE** 权限返回有关当前数据库中的所有对象的信息。
若要向特定数据库用户授予 **VIEW DATABASE STATE** 权限，请运行以下查询：

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

在本地 SQL Server 的实例中，动态管理视图会返回服务器状态信息。 在 SQL 数据库中，这些视图会返回只与当前逻辑数据库相关的信息。

## <a name="identify-cpu-performance-issues"></a>识别 CPU 性能问题

如果 CPU 使用率长时间超过 80%，请考虑以下故障排除步骤：

### <a name="the-cpu-issue-is-occurring-now"></a>目前正在发生 CPU 问题

如果目前已出现问题，则可能存在两种情况：

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>存在许多单独的查询，它们共同消耗了很多的 CPU 资源

使用以下查询来识别消耗量靠前的查询哈希：

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>消耗 CPU 的长时间运行的查询仍在运行

使用以下查询来识别这些查询：

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>过去发生了 CPU 问题

如果问题是在过去发生的，你想要执行根本原因分析，请使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。 拥有数据库访问权限的用户可以使用 T-SQL 对查询存储数据执行查询。  查询存储的默认配置使用 1 小时粒度。  使用以下查询来查看 CPU 消耗量较高的查询的活动。 此查询将返回 CPU 消耗量最高的 15 个查询。  请记得更改 `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`：

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

识别出有问题的查询后，可以优化这些查询，以降低 CPU 利用率。  如果没有时间优化查询，可以选择升级数据库的 SLO 来解决问题。

## <a name="identify-io-performance-issues"></a>识别 IO 性能问题

识别 IO 性能问题时，与 IO 问题最相关的等待类型包括：

- `PAGEIOLATCH_*`

  数据文件 IO 问题（包括 `PAGEIOLATCH_SH`、`PAGEIOLATCH_EX`、`PAGEIOLATCH_UP`）。  如果等待类型名称中包含 **IO**，则此类型与某个 IO 问题相关。 如果页面闩锁等待名称中不包含 **IO**，则此类型与不同类型的问题（例如 tempdb 争用）相关。

- `WRITE_LOG`

  事务日志 IO 问题。

### <a name="if-the-io-issue-is-occurring-right-now"></a>如果目前已经出现了 IO 问题

使用 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 或 [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 查看 `wait_type` 和 `wait_time`。

#### <a name="identify-data-and-log-io-usage"></a>识别数据和日志 IO 用量

使用以下查询来识别数据和日志 IO 用量。 如果数据或日志 IO 用量超过 80%，则表示用户对 SQL 数据库服务层级使用了可用 IO。

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

如果已达到 IO 限制，可使用两种选项：

- 选项 1：升级计算大小或服务层级
- 选项 2：识别并优化 IO 消耗量最大的查询。

#### <a name="view-buffer-related-io-using-the-query-store"></a>使用查询存储查看缓冲区相关的 IO

对于选项 2，可以针对缓冲区相关 IO 的查询存储使用以下查询，以查看过去两个小时的跟踪活动：

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>查看 WRITELOG 等待类型的日志 IO 总计

如果等待类型为 `WRITELOG`，请使用以下查询按语句查看日志 IO 总计：

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>识别 `tempdb` 性能问题

识别 IO 性能问题时，与 `tempdb` 问题最相关的等待类型是 `PAGELATCH_*`（而不是 `PAGEIOLATCH_*`）。 但是，出现 `PAGELATCH_*` 等待并不总是意味着发生了 `tempdb` 争用。  这种等待可能还意味着，由于并发请求面向相同的数据页面，发生了用户对象数据页面争用。 若要进一步确认 `tempdb` 争用，请使用 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 确认 wait_resource 值是否以 `2:x:y` 开头，其中，`tempdb` 是数据库 ID，`x` 是文件 ID，`y` 是页面 ID。  

对于 tempdb 争用，常用的方法是减少依赖于 `tempdb` 的重写应用程序代码。  常见的 `tempdb` 使用区域包括：

- 临时表
- 表变量
- 表值参数
- 版本存储使用（特别是与长时间运行的事务关联的用法）
- 包含使用排序、哈希联接和 spool 的查询计划的查询

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>使用表变量和临时表的最相关查询

使用以下查询来识别使用表变量和临时表的最相关查询：

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>识别长时间运行的事务

使用以下查询来识别长时间运行的事务。 长时间运行的事务会阻止版本存储的清理。

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>识别内存授予等待性能问题

如果最相关等待类型为 `RESOURCE_SEMAHPORE`，但你未遇到 CPU 使用率偏高的问题，则可能是出现了内存授予等待问题。

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>确定 `RESOURCE_SEMAHPORE` 等待是否为最相关的等待

使用以下查询来确定 `RESOURCE_SEMAHPORE` 等待是否为最相关的等待

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>识别内存消耗量较高的语句

使用以下查询来识别内存消耗量较高的语句：

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>识别最活跃的 10 个内存授予

使用以下查询来识别最活跃的 10 个内存授予：

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>计算数据库和对象大小

下面的查询将返回数据库的大小（以 MB 为单位）：

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

下面的查询将返回数据库中各个对象的大小（以 MB 为单位）：

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>监视连接

可以使用 [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) 视图检索与特定 Azure SQL 数据库服务器建立的连接的相关信息和每个连接的详细信息。 此外，[sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) 视图在检索有关所有活动用户连接和内部任务的信息时非常有用。
下面的查询将检索当前连接上的信息：

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> 在执行 **sys.dm_exec_requests** 和 **sys.dm_exec_sessions views** 视图时，如果具有对数据库的 **VIEW DATABASE STATE** 权限，会看到数据库上所有正在执行的会话；否则，只会看到当前会话。

## <a name="monitor-resource-use"></a>监视资源使用情况

可以使用 [SQL 数据库 Query Performance Insight](sql-database-query-performance.md) 和 [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) 监视资源使用情况。

也可以使用以下两个视图来监视使用情况：

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

可以在每个 SQL 数据库中使用 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 视图。 **Sys.dm_db_resource_stats** 视图显示相对于服务层级的最新资源使用数据。 CPU、数据 IO、日志写入以及内存的平均百分比每 15 秒记录一次，并保留 1 小时。

由于此视图提供了更精细的资源使用情况，因此首先将 **sys.dm_db_resource_stats** 用于任何当前状态分析或故障排除。 例如，此查询显示过去一小时的当前数据库平均和最大资源使用情况：

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

有关其他查询，请参阅 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 中的示例。

### <a name="sysresourcestats"></a>sys.resource_stats

**master** 数据库中的 [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 视图包含可帮助监视 SQL 数据库在特定服务层级和计算大小的性能。 每 5 分钟收集一次数据，并且会保留大约 14 天。 此视图可用于 SQL 数据库使用资源的方式的长期历史分析。

下图显示一周内每小时的 P2 计算大小高级数据库的 CPU 资源使用情况。 此图形从周一开始，显示 5 个工作日，并显示周末（应用程序上很少发生的情况）。

![SQL 数据库资源使用情况](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

从数据而言，此数据库当前有一个峰值 CPU 负载刚好超过相对于 P2 计算大小的 50% CPU 使用率（星期二中午）。 如果 CPU 是应用程序资源配置文件的决定因素，可以决定 P2 是适当的计算大小以保证工作负荷始终适合。 如果希望应用程序可以随时间增长，最好具有额外的资源缓冲，以便应用程序不会达到性能级别的限制。 如果增加计算大小，则有助于避免当数据库没有足够能力有效处理请求（尤其是在易受延迟影响的环境中）时向客户显示错误。 一个示例是支持应用程序根据数据库调用结果绘制网页的数据库。

其他应用程序类型可能以不同的方式解释同一图形。 例如，如果某个应用程序尝试每天处理工资数据并使用相同的图表，则在 P1 计算大小也许就能让此类“批处理作业”模型正常工作。 P1 计算大小有 100 个 DTU，P2 计算大小有 200 个 DTU。 P1 计算大小提供的性能是 P2 计算大小的一半。 因此，P2 中 50% 的 CPU 使用率相当于 P1 中 100 % 的 CPU 使用率。 如果应用程序没有超时，则作业耗时 2 小时或 2.5 小时完成并不重要（如果今天完成）。 此类别中的应用程序可能使用 P1 计算大小。 可以充分利用一天之中资源使用率较低的时间段，以便“大峰值”可以溢出到当天稍后的某个低谷。 只要作业可以每天按时完成，P1 计算大小就适用于该类型的应用程序（且节省费用）。

Azure SQL 数据库在每个服务器的 **master** 数据库的 **sys.resource_stats** 视图中，公开每个活动数据库的资源耗用信息。 表中的数据以 5 分钟为间隔收集而得。 对于“基本”、“标准”和“高级”服务层级，数据可能需要再耗费 5 分钟才会出现在表中，以使此数据更有利于历史分析而非接近实时的分析。 查询 **sys.resource_stats** 视图，以查看数据库的最近历史记录和验证你选择的保留是否提供了所需的性能。

> [!NOTE]
> 必须连接到 SQL 数据库服务器的 master 数据库，才能查询下面示例中的 sys.resource_stats。

此示例演示如何公开此视图中的数据：

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![sys.resource_stats 目录视图](./media/sql-database-performance-guidance/sys_resource_stats.png)

下面的示例演示可以用不同方式使用 **sys.resource_stats** 目录视图以获取有关 SQL 数据库使用资源的方式的信息：

1. 若要查看数据库 userdb1 过去一周的资源使用情况，可以运行此查询：

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. 若要评估工作负荷与计算大小的适合程度，需要向下钻取资源指标的每个方面：CPU、读取数、写入数、辅助进程数和会话数。 下面是使用 **sys.resource_stats** 的修订查询，用于报告这些资源度量值的平均值和最大值：

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. 使用每个资源指标的平均值和最大值信息，可以评估工作负荷与所选计算大小的适合程度。 通常情况下，**sys.resource_stats** 中的平均值提供了一个用于目标大小的良好基准。 它应该是主要测量标杆。 例如，你可能正在使用 S2 计算大小的“标准”服务层级。 CPU 和 IO 读写的平均使用百分比低于 40%，平均辅助角色数低于 50，平均会话数低于 200。 工作负荷可能适合 S1 计算大小。 很容易查看数据库是否在辅助角色和会话限制内。 若要查看数据库是否适合 CPU 和读写数等更小的计算大小，请将更小计算大小的 DTU 数除以当前计算大小的 DTU 数，并将结果乘以 100：

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    结果是以百分比表示的两个计算大小之间的相对性能差异。 如果资源使用不超出此量，则工作负荷可能适合更低的计算大小。 但是，需要查看资源用量值的所有范围，并按百分比确定数据库工作负荷适合更小计算大小的频率。 以下查询会根据此示例中计算得出的 40% 阈值，输出每个资源维度的适合百分比：

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    可以根据数据库服务层级的情况来确定工作负荷是否适合更小的计算大小。 如果数据库工作负荷目标为 99.9%，而上述查询针对所有三个资源维度返回的值大于 99.9%，则工作负荷可能适合更小的计算大小。

    查看适合性百分比还可以深入分析是否应转到下一个更大的计算大小以满足目标。 例如，userdb1 显示过去一周的如下 CPU 使用率：

   | 平均 CPU 百分比 | 最大 CPU 百分比 |
   | --- | --- |
   | 24.5 |100.00 |

    平均 CPU 大约是计算大小限制的四分之一，这意味着它很适合数据库的计算大小限制。 但是，最大值显示该数据库达到了计算大小的限制。 在这种情况下，是否需要转到下一个更大的计算大小？ 查看工作负荷达到 100% 的次数，并将这种情况与数据库工作负荷目标进行比较。

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    如果对于三个资源维度中的任何一个维度，此查询返回的值小于 99.9%，请考虑转到下一个更大的计算大小，或使用应用程序优化技术来减少 SQL 数据库上的负载。

4. 本练习还应将未来预计的工作负荷增加考虑在内。

对于弹性池，可以使用本节中所述的方法来监视池中的单个数据库。 但也可总体监视该池。 有关信息，请参阅[监视和管理弹性池](sql-database-elastic-pool-manage-portal.md)。

### <a name="maximum-concurrent-requests"></a>最大并发请求数

若要查看并发请求数，请在 SQL 数据库中运行以下 Transact-SQL 查询：

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

若要分析本地 SQL Server 数据库的工作负荷，请修改此查询以筛选要分析的特定数据库。 例如，如果有一个名为 MyDatabase 的本地数据库，此 TRANSACT-SQL 查询返回该数据库中的并发请求计数：

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

这只是某一时刻的快照。 若要更好地了解工作负荷和并发请求需求，需在一定时间内收集多个样本。

### <a name="maximum-concurrent-logins"></a>最大并发登录数

可以通过分析用户和应用程序模式来了解登录频率。 还可以在测试环境中运行实际负荷，确保不会超过本文所讨论的这样或那样的限制。 无法通过单一查询或动态管理视图 (DMV) 了解并发登录计数或历史记录。

如果多个客户端使用相同的连接字符串，该服务也会对每个登录名进行身份验证。 如果 10 个用户使用相同的用户名和密码同时连接到数据库，则会有 10 个并发登录。 此限制仅适用于登录和身份验证期间。 如果相同的 10 个用户按顺序连接到数据库，则并发登录数将不会大于 1。

> [!NOTE]
> 此限制目前不适用于弹性池中的数据库。

### <a name="maximum-sessions"></a>最大会话数

若要查看当前活动会话数，请在 SQL 数据库中运行以下 Transact-SQL 查询：

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

若要分析本地 SQL Server 工作负荷，可以对查询进行修改，使之专注于特定的数据库。 如果考虑将数据库移至 Azure SQL 数据库，此查询可帮助你确定该数据库可能的会话需求。

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

同样，这些查询将返回时间点计数。 如果在一段内收集多个样本，则会对会话的使用情况有最佳了解。

对于 SQL 数据库分析，也可以通过查询 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 视图并查看 **active_session_count** 列获取会话的历史统计信息。

## <a name="monitoring-query-performance"></a>监视查询性能

缓慢或长时间运行的查询会消耗大量系统资源。 本部分演示如何使用动态管理视图来检测一些常见的查询性能问题。 一个较旧但仍很有帮助的故障排除参考是 Microsoft TechNet 上的[排查 SQL Server 2008 中的性能问题](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx)这篇文章。

### <a name="finding-top-n-queries"></a>查找前 n 个查询

下列示例返回了按平均 CPU 时间排名的前五个查询的信息。 该示例根据查询散列收集了查询，以便逻辑上等值的查询能够根据累积资源消耗分组。

    ```sql
    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
       SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
       MIN(query_stats.statement_text) AS "Statement Text"
    FROM
       (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
           WHEN -1 THEN DATALENGTH(ST.text)
           ELSE QS.statement_end_offset END
           - QS.statement_start_offset)/2) + 1) AS statement_text
    FROM sys.dm_exec_query_stats AS QS
    CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;
    ```

### <a name="monitoring-blocked-queries"></a>监视受阻的查询

缓慢或长时间运行的查询会造成过多的资源消耗并会导致查询受阻。 受阻的原因可能是应用程序设计欠佳、查询计划不良、缺乏有用的索引等。 可以使用 sys.dm_tran_locks 视图来获取有关 Azure SQL 数据库中当前锁定活动的信息。 有关示例代码，请参阅 SQL Server 联机丛书中的 [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx)。

### <a name="monitoring-query-plans"></a>监视查询计划

低效的查询计划还可能会增加 CPU 占用率。 下面的示例使用 [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) 视图来确定哪一查询累积的 CPU 占用率最高。

    ```sql
    SELECT
       highest_cpu_queries.plan_handle,
       highest_cpu_queries.total_worker_time,
       q.dbid,
       q.objectid,
       q.number,
       q.encrypted,
       q.[text]
    FROM
       (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
    ORDER BY highest_cpu_queries.total_worker_time DESC;
    ```

## <a name="see-also"></a>请参阅

[SQL 数据库简介](sql-database-technical-overview.md)
