---
title: Azure SQL 数据库中的查询性能问题的类型
description: 本文介绍 Azure SQL 数据库中的查询性能问题类型，并了解如何识别和解决这些问题的查询
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: e155321c2727bf4ee871ef7be7b61b6a523ec1fc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256128"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL 数据库中的查询性能瓶颈的可检测类型

尝试解决性能瓶颈时，首先确定在查询处于运行状态还是处于等待状态时是否会出现瓶颈。 根据此决定，会应用不同的分辨率。 使用下图来帮助了解可能导致与运行相关的问题或与等待相关的问题的因素。 本文中讨论了与每种问题相关的问题和解决方法。

可以使用 Azure SQL 数据库[智能见解](sql-database-intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns)或 SQL Server [dmv](sql-database-monitoring-with-dmvs.md)来检测这些类型的性能瓶颈。

![工作负荷状态](./media/sql-database-monitor-tune-overview/workload-states.png)

**与运行相关的问题**：与运行相关的问题通常与编译问题相关，导致不太理想的查询计划或执行问题与资源不足或过度使用。
**与等待相关的问题**：通常与相关的问题相关：

- 锁（阻塞）
- I/O
- 与 TempDB 使用情况相关的争用
- 内存授予等待

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>导致查询计划不理想的编译问题

SQL 查询优化器生成的不理想计划可能是查询性能缓慢的原因。 SQL 查询优化器可能会生成一个不理想的计划，因为缺少索引、过时统计信息、要处理的行数的估计值不正确，或者所需内存的估计值不准确。 如果知道查询是在过去或另一个实例（托管实例或 SQL Server 实例）上更快执行的，请比较实际的执行计划，以确定它们是否不同。

- 使用以下方法之一标识任何缺失的索引：

  - 使用[智能见解](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index)。
  - 针对单一数据库和共用数据库的[数据库顾问](sql-database-advisor.md)。
  - Dmv. 此示例显示缺失索引的影响、如何使用 Dmv 检测[缺少的索引](sql-database-performance-guidance.md#identifying-and-adding-missing-indexes)，以及实现缺少索引建议的影响。
- 尝试应用[查询提示](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)、[更新统计信息](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)或[重新生成索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)，以获得更好的计划。 在 Azure SQL 数据库中启用[自动计划更正](sql-database-automatic-tuning.md)，以自动缓解这些问题。

  此[示例](sql-database-performance-guidance.md#query-tuning-and-hinting)显示由于参数化查询、如何检测此情况以及如何使用查询提示进行解析而导致不理想查询计划的影响。

- 尝试更改数据库兼容级别并实现智能查询处理。 根据数据库的兼容级别，SQL 查询优化器可能会生成不同的查询计划。 更高的兼容性级别提供了更[智能的查询处理功能](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)。

  - 有关查询处理的详细信息，请参阅[查询处理体系结构指南](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)。
  - 若要更改数据库兼容级别并详细了解兼容级别之间的差异，请参阅[ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。
  - 若要阅读有关基数估算的详细信息，请参阅[基数估算](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>解析包含不理想查询执行计划的查询

以下部分介绍了如何解析包含不理想查询执行计划的查询。

### <a name="ParamSniffing"></a>具有参数敏感计划（PSP）问题的查询

当查询优化器生成仅对特定参数值（或一组值）最佳的查询执行计划时，将发生参数敏感计划（PSP）问题，且缓存计划对于连续使用的参数值不是最佳的执行. 这样的计划可能会导致查询性能问题并降低整体工作负荷吞吐量。

有关参数嗅探和查询处理的详细信息，请参阅[查询处理体系结构指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

几种解决方法可以减轻 PSP 问题。 每个解决方法都有关联的折衷与缺点：

- 在每次执行查询时使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示。 此解决方法以编译时间和 CPU 增加为代价来换取更好的计划质量。 `RECOMPILE` 选项通常不能用于需要高吞吐量的工作负荷。
- 使用[选项（OPTIMIZE FOR ...）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查询提示可使用典型的参数值替代实际参数值，该参数值可生成一个足以满足大多数参数值可能性的计划。 此选项要求充分了解最佳参数值和相关的计划特征。
- 使用[选项（"优化未知](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)值"）查询提示替代实际参数值，而改用密度向量平均。 还可以通过捕获局部变量中的传入参数值，然后在谓词中使用局部变量，而不是使用参数本身，来实现此目的。 对于此修补程序，平均密度必须*足够好*。
- 使用[DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查询提示完全禁用参数探查。
- 使用[KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查询提示可防止在缓存中重新编译。 此解决方法假设已有足够的通用计划是缓存中的计划。 你还可以禁用自动统计信息更新，以减少将逐出良好计划并将编译新的错误计划的机会。
- 通过重写查询并在查询文本中添加提示来强制[执行](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)计划。 或通过使用查询存储或启用[自动优化](sql-database-automatic-tuning.md)来设置特定计划。
- 将单个过程替换为一组嵌套的过程，可以根据条件逻辑和关联的参数值来使用其中每个过程。
- 创建动态字符串执行来替代静态过程定义。

有关解决 PSP 问题的详细信息，请参阅以下博客文章：

- [我气味一个参数](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor 与动态的 SQL 与过程的对比和参数化查询的计划质量](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 中的 SQL 查询优化方法：参数嗅探](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>由于参数化不正确而导致的编译活动

当查询具有文本时，数据库引擎会自动参数化语句或用户显式参数化语句，以减少编译数。 使用相同模式但不同文本值的查询的大量编译可以导致 CPU 使用率高。 同样，如果您只对继续具有文本的查询进行部分参数化，则数据库引擎不会对查询进行进一步的参数化。

下面是部分参数化查询的示例：

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在此示例中，`t1.c1` 采用 `@p1`，但 `t2.c2` 继续采用 GUID 作为文本。 在这种情况下，如果更改 `c2`的值，则查询将被视为不同的查询，并且将发生新的编译。 若要在此示例中减少编译，还应参数化 GUID。

下面的查询按查询哈希显示查询计数，以确定查询是否已正确参数化：

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC
```

### <a name="factors-that-affect-query-plan-changes"></a>影响查询计划更改的因素

重新编译查询执行计划可能会导致生成的查询计划与原始缓存计划不同。 由于各种原因，可能会自动重新编译现有的原始计划：

- 此查询引用了架构中的更改
- 查询引用对表的数据更改
- 查询上下文选项已更改

由于各种原因，编译的计划可能会从缓存中弹出，例如：

- 实例重启
- 数据库范围的配置更改
- 内存压力
- 清除缓存的显式请求

如果使用重新编译提示，则不会缓存计划。

重新编译（或缓存逐出后的新编译）仍可能导致生成与原始相同的查询执行计划。 如果计划从以前的或原始计划中更改，则可能会出现以下说明：

- **更改了物理设计**：例如，新创建的索引可以更有效地涵盖查询的要求。 如果查询优化器决定使用新索引比最初为查询执行的第一个版本选择的数据结构更好，则可以对新的编译使用新的索引。 在编译时，对被引用对象的任何物理更改都可能会导致新的计划选择。

- **服务器资源差异**：当一个系统中的计划与另一个系统中的计划不同时，资源可用性（如可用处理器的数量）可能会影响生成的计划。 例如，如果一个系统有多个处理器，则可能会选择并行计划。

- **不同统计信息**：与引用对象关联的统计信息可能已更改，或者可能与原始系统的统计信息有很大差异。 如果更改了统计信息和重新编译，则查询优化器会在更改时使用从开始的统计信息。 修订的统计信息的数据分布和频率可能与原始编译的不同。 这些更改用于创建基数估算。 （*基数估算值*是预期通过逻辑查询树流动的行数。）对基数估计的更改可能会导致你选择不同的物理运算符和操作的相关顺序。 即使对统计信息进行少量更改，也可能导致查询执行计划发生变化。

- **更改的数据库兼容级别或基数估计器版本**：对数据库兼容性级别的更改可以启用可能导致不同查询执行计划的新策略和功能。 除了数据库兼容性级别外，禁用或启用的跟踪标志4199或数据库范围内配置的已更改状态 QUERY_OPTIMIZER_HOTFIXES 也可能会影响编译时的查询执行计划选择。 跟踪标志9481（强制旧版 CE）和2312（强制默认 CE）还会影响计划。

## <a name="resource-limits-issues"></a>资源限制问题

速度慢的查询性能与不理想的查询计划相关，缺少的索引通常与资源不足或过度资源相关。 如果查询计划最佳，则查询（和数据库）可能会达到数据库、弹性池或托管实例的资源限制。 对于服务级别，可能会有超过日志写入吞吐量的示例。

- 使用 Azure 门户检测资源问题：若要查看是否存在资源限制，请参阅[SQL 数据库资源监视](sql-database-monitor-tune-overview.md#sql-database-resource-monitoring)。 有关单一数据库和弹性池的信息，请参阅[数据库顾问性能建议](sql-database-advisor.md)和[查询性能见解](sql-database-query-performance.md)。
- 使用[智能见解](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)检测资源限制
- 使用[dmv](sql-database-monitoring-with-dmvs.md)检测资源问题：

  - [Sys. dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 SQL 数据库的 CPU、i/o 和内存消耗。 即使数据库中没有活动，也会每隔15秒间隔一行。 历史数据将保留一小时。
  - [Sys. resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 Azure SQL 数据库的 CPU 使用率和存储数据。 数据按五分钟间隔收集和聚合。
  - [累积消耗高 CPU 的多个单独查询](sql-database-monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)
  - 

如果将问题标识为资源不足，则可以升级资源，增加 SQL 数据库的容量以满足 CPU 需求。 有关详细信息，请参阅在[AZURE Sql 数据库中缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放 azure sql 数据库中的弹性池资源](sql-database-elastic-pool-scale.md)。 有关缩放托管实例的信息，请参阅[服务层资源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="performance-problems-caused-by-increased-workload-volume"></a>由于工作负荷量增加而导致的性能问题

应用程序流量和工作负荷量增加会导致 CPU 使用率增加。 但必须小心地诊断此问题。 出现高 CPU 问题时，请回答以下问题，确定增加是否是由工作负荷量的更改引起的：

- 应用程序中的查询是否导致了高 CPU 问题的原因？
- 对于[最常见的 CPU 消耗查询，您可以确定](sql-database-monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)：

  - 是否有多个与同一查询相关联的执行计划？ 如果是，为什么？
  - 对于具有相同执行计划的查询，执行时间是否一致？ 执行计数是否增加？ 如果是这样，则工作负荷增加可能会导致性能问题。

总之，如果查询执行计划未以不同的方式执行，但 CPU 使用率随执行计数一起增加，则性能问题可能与工作负荷增加相关。

确定驱动 CPU 问题的工作负荷量发生变化并不是很容易。 请考虑以下因素：

- **更改的资源使用**情况：例如，假设 CPU 使用率长时间增加到80%。 仅 CPU 使用率并不意味着工作负荷量发生变化。 即使应用程序执行相同的工作负载，查询执行计划中的回归和数据分发的更改也会导致更多的资源使用。

- **新查询的外观**：应用程序可能会在不同时间驱动一组新查询。

- **请求数增加或减少**：此方案是工作负荷最明显的度量值。 查询数量并不一定与资源利用率增加相对应。 但是，如果其他因素未改变，此指标仍是一个非常重要的信号。

使用智能见解来检测[工作负荷增加](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase)和[计划回归](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression)。

## <a name="waiting-related-problems"></a>等待相关的问题

消除了与执行问题相关的不理想计划和*等待相关*问题后，性能问题通常是查询可能正在等待某些资源。 等待相关的问题可能由以下原因引起：

- **阻止**：

  一个查询可能持有数据库中对象的锁，而其他查询则尝试访问相同的对象。 可以通过使用[dmv](sql-database-monitoring-with-dmvs.md#monitoring-blocked-queries)或[智能见解](sql-database-intelligent-insights-troubleshoot-performance.md#locking)来识别阻塞查询。
- **IO 问题**

  查询可能正在等待将页写入数据文件或日志文件。 在这种情况下，请检查 DMV 中的 `INSTANCE_LOG_RATE_GOVERNOR`、`WRITE_LOG`或 `PAGEIOLATCH_*` 等待统计信息。 请参阅使用 Dmv[识别 IO 性能问题](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)。
- **TempDB 问题**

  如果工作负荷使用临时表，或者计划中存在 TempDB 溢出，则查询可能会遇到 TempDB 吞吐量问题。 请参阅使用 Dmv 来[标识 TempDB 问题](sql-database-monitoring-with-dmvs.md#identify-tempdb-performance-issues)。
- **与内存相关的问题**

  如果工作负荷没有足够的内存，则页生存期可能会下降，或者查询可能会获得比所需内存更少的内存。 在某些情况下，查询优化器中的内置智能将修复与内存相关的问题。 请参阅使用 Dmv[识别内存授予问题](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)。

### <a name="methods-to-show-top-wait-categories"></a>用于显示顶级等待类别的方法

这些方法通常用于显示等待类型的顶级类别：

- 使用智能见解来识别因[等待时间增加](sql-database-intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)而性能降低的查询
- 使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)可查找一段时间内每个查询的等待统计信息。 在查询存储中，等待类型合并成等待类别。 你可以在[sys. query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)中找到等待类型的映射。
- 使用[sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)返回有关在查询操作期间执行的线程所遇到的所有等待的信息。 你可以使用此聚合视图诊断 Azure SQL 数据库的性能问题，以及特定查询和批处理。 查询可以等待资源、队列等待或外部等待。
- 使用[sys. dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)返回有关正在等待某个资源的任务队列的信息。

在高 CPU 情况下，如果下列情况，查询存储和等待统计信息可能不会反映 CPU 使用率：

- 高 CPU 消耗查询仍在执行。
- 发生故障转移时，消耗大量 CPU 的查询正在运行。

跟踪查询存储和等待统计信息的 Dmv 只显示已成功完成和超时查询的结果。 在语句完成之前，它们不会显示当前正在执行的语句的数据。 使用动态管理视图[dm_exec_requests sys.databases](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)跟踪当前正在执行的查询和关联的工作进程时间。

> [!TIP]
> 其他工具：
>
> - [TigerToolbox 等待和闩锁](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>后续步骤

[SQL 数据库监视和优化概述](sql-database-monitor-tune-overview.md)