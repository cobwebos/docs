---
title: 监视和性能优化 - Azure SQL 数据库 | Microsoft Docs
description: 有关通过评估和改进来调整 Azure SQL 数据库性能的提示。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 3c809638cef89d111a032e5876b1f2f1b2c1eb7b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602340"
---
# <a name="monitoring-and-performance-tuning"></a>监视和性能优化

Azure SQL 数据库由系统自动管理，它是一个灵活的数据服务，可在其中轻松监视使用情况、添加或删除资源（CPU、内存、I/O）、查找可以提高数据库性能的建议，或者让数据库适应你的工作负荷并自动优化性能。

## <a name="monitoring-database-performance"></a>监视数据库性能

若要监视 Azure 中的 SQL 数据库的性能，首先需要监视所选数据库性能级别相关的资源利用率。 在 Azure SQL 数据库中，可以通过查看[性能优化建议](sql-database-advisor.md)来找到提高和优化查询性能的机会，而无需更改资源。 缺少索引与查询优化不足是数据库性能不佳的常见原因。 可以应用这些优化建议来提高工作负荷的性能。 此外，可以通过应用所有已确定的建议并验证它们是否可以提高数据库的性能，让 Azure SQL 数据库[自动优化查询的性能](sql-database-automatic-tuning.md)。

可使用以下选项来监视数据库性能以及对其进行故障排除：

- 在 [Azure 门户](https://portal.azure.com)中单击“SQL 数据库”，选择数据库，然后使用“监视”图表查找接近其上限的资源。 默认会显示 DTU 消耗量。 单击“**编辑**”更改显示的时间范围和值。
- 使用 [Query Performance Insight](sql-database-query-performance.md) 来识别资源消耗量最大的查询。
- 使用 [Azure SQL 数据库顾问](sql-database-advisor-portal.md)查看有关创建和删除索引、参数化查询，以及解决架构问题的建议。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 自动监视数据库性能。 检测到性能问题后，将生成一个包含问题的详细信息和根本原因分析 (RCA) 的诊断日志。 在可能的情况下提供性能改善建议。
- [启用自动优化](sql-database-automatic-tuning-enable.md)，并让 Azure SQL 数据库自动修复已识别到的性能问题。
- 使用[动态管理视图 (DMV)](sql-database-monitoring-with-dmvs.md)、[扩展事件](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)和[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)更细致地排查性能问题。

> [!TIP]
> 请参阅[性能指南](sql-database-performance-guidance.md)，了解在使用上述一种或多种方法识别性能问题后，可以利用哪些技术来提高 Azure SQL 数据库的性能。

## <a name="monitor-databases-using-the-azure-portal"></a>使用 Azure 门户监视数据库

在 [Azure 门户](https://portal.azure.com/)中，可以通过选择数据库并单击“监视”图表来监视单一数据库的利用率。 这会显示“指标”窗口，可通过单击“编辑图表”按钮来对其进行更改。 添加以下指标：

- CPU 百分比
- DTU 百分比
- 数据 IO 百分比
- 数据库大小百分比

添加这些指标后，可以继续在“监视”图表上查看它们，并可在“指标”窗口上查看更多详细信息。 **DTU** 的平均利用率百分比。 有关服务层的详细信息，请参阅[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)文章。  

![监视数据库服务层的性能。](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

还可以针对性能指标配置警报。 在“指标”窗口中单击“添加警报”按钮。 按照向导说明来配置警报。 可以选择在指标超出特定阈值或指标低于特定阈值时显示警报。

例如，如果预期数据库的工作负荷会增长，则可以选择配置在数据库的任何性能指标达到 80% 时发出电子邮件警报。 可以将此警报用作预警，以确定你何时需要切换到下一个更高的计算大小。

性能指标还可以帮助你确定是否能够降级到更低的计算大小。 假定你正在使用一个标准 S2 数据库并且所有性能指标均显示该数据库在任何给定时间平均的使用率不超过 10%。 很可能该数据库在标准 S1 中会很好地正常工作。 但是，在决定转换到更低的计算大小之前，请注意出现峰值或波动情况的工作负荷。

## <a name="troubleshoot-performance-issues"></a>排查性能问题

若要诊断并解决性能问题，请先了解每个活动查询的状态，以及哪些条件导致出现了与每种工作负荷状态相关的性能问题。 若要提高 Azure SQL 数据库的性能，请了解来自应用程序的每个活动查询请求是处于运行状态还是等待状态。 在 Azure SQL 数据库中排查性能问题时，请在阅读本文以诊断并解决性能问题时注意以下图表。

![工作负荷状态](./media/sql-database-monitor-tune-overview/workload-states.png)

对于出现性能问题的工作负荷，问题的原因可能是 CPU 争用（**运行相关的**条件）或单个查询正在等待某个资源（**等待相关的**条件）。

## <a name="running-related-performance-issues"></a>运行相关的性能问题

一般原则是，如果 CPU 利用率一贯达到或超过 80%，则表示出现了运行相关的性能问题。 如果出现运行相关的问题，原因可能是 CPU 资源不足，或者出现了与以下条件之一相关的问题：

- 运行查询过多
- 编译查询过多
- 一个或多个执行查询使用了欠佳的查询计划

如果确定遇到了运行相关的性能问题，则目标应是使用一种或多种方法识别确切的问题。 用于标识运行相关问题的最常见方法包括：

- 使用 [Azure 门户](#monitor-databases-using-the-azure-portal)监视 CPU 利用率百分比。
- 使用以下[动态管理视图](sql-database-monitoring-with-dmvs.md)：

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 返回 Azure SQL 数据库中数据库的 CPU、I/O 和内存消耗量。 即使数据库中没有活动，也会每 15 秒存在一行。 历史数据将保留一小时。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 返回 Azure SQL 数据库的 CPU 使用率和存储数据。 在五分钟的间隔内收集和聚合数据。

> [!IMPORTANT]
> 请参阅[识别 CPU 性能问题](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)，其中提供了一组使用这些 DMV 排查 CPU 利用率问题的 T-SQL 查询。

### <a name="troubleshoot-queries-with-parameter-sensitive-query-execution-plan-issues"></a>使用参数敏感查询执行计划问题对查询进行故障排除

参数敏感计划 (PSP) 问题是指查询优化器生成的查询执行计划仅适用于某个或某组特定的参数值，而缓存计划对于连续执行操作中所用的参数值并非最佳。 这样一来，非最佳计划可能会导致整体工作负荷吞吐量下降并出现查询性能问题。

可通过以下几种解决方法来缓解这些问题，但每种方法各有利弊：

- 在每次执行查询时使用 [RECOMPILE](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示。 此解决方法以编译时间和 CPU 增加为代价来换取更好的计划质量。 对于需要高吞吐量的工作负荷，通常无法使用 `RECOMPILE` 选项。
- 使用 [OPTION (OPTIMIZE FOR…)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示将实际参数值替代为典型的参数值，以便为大部分可能的参数值生成一个足够好的计划。   此选项要求充分了解最佳参数值和相关的计划特征。
- 使用 [OPTION (OPTIMIZE FOR UNKNOWN)](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示将实际参数值替代为密度向量平均值。 另一种方法是将传入的参数值捕获到局部变量中，然后在谓词内使用局部变量，而不是使用参数本身。 在此特定修复方法中，平均密度必须*足够好*。
- 使用 [DISABLE_PARAMETER_SNIFFING](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示完全禁用参数探查。
- 使用 [KEEPFIXEDPLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示防止在缓存中重新编译。 此解决方法假定缓存中已有的通用计划已经*足够好*。 你还可以禁用统计信息自动更新，以便减少收回好计划而编译新的不良计划的可能性。
- 通过显式使用 [USE PLAN](https://docs.microsoft.com/sql/t-sql/queries/hints-transact-sql-query) 查询提示（通过显式指定、使用查询存储设置特定计划或启用[自动优化](sql-database-automatic-tuning.md)）强制执行计划。
- 将单个过程替换为一组嵌套的过程，可以根据条件逻辑和关联的参数值来使用其中每个过程。
- 创建动态字符串执行来替代静态过程定义。

有关解决这些类型的问题的其他信息，请参阅：

- 这篇[探查参数](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)博客文章
- 这篇[参数探查问题和解决方法](https://blogs.msdn.microsoft.com/turgays/2013/09/10/parameter-sniffing-problem-and-possible-workarounds/)博客文章
- 这篇[大象与老鼠参数探查](https://www.brentozar.com/archive/2013/06/the-elephant-and-the-mouse-or-parameter-sniffing-in-sql-server/)博客文章
- 这篇[动态 sql 与参数化查询的计划质量](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)博客文章

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>排查因参数化不当而导致的编译活动问题

当查询包含文本时，为了减少编译次数，要么数据库引擎选择自动参数化语​​句，要么用户可以将其显式参数化。 使用相同模式但不同文本值的大量查询编译可能会导致 CPU 利用率较高。 同样，如果仅将查询部分参数化，导致该查询仍包含文本，则数据库引擎不会将其进一步参数化。  以下是部分参数化查询的示例：

```sql
select * from t1 join t2 on t1.c1=t2.c1
where t1.c1=@p1 and t2.c2='961C3970-0E54-4E8E-82B6-5545BE897F8F'
```

在前面的示例中，`t1.c1` 采用 `@p1`，而 `t2.c2` 继续采用 GUID 作为文本。 在这种情况下，如果更改 `c2` 的值，该查询将被视为不同的查询，并且将进行新的编译。 若要减少前面示例中的编译次数，解决方案还是参数化 GUID。

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

### <a name="resolve-problem-queries-or-provide-more-resources"></a>解决问题查询或提供更多资源

识别问题后，可以优化有问题的查询，或升级计算大小或服务层，以增加 Azure SQL 数据库的容量来满足 CPU 要求。 有关缩放单一数据库资源的信息，请参阅[缩放 Azure SQL 数据库中的单一数据库资源](sql-database-single-database-scale.md)；有关缩放弹性池资源的信息，请参阅[缩放 Azure SQL 数据库中的弹性池资源](sql-database-elastic-pool-scale.md)。 有关缩放托管实例的信息，请参阅[实例级资源限制](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)。

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

确定不存在 CPU 消耗量较高的运行相关性能问题后，则表示出现了等待相关的性能问题。 即，由于 CPU 正在等待其他某个资源，因此 CPU 资源未得到有效利用。 对于这种情况，下一步是识别 CPU 资源正在等待哪个资源。 用于显示最常见的等待类型类别的最常用方法：

- [查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)每个查询在不同时间段的等待统计信息。 在查询存储中，等待类型合并成等待类别。 等待类别到等待类型的映射在 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql?view=sql-server-2017#wait-categories-mapping-table) 中提供。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 返回有关操作期间执行的线程遇到的所有等待的信息。 可以使用此聚合视图来诊断 Azure SQL 数据库以及特定查询和批的性能问题。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 返回有关正在等待某个资源的任务的等待队列的信息。

在高 CPU 方案中，查询存储和等待统计信息并不一定反映 CPU 利用率，原因有二：

- CPU 消耗量较高的查询可能仍在执行，查询尚未完成
- 发生故障转移时，正在运行 CPU 消耗量较高的查询

查询存储和等待统计信息跟踪动态管理视图仅显示成功完成的查询和超时查询的结果，而不显示当前正在执行的语句的数据（直到其完成）。  动态管理视图 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 可用于跟踪当前正在执行的查询以及相关的工作线程时间。

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

## <a name="improving-database-performance-with-more-resources"></a>使用更多的资源提高数据库的性能

最后，如果没有可行的措施可以提高数据库的性能，可以更改 Azure SQL 数据库中可用的资源量。 随时可以通过更改单一数据库的 [DTU 服务层](sql-database-service-tiers-dtu.md)或者增加弹性池的 eDTU 数目，来分配更多资源。 或者，如果使用[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)，则可更改服务层或增加分配给数据库的资源。

1. 对于单一数据库，可以根据需要[更改服务层](sql-database-service-tiers-dtu.md)或[计算资源](sql-database-service-tiers-vcore.md)以提高数据库性能。
2. 对于多个数据库，请考虑使用[弹性池](sql-database-elastic-pool-guidance.md)自动缩放资源。

## <a name="tune-and-refactor-application-or-database-code"></a>优化和重构应用程序或数据库代码

可以更改应用程序代码来最好地利用数据库、更改索引、强制计划，或使用提示来手动让数据库适应工作负荷。 [性能指南主题](sql-database-performance-guidance.md)文章中提供了一些有关手动优化和重写代码的指导与提示。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并使自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请参阅 [Azure 门户中的优化建议](sql-database-advisor-portal.md)，然后手动应用一些可提高查询性能的建议。
- 通过更改 [Azure SQL 数据库服务层](sql-database-performance-guidance.md)来更改数据库中可用的资源
