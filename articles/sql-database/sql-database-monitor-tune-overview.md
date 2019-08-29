---
title: 监视和性能优化 - Azure SQL 数据库 | Microsoft Docs
description: 有关通过评估和改进来调整 Azure SQL 数据库性能的提示。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: ee4bd9d61856ef4ea1afdd027d6f39e730b92d78
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129204"
---
# <a name="monitoring-and-performance-tuning"></a>监视和性能优化

Azure SQL 数据库提供工具和方法, 可轻松监视使用情况, 添加或删除资源 (CPU、内存、i/o)、排查潜在问题, 并提供有关提高数据库性能的建议。 Azure SQL 数据库具有可自动修复数据库中的问题的功能。 自动优化可使数据库适应工作负荷并自动优化性能。 但是, 有些自定义问题可能需要进行故障排除。 本文介绍一些可用于排查性能问题的最佳做法和工具。

应该执行两个主要活动, 以确保数据库正常运行。
- [监视数据库性能](#monitoring-database-performance), 以确保分配给数据库的资源能够处理工作负荷。 如果看到数据库已达到资源限制, 请考虑:
   - 识别和优化资源消耗最多的查询。
   - 通过升级服务层来添加更多的资源。
- [解决性能问题](#troubleshoot-performance-issues)若要确定出现潜在问题的原因, 请确定问题的根本原因。 确定根本原因后, 请执行修复此问题的步骤。

## <a name="monitoring-database-performance"></a>监视数据库性能

监视 Azure 中的 SQL 数据库的性能从监视相对于所选数据库性能级别所使用的资源开始。 应监视以下资源:
 - **Cpu 使用情况**-检查数据库是否长时间达到 100% 的 CPU 使用率。 CPU 使用率较高可能表示应该确定和优化使用最多计算能力的查询。 或者, 高 CPU 使用率可能表明数据库或实例应升级到更高的服务层。 
 - **等待统计信息**-使用[Sys.databases _os_wait_stats (transact-sql)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)确定查询遇到的等待时间。 查询可以等待资源、队列等待或外部等待。 
 - **IO 使用情况**-检查数据库是否达到基础存储的 IO 限制。
 - **内存使用情况**-数据库或实例的可用内存量与 vcore 数成正比。 验证内存是否足以满足工作负荷。 页生存期是可以指示从内存中删除页面的速度的参数之一。

Azure SQL 数据库服务**包括工具和资源, 可帮助进行故障排除并解决潜在的性能问题**。 通过查看[性能优化建议](sql-database-advisor.md), 可确定机会以提高和优化查询性能, 而无需更改资源。 缺少索引与查询优化不足是数据库性能不佳的常见原因。 可以应用这些优化建议来提高工作负荷的性能。 我们还可以让 Azure SQL 数据库通过应用所有已确定的建议并验证建议改善数据库性能, 从而[自动优化查询性能](sql-database-automatic-tuning.md)。

以下选项可用于监视数据库性能并对其进行故障排除:

- 在[Azure 门户](https://portal.azure.com)中, 单击 " **SQL 数据库**", 选择数据库, 然后使用 "监视" 图表查找接近其最大利用率的资源。 默认会显示 DTU 消耗量。 单击“**编辑**”更改显示的时间范围和值。
- SQL Server Management Studio 的工具提供了许多有用的报告, 如[性能仪表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017), 可监视资源利用率并识别最常见的资源消耗查询。[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)可用于识别具有回归性能的查询。
- 使用[Azure 门户](https://portal.azure.com)中的[Query Performance Insight](sql-database-query-performance.md)来识别利用资源最多的查询。 此功能仅在单一数据库和弹性池中可用。
- 使用 [Azure SQL 数据库顾问](sql-database-advisor-portal.md)查看有关创建和删除索引、参数化查询，以及解决架构问题的建议。 此功能仅在单一数据库和弹性池中可用。
- 使用[AZURE SQL 智能见解](sql-database-intelligent-insights.md)自动监视数据库性能。 检测到性能问题后，将生成一个包含问题的详细信息和根本原因分析 (RCA) 的诊断日志。 在可能的情况下提供性能改善建议。
- [启用自动优化](sql-database-automatic-tuning-enable.md)，并让 Azure SQL 数据库自动修复已识别到的性能问题。
- 使用[动态管理视图 (DMV)](sql-database-monitoring-with-dmvs.md)、[扩展事件](sql-database-xevent-db-diff-from-svr.md)和[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)更细致地排查性能问题。

> [!TIP]
> 请参阅[性能指南](sql-database-performance-guidance.md)，了解在使用上述一种或多种方法识别性能问题后，可以利用哪些技术来提高 Azure SQL 数据库的性能。

## <a name="troubleshoot-performance-issues"></a>排查性能问题

若要诊断并解决性能问题，请先了解每个活动查询的状态，以及哪些条件导致出现了与每种工作负荷状态相关的性能问题。 若要改进 Azure SQL 数据库性能, 请了解应用程序中的每个活动查询请求是处于 "正在运行" 状态还是 "正在等待" 状态。 排查 Azure SQL 数据库中的性能问题时, 请记住以下图表, 因为我们通读本文以诊断并解决性能问题。

![工作负荷状态](./media/sql-database-monitor-tune-overview/workload-states.png)

对于出现性能问题的工作负荷，问题的原因可能是 CPU 争用（**运行相关的**条件）或单个查询正在等待某个资源（**等待相关的**条件）。

**与运行相关**的问题的原因可能是:
- **编译问题**-SQL 查询优化器可能会由于过时统计信息、要处理的行数的错误估计或所需内存的估计值不准确而产生不理想的计划。 如果知道查询在过去或其他实例上的执行速度更快 (托管实例或 SQL Server 实例), 请使用实际的执行计划并进行比较, 以确定它们是否不同。 尝试应用查询提示或重新生成统计信息, 或重新生成索引以获得更好的计划。 在 Azure SQL 数据库中启用“自动更正计划”，以自动缓解这些问题。
- **执行问题**-如果查询计划是最佳的, 则可能会在数据库中达到资源限制 (如日志写入吞吐量) 或使用应重新生成的零碎索引。 大量需要相同资源的并发查询也可能导致执行问题。 大多数情况下, 与**等待相关**的问题与执行问题相关, 因为没有有效执行的查询可能会等待某些资源。

**与等待相关**的问题的原因可能是:
- **阻塞**-一个查询可能持有数据库中对象的锁, 而另一些查询尝试访问相同的对象。 使用 Dmv 或监视工具可以轻松识别阻塞查询。
- **IO 问题**-查询可能正在等待将页写入数据文件或日志文件。 在此示例中`INSTANCE_LOG_RATE_GOVERNOR`, `WRITE_LOG`请参阅`PAGEIOLATCH_*` 、或等待 DMV 中的统计信息。
- **TempDB 问题**-如果工作负荷使用临时表, 或者在计划中有 tempdb 溢出, 则查询可能会遇到 TempDB 吞吐量问题。 
- **与内存相关的问题**: 可能没有足够的内存用于工作负荷, 因此页生存期可能会下降, 或者查询的内存量超出了所需的内存量。 在某些情况下，查询优化器中的内置智能可解决这些问题。
 
 以下部分将介绍如何识别并解决其中的一些问题。

## <a name="running-related-performance-issues"></a>运行相关的性能问题

一般原则是, 如果 CPU 使用率持续等于或高于 80%, 则会出现与运行相关的性能问题。 如果存在与运行相关的问题, 则可能是由于 CPU 资源不足引起的, 或者可能与下列情况之一相关:

- 运行查询过多
- 编译查询过多
- 一个或多个正在执行的查询正在使用不理想的查询计划

如果确定存在运行相关的性能问题, 则目标是使用一种或多种方法来确定确切的问题。 用于标识运行相关问题的最常见方法包括：

- 使用 [Azure 门户](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)监视 CPU 利用率百分比。
- 使用以下[动态管理视图](sql-database-monitoring-with-dmvs.md)：

  - [sys.databases _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use)返回 Azure SQL 数据库的 CPU、i/o 和内存消耗。 即使数据库中没有活动, 也会每隔15秒间隔一行。 历史数据将保留一小时。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 返回 Azure SQL 数据库的 CPU 使用率和存储数据。 在五分钟的间隔内收集和聚合数据。

> [!IMPORTANT]
> 有关使用 _db_resource_stats 和 resource_stats Dmv 排查 CPU 利用率问题的 T-sql 查询, 请参阅[确定 cpu 性能问题](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="ParamSniffing"></a> 使用参数敏感查询执行计划问题对查询进行故障排除

参数敏感计划 (PSP) 问题是指查询优化器生成的查询执行计划仅适用于某个或某组特定的参数值，而缓存计划对于连续执行操作中所用的参数值并非最佳。 这样一来，非最佳计划可能会导致整体工作负荷吞吐量下降并出现查询性能问题。 有关参数探查和查询处理的详细信息，请参阅[查询处理体系结构指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有多种解决方法用于缓解这些问题, 每个都有关联的折衷和缺点:

- 在每次执行查询时使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示。 此解决方法会将编译时间和增加的 CPU 进行交易, 以获得更好的计划质量。 对于需要高吞吐量的工作负荷，通常无法使用 `RECOMPILE` 选项。
- 使用 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示将实际参数值替代为典型的参数值，以便为大部分可能的参数值生成一个足够好的计划。   此选项要求充分了解最佳参数值和相关的计划特征。
- 使用 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示将实际参数值替代为密度向量平均值。 另一种方法是将传入的参数值捕获到局部变量中，然后在谓词内使用局部变量，而不是使用参数本身。 在此特定修复方法中，平均密度必须*足够好*。
- 使用 [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示完全禁用参数探查。
- 使用 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示防止在缓存中重新编译。 此解决方法假定缓存中已有的通用计划已经*足够好*。 你还可以禁用统计信息自动更新，以便减少收回好计划而编译新的不良计划的可能性。
- 通过显式使用 [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示（通过显式指定、使用查询存储设置特定计划或启用[自动优化](sql-database-automatic-tuning.md)）强制执行计划。
- 将单个过程替换为一组嵌套的过程，可以根据条件逻辑和关联的参数值来使用其中每个过程。
- 创建动态字符串执行来替代静态过程定义。

有关解决这些类型的问题的详细信息, 请参阅博客文章:

- [我气味一个参数](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
- [动态 sql 与参数化查询的计划质量](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)
- [SQL Server 中的 SQL 查询优化方法:参数嗅探](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>排查因参数化不当而导致的编译活动问题

当查询包含文本时，为了减少编译次数，要么数据库引擎选择自动参数化语​​句，要么用户可以将其显式参数化。 使用相同模式但不同文本值进行查询的数量过多会导致 CPU 利用率高。 同样，如果仅将查询部分参数化，导致该查询仍包含文本，则数据库引擎不会将其进一步参数化。  以下是部分参数化查询的示例：

```sql
SELECT * 
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在前面的示例中`t1.c1` , `@p1`将`t2.c2`使用 GUID 作为文本。 在这种情况下，如果更改 `c2` 的值，该查询将被视为不同的查询，并且将进行新的编译。 若要减少前面示例中的编译次数，解决方案还是参数化 GUID。

以下查询显示查询哈希的查询计数，以确定查询是否已正确参数化：

```sql
SELECT  TOP 10  
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
### <a name="factors-influencing-query-plan-changes"></a>影响查询计划更改的因素

重新编译查询执行计划可能会导致生成的查询计划与最初缓存的计划不同。 有多种原因可能导致自动重新编译现有的原始计划：
- 查询引用的架构发生更改
- 对查询引用的表进行数据更改 
- 对查询上下文选项进行更改 

由于各种原因 (包括实例重启、数据库范围内的配置更改、内存压力和显式请求清除缓存), 编译的计划可能会从缓存中弹出。 此外，使用 RECOMPILE 提示意味着不会缓存计划。

重新编译（或者在缓存逐出后重新编译）仍可能导致从最初观察到的计划生成相同的查询执行计划。  如果对计划所做的更改与以前或原始计划的更改有关, 以下是有关查询执行计划更改的最常见说明:

- **更改了物理设计**。 例如, 创建了新的索引, 以便更有效地涵盖查询的要求。 如果查询优化器决定使用该新索引的最佳方式比使用最初为第一次查询执行第一版选择的数据结构, 则可在新的编译中使用新索引。  在编译时, 对被引用对象的任何物理更改都可能会导致新的计划选择。

- **服务器资源差异**。 在一个计划不同于 "系统 A" 和 "系统 B" 的情况下 (资源的可用性, 如可用处理器的数量), 可能会影响生成的计划。  例如，如果一个系统的处理器数较多，则可能会选择并行计划。 

- **不同的统计信息**。 与引用的对象关联的统计信息发生了更改，或者与原始系统的统计信息有本质的差别。  如果统计信息发生更改并重新编译，则查询优化器将使用截至该特定时间点的统计信息。 修改后的统计信息可能具有不同于原始编译的数据分发和频率。  这些更改将用于估算基数（预计要流经逻辑查询树的行数）。  更改基数估算值可以引导我们选择不同的物理运算符和关联的操作顺序。  即使对统计信息进行少量的更改，也可能会导致查询执行计划发生变化。

- **更改了数据库兼容性级别或基数估算器版本**。  更改数据库兼容性级别可以启用新的策略和功能，从而可能导致生成不同的查询执行计划。  除了数据库兼容性级别以外, 禁用或启用跟踪标志4199或更改数据库作用域配置 QUERY_OPTIMIZER_HOTFIXES 的状态还可能会影响编译时的查询执行计划选择。  跟踪标志 9481（强制旧式 CE）和 2312（强制默认 CE）也会影响计划。 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解决问题查询或提供更多资源

识别问题后，可以优化有问题的查询，或升级计算大小或服务层级，以增加 Azure SQL 数据库的容量来满足 CPU 要求。 有关缩放单一数据库资源的信息，请参阅[缩放 Azure SQL 数据库中的单一数据库资源](sql-database-single-database-scale.md)；有关缩放弹性池资源的信息，请参阅[缩放 Azure SQL 数据库中的弹性池资源](sql-database-elastic-pool-scale.md)。 有关缩放托管实例的信息，请参阅[实例级资源限制](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。

### <a name="determine-if-running-issues-due-to-increase-workload-volume"></a>确定运行问题是否是由工作负荷量增加引起的

应用程序流量和工作负荷的增加可能会导致 CPU 利用率的增加，但是，要想正确诊断此问题，必须慎之又慎。 在高 CPU 方案中，回答这些问题以确定 CPU 增加是否确实是由工作负荷量变化引起的：

1. 来自应用程序的查询是否是导致高 CPU 问题的原因？
2. 对于 CPU 消耗量靠前的查询（可以识别）：

   - 确定同一查询是否关联了多个执行计划。 如果是，请确定原因。
   - 对于具有相同执行计划的查询，请确定执行时间是否一致以及执行计数是否增加。 如果是，则可能由于工作负荷增加而导致性能问题。

总而言之，如果查询执行计划没有以不同的方式执行，CPU 利用率却随执行计数的增加而增加，则可能出现与工作负荷增加相关的性能问题。

要得出是工作负荷量变化导致了 CPU 问题的结论并不容易。   需考虑的因素包括： 

- **资源使用率发生了变化**

  以 CPU 长时间增加到 80% 的情况为例。  单凭 CPU 利用率并不能说明工作负荷量发生了变化。  即使应用程序执行的工作负荷完全相同，查询执行计划的回归和数据分布的变化也有可能导致资源使用率的增加。

- **出现了新查询**

   应用程序有可能在不同时间发出一组新的查询。

- **请求数量增加或减少**

   这种情况最能说明工作负荷量的变化。 查询数量并不一定与资源利用率增加相对应。 但是，在其他因素不变的前提下，该指标仍然是一个重要信号。

## <a name="waiting-related-performance-issues"></a>等待相关的性能问题

确定不存在 CPU 消耗量较高的运行相关性能问题后，则表示出现了等待相关的性能问题。 即，由于 CPU 正在等待其他某个资源，因此 CPU 资源未得到有效利用。 对于这种情况，下一步是识别 CPU 资源正在等待哪个资源。 显示最常见等待类型类别的最常见方法包括:

- [查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)每个查询在不同时间段的等待统计信息。 在查询存储中，等待类型合并成等待类别。 等待类别到等待类型的映射在 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) 中提供。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 返回有关操作期间执行的线程遇到的所有等待的信息。 可以使用此聚合视图来诊断 Azure SQL 数据库以及特定查询和批的性能问题。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 返回有关正在等待某个资源的任务的等待队列的信息。

在高 CPU 方案中，查询存储和等待统计信息并不一定反映 CPU 利用率，原因有二：

- CPU 消耗量较高的查询可能仍在执行，查询尚未完成
- 发生故障转移时，正在运行 CPU 消耗量较高的查询

查询存储和等待统计信息跟踪动态管理视图仅显示成功完成的查询和超时查询的结果，而不显示当前正在执行的语句的数据（直到其完成）。 动态管理视图[sys.databases _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)允许您跟踪当前正在执行的查询和关联的辅助进程时间。

如上面的图表中所示，是最常见的等待是：

- 锁（阻塞）
- I/O
- `tempdb` 相关的争用
- 内存授予等待

> [!IMPORTANT]
> 有关使用这些 DMV 排查这些等待相关问题的一组 T-SQL 查询，请参阅：
>
> - [识别 I/O 性能问题](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [识别 `tempdb` 性能问题](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [识别内存授予等待](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox - 等待和闩锁](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox - usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improving-database-performance-with-more-resources"></a>使用更多的资源提高数据库的性能

最后，如果没有可行的措施可以提高数据库的性能，可以更改 Azure SQL 数据库中可用的资源量。 通过更改单个数据库的[DTU 服务层](sql-database-service-tiers-dtu.md), 或随时增加弹性池的 edtu, 分配更多的资源。 或者, 如果使用的是[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md), 请更改服务层或增加分配给数据库的资源。

1. 对于单一数据库，可以根据需要[更改服务层级](sql-database-single-database-scale.md)或[计算资源](sql-database-single-database-scale.md)以提高数据库性能。
2. 对于多个数据库，请考虑使用[弹性池](sql-database-elastic-pool-guidance.md)自动缩放资源。

## <a name="tune-and-refactor-application-or-database-code"></a>优化和重构应用程序或数据库代码

可以更改应用程序代码来最好地利用数据库、更改索引、强制计划，或使用提示来手动让数据库适应工作负荷。 查找有关手动优化和重写[性能指南主题](sql-database-performance-guidance.md)文章中的代码的指南和提示。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并使自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请参阅 [Azure 门户中的优化建议](sql-database-advisor-portal.md)，然后手动应用一些可提高查询性能的建议。
- 通过更改 [Azure SQL 数据库服务层级](sql-database-performance-guidance.md)来更改数据库中可用的资源
