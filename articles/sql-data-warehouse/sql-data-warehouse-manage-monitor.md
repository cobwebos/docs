---
title: DMV를 사용하여 작업 모니터링
description: DMV를 사용하여 작업을 모니터링하는 방법을 알아봅니다.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/23/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 14c4bb843a93fe6d235354f24475b9974142db79
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721143"
---
# <a name="monitor-your-workload-using-dmvs"></a>DMV를 사용하여 작업 모니터링
이 문서에서는 동적 관리 뷰(DMV)를 사용하여 워크로드를 모니터링하는 방법을 설명합니다. 其中包括调查 Azure SQL 数据仓库中的查询执行情况。

## <a name="permissions"></a>권한
이 문서의 DMV를 쿼리하려면 VIEW DATABASE STATE 또는 CONTROL 권한이 필요합니다. 일반적으로 VIEW DATABASE STATE 권한 부여를 선호합니다. 훨씬 제한적이기 때문입니다.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>연결 모니터링
SQL Data Warehouse에 대한 모든 로그인은 [sys.dm_pdw_exec_sessions](https://msdn.microsoft.com/library/mt203883.aspx)에 기록됩니다.  이 DMV에는 마지막 10,000회의 로그인 정보가 포함됩니다.  session_id(기본 키)는 각각의 새 로그인에 대해 순차적으로 할당됩니다.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>쿼리 실행 모니터링
SQL Data Warehouse에 대해 실행되는 모든 쿼리는 [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx)에 기록됩니다.  이 DMV에는 마지막으로 실행한 쿼리 10,000개가 포함됩니다.  이 DMV의 기본 키인 request_id는 각 쿼리를 고유하게 식별합니다.  request_id는 각각의 새 쿼리에 대해 순차적으로 할당되며 쿼리 ID를 나타내는 QID가 접두사로 추가됩니다.  이 DMV에서 지정된 session_id를 쿼리하면 지정된 로그온에 대한 모든 쿼리가 표시됩니다.

> [!NOTE]
> 저장 프로시저는 여러 요청 ID를 사용합니다.  요청 ID는 순차적으로 할당됩니다. 
> 
> 

특정 쿼리에 대한 쿼리 실행 계획 및 시간을 조사하기 위해 수행하는 단계는 다음과 같습니다.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>1단계: 조사하려는 쿼리 식별
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

위의 쿼리 결과에서 조사할 쿼리의 **요청 ID를 적어 둡니다** .

处于**挂起**状态的查询可以排队，因为有大量活动运行的查询。 这些查询还会出现在 UserConcurrencyResourceType 类型的 " [sys. dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)等待查询中。 有关并发限制的信息，请参阅适用于[AZURE SQL 数据仓库的内存和并发限制](memory-concurrency-limits.md)或[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。 쿼리는 개체 잠금 등의 기타 이유로 인해 대기 상태일 수도 있습니다.  쿼리가 리소스를 대기 중인 경우 이 문서 뒷부분의 [리소스를 대기 중인 쿼리 조사](#monitor-waiting-queries) 를 참조하세요.

若要简化对[sys.databases. dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)表中的查询的查找，请使用 "[标签](https://msdn.microsoft.com/library/ms190322.aspx)" 为查询指定注释，该注释可以在 sys.databases. dm_pdw_exec_requests 视图中查找。

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

### <a name="step-2-investigate-the-query-plan"></a>2단계: 쿼리 계획 조사
요청 ID를 사용하여 [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx)에서 쿼리의 DSQL(분산된 SQL) 계획을 검색합니다.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL 계획의 시간이 생각보다 오래 걸리는 경우 계획이 여러 DSQL 단계를 포함하여 복잡하거나 한 단계에 시간이 오래 걸리는 것일 수 있습니다.  계획에 많은 단계가 포함되어 있으며 여러 이동 작업이 수행되는 경우에는 테이블 분산을 최적화하여 데이터 이동을 줄일 수 있습니다. [表分布](sql-data-warehouse-tables-distribute.md)一文说明了为何必须移动数据才能解决查询。 本文还介绍了一些用于最大程度减少数据移动的分发策略。

한 단계에서 추가 세부 정보를 조사하려면 오래 실행되는 쿼리 단계의 *operation_type* 열을 확인하고 **단계 인덱스**를 적어 둡니다.

* OnOperation, RemoteOperation, ReturnOperation 등의 **SQL 작업**에 대해 3a단계를 진행합니다.
* ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation 등의 **데이터 이동 작업**에 대해 3b단계를 진행합니다.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>3a단계: 분산 데이터베이스에서 SQL 조사
요청 ID와 단계 인덱스를 사용하여 [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx)에서 세부 정보를 검색합니다. 이 보기에는 모든 분산 데이터베이스에 대한 쿼리 단계의 실행 정보가 포함되어 있습니다.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

쿼리 단계가 실행되고 있으면 [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)을 사용하여 특정 분산에서 현재 실행 중인 단계에 대해 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>3b단계: 분산 데이터베이스에서 데이터 이동 조사
요청 ID 및 단계 인덱스를 사용하여 [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx)에서 각 분산에 대해 실행 중인 데이터 이동 단계에 대한 정보를 검색합니다.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* *total_elapsed_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다.
* 장기 실행 배포의 경우 *rows_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 그렇다면 이 결과는 기본 데이터의 왜곡을 나타낼 수 있습니다.

如果查询正在运行，则可以使用[DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)从 SQL Server 计划缓存中检索特定分发中当前正在运行的 SQL 步骤的 SQL Server 估算计划。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>대기 중인 쿼리 모니터링
쿼리가 리소스를 대기하는 중이어서 진행되고 있지 않은 경우, 쿼리가 대기 중인 모든 리소스를 표시하는 쿼리는 다음과 같습니다.

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

쿼리가 적극적으로 다른 쿼리의 리소스를 대기 중인 경우 상태는 **AcquireResources**입니다.  쿼리가 필요한 리소스를 모두 가지고 있으면 상태는 **Granted**입니다.

## <a name="monitor-tempdb"></a>tempdb 모니터링
Tempdb 用于在执行查询时保存中间结果。 Tempdb 数据库的使用率很高可能导致查询性能下降。 对于 tempdb，Azure SQL 数据仓库中的每个节点都有大约 1 TB 的原始空间。 下面是用于监视 tempdb 使用情况以及在查询中减小 tempdb 使用情况的提示。 

### <a name="monitoring-tempdb-with-views"></a>用视图监视 tempdb
若要监视 tempdb 使用情况，请首先从[适用于 Sql 数据仓库的 Microsoft 工具包](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)安装[vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql)视图。 然后，可以执行以下查询来查看所有已执行查询的每个节点的 tempdb 使用情况：

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

如果查询正在消耗大量内存或收到一条与 tempdb 的分配相关的错误消息，则可能是[CREATE TABLE 由于 select （CTAS）](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)或[INSERT select](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)语句正在运行，但在最终的数据移动操作中失败。 这通常可以在分布式查询计划中标识为最后一个 INSERT SELECT 之前的 ShuffleMove 操作。  [Dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)使用 ShuffleMove 监视操作。 

最常见的缓解措施是将 CTAS 或 INSERT SELECT 语句分割为多个 load 语句，以便数据量不会超过每节点 tempdb 的最大限制。 你还可以将群集缩放到更大的大小，这会将 tempdb 大小分散到多个节点上，从而减少每个节点上的 tempdb。

除了 CTAS 和 INSERT SELECT 语句外，运行内存不足的大型复杂查询也可能溢出到 tempdb 中，导致查询失败。  请考虑使用较大的[资源类](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management)运行，以避免溢出到 tempdb 中。

## <a name="monitor-memory"></a>메모리 모니터링

메모리는 성능 저하 및 메모리 부족 문제의 근본 원인일 수 있습니다. SQL Server 메모리 사용량이 쿼리 실행 중 한계에 도달한 것을 발견한 경우 데이터 웨어하우스를 확장하는 것이 좋습니다.

다음 쿼리는 노드당 SQL Server 메모리 사용량 및 메모리 부족을 반환합니다.   
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
## <a name="monitor-transaction-log-size"></a>트랜잭션 로그 크기 모니터링
다음 쿼리는 각 배포에서 트랜잭션 로그 크기를 반환합니다. 로그 파일 중 하나가 160GB에 도달하는 경우 인스턴스를 확장하거나 트랜잭션 크기를 제한해야 합니다. 
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
## <a name="monitor-transaction-log-rollback"></a>트랜잭션 로그 롤백 모니터링
쿼리가 실패하거나 진행하는 데 시간이 오래 걸리는 경우 트랜잭션 롤백이 있는지 확인하고 모니터링할 수 있습니다.
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

## <a name="next-steps"></a>다음 단계
有关 Dmv 的详细信息，请参阅[系统视图](./sql-data-warehouse-reference-tsql-system-views.md)。
