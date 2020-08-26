---
title: 服务器参数–超大规模 (超大规模 (Citus) -Azure Database for PostgreSQL
description: 超大规模中的参数 (Citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136735"
---
# <a name="server-parameters"></a>服务器参数

有多种服务器参数会影响超大规模 (Citus) 的行为、标准 PostgreSQL 中的两个参数，以及特定于超大规模 (Citus) 的参数。 若要了解有关 PostgreSQL 配置参数的详细信息，可以访问 PostgreSQL 文档的[运行时配置](http://www.postgresql.org/docs/current/static/runtime-config.html)部分。

本参考的其余部分旨在讨论超大规模 (Citus) 特定的配置参数。 可以在超大规模 (Citus) 服务器组的 "**设置**" 下的 "**辅助角色节点参数**" 下的 Azure 门户中设置这些参数。

> [!NOTE]
>
> 运行旧版本的 Citus 引擎的超大规模服务器组可能不提供以下列出的所有参数。

## <a name="general-configuration"></a>常规配置

### <a name="citususe_secondary_nodes-enum"></a>citus) 使用 \_ 辅助 \_ 节点 (枚举

设置选择查询的节点时要使用的策略。 如果设置为 "always"，则 planner 将仅查询[pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中标记为 "辅助" noderole 的节点。

此枚举支持的值包括：

-   **从不：** (默认值) 所有读取发生在主节点上。
-   **始终：** 读取是对辅助节点运行的，而 insert/update 语句则禁用。

### <a name="cituscluster_name-text"></a>citus \_ (文本) 的群集名称

向协调器节点 planner 通知它所协调的群集。 设置群集 \_ 名称后，planner 会单独查询群集中的辅助节点。

### <a name="citusenable_version_checks-boolean"></a>citus \_ \_ (布尔) 启用版本检查

升级超大规模 (Citus) 版本需要服务器重启 (，以便选取新的共享库) ，然后执行 ALTER EXTENSION UPDATE 命令。  同时执行这两个步骤可能会导致错误或故障。
超大规模 (Citus) 因此验证代码版本和扩展的版本，如果不是，则会出现错误 \' 。

此值默认为 true，并且在协调器上有效。 在极少数情况下，复杂的升级过程可能需要将此参数设置为 false，从而禁用检查。

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus \_ 分布式 \_ 死锁 \_ 检测 (布尔) 

是否在服务器日志中记录分布式死锁检测相关处理。 默认值为 false。

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus \_ 浮点 (分布式死锁 \_ 检测 \_ 因子) 

设置在检查分布式死锁之前要等待的时间。 特别要等待的时间将是此值乘以 PostgreSQL \' s[死锁 \_ 超时](https://www.postgresql.org/docs/current/static/runtime-config-locks.html)设置。 默认值是 `2`。 值 `-1` 禁用分布式死锁检测。

### <a name="citusnode_connection_timeout-integer"></a>citus \_ 连接 \_ 超时 (整数) 

`citus.node_connection_timeout`GUC 将以毫秒为单位的最长持续时间 () 以等待建立连接。 如果在至少建立了一个辅助进程连接之前超时，超大规模 (Citus) 将引发错误。 此 GUC 影响从协调器到工作人员的连接，以及彼此之间的连接。

-   默认值：5秒
-   最小值：10毫秒
-   最大值：1小时

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>查询统计信息

### <a name="citusstat_statements_purge_interval-integer"></a>citus \_ 语句 \_ 清除 \_ 间隔 (整数) 

设置维护守护程序从不匹配的[citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)中删除记录的频率 `pg_stat_statements` 。 此配置值设置每次清除的时间间隔（以秒为单位），默认值为10。 值0将禁用清除。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

此参数在协调器中有效，并且可在运行时进行更改。

## <a name="data-loading"></a>数据加载

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus)  (枚举的多个 \_ 分片 \_ 提交 \_ 协议

设置在哈希分布式表中执行复制时使用的提交协议。 在每个单独的分片位置上，复制在事务块中执行，以确保在复制过程中发生错误时不会引入任何数据。 但是，有一种特殊的故障情况，即复制在所有位置都成功完成，但在提交所有事务之前) 故障的 (硬件。 此参数可用于在以下提交协议之间进行选择，以防止在这种情况下数据丢失：

-   **2pc：** (默认) 在分片放置中执行复制的事务首先使用 PostgreSQL 的 \' [两阶段提交](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html)来准备，然后提交。 可以使用 "提交准备就绪" 或 "已准备回滚" 手动恢复或中止失败的提交。
    使用2pc 时，应在所有辅助角色上增加已[准备好的最大 \_ \_ 事务](http://www.postgresql.org/docs/current/static/runtime-config-resource.html)数，通常为与最大连接相同的值 \_ 。
-   **1pc：** 在分片放置中执行复制的事务在单个轮上提交。 如果在 (罕见) 的所有放置后，提交失败，则数据可能会丢失。

### <a name="citusshard_replication_factor-integer"></a>citus \_ \_)  (整数

设置分片的复制因子，即，将在其上放置分片的节点数，默认值为1。 此参数可在运行时设置，并且在协调器上有效。 此参数的理想值取决于群集的大小和节点故障率。  例如，如果你运行大型群集并更频繁地观察节点故障，你可能需要提高此复制因子。

### <a name="citusshard_count-integer"></a>citus 分片 \_ (整数) 

为哈希分区表设置分片计数，默认值为32。  此值由[create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF 在创建哈希分区表时使用。 此参数可在运行时设置，并且在协调器上有效。

### <a name="citusshard_max_size-integer"></a>citus \_ \_)  (整数的最大大小

设置分片在被拆分之前会增长到的最大大小，默认值为 1 GB。 如果源文件的 \' 大小 (用于一个分片的过渡) 超过此配置值，则数据库将确保创建新的分片。 此参数可在运行时设置，并且在协调器上有效。

## <a name="planner-configuration"></a>Planner 配置

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus \_ 子句 \_ 行 \_ 提取 \_ 计数 (整数) 

为 limit 子句优化设置每个任务要提取的行数。
在某些情况下，选择带有限制子句的查询可能需要提取每个任务中的所有行来生成结果。 在这种情况下，如果某个近似值产生有意义的结果，此配置值将设置从每个分片提取的行数。 默认情况下，将禁用限制近似值，此参数设置为-1。 此值可在运行时设置，并且在协调器上有效。

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus \_ 浮点 (的非重复 \_ 错误率 \_) 

超大规模 (Citus) 可以计算 (distinct) ~ 使用 postgresql-hll 扩展的计数。 当计算 (distinct) 计算计数时，此配置项设置所需的错误率。 0.0 （默认值）禁用 (distinct) 的计数的近似值;和1.0 不保证结果的准确性。 建议将此参数设置为0.005 以获得最佳结果。 此值可在运行时设置，并且在协调器上有效。

### <a name="citustask_assignment_policy-enum"></a>citus \_ \_ 枚举 () 的任务分配策略

> [!NOTE]
> 仅当[shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer)大于一，或针对[reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)的查询时，此 GUC 才适用。

设置向工作线程分配任务时要使用的策略。 协调器将任务分配给基于分片位置的辅助角色。 此配置值指定进行这些分配时要使用的策略。
目前可以使用三种可能的任务分配策略。

-   **贪婪：** 默认情况下，贪心策略是默认值，旨在跨辅助角色平均分配任务。
-   **轮循机制：** 轮循机制策略将任务以循环方式分配给不同的副本。 当表的分片计数比辅助角色的数量低时，此策略可提高群集利用率。
-   **第一个副本：** 第一个副本策略基于插入顺序分配任务， (副本) 分片。 换言之，分片的片段查询分配给具有该分片的第一个副本的辅助角色。
    利用此方法，您可以更好地保证哪些分片将用于哪些节点 (即，更强的内存驻留保证) 。

此参数可在运行时设置，并且在协调器上有效。

## <a name="intermediate-data-transfer"></a>中间数据传输

### <a name="citusbinary_worker_copy_format-boolean"></a>citus \_)  (的二进制辅助角色 \_ 复制 \_ 格式

使用二进制复制格式在辅助角色之间传输中间数据。
在大表联接过程中，超大规模 (Citus) 可能必须在不同的辅助角色之间动态重新分区和无序播放数据。 默认情况下，将以文本格式传输此数据。 如果启用此参数，则指示数据库使用 PostgreSQL 的二进制序列化格式传输此数据。 此参数对辅助角色有效，需要在 postgresql 文件中进行更改。 编辑完配置文件后，用户可以发送 SIGHUP 信号或重新启动服务器以使此更改生效。

### <a name="citusbinary_master_copy_format-boolean"></a>citus \_ master \_ copy \_ 格式 (布尔) 

使用二进制复制格式在协调器和辅助角色之间传输数据。 运行分布式查询时，工作线程将其中间结果传输到用于最终聚合的协调器。 默认情况下，将以文本格式传输此数据。 如果启用此参数，则指示数据库使用 PostgreSQL 的二进制序列化格式传输此数据。
此参数可在运行时设置，并且对协调器有效。

### <a name="citusmax_intermediate_result_size-integer"></a>citus (整数的最大 \_ 中间 \_ 结果 \_ 大小) 

无法向下推送到辅助角色节点以执行的 Cte 的中间结果的最大大小（KB）。 默认值为 1 GB，值为-1 表示无限制。
超出限制的查询将被取消，并生成错误消息。

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus \_ \_ (布尔) 启用 ddl 传播

指定是否将 DDL 更改从协调器自动传播到所有工作线程。 默认值为 true。 由于某些架构更改需要表的访问排他锁，并且因为自动传播按顺序应用到所有工作线程，因此它可以使超大规模 (Citus) 群集暂时降低响应能力。 你可以选择禁用此设置并手动传播更改。

## <a name="executor-configuration"></a>执行器配置

### <a name="general"></a>常规

#### <a name="citusall_modifications_commutative"></a>citus。所有 \_ 修改都是 \_ 交换的

超大规模 (Citus) 强制执行交换性规则，并获取修改操作的相应锁以保证行为的正确性。 例如，假设 INSERT 语句与另一个 INSERT 语句 commutes，而不是与 UPDATE 或 DELETE 语句一起使用。 同样，它假设 UPDATE 或 DELETE 语句不使用另一个 UPDATE 或 DELETE 语句。 此预防措施意味着更新和删除需要超大规模 (Citus) 才能获取更强的锁。

如果有可使用插入或其他更新来交换的 UPDATE 语句，则可以通过将此参数设置为 true 来放宽这些交换性假设。 如果此参数设置为 "true"，则所有命令都被视为可交换的，并声明一个共享锁，这可以提高总体吞吐量。 此参数可在运行时设置，并且对协调器有效。

#### <a name="citusremote_task_check_interval-integer"></a>citus \_ \_ (整数) 的远程任务检查 \_ 间隔

设置超大规模 (Citus) 检查任务跟踪器执行器管理的作业状态的频率。 默认值为10毫秒。 协调器将任务分配给工作人员，然后与他们一起定期检查每个任务的 \' 进度。 此配置值设置两次后续检查之间的时间间隔。 此参数在协调器中有效，并且可在运行时设置。

#### <a name="citustask_executor_type-enum"></a>citus \_ \_)  (枚举的执行器类型

超大规模 (Citus) 具有三个用于运行分布式选择查询的执行器类型。  可以通过设置此配置参数来选择所需的执行器。 此参数的可接受值为：

-   **自适应：** 默认值。 对于涉及跨多个分片的聚合和分散联接的查询，最佳响应是最佳方法。
-   **任务跟踪器：** 任务跟踪器执行程序非常适合用于长时间运行的复杂查询，需要跨工作节点混排数据，并实现高效的资源管理。
-   **实时：** (弃用) 与自适应执行程序具有类似的用途，但不太灵活，并且可能会导致辅助节点上的连接压力更大。

此参数可在运行时设置，并且在协调器上有效。

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus \_ \_ \_ \_) {#multi_task_logging} (枚举的多任务查询日志级别

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

#### <a name="citusenable_repartition_joins-boolean"></a>citus \_ \_ (布尔) 启用分区联接

通常，尝试使用自适应执行器执行重新分区联接会失败，并出现错误消息。  但是， `citus.enable_repartition_joins` 如果将设置为 true，则超大规模 (Citus) 暂时切换到任务跟踪器执行程序以执行联接。  默认值为 false。

### <a name="task-tracker-executor-configuration"></a>任务跟踪器执行程序配置

#### <a name="citustask_tracker_delay-integer"></a>citus \_ (整数) 的任务跟踪器 \_ 延迟

此参数将任务管理舍入和默认值之间的任务跟踪器休眠时间设置为200毫秒。 任务跟踪器进程定期唤醒，遍历分配给它的所有任务，并计划并执行这些任务。  然后，任务跟踪器会在一段时间内休眠，然后再次执行这些任务。
此配置值确定休眠期限的长度。 此参数对辅助角色有效，需要在 postgresql 文件中进行更改。 编辑完配置文件后，用户可以发送 SIGHUP 信号或重新启动服务器以使更改生效。

可以通过减少管理舍入之间的时间间隔，缩短此参数以剪裁由任务跟踪器执行器导致的延迟。
如果分片查询较短，因而会定期更新其状态，则减少延迟非常有用。

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus \_ 分配 \_ 任务 \_ 批 \_ 大小 (整数) 

协调器上的任务跟踪器执行程序将批处理中的任务同步分配给辅助角色上的后台程序。 此参数设置要在单个批处理中分配的最大任务数。 选择更大的批大小可以加快任务分配。 但是，如果工作线程数很大，则所有工作人员可能需要更长时间才能获取任务。  此参数可在运行时设置，并且对协调器有效。

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus \_ \_ \_ \_ (整数) 每个节点最多运行任务

任务跟踪器进程根据需要计划和执行分配给它的任务。 此配置值设置在任意给定时间在一个节点上并发执行的最大任务数，默认值为8。

该限制确保你不 \' 会有许多任务同时到达磁盘，并有助于避免磁盘 i/o 争用。 如果查询是通过内存或 Ssd 来提供的，则可以增加 \_ 每个节点的最大运行 \_ 任务 \_ 数， \_ 而无需担心。

#### <a name="cituspartition_buffer_size-integer"></a>citus \_ \_)  (整数的分区缓冲区大小

设置用于分区操作的缓冲区大小，默认值为 8 MB。
超大规模 (Citus) 允许在联接两个大型表时将表数据重新分区为多个文件。 填充此分区缓冲区后，重新分区的数据将被刷新到磁盘上的文件中。  此配置条目可在运行时设置，在辅助角色上有效。

### <a name="explain-output"></a>说明输出

#### <a name="citusexplain_all_tasks-boolean"></a>citus 说明 \_ \_ (布尔) 的所有任务

默认情况下，超大规模 (Citus) 显示在分布式查询上运行[说明](http://www.postgresql.org/docs/current/static/sql-explain.html)时单个任意任务的输出。 在大多数情况下，说明输出在任务中是类似的。 偶尔，某些任务将以不同的方式进行计划或具有更高的执行时间。 在这种情况下，启用此参数会很有用，之后说明输出将包括所有任务。 说明所有任务可能会导致说明所需的时间更长。

## <a name="next-steps"></a>后续步骤

* 除服务器参数外，另一种形式的配置是超大规模 (Citus) 服务器组中的资源[配置选项](concepts-hyperscale-configuration-options.md)。
* 基础 PostgreSQL 数据库也有[配置参数](http://www.postgresql.org/docs/current/static/runtime-config.html)。
