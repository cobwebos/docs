---
title: 服务器参数–超大规模 (Citus) -Azure Database for PostgreSQL
description: 超大规模中的参数 (Citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336235"
---
# <a name="server-parameters"></a>服务器参数

有多种服务器参数会影响超大规模 (Citus) 的行为，无论是标准 PostgreSQL，还是特定于超大规模 (Citus) 。
可以在超大规模 (Citus) 服务器组的 Azure 门户中设置这些参数。 在 " **设置** " 类别下，选择 **辅助角色节点** 参数或 **协调器节点参数**。 这些页允许为所有辅助角色节点设置参数，或仅为协调器节点设置参数。

## <a name="hyperscale-citus-parameters"></a>超大规模 (Citus) 参数

> [!NOTE]
>
> 运行旧版 Citus 引擎的超大规模 (Citus) 服务器组可能不提供以下列出的所有参数。

### <a name="general-configuration"></a>常规配置

#### <a name="citususe_secondary_nodes-enum"></a>citus) 使用 \_ 辅助 \_ 节点 (枚举

设置选择查询的节点时要使用的策略。 如果设置为 "always"，则 planner 将仅查询 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中标记为 "辅助" noderole 的节点。

此枚举支持的值包括：

-   **从不：** (默认值) 所有读取发生在主节点上。
-   **始终：** 读取是对辅助节点运行的，而 insert/update 语句则禁用。

#### <a name="cituscluster_name-text"></a>citus \_ (文本) 的群集名称

向协调器节点 planner 通知它所协调的群集。 设置群集 \_ 名称后，planner 会单独查询群集中的辅助节点。

#### <a name="citusenable_version_checks-boolean"></a>citus \_ \_ (布尔) 启用版本检查

升级超大规模 (Citus) 版本需要服务器重启 (，以便选取新的共享库) ，然后执行 ALTER EXTENSION UPDATE 命令。  同时执行这两个步骤可能会导致错误或故障。
超大规模 (Citus) 因此验证代码版本和扩展的版本，如果不是，则会出现错误 \' 。

此值默认为 true，并且在协调器上有效。 在极少数情况下，复杂的升级过程可能需要将此参数设置为 false，从而禁用检查。

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus \_ 分布式 \_ 死锁 \_ 检测 (布尔) 

是否在服务器日志中记录分布式死锁检测相关处理。 默认值为 false。

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus \_ 浮点 (分布式死锁 \_ 检测 \_ 因子) 

设置在检查分布式死锁之前要等待的时间。 特别要等待的时间将是此值乘以 PostgreSQL \' s [死锁 \_ 超时](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 设置。 默认值是 `2`。 值 `-1` 禁用分布式死锁检测。

#### <a name="citusnode_connection_timeout-integer"></a>citus \_ 连接 \_ 超时 (整数) 

`citus.node_connection_timeout`GUC 将以毫秒为单位的最长持续时间 () 以等待建立连接。 如果在至少建立了一个辅助进程连接之前超时，超大规模 (Citus) 将引发错误。 此 GUC 影响从协调器到工作人员的连接，以及彼此之间的连接。

-   默认值：5秒
-   最小值：10毫秒
-   最大值：1小时

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>查询统计信息

#### <a name="citusstat_statements_purge_interval-integer"></a>citus \_ 语句 \_ 清除 \_ 间隔 (整数) 

设置维护守护程序从不匹配的 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) 中删除记录的频率 `pg_stat_statements` 。 此配置值设置每次清除的时间间隔（以秒为单位），默认值为10。 值0将禁用清除。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

此参数在协调器中有效，并且可在运行时进行更改。

### <a name="data-loading"></a>数据加载

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus)  (枚举的多个 \_ 分片 \_ 提交 \_ 协议

设置在哈希分布式表中执行复制时使用的提交协议。 在每个单独的分片位置上，复制在事务块中执行，以确保在复制过程中发生错误时不会引入任何数据。 但是，有一种特殊的故障情况，即复制在所有位置都成功完成，但在提交所有事务之前) 故障的 (硬件。 此参数可用于在以下提交协议之间进行选择，以防止在这种情况下数据丢失：

-   **2pc：** (默认) 在分片放置中执行复制的事务首先使用 PostgreSQL 的 \' [两阶段提交](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) 来准备，然后提交。 可以使用 "提交准备就绪" 或 "已准备回滚" 手动恢复或中止失败的提交。
    使用2pc 时，应在所有辅助角色上增加已 [准备好的最大 \_ \_ 事务](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) 数，通常为与最大连接相同的值 \_ 。
-   **1pc：** 在分片放置中执行复制的事务在单个轮上提交。 如果在 (罕见) 的所有放置后，提交失败，则数据可能会丢失。

#### <a name="citusshard_replication_factor-integer"></a>citus \_ \_)  (整数

设置分片的复制因子，即，将在其上放置分片的节点数，默认值为1。 此参数可在运行时设置，并且在协调器上有效。 此参数的理想值取决于群集的大小和节点故障率。  例如，如果你运行大型群集并更频繁地观察节点故障，你可能需要提高此复制因子。

#### <a name="citusshard_count-integer"></a>citus 分片 \_ (整数) 

为哈希分区表设置分片计数，默认值为32。  此值由 [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF 在创建哈希分区表时使用。 此参数可在运行时设置，并且在协调器上有效。

#### <a name="citusshard_max_size-integer"></a>citus \_ \_)  (整数的最大大小

设置分片在被拆分之前会增长到的最大大小，默认值为 1 GB。 如果源文件的 \' 大小 (用于一个分片的过渡) 超过此配置值，则数据库将确保创建新的分片。 此参数可在运行时设置，并且在协调器上有效。

### <a name="planner-configuration"></a>Planner 配置

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus \_ 子句 \_ 行 \_ 提取 \_ 计数 (整数) 

为 limit 子句优化设置每个任务要提取的行数。
在某些情况下，选择带有限制子句的查询可能需要提取每个任务中的所有行来生成结果。 在这种情况下，如果某个近似值产生有意义的结果，此配置值将设置从每个分片提取的行数。 默认情况下，将禁用限制近似值，此参数设置为-1。 此值可在运行时设置，并且在协调器上有效。

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus \_ 浮点 (的非重复 \_ 错误率 \_) 

超大规模 (Citus) 可以计算 (distinct) ~ 使用 postgresql-hll 扩展的计数。 当计算 (distinct) 计算计数时，此配置项设置所需的错误率。 0.0 （默认值）禁用 (distinct) 的计数的近似值;和1.0 不保证结果的准确性。 建议将此参数设置为0.005 以获得最佳结果。 此值可在运行时设置，并且在协调器上有效。

#### <a name="citustask_assignment_policy-enum"></a>citus \_ \_ 枚举 () 的任务分配策略

> [!NOTE]
> 仅当 [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) 大于一，或针对 [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)的查询时，此 GUC 才适用。

设置向工作线程分配任务时要使用的策略。 协调器将任务分配给基于分片位置的辅助角色。 此配置值指定进行这些分配时要使用的策略。
目前可以使用三种可能的任务分配策略。

-   **贪婪：** 默认情况下，贪心策略是默认值，旨在跨辅助角色平均分配任务。
-   **轮循机制：** 轮循机制策略将任务以循环方式分配给不同的副本。 当表的分片计数比辅助角色的数量低时，此策略可提高群集利用率。
-   **第一个副本：** 第一个副本策略基于插入顺序分配任务， (副本) 分片。 换言之，分片的片段查询分配给具有该分片的第一个副本的辅助角色。
    利用此方法，您可以更好地保证哪些分片将用于哪些节点 (即，更强的内存驻留保证) 。

此参数可在运行时设置，并且在协调器上有效。

### <a name="intermediate-data-transfer"></a>中间数据传输

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus \_)  (的二进制辅助角色 \_ 复制 \_ 格式

使用二进制复制格式在辅助角色之间传输中间数据。
在大表联接过程中，超大规模 (Citus) 可能必须在不同的辅助角色之间动态重新分区和无序播放数据。 默认情况下，将以文本格式传输此数据。 如果启用此参数，则指示数据库使用 PostgreSQL 的二进制序列化格式传输此数据。 此参数对辅助角色有效，需要在 postgresql 文件中进行更改。 编辑完配置文件后，用户可以发送 SIGHUP 信号或重新启动服务器以使此更改生效。

#### <a name="citusbinary_master_copy_format-boolean"></a>citus \_ master \_ copy \_ 格式 (布尔) 

使用二进制复制格式在协调器和辅助角色之间传输数据。 运行分布式查询时，工作线程将其中间结果传输到用于最终聚合的协调器。 默认情况下，将以文本格式传输此数据。 如果启用此参数，则指示数据库使用 PostgreSQL 的二进制序列化格式传输此数据。
此参数可在运行时设置，并且对协调器有效。

#### <a name="citusmax_intermediate_result_size-integer"></a>citus (整数的最大 \_ 中间 \_ 结果 \_ 大小) 

无法向下推送到辅助角色节点以执行的 Cte 的中间结果的最大大小（KB）。 默认值为 1 GB，值为-1 表示无限制。
超出限制的查询将被取消，并生成错误消息。

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus \_ \_ (布尔) 启用 ddl 传播

指定是否将 DDL 更改从协调器自动传播到所有工作线程。 默认值为 true。 由于某些架构更改需要表的访问排他锁，并且因为自动传播按顺序应用到所有工作线程，因此它可以使超大规模 (Citus) 群集暂时降低响应能力。 你可以选择禁用此设置并手动传播更改。

### <a name="executor-configuration"></a>执行器配置

#### <a name="general"></a>常规

##### <a name="citusall_modifications_commutative"></a>citus。所有 \_ 修改都是 \_ 交换的

超大规模 (Citus) 强制执行交换性规则，并获取修改操作的相应锁以保证行为的正确性。 例如，假设 INSERT 语句与另一个 INSERT 语句 commutes，而不是与 UPDATE 或 DELETE 语句一起使用。 同样，它假设 UPDATE 或 DELETE 语句不使用另一个 UPDATE 或 DELETE 语句。 此预防措施意味着更新和删除需要超大规模 (Citus) 才能获取更强的锁。

如果有可使用插入或其他更新来交换的 UPDATE 语句，则可以通过将此参数设置为 true 来放宽这些交换性假设。 如果此参数设置为 "true"，则所有命令都被视为可交换的，并声明一个共享锁，这可以提高总体吞吐量。 此参数可在运行时设置，并且对协调器有效。

##### <a name="citusremote_task_check_interval-integer"></a>citus \_ \_ (整数) 的远程任务检查 \_ 间隔

设置超大规模 (Citus) 检查任务跟踪器执行器管理的作业状态的频率。 默认值为10毫秒。 协调器将任务分配给工作人员，然后与他们一起定期检查每个任务的 \' 进度。 此配置值设置两次后续检查之间的时间间隔。 此参数在协调器中有效，并且可在运行时设置。

##### <a name="citustask_executor_type-enum"></a>citus \_ \_)  (枚举的执行器类型

超大规模 (Citus) 具有三个用于运行分布式选择查询的执行器类型。  可以通过设置此配置参数来选择所需的执行器。 此参数的可接受值为：

-   **自适应：** 默认值。 对于涉及跨多个分片的聚合和分散联接的查询，最佳响应是最佳方法。
-   **任务跟踪器：** 任务跟踪器执行程序非常适合用于长时间运行的复杂查询，需要跨工作节点混排数据，并实现高效的资源管理。
-   **实时：** (弃用) 与自适应执行程序具有类似的用途，但不太灵活，并且可能会导致辅助节点上的连接压力更大。

此参数可在运行时设置，并且在协调器上有效。

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus \_ \_ \_ \_) {#multi_task_logging} (枚举的多任务查询日志级别

为生成多个任务 (的任何查询设置日志级别，这些任务将命中多个分片) 。 在多租户应用程序迁移期间，日志记录非常有用，因为您可以选择对此类查询进行错误或警告，以便查找这些查询并向其添加租户 \_ id 筛选器。 此参数可在运行时设置，并且对协调器有效。 此参数的默认值为 \' off \' 。

此枚举支持的值包括：

-   **关闭：** 关闭将生成多个任务的所有查询的日志记录 (也就是说，跨越多个分片) 
-   **调试：** 在调试严重性级别记录语句。
-   **日志：** 日志严重级别的日志语句。 日志行将包含运行的 SQL 查询。
-   **注意：** 记录语句的严重性级别。
-   **警告：** 记录严重级别的日志语句。
-   **错误：** 错误严重级别的日志语句。

`error`在开发测试过程中使用时，以及在 `log` 实际生产部署期间更低的日志级别可能会很有用。
选择 `log` 将导致多任务查询在数据库日志中显示，并在语句后显示查询本身 \" 。\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus \_ \_ (布尔) 启用分区联接

通常，尝试使用自适应执行器执行重新分区联接会失败，并出现错误消息。  但是， `citus.enable_repartition_joins` 如果将设置为 true，则超大规模 (Citus) 暂时切换到任务跟踪器执行程序以执行联接。  默认值是 False。

#### <a name="task-tracker-executor-configuration"></a>任务跟踪器执行程序配置

##### <a name="citustask_tracker_delay-integer"></a>citus \_ (整数) 的任务跟踪器 \_ 延迟

此参数将任务管理舍入和默认值之间的任务跟踪器休眠时间设置为200毫秒。 任务跟踪器进程定期唤醒，遍历分配给它的所有任务，并计划并执行这些任务。  然后，任务跟踪器会在一段时间内休眠，然后再次执行这些任务。
此配置值确定休眠期限的长度。 此参数对辅助角色有效，需要在 postgresql 文件中进行更改。 编辑完配置文件后，用户可以发送 SIGHUP 信号或重新启动服务器以使更改生效。

可以通过减少管理舍入之间的时间间隔，缩短此参数以剪裁由任务跟踪器执行器导致的延迟。
如果分片查询较短，因而会定期更新其状态，则减少延迟非常有用。

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus \_ 分配 \_ 任务 \_ 批 \_ 大小 (整数) 

协调器上的任务跟踪器执行程序将批处理中的任务同步分配给辅助角色上的后台程序。 此参数设置要在单个批处理中分配的最大任务数。 选择更大的批大小可以加快任务分配。 但是，如果工作线程数很大，则所有工作人员可能需要更长时间才能获取任务。  此参数可在运行时设置，并且对协调器有效。

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus \_ \_ \_ \_ (整数) 每个节点最多运行任务

任务跟踪器进程根据需要计划和执行分配给它的任务。 此配置值设置在任意给定时间在一个节点上并发执行的最大任务数，默认值为8。

该限制确保你不 \' 会有许多任务同时到达磁盘，并有助于避免磁盘 i/o 争用。 如果查询是通过内存或 Ssd 来提供的，则可以增加 \_ 每个节点的最大运行 \_ 任务 \_ 数， \_ 而无需担心。

##### <a name="cituspartition_buffer_size-integer"></a>citus \_ \_)  (整数的分区缓冲区大小

设置用于分区操作的缓冲区大小，默认值为 8 MB。
超大规模 (Citus) 允许在联接两个大型表时将表数据重新分区为多个文件。 填充此分区缓冲区后，重新分区的数据将被刷新到磁盘上的文件中。  此配置条目可在运行时设置，在辅助角色上有效。

#### <a name="explain-output"></a>说明输出

##### <a name="citusexplain_all_tasks-boolean"></a>citus 说明 \_ \_ (布尔) 的所有任务

默认情况下，超大规模 (Citus) 显示在分布式查询上运行 [说明](http://www.postgresql.org/docs/current/static/sql-explain.html) 时单个任意任务的输出。 在大多数情况下，说明输出在任务中是类似的。 偶尔，某些任务将以不同的方式进行计划或具有更高的执行时间。 在这种情况下，启用此参数会很有用，之后说明输出将包括所有任务。 说明所有任务可能会导致说明所需的时间更长。

## <a name="postgresql-parameters"></a>PostgreSQL 参数

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) -设置日期和时间值的显示格式
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) -设置间隔值的显示格式
* [时区](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) -设置用于显示和解释时间戳的时区
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) -设置要在统计信息和日志中报告的应用程序名称
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) -启用数组中 NULL 元素的输入
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) -启动 autovacuum 子进程
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) -在作为 reltuples 的一部分进行分析之前插入、更新或删除的元组的数目
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) -在分析之前插入、更新或删除的元组插入、更新或删除的最小数目
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) -autovacuum 之间的睡眠时间
* autovacuum 的[autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY)真空成本延迟（以毫秒为单位）
* napping 之前提供[autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT)真空成本量，适用于 autovacuum
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) -作为 reltuples 的一部分，在真空之前更新或删除的元组的数目
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) -在真空角阀之前更新或删除的元组最小数目
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) -设置每个 autovacuum 工作进程要使用的最大内存
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) -在其之前执行的写入操作将被刷新到磁盘的页数
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) -设置 \' 字符串文本中是否允许 ""
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) 在舍入之间的后台写入器休眠时间
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) -在其之前执行的写入操作将被刷新到磁盘的页数
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -后台写入器每轮要刷新的 lru 页面的最大数量
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) -每个轮出的平均缓冲区使用率的倍数
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) -设置 bytea 的输出格式
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) -在 CREATE 函数过程中检查函数主体
* 在检查点期间刷新脏缓冲的[checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET)时间，作为检查点间隔的一部分
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) -设置自动 WAL 检查点之间的最大时间
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) -如果检查点段比此更频繁，则启用警告
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) -设置客户端的字符集编码
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) -设置发送到客户端的消息级别
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) -设置事务提交与 WAL 到磁盘之间的延迟（以微秒为单位）
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) -在执行之前设置最小并发打开事务 commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) -使规划器可以使用约束来优化查询
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) -设置 planner 在索引扫描过程中处理每个索引条目的成本的估计值
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) -设置 planner 估计处理每个运算符或函数调用的成本
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) -设置 planner 对处理每个元组 (行的成本的估计) 
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) -设置 planner 对要检索的游标行的分数的估计值
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) -设置在检查死锁之前等待锁的时间量（以毫秒为单位）
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) 缩进分析和计划树显示
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -记录每个查询的分析树
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -记录每个查询的执行计划
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -记录每个查询的重写分析树
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) -设置默认统计信息目标
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) -设置要在其中创建表和索引的默认表空间
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) 设置默认文本搜索配置
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) -设置新事务的默认 "延迟" 状态
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) -设置每个新事务的事务隔离级别
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) -设置新事务的默认只读状态
* default_with_oids-默认情况下，使用 Oid 创建新表
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) -设置 planner 关于磁盘缓存大小的假设
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) -启用 planner 使用位图扫描计划
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) -支持 planner 使用集合合并计划
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) -启用 planner 的哈希聚合计划的使用
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) -支持 planner 使用哈希联接计划
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) -启用 planner 仅使用索引扫描计划
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) -支持 planner 使用索引扫描计划
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) -支持 planner 使用具体化
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) -支持 planner 使用合并联接计划
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) -支持 planner 使用嵌套循环联接计划
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) -支持 planner 使用顺序扫描计划
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) -启用 planner 使用显式排序步骤
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) -启用 planner 扫描计划的使用
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) -在普通字符串文本中发出反斜杠转义警告
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -在出现任何错误时终止会话
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) -设置为浮点值显示的位数
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -强制使用并行查询工具
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) -设置不折叠子查询的 from 列表大小
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -启用遗传查询优化
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo：投入用于设置其他 geqo 参数的默认值
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) geqo：算法的迭代次数
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo：人口中的个人人数
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo：随机路径选择的种子
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo：总体内的选择性压力
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) -从使用 geqo 的项中设置阈值
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) -设置 gin 的精确搜索允许的最大结果数
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) -设置 gin 索引的挂起列表的最大大小
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) -设置任何置于空闲状态事务允许的最大持续时间
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) -设置不平展联接构造的 FROM 列表大小
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) -设置用于货币量格式的区域设置
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) -设置格式设置数字的区域设置
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) -在大型对象上启用权限检查的向后兼容模式
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) -设置任何等待锁所允许的最大持续时间 (（以毫秒为) 单位）。 0关闭
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) -设置将记录 autovacuum 操作的最短执行时间
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) -记录每个检查点
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) -记录每个成功的连接
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) -设置服务器日志输出的目标
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -记录会话结束时间，包括持续时间
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) -记录每个已完成 SQL 语句的持续时间
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) -设置记录消息的详细级别
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) -记录长锁等待
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) ，将记录的最短执行时间 (以毫秒) ，超过此时间后将记录语句。 -1 禁用日志记录语句持续时间
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -导致记录在此级别或更高版本上生成错误的所有语句
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) -设置记录的消息级别
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) -记录每个复制命令
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) -设置记录的语句的类型
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) -对于每个查询，将累积性能统计信息写入服务器日志
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) -将临时文件的使用情况记录得超过此千字节数
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) -设置要用于维护操作的最大内存
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) -设置一次可以同时处于活动状态的并行工作线程的最大数目
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) -设置每个执行器节点的最大并行进程数
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) -设置每页的谓词锁定的元组的最大数目
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) -设置每个关系的谓词锁定页和元组的最大数目
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) -设置在热备用服务器处理存档的 WAL 数据时取消查询之前的最大延迟
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) -设置在热备用服务器处理流式处理的 WAL 数据时取消查询之前的最大延迟
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) -每个订阅的最大表同步工作线程数
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) -设置触发检查点的 wal 大小
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) -设置并行扫描的最小索引数据量
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) -设置 wal 收缩的最小大小
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) -为自 PostgreSQL 9.4 以来更改的含义发出警告
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) -设置 planner 为并行查询启动工作进程的成本估计
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) -设置 planner 对每个元组 (行) 从辅助端向后端传递的成本的估计
* [pg_stat_statements 保存](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -跨服务器关闭保存 pg_stat_statements 统计信息
* [pg_stat_statements。跟踪](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -选择要跟踪的语句 pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -选择是否由 pg_stat_statements 跟踪实用工具命令
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) -生成 SQL 片段时，引用所有标识符
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) -设置 planner 对 nonsequentially 提取磁盘页面的成本的估计值
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) -启用行安全性
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) -设置非架构限定名称的架构搜索顺序
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) -设置 planner 对按顺序提取磁盘页面的成本的估计值
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) -设置会话的触发器行为和重写规则
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -导致 "..."要字面对待反斜杠的字符串
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) -设置任意语句允许的最大持续时间 (（以毫秒) 为单位）。 0关闭
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) -启用同步顺序扫描
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) -设置当前事务的同步级别
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -tcp keepalive 重新传输的最大次数
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) 颁发 tcp keepalive 之间的时间
* 在 TCP keepalive 重新传输之间[tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL)时间
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) -设置每个数据库会话使用的临时缓冲区的最大数目
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) -设置表空间 (s) 用于临时表和对文件排序
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) -收集有关执行命令的信息
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) -收集有关数据库活动的统计信息
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) -收集数据库活动的函数级统计信息
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) -收集数据库 i/o 活动的计时统计信息
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -将 "EXPR = null" 视为 "EXPR IS null"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) -真空成本延迟（以毫秒为单位）
* napping 之前可用[vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT)真空成本量
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) 与真空更新的页面的成本
* 在缓冲区缓存中找到的页[vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT)真空成本
* 在缓冲区缓存中找不到页面的[vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS)真空成本
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -应延迟真空度和热清理的事务数（如果有的话）
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -真空应冻结表格行的最小年龄
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) -真空应扫描整个表以冻结元组的 age
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) -真空应在表行中冻结 MultiXactId 的最小年龄
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) -应将整个表扫描到的 multixact age，以冻结元组
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) -将 wal 接收方状态报告之间的最大时间间隔设置为主
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) 在 wal 编写器中执行的 wal 刷新之间的时间
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -由 wal 编写器写入的、触发刷新的 wal 量
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) -设置内部排序操作和哈希表在写入临时磁盘文件之前要使用的内存量
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) -设置二进制值在 XML 中的编码方式
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) -设置隐式分析和序列化操作中的 XML 数据是否被视为文档或内容片段

## <a name="next-steps"></a>后续步骤

* 除服务器参数外，另一种形式的配置是超大规模 (Citus) 服务器组中的资源 [配置选项](concepts-hyperscale-configuration-options.md) 。
* 基础 PostgreSQL 数据库也有 [配置参数](http://www.postgresql.org/docs/current/static/runtime-config.html)。
