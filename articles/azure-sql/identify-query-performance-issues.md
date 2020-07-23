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
ms.openlocfilehash: b33d8db9d43b151cb0405ea24e0bea87e21cbdc9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84345336"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>Azure SQL 数据库中的查询性能瓶颈的可检测类型
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

尝试解决性能瓶颈时，首先确定在查询处于运行状态还是处于等待状态时是否会出现瓶颈。 根据此决定，会应用不同的分辨率。 使用下图来帮助了解可能导致与运行相关的问题或与等待相关的问题的因素。 本文中讨论了与每种问题相关的问题和解决方法。

可以使用 Azure SQL 数据库[智能见解](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns)或 SQL Server [dmv](database/monitoring-with-dmvs.md)来检测这些类型的性能瓶颈。

![工作负荷状态](./media/identify-query-performance-issues/workload-states.png)

**与运行相关的问题**：与运行相关的问题通常与编译问题相关，导致不太理想的查询计划或执行问题与资源不足或过度使用。
**与等待相关的问题**：通常与相关的问题相关：

- 锁（阻塞）
- I/O
- 与 TempDB 使用情况相关的争用
- 内存授予等待

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>导致查询计划不理想的编译问题

SQL 查询优化器生成的不理想计划可能是查询性能缓慢的原因。 SQL 查询优化器可能会生成一个不理想的计划，因为缺少索引、过时统计信息、要处理的行数的估计值不正确，或者所需内存的估计值不准确。 如果知道查询在过去或其他实例上的执行速度更快，请比较实际执行计划，以确定它们是否不同。

- 使用以下方法之一标识任何缺失的索引：

  - 使用[智能见解](database/intelligent-insights-troubleshoot-performance.md#missing-index)。
  - 针对单一数据库和共用数据库的[数据库顾问](database/database-advisor-implement-performance-recommendations.md)。
  - Dmv. 此示例显示缺失索引的影响、如何使用 Dmv 检测[缺少的索引](database/performance-guidance.md#identifying-and-adding-missing-indexes)，以及实现缺少索引建议的影响。
- 尝试应用[查询提示](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)、[更新统计信息](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql)或[重新生成索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)，以获得更好的计划。 在 Azure SQL 数据库中启用[自动计划更正](../azure-sql/database/automatic-tuning-overview.md)，以自动缓解这些问题。

  此[示例](database/performance-guidance.md#query-tuning-and-hinting)显示由于参数化查询、如何检测此情况以及如何使用查询提示进行解析而导致不理想查询计划的影响。

- 尝试更改数据库兼容级别并实现智能查询处理。 根据数据库的兼容级别，SQL 查询优化器可能会生成不同的查询计划。 更高的兼容性级别提供了更[智能的查询处理功能](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing)。

  - 有关查询处理的详细信息，请参阅[查询处理体系结构指南](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide)。
  - 若要更改数据库兼容级别并详细了解兼容级别之间的差异，请参阅[ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)。
  - 若要阅读有关基数估算的详细信息，请参阅[基数估算](https://docs.microsoft.com/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>解析包含不理想查询执行计划的查询

以下部分介绍了如何解析包含不理想查询执行计划的查询。

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a>具有参数敏感计划（PSP）问题的查询

当查询优化器生成的查询执行计划仅适用于某个或某组特定的参数值，而缓存计划对于连续执行操作中所用的参数值并非最佳时，将发生参数敏感计划 (PSP) 问题。 这样，并非最佳的计划可能导致查询性能问题，并降低整体工作负荷吞吐量。

有关参数探查和查询处理的详细信息，请参阅[查询处理体系结构指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有多种解决方法可以缓解 PSP 问题。 每种解决方法各有利弊：

- 在每次执行查询时使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示。 此解决方法以编译时间和 CPU 增加为代价来换取更好的计划质量。 对于需要高吞吐量的工作负荷，通常无法使用 `RECOMPILE` 选项。
- 使用 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示将实际参数值替代为典型的参数值，以便为大部分可能的参数值生成一个足够好的计划。 此选项要求充分了解最佳参数值和相关的计划特征。
- 使用 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示替代实际参数值，并改用密度向量平均值。 还可以将传入的参数值捕获到局部变量中，然后在谓词内使用局部变量，而不是使用参数本身。 对于此修复方法，平均密度必须足够好。**
- 使用 [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示完全禁用参数探查。
- 使用 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示防止在缓存中重新编译。 此解决方法假定缓存中已有的通用计划已经足够好。 还可以禁用统计信息自动更新，以减少逐出良好计划而编译新的不良计划的可能性。
- 显式使用 [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示，通过重写查询并在查询文本中添加提示来强制执行计划。 或者，使用查询存储或启用[自动优化](../azure-sql/database/automatic-tuning-overview.md)来设置特定的计划。
- 将单个过程替换为一组嵌套的过程，可以根据条件逻辑和关联的参数值来使用其中每个过程。
- 创建动态字符串执行来替代静态过程定义。

有关解决 PSP 问题的详细信息，请参阅以下博客文章：

- [我气味一个参数](https://docs.microsoft.com/archive/blogs/queryoptteam/i-smell-a-parameter)
- [Conor vs. dynamic SQL vs. procedures vs. plan quality for parameterized queries](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)（参数化查询的 Conor 与动态 SQL、过程与计划质量）
- [SQL Server 中的 SQL 查询优化方法：参数嗅探](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>参数化不当而导致的编译活动

当查询包含文本时，为了减少编译次数，要么数据库引擎选择自动参数化语​​句，要么用户显式参数化语句。 使用相同模式但不同文本值的大量查询编译可能会导致 CPU 使用率较高。 同样，如果仅将查询部分参数化，导致该查询仍包含文本，则数据库引擎不会将查询进一步参数化。

以下是部分参数化查询的示例：

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在此示例中，`t1.c1` 采用 `@p1`，而 `t2.c2` 继续采用文本形式的 GUID。 在这种情况下，如果更改 `c2` 的值，该查询将被视为不同的查询，并且将进行新的编译。 若要减少此示例中的编译次数，也要参数化 GUID。

以下查询显示查询哈希的查询计数，以确定查询是否已正确参数化：

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

重新编译查询执行计划可能会导致生成的查询计划与最初缓存的计划不同。 有多种原因可能导致自动重新编译现有的原始计划：

- 此查询引用了架构中的更改
- 查询引用对表的数据更改
- 查询上下文选项已更改

编译的计划可能会出于各种原因从缓存中逐出，例如：

- 实例重启
- 数据库范围的配置更改
- 内存压力
- 清除缓存的显式请求

如果使用 RECOMPILE 提示，则不会缓存计划。

重新编译（或者在缓存逐出后重新编译）仍可能导致从生成与原始计划相同的查询执行计划。 如果计划与以前的或原始的计划不同，可能的解释如下：

- **更改了物理设计**：例如，新创建的索引可以更有效地涵盖查询的要求。 如果查询优化器认为利用这些新索引比最初选择用于第一个版本的查询执行的数据结构更有利，则可以在新的编译中使用这些新索引。 对引用的对象进行任何物理更改可能会导致在编译时生成新的计划选项。

- **服务器资源差异**：当一个系统中的计划与另一个系统中的计划不同时，资源可用性（如可用处理器的数量）可能会影响生成的计划。 例如，如果一个系统的处理器数较多，则可能会选择并行计划。

- **不同统计信息**：与引用对象关联的统计信息可能已更改，或者可能与原始系统的统计信息有很大差异。 如果统计信息发生更改并重新编译，则查询优化器将使用从更改时间开始的统计信息。 修改后的统计信息的数据分布和频率可能不同于原始编译中的情况。 这些更改用于创建基数估算。 （*基数估算值*是预期通过逻辑查询树流动的行数。）对基数估计的更改可能会导致你选择不同的物理运算符和操作的相关顺序。 即使对统计信息进行少量的更改，也可能会导致查询执行计划发生变化。

- **更改的数据库兼容级别或基数估计器版本**：对数据库兼容性级别的更改可以启用可能导致不同查询执行计划的新策略和功能。 除了数据库兼容性级别以外，禁用或启用跟踪标志 4199 或更改数据库范围的配置 QUERY_OPTIMIZER_HOTFIXES 的状态也可能会影响编译时的查询执行计划选项。 跟踪标志 9481（强制旧式 CE）和 2312（强制默认 CE）也会影响计划。

## <a name="resource-limits-issues"></a>资源限制问题

速度慢的查询性能与不理想的查询计划相关，缺少的索引通常与资源不足或过度资源相关。 如果查询计划最佳，则查询（和数据库）可能会达到数据库、弹性池或托管实例的资源限制。 对于服务级别，可能会有超过日志写入吞吐量的示例。

- 使用 Azure 门户检测资源问题：若要查看是否存在资源限制，请参阅[SQL 数据库资源监视](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)。 有关单一数据库和弹性池的信息，请参阅[数据库顾问性能建议](database/database-advisor-implement-performance-recommendations.md)和[查询性能见解](database/query-performance-insight-use.md)。
- 使用[智能见解](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits)检测资源限制
- 使用[dmv](database/monitoring-with-dmvs.md)检测资源问题：

  - [Sys. dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV 返回数据库的 CPU、i/o 和内存消耗。 即使数据库中没有活动，也会每隔 15 秒返回一行数据。 历史数据将保留一小时。
  - [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 Azure SQL 数据库的 CPU 使用率和存储数据。 在 5 分钟间隔内收集并聚合数据。
  - [存在许多单独的查询，它们共同消耗了很多的 CPU 资源](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

如果将问题标识为资源不足，可以升级资源来增加数据库的容量以满足 CPU 需求。 有关详细信息，请参阅[在 Azure SQL 数据库中缩放单一数据库资源](database/single-database-scale.md)和[在 Azure SQL 数据库中缩放弹性池资源](database/elastic-pool-scale.md)。 有关缩放托管实例的信息，请参阅[服务层资源限制](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>工作负荷量增加导致的性能问题

应用程序流量和工作负荷量的增加可能会导致 CPU 使用率增大。 但是，要想正确诊断此问题，必须慎之又慎。 如果出现 CPU 使用率偏高的情况，请回答以下问题来确定 CPU 使用率增大是否由工作负荷量变化而引起：

- 来自应用程序的查询是否是导致高 CPU 问题的原因？
- 对于[最常见的 CPU 消耗查询，您可以确定](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)：

  - 同一查询是否关联了多个执行计划？ 如果是，为什么？
  - 对于具有相同执行计划的查询，执行时间是否一致？ 执行计数是否增加？ 如果是，则性能问题可能是由工作负荷增加造成。

总而言之，如果查询执行计划没有以不同的方式执行，CPU 使用率却随执行计数的增加而增加，则可能出现了与工作负荷增加相关的性能问题。

要得出是工作负荷量变化导致 CPU 问题的结论并不容易。 请考虑以下因素：

- **更改的资源使用**情况：例如，假设 CPU 使用率长时间增加到80%。 单凭 CPU 使用率并不能说明工作负荷量发生了变化。 即使应用程序执行的工作负荷完全相同，查询执行计划的回归和数据分布的变化也有可能导致资源使用率的增加。

- **新查询的外观**：应用程序可能会在不同时间驱动一组新查询。

- **请求数增加或减少**：此方案是工作负荷最明显的度量值。 查询数量并不一定与资源利用率增加相对应。 但是，在其他因素不变的前提下，该指标仍然是一个重要信号。

使用智能见解来检测[工作负荷增加](database/intelligent-insights-troubleshoot-performance.md#workload-increase)和[计划回归](database/intelligent-insights-troubleshoot-performance.md#plan-regression)。

## <a name="waiting-related-problems"></a>等待相关的问题

消除了与执行问题相关的不理想计划和*等待相关*问题后，性能问题通常是查询可能正在等待某些资源。 等待相关的问题的可能原因如下：

- **阻止**：

  一个查询可能持有数据库中某些对象的锁，而其他查询正在尝试访问相同的对象。 可以通过使用[dmv](database/monitoring-with-dmvs.md#monitoring-blocked-queries)或[智能见解](database/intelligent-insights-troubleshoot-performance.md#locking)来识别阻塞查询。
- **IO 问题**

  查询可能正在等待将页面写入数据文件或日志文件。 在这种情况下，请检查 DMV 中的 `INSTANCE_LOG_RATE_GOVERNOR`、`WRITE_LOG` 或 `PAGEIOLATCH_*` 等待统计信息。 请参阅使用 Dmv[识别 IO 性能问题](database/monitoring-with-dmvs.md#identify-io-performance-issues)。
- **TempDB 问题**

  如果工作负荷使用大量的临时表，或者计划中存在大量的 TempDB 溢出，则可能表示查询遇到了 TempDB 吞吐量问题。 请参阅使用 Dmv 来[标识 TempDB 问题](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues)。
- **与内存相关的问题**

  如果工作负荷没有足够的内存，页生存期可能会下降，或者查询获得的内存量小于所需的量。 在某些情况下，查询优化器中的内置智能会解决内存相关的问题。 请参阅使用 Dmv[识别内存授予问题](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)。

### <a name="methods-to-show-top-wait-categories"></a>用于显示顶级等待类别的方法

用于显示最常见等待类别的最常用方法如下：

- 使用智能见解来识别因[等待时间增加](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic)而性能降低的查询
- 使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)查找每个查询在不同时间段的等待统计信息。 在查询存储中，等待类型合并成等待类别。 可在 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) 中找到等待类别到等待类型的映射。
- 使用[sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)返回有关在查询操作期间执行的线程所遇到的所有等待的信息。 可以使用此聚合视图来诊断 Azure SQL 数据库以及特定查询和批的性能问题。 查询可能正在等待资源，发生了队列等待或外部等待。
- 使用 [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 返回有关正在等待某个资源的任务队列的信息。

在 CPU 使用率偏高时，如果存在以下情况，查询存储和等待统计信息可能不会反映 CPU 使用率：

- CPU 消耗量较高的查询仍在执行。
- 发生故障转移时，正在运行 CPU 消耗量较高的查询。

跟踪查询存储和等待统计信息的 DMV 仅显示成功完成的查询和超时查询的结果。 它们不显示当前正在执行的语句的数据（直到其完成）。 使用动态管理视图 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 跟踪当前正在执行的查询以及相关的工作线程时间。

> [!TIP]
> 其他工具：
>
> - [TigerToolbox 等待和闩锁](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>后续步骤

[SQL 数据库监视和优化概述](database/monitor-tune-overview.md)
