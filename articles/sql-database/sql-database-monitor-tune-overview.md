---
title: 监视和性能优化-Azure SQL 数据库
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
ms.openlocfilehash: c11112963ec82a0e53df156048495e7b5141bcb7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687762"
---
# <a name="monitoring-and-performance-tuning"></a>监视和性能优化

Azure SQL 数据库提供所需的工具和方法，用于轻松监视使用情况、添加或删除资源（例如 CPU、内存或 I/O）、排查潜在问题，以及提供建议来提高数据库的性能。 Azure SQL 数据库中的功能可以自动修复数据库的问题。 

自动优化可让数据库适应工作负荷并自动优化性能。 不过，可能需要排查某些自定义的问题。 本文介绍一些最佳做法，以及可用于排查性能问题的一些工具。

若要确保数据库正常运行，应该：
- [监视数据库性能](#monitor-database-performance)，以确保分配给数据库的资源能够处理工作负荷。 如果数据库即将达到资源限制，请考虑：
   - 识别资源消耗量最大的查询并对其进行优化。
   - 通过[升级服务层级](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)来添加更多的资源。
- [排查性能问题](#troubleshoot-performance-problems)，以识别潜在问题的原因，并识别问题的根本原因。 识别根本原因后，采取措施来解决该问题。

## <a name="monitor-database-performance"></a>监视数据库性能

若要监视 Azure 中的 SQL 数据库的性能，首先需要监视相对于所选数据库性能级别使用的资源。 监视以下资源：
 - **Cpu 使用率**：检查数据库是否长时间达到100% 的 CPU 使用率。 CPU 使用率较高可能意味着需要识别并优化使用大部分算力的查询。 CPU 使用率较高还可能意味着需要将数据库或实例升级到更高的服务层级。 
 - **等待统计信息**：使用[Sys.databases _os_wait_stats （transact-sql）](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)确定查询等待多长时间。 查询可能正在等待资源，发生了队列等待或外部等待。 
 - **IO 使用率**：检查数据库是否达到基础存储的 IO 限制。
 - **内存使用率**：数据库或实例的可用内存量与 vcore 数成正比。 确保内存对于工作负荷而言足够。 页生存期是可以指示是否很快从内存中删除页面的参数之一。

Azure SQL 数据库服务包含相应的工具和资源用于帮助排查和解决潜在的性能问题。 可以通过查看[性能优化建议](sql-database-advisor.md)找到机会来改进和优化查询性能，而无需更改资源。 

缺少索引与查询优化不足是数据库性能不佳的常见原因。 可以应用这些优化建议来提高工作负荷的性能。 还可以通过应用所有已确定的建议来让 Azure SQL 数据库[自动优化查询性能](sql-database-automatic-tuning.md)。 然后，确认这些建议是否提高了数据库性能。

> [!NOTE]
> 索引仅适用于单一数据库和弹性池。 索引在托管实例中不可用。

选择以下选项来监视数据库性能并对其进行故障排除：

- 在 [Azure 门户](https://portal.azure.com)中选择“SQL 数据库”，然后选择该数据库。 在“监视”图表查找接近其最大利用率的资源。 默认会显示 DTU 消耗量。 选择“编辑”更改所显示的时间范围和值。
- SQL Server Management Studio 等工具提供许多有用的报告，例如[性能仪表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard)。 使用这些报告可以监视资源使用率，并识别资源消耗量最大的查询。 可以使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)来识别性能倒退的查询。
- 在 [Azure 门户](https://portal.azure.com)中，使用 [Query Performance Insight](sql-database-query-performance.md) 识别占用最多资源的查询。 此功能仅在单一数据库和弹性池中可用。
- 使用 [SQL 数据库顾问](sql-database-advisor-portal.md)查看建议，以帮助创建和删除索引、参数化查询以及解决架构问题。 此功能仅在单一数据库和弹性池中可用。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 自动监视数据库性能。 检测到性能问题时，会生成诊断日志。 日志提供问题的详细信息和根本原因分析 (RCA)。 在可能的情况下提供性能改善建议。
- [启用自动优化](sql-database-automatic-tuning-enable.md)，让 Azure SQL 数据库自动解决性能问题。
- 借助[动态管理视图 (DMV)](sql-database-monitoring-with-dmvs.md)、[扩展事件](sql-database-xevent-db-diff-from-svr.md)和[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)更细致地排查性能问题。

> [!TIP]
> 识别性能问题后，查看我们的[性能指南](sql-database-performance-guidance.md)找到提高 Azure SQL 数据库性能的方法。

## <a name="troubleshoot-performance-problems"></a>排查性能问题

若要诊断并解决性能问题，请先确定每个活动查询的状态，以及哪些条件导致出现了与每种工作负荷状态相关的性能问题。 若要提高 Azure SQL 数据库的性能，需要了解来自应用程序的每个活动查询请求是处于运行状态还是等待状态。 排查 Azure SQL 数据库的性能问题时，请注意以下图表。

![工作负荷状态](./media/sql-database-monitor-tune-overview/workload-states.png)

工作负荷的性能问题可能由 CPU 争用（运行相关的条件）或单个查询正在等待某个资源（等待相关的条件）造成。

运行相关的问题的可能原因如下：
- **编译问题**： SQL 查询优化器可能会生成一个不理想的计划，因为过时的统计信息、对要处理的行数的估计值不正确，或者所需内存的估计值不准确。 如果我们知道该查询在过去或者在其他实例（托管实例或 SQL Server 实例）上的执行速度较快，则可以比较实际的执行计划，以找到其差异。 尝试应用查询提示，或者重新生成统计信息或索引，以获得更好的计划。 在 Azure SQL 数据库中启用自动计划更正，以自动缓解这些问题。
- **执行问题**：如果查询计划是最佳的，则可能会达到数据库的资源限制，如日志写入吞吐量。 或者该计划可能正在使用应重新生成的碎片化索引。 当大量并发查询需要相同的资源时，也可能发生执行问题。 等待相关的问题往往与执行问题有关，因为未有效执行的查询可能正在等待某些资源。

等待相关的问题的可能原因如下：
- **阻止**：一个查询可能持有数据库中对象的锁，而其他查询则尝试访问相同的对象。 可以使用 DMV 或监视工具来识别阻塞的查询。
- **IO 问题**：查询可能正在等待将页写入数据文件或日志文件。 在这种情况下，请检查 DMV 中的 `INSTANCE_LOG_RATE_GOVERNOR`、`WRITE_LOG` 或 `PAGEIOLATCH_*` 等待统计信息。
- **TempDB 问题**：如果工作负荷使用临时表，或者计划中存在 tempdb 溢出，则查询可能会遇到 tempdb 吞吐量问题。 
- **与内存相关的问题**：如果工作负荷没有足够的内存，则页生存期可能会下降，或者查询的内存可能会超过所需的内存。 在某些情况下，查询优化器中的内置智能会解决内存相关的问题。
 
以下部分介绍如何识别并排查某些类型的问题。

## <a name="performance-problems-related-to-running"></a>运行相关的性能问题

一般指导原则是，如果 CPU 使用率一贯达到或超过 80%，则表示性能问题与运行相关。 运行相关的问题可能是 CPU 资源不足造成的。 或者，可能与以下状况之一相关：

- 运行查询过多
- 编译查询过多
- 一个或多个执行查询使用了欠佳的查询计划

如果你遇到了运行相关的性能问题，则目标应是使用一种或多种方法识别确切的问题。 下面是识别运行相关问题的最常用方法：

- 使用 [Azure 门户](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)监视 CPU 使用率百分比。
- 使用以下 [DMV](sql-database-monitoring-with-dmvs.md)：

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 SQL 数据库的 CPU、I/O 和内存消耗量。 即使数据库中没有活动，也会每隔 15 秒返回一行数据。 历史数据将保留一小时。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) DMV 返回 Azure SQL 数据库的 CPU 使用率和存储数据。 在 5 分钟间隔内收集并聚合数据。

> [!IMPORTANT]
> 若要排查使用 sys.dm_db_resource_stats 和 sys.resource_stats DMV 的 T-SQL 查询的 CPU 使用率问题，请参阅[识别 CPU 性能问题](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)。

### <a name="ParamSniffing"></a> 存在 PSP 问题的查询

当查询优化器生成的查询执行计划仅适用于某个或某组特定的参数值，而缓存计划对于连续执行操作中所用的参数值并非最佳时，将发生参数敏感计划 (PSP) 问题。 这样，并非最佳的计划可能导致查询性能问题，并降低整体工作负荷吞吐量。 

有关参数探查和查询处理的详细信息，请参阅[查询处理体系结构指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

有多种解决方法可以缓解 PSP 问题。 每种解决方法各有利弊：

- 在每次执行查询时使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示。 此解决方法以编译时间和 CPU 增加为代价来换取更好的计划质量。 对于需要高吞吐量的工作负荷，通常无法使用 `RECOMPILE` 选项。
- 使用 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示将实际参数值替代为典型的参数值，以便为大部分可能的参数值生成一个足够好的计划。 此选项要求充分了解最佳参数值和相关的计划特征。
- 使用 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示替代实际参数值，并改用密度向量平均值。 还可以将传入的参数值捕获到局部变量中，然后在谓词内使用局部变量，而不是使用参数本身。 对于此修复方法，平均密度必须足够好。
- 使用 [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示完全禁用参数探查。
- 使用 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示防止在缓存中重新编译。 此解决方法假定缓存中已有的通用计划已经足够好。 还可以禁用统计信息自动更新，以减少逐出良好计划而编译新的不良计划的可能性。
- 显式使用 [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示，通过重写查询并在查询文本中添加提示来强制执行计划。 或者，使用查询存储或启用[自动优化](sql-database-automatic-tuning.md)来设置特定的计划。
- 将单个过程替换为一组嵌套的过程，可以根据条件逻辑和关联的参数值来使用其中每个过程。
- 创建动态字符串执行来替代静态过程定义。

有关解决 PSP 问题的详细信息，请参阅以下博客文章：

- [I smell a parameter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)（探查参数）
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

重新编译查询执行计划可能会导致生成的查询计划与最初缓存的计划不同。 有多种原因可能导致自动重新编译现有的原始计划：
- 查询引用的架构发生更改。
- 对查询引用的表进行数据更改。 
- 查询上下文选项已更改。

编译的计划可能会出于各种原因从缓存中逐出，例如：

- 实例重启。
- 数据库范围的配置更改。
- 内存压力。
- 清除缓存的显式请求。

如果使用 RECOMPILE 提示，则不会缓存计划。

重新编译（或者在缓存逐出后重新编译）仍可能导致从生成与原始计划相同的查询执行计划。 如果计划与以前的或原始的计划不同，可能的解释如下：

- **更改了物理设计**：例如，新创建的索引可以更有效地涵盖查询的要求。 如果查询优化器认为利用这些新索引比最初选择用于第一个版本的查询执行的数据结构更有利，则可以在新的编译中使用这些新索引。  对引用的对象进行任何物理更改可能会导致在编译时生成新的计划选项。

- **服务器资源差异**：当一个系统中的计划与另一个系统中的计划不同时，资源可用性（如可用处理器的数量）可能会影响生成的计划。  例如，如果一个系统的处理器数较多，则可能会选择并行计划。 

- **不同统计信息**：与引用对象关联的统计信息可能已更改，或者可能与原始系统的统计信息有很大差异。  如果统计信息发生更改并重新编译，则查询优化器将使用从更改时间开始的统计信息。 修改后的统计信息的数据分布和频率可能不同于原始编译中的情况。  这些更改用于创建基数估算。 （*基数估算值*是预期通过逻辑查询树流动的行数。）对基数估计的更改可能会导致你选择不同的物理运算符和操作的相关顺序。  即使对统计信息进行少量的更改，也可能会导致查询执行计划发生变化。

- **更改的数据库兼容级别或基数估计器版本**：对数据库兼容性级别的更改可以启用可能导致不同查询执行计划的新策略和功能。  除了数据库兼容性级别以外，禁用或启用跟踪标志 4199 或更改数据库范围的配置 QUERY_OPTIMIZER_HOTFIXES 的状态也可能会影响编译时的查询执行计划选项。  跟踪标志 9481（强制旧式 CE）和 2312（强制默认 CE）也会影响计划。 

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解决问题查询或提供更多资源

识别问题后，可以优化有问题的查询，或升级计算大小或服务层级，以增加 SQL 数据库的容量来满足 CPU 要求。 

有关详细信息，请参阅[在 Azure SQL 数据库中缩放单一数据库资源](sql-database-single-database-scale.md)和[在 Azure SQL 数据库中缩放弹性池资源](sql-database-elastic-pool-scale.md)。 有关缩放托管实例的信息，请参阅[服务层资源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

### <a name="performance-problems-caused-by-increased-workload-volume"></a>工作负荷量增加导致的性能问题

应用程序流量和工作负荷量的增加可能会导致 CPU 使用率增大。 但是，要想正确诊断此问题，必须慎之又慎。 如果出现 CPU 使用率偏高的情况，请回答以下问题来确定 CPU 使用率增大是否由工作负荷量变化而引起：

- 来自应用程序的查询是否是导致高 CPU 问题的原因？
- 对于可以识别的 CPU 消耗量靠前的查询：

   - 同一查询是否关联了多个执行计划？ 如果是，为什么？
   - 对于具有相同执行计划的查询，执行时间是否一致？ 执行计数是否增加？ 如果是，则性能问题可能是由工作负荷增加造成。

总而言之，如果查询执行计划没有以不同的方式执行，CPU 使用率却随执行计数的增加而增加，则可能出现了与工作负荷增加相关的性能问题。

要得出是工作负荷量变化导致 CPU 问题的结论并不容易。 请考虑以下因素： 

- **更改的资源使用**情况：例如，假设 CPU 使用率长时间增加到80%。  单凭 CPU 使用率并不能说明工作负荷量发生了变化。 即使应用程序执行的工作负荷完全相同，查询执行计划的回归和数据分布的变化也有可能导致资源使用率的增加。

- **新查询的外观**：应用程序可能会在不同时间驱动一组新查询。

- **请求数增加或减少**：此方案是工作负荷最明显的度量值。 查询数量并不一定与资源利用率增加相对应。 但是，在其他因素不变的前提下，该指标仍然是一个重要信号。

## <a name="waiting-related-performance-problems"></a>等待相关的性能问题 

如果你确定性能问题与 CPU 使用率较高或运行无关，则问题与等待相关。 即，由于 CPU 正在等待其他某个资源，因此 CPU 资源未得到有效利用。 对于这种情况，请识别 CPU 资源正在等待哪个资源。 

用于显示最常见等待类别的最常用方法如下：

- 使用[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)查找每个查询在不同时间段的等待统计信息。 在查询存储中，等待类型合并成等待类别。 可在 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) 中找到等待类别到等待类型的映射。
- 使用 [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 返回有关操作期间执行的线程遇到的所有等待的信息。 可以使用此聚合视图来诊断 Azure SQL 数据库以及特定查询和批的性能问题。
- 使用 [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 返回有关正在等待某个资源的任务队列的信息。

在 CPU 使用率偏高时，如果存在以下情况，查询存储和等待统计信息可能不会反映 CPU 使用率：

- CPU 消耗量较高的查询仍在执行。
- 发生故障转移时，正在运行 CPU 消耗量较高的查询。

跟踪查询存储和等待统计信息的 DMV 仅显示成功完成的查询和超时查询的结果。 它们不显示当前正在执行的语句的数据（直到其完成）。 使用动态管理视图 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 跟踪当前正在执行的查询以及相关的工作线程时间。

本文开头附近的图表显示最常见的等待为：

- 锁（阻塞）
- I/O
- 与 TempDB 相关的争用
- 内存授予等待

> [!IMPORTANT]
> 有关使用 DMV 排查等待相关问题的一组 T-SQL 查询，请参阅：
>
> - [识别 I/O 性能问题](sql-database-monitoring-with-dmvs.md#identify-io-performance-issues)
> - [识别内存授予等待](sql-database-monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues)
> - [TigerToolbox 等待和闩锁](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="improve-database-performance-with-more-resources"></a>使用更多资源提高数据库性能

如果没有任何可行的措施能够改进数据库性能，你可以更改 Azure SQL 数据库中可用的资源量。 通过更改单一数据库的 [DTU 服务层级](sql-database-service-tiers-dtu.md)来分配更多资源。 或者随时增加弹性池的 eDTU。 或者，如果使用[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)，则可更改服务层级或增加分配给数据库的资源。

对于单一数据库，可以根据需要[更改服务层级或计算资源](sql-database-single-database-scale.md)来提高数据库性能。 对于多个数据库，请考虑使用 [弹性池](sql-database-elastic-pool-guidance.md) 自动调整资源规模。

## <a name="tune-and-refactor-application-or-database-code"></a>优化和重构应用程序或数据库代码

可以优化数据库的应用程序代码、更改索引、强制实施计划，或使用提示来手动使数据库适应工作负荷。 有关手动优化和重写代码的信息，请参阅[性能优化指南](sql-database-performance-guidance.md)。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并让自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请查看 [Azure 门户中的优化建议](sql-database-advisor-portal.md)。 手动应用可以提高查询性能的建议。
- 通过更改 [Azure SQL 数据库服务层级](sql-database-performance-guidance.md)来更改数据库中可用的资源。
