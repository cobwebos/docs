---
title: 监视和性能优化-Azure SQL 数据库 |Microsoft Docs
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
ms.openlocfilehash: 83ff39e9f3b7f95256466c74011e55ebdc22a7a9
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910531"
---
# <a name="monitoring-and-performance-tuning"></a>监视和性能优化

Azure SQL 数据库提供的工具和方法可用于轻松监视使用情况、添加或删除资源（如 CPU、内存或 i/o）、排查潜在问题，并提供改进数据库性能的建议。 Azure SQL 数据库中的功能可以自动修复数据库中的问题。 

自动优化可使数据库适应工作负荷并自动优化性能。 但是，某些自定义问题可能需要进行故障排除。 本文介绍一些最佳实践和一些可用于排查性能问题的工具。

若要确保数据库运行时没有问题，应执行以下操作：
- [监视数据库性能](#monitor-database-performance)，以确保分配给数据库的资源能够处理工作负荷。 如果数据库命中资源限制，请考虑：
   - 识别和优化资源消耗最多的查询。
   - 通过[升级服务层](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)来添加更多的资源。
- [排查性能问题](#troubleshoot-performance-problems)以确定出现潜在问题的原因，并确定问题的根本原因。 确定根本原因后，请采取措施来解决问题。

## <a name="monitor-database-performance"></a>监视数据库性能

若要监视 Azure 中的 SQL 数据库的性能，请先监视与所选数据库性能级别相关的资源。 监视以下资源：
 - **CPU 使用率**：查看数据库是否长时间达到 100% 的 CPU 使用率。 高 CPU 使用率可能表明需要标识和优化使用最多计算能力的查询。 高 CPU 使用率也可能表明数据库或实例应升级到更高的服务层。 
 - **等待统计信息**：使用[sys. _os_wait_stats （transact-sql）](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)确定查询等待的时间。 查询可以等待资源、队列等待或外部等待。 
 - **IO 使用情况**：查看数据库是否达到了基础存储的 IO 限制。
 - **内存使用率**：数据库或实例的可用内存量与 Vcore 数成正比。 请确保内存足以满足工作负荷。 页生存期是可以指示是否很快从内存中删除页面的参数之一。

Azure SQL Database 服务包含一些工具和资源，可帮助你排除故障和解决潜在的性能问题。 您可以通过查看[性能优化建议](sql-database-advisor.md)来确定提高和优化查询性能的机会，无需更改资源。 

缺少索引与查询优化不足是数据库性能不佳的常见原因。 可以应用优化建议来提高工作负荷的性能。 还可以通过应用所有已确定的建议，让 Azure SQL 数据库[自动优化查询性能](sql-database-automatic-tuning.md)。 然后，验证建议是否改进了数据库性能。

> [!NOTE]
> 索引仅适用于单一数据库和弹性池。 索引在托管实例中不可用。

从以下选项中进行选择，以监视数据库性能并对其进行故障排除：

- 在[Azure 门户](https://portal.azure.com)中，选择 " **SQL 数据库**" 并选择数据库。 在 "**监视**" 图表中，查找接近其最大利用率的资源。 默认会显示 DTU 消耗量。 选择 "**编辑**" 可更改显示的时间范围和值。
- SQL Server Management Studio 的工具提供许多有用的报告，如[性能仪表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard)。 使用这些报表来监视资源使用情况，并确定资源使用排名靠前的查询。 您可以使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)来确定性能回归的查询。
- 在[Azure 门户](https://portal.azure.com)中，使用[Query Performance Insight](sql-database-query-performance.md)确定使用资源最多的查询。 此功能仅适用于单一数据库和弹性池。
- 使用[SQL 数据库顾问](sql-database-advisor-portal.md)查看建议，以帮助您创建和删除索引、参数化查询以及解决架构问题。 此功能仅适用于单一数据库和弹性池。
- 使用[AZURE SQL 智能见解](sql-database-intelligent-insights.md)自动监视数据库性能。 检测到性能问题时，将生成诊断日志。 日志提供了问题的详细信息和根本原因分析（RCA）。 如果可能，将提供性能改进建议。
- [启用自动优化](sql-database-automatic-tuning-enable.md)以允许 Azure SQL 数据库自动修复性能问题。
- 使用[动态管理视图（dmv）](sql-database-monitoring-with-dmvs.md)、[扩展事件](sql-database-xevent-db-diff-from-svr.md)和[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)获取有关性能问题的更详细故障排除的帮助。

> [!TIP]
> 确定性能问题后，请查看我们的[性能指南](sql-database-performance-guidance.md)，查找提高 Azure SQL 数据库性能的技巧。

## <a name="troubleshoot-performance-problems"></a>排查性能问题

若要诊断并解决性能问题，请先找出每个活动查询的状态，以及导致与每个工作负荷状态相关的性能问题的条件。 若要改进 Azure SQL 数据库性能，需要了解应用程序中的每个活动查询请求是处于 "正在运行" 状态还是 "正在等待" 状态。 解决 Azure SQL 数据库中的性能问题时，请记住下面的关系图。

![工作负荷状态](./media/sql-database-monitor-tune-overview/workload-states.png)

工作负荷中的性能问题可能是由 CPU 争用（*运行相关*的条件）或正在等待某个事物的单个查询（*等待相关*的条件）引起的。

运行相关的问题可能由以下原因引起：
- **编译问题**：SQL 查询优化器可能会生成一个不理想的计划，因为有陈旧的统计信息，对要处理的行数的估计值不正确，或者所需内存的估计值不准确。 如果知道查询是在过去或另一个实例（托管实例或 SQL Server 实例）上更快执行的，请比较实际的执行计划，以确定它们是否不同。 尝试应用查询提示或重新生成统计信息或索引，以获得更好的计划。 在 Azure SQL 数据库中启用自动计划更正，以自动缓解这些问题。
- **执行问题**：如果查询计划是最佳的，则可能会达到数据库的资源限制，如日志写入吞吐量。 或者，它可能使用应重新生成的零碎索引。 当大量并发查询需要相同的资源时，也会发生执行问题。 与*等待相关*的问题通常与执行问题相关，因为没有有效执行的查询可能会等待一些资源。

等待相关的问题可能由以下原因引起：
- **阻止**：一个查询可能持有数据库中对象的锁，而其他查询则尝试访问相同的对象。 可以通过使用 Dmv 或监视工具来确定阻塞查询。
- **IO 问题**：查询可能正在等待将页写入数据文件或日志文件。 在这种情况下， `INSTANCE_LOG_RATE_GOVERNOR`请`WRITE_LOG`检查 DMV `PAGEIOLATCH_*`中的、或等待统计信息。
- **TempDB 问题**：如果工作负荷使用临时表，或者计划中存在 TempDB 溢出，则查询可能会遇到 TempDB 吞吐量问题。 
- **与内存相关的问题**：如果工作负荷没有足够的内存，则页生存期可能会下降，或者查询可能会获得比所需内存更少的内存。 在某些情况下，查询优化器中的内置智能将修复与内存相关的问题。
 
以下各节说明如何识别和解决某些类型的问题。

## <a name="performance-problems-related-to-running"></a>与运行相关的性能问题

一般原则是，如果 CPU 使用率持续等于或高于 80%，则性能问题正在运行-相关。 与运行相关的问题可能是由于 CPU 资源不足引起的。 或者，它可能与以下条件之一相关：

- 运行查询过多
- 编译查询过多
- 使用不理想查询计划的一个或多个执行查询

如果发现运行相关的性能问题，则您的目标是通过使用一种或多种方法来确定确切的问题。 这些方法是识别与运行相关的问题的最常见方式：

- 使用[Azure 门户](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)监视 CPU 使用率百分比。
- 使用以下[dmv](sql-database-monitoring-with-dmvs.md)：

  - [Sys.databases _db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 SQL 数据库的 CPU、i/o 和内存消耗。 即使数据库中没有活动，也会每隔15秒间隔一行。 历史数据将保留一小时。
  - [Resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回适用于 Azure SQL 数据库的 CPU 使用率和存储数据。 数据按五分钟间隔收集和聚合。

> [!IMPORTANT]
> 若要解决使用 _db_resource_stats 和 resource_stats Dmv 的 T-sql 查询的 CPU 使用问题，请参阅[确定 cpu 性能问题](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="ParamSniffing"></a>具有 PSP 问题的查询

当查询优化器生成仅对特定参数值（或一组值）最佳的查询执行计划时，将发生参数敏感计划（PSP）问题，且缓存计划对于连续使用的参数值不是最佳的执行. 这样的计划可能会导致查询性能问题并降低整体工作负荷吞吐量。 

有关参数嗅探和查询处理的详细信息，请参阅[查询处理体系结构指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

几种解决方法可以减轻 PSP 问题。 每个解决方法都有关联的折衷与缺点：

- 在每次执行查询时使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示。 此解决方法以编译时间和 CPU 增加为代价来换取更好的计划质量。 对于`RECOMPILE`需要高吞吐量的工作负荷，通常不可能使用此选项。
- 使用[选项（OPTIMIZE FOR ...）](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查询提示可使用典型的参数值替代实际参数值，该参数值可生成一个足以满足大多数参数值可能性的计划。 此选项要求充分了解最佳参数值和相关的计划特征。
- 使用[选项（"优化未知](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)值"）查询提示替代实际参数值，而改用密度向量平均。 还可以通过捕获局部变量中的传入参数值，然后在谓词中使用局部变量，而不是使用参数本身，来实现此目的。 对于此修补程序，平均密度必须*足够好*。
- 使用[DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查询提示完全禁用参数探查。
- 使用[KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)查询提示可防止在缓存中重新编译。 此解决方法假设已有足够的通用计划是缓存中的计划。 你还可以禁用自动统计信息更新，以减少将逐出良好计划并将编译新的错误计划的机会。
- 通过重写查询并在查询文本中添加提示来强制[执行](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query)计划。 或通过使用查询存储或启用[自动优化](sql-database-automatic-tuning.md)来设置特定计划。
- 将单个过程替换为一组嵌套的过程，可以根据条件逻辑和关联的参数值来使用其中每个过程。
- 创建动态字符串执行来替代静态过程定义。

有关解决 PSP 问题的详细信息，请参阅以下博客文章：

- [我气味一个参数](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)
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

在此示例中`t1.c1` ， `@p1`将采用`t2.c2` ，但继续采用 GUID 作为文本。 在这种情况下，如果您更改的`c2`值，则查询将被视为不同的查询，并且将发生新的编译。 若要在此示例中减少编译，还应参数化 GUID。

下面的查询按查询哈希显示查询计数，以确定查询是否已正确参数化：

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

### <a name="factors-that-affect-query-plan-changes"></a>影响查询计划更改的因素

重新编译查询执行计划可能会导致生成的查询计划与原始缓存计划不同。 由于各种原因，可能会自动重新编译现有的原始计划：
- 此查询引用了架构中的更改。
- 查询引用对表的数据更改。 
- 查询上下文选项已更改。

由于各种原因，编译的计划可能会从缓存中弹出，例如：

- 实例重启。
- 数据库范围的配置更改。
- 内存压力。
- 用于清除缓存的显式请求。

如果使用重新编译提示，则不会缓存计划。

重新编译（或缓存逐出后的新编译）仍可能导致生成与原始相同的查询执行计划。 如果计划从以前的或原始计划中更改，则可能会出现以下说明：

- **更改的物理设计**：例如，新创建的索引可以更有效地涵盖查询的要求。 如果查询优化器决定使用新索引比最初为查询执行的第一个版本选择的数据结构更好，则可以对新的编译使用新的索引。  在编译时，对被引用对象的任何物理更改都可能会导致新的计划选择。

- **服务器资源差异**：如果一个系统中的计划与另一个系统中的计划不同，则资源可用性（如可用处理器的数量）可能会影响生成的计划。  例如，如果一个系统有多个处理器，则可能会选择并行计划。 

- **不同统计信息**：与引用对象关联的统计信息可能已更改，或者可能与原始系统的统计信息有很大差异。  如果更改了统计信息和重新编译，则查询优化器会在更改时使用从开始的统计信息。 修订的统计信息的数据分布和频率可能与原始编译的不同。  这些更改用于创建基数估算。 （*基数估算值*是预期通过逻辑查询树流动的行数。）对基数估计的更改可能会导致你选择不同的物理运算符和操作的相关顺序。  即使对统计信息进行少量的更改，也可能会导致查询执行计划发生变化。

- **更改的数据库兼容级别或基数估计器版本**：更改数据库兼容级别可以启用可能导致不同查询执行计划的新策略和功能。  除了数据库兼容性级别外，已禁用或已启用的跟踪标志4199或数据库作用域配置 QUERY_OPTIMIZER_HOTFIXES 的已更改状态还可能会影响编译时的查询执行计划选择。  跟踪标志9481（强制旧版 CE）和2312（强制默认 CE）还会影响计划。 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解决问题查询或提供更多资源

确定问题后，可以调整问题查询或升级计算大小或服务层，以便增加 SQL 数据库的容量以满足 CPU 需求。 

有关详细信息，请参阅在[AZURE Sql 数据库中缩放单一数据库资源](sql-database-single-database-scale.md)和[缩放 azure sql 数据库中的弹性池资源](sql-database-elastic-pool-scale.md)。 有关缩放托管实例的信息，请参阅[实例级资源限制](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。

### <a name="performance-problems-caused-by-increased-workload-volume"></a>由于工作负荷量增加而导致的性能问题

应用程序流量和工作负荷量增加会导致 CPU 使用率增加。 但必须小心地诊断此问题。 出现高 CPU 问题时，请回答以下问题，确定增加是否是由工作负荷量的更改引起的：

- 应用程序中的查询是否导致了高 CPU 问题的原因？
- 对于最常见的 CPU 消耗查询，您可以确定：

   - 是否有多个与同一查询相关联的执行计划？ 如果是，为什么？
   - 对于具有相同执行计划的查询，执行时间是否一致？ 执行计数是否增加？ 如果是这样，则工作负荷增加可能会导致性能问题。

总之，如果查询执行计划未以不同的方式执行，但 CPU 使用率随执行计数一起增加，则性能问题可能与工作负荷增加相关。

确定驱动 CPU 问题的工作负荷量发生变化并不是很容易。 请考虑以下因素： 

- **更改的资源使用情况**：例如，假设 CPU 使用率长时间增加到 80%。  仅 CPU 使用率并不意味着工作负荷量发生变化。 即使应用程序执行相同的工作负载，查询执行计划中的回归和数据分发的更改也会导致更多的资源使用。

- **新查询的外观**：应用程序可以在不同时间驱动一组新查询。

- **请求数增加或减少**：此方案是工作负荷最明显的度量值。 查询数量并不一定与资源利用率增加相对应。 但是，如果其他因素未改变，此指标仍是一个非常重要的信号。

## <a name="waiting-related-performance-problems"></a>与等待相关的性能问题 

如果确定性能问题与 CPU 使用率高或运行状况不相关，则问题与等待有关。 也就是说，CPU 资源并未有效地使用，因为 CPU 正在等待某个其他资源。 在这种情况下，请确定 CPU 资源正在等待的资源。 

这些方法通常用于显示等待类型的顶级类别：

- 使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)可查找一段时间内每个查询的等待统计信息。 在查询存储中，等待类型合并成等待类别。 可以在[query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)中找到等待类别到等待类型的映射。
- 使用[sys.databases _db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)返回有关在操作期间执行的线程所遇到的所有等待的信息。 你可以使用此聚合视图诊断 Azure SQL 数据库的性能问题，以及特定查询和批处理。
- 使用[sys.databases _os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)返回有关正在等待某个资源的任务队列的信息。

在高 CPU 情况下，如果下列情况，查询存储和等待统计信息可能不会反映 CPU 使用率：

- 高 CPU 消耗查询仍在执行。
- 发生故障转移时，消耗大量 CPU 的查询正在运行。

跟踪查询存储和等待统计信息的 Dmv 只显示已成功完成和超时查询的结果。 在语句完成之前，它们不会显示当前正在执行的语句的数据。 使用动态管理视图[sys.databases _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)跟踪当前正在执行的查询和关联的工作进程时间。

本文开头附近的图表显示最常见的等待：

- 锁（阻塞）
- I/O
- 与 TempDB 相关的争用
- 内存授予等待

> [!IMPORTANT]
> 有关使用 Dmv 对等待相关的问题进行故障排除的一组 T-sql 查询，请参阅：
>
> - [识别 I/O 性能问题](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [识别内存授予等待](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox 等待和闩锁](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>利用更多资源提高数据库性能

如果没有可操作项可以提高数据库性能，你可以更改 Azure SQL 数据库中的可用资源量。 通过更改单个数据库的[DTU 服务层](sql-database-service-tiers-dtu.md)分配更多的资源。 或者随时增加弹性池的 Edtu。 或者，如果使用的是[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)，请更改服务层或增加分配给数据库的资源。

对于单一数据库，可以根据需要[更改服务层或计算资源](sql-database-single-database-scale.md)以提高数据库性能。 对于多个数据库，请考虑使用[弹性池](sql-database-elastic-pool-guidance.md)自动缩放资源。

## <a name="tune-and-refactor-application-or-database-code"></a>优化和重构应用程序或数据库代码

您可以优化数据库的应用程序代码、更改索引、强制计划，或使用提示来手动将数据库调整为工作负荷。 有关手动优化和重写代码的信息，请参阅[性能优化指南](sql-database-performance-guidance.md)。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并让自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请查看[Azure 门户中的优化建议](sql-database-advisor-portal.md)。 手动应用改进查询性能的建议。
- 通过更改[AZURE SQL 数据库服务层](sql-database-performance-guidance.md)来更改数据库中可用的资源。
