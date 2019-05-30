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
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: cae0fbd450e6b392e1689d4642181f6e5279752b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393214"
---
# <a name="monitoring-and-performance-tuning"></a>监视和性能优化

Azure SQL 数据库，您可以轻松地监视使用情况，添加或删除资源 （CPU、 内存、 I/O），解决潜在的问题，并查找可以提高数据库性能的建议。 Azure SQL 数据库具有许多功能，可以自动修复问题数据库中如果你想要让数据库适应工作负荷并自动优化性能。 但是，有一些可能需要进行故障排除的自定义问题。 此文章介绍了一些最佳做法和工具，可用于解决性能问题。

有两个主要活动，您需要做才能确保您数据库运行时未出现问题：
- [监视数据库性能](#monitoring-database-performance)这样可确保分配给你的数据库的资源可以处理工作负荷。 如果看到已达到资源限制，您需要以标识资源使用查询和优化它们，或通过升级服务层中添加更多资源。
- [排查性能问题](#troubleshoot-performance-issues)若要确定发生了一些潜在问题的原因，确定根本原因问题并将解决此问题的操作。

## <a name="monitoring-database-performance"></a>监视数据库性能

若要监视 Azure 中的 SQL 数据库的性能，首先需要监视所选数据库性能级别相关的资源利用率。 您需要监视以下资源：
 - **CPU 使用率**-您需要检查在较长的时间达到 100%的 CPU 使用情况。 这可能表示可能需要升级你的数据库或实例或识别并优化使用大多数的计算能力的查询。
 - **等待统计信息**-您需要检查什么查询正在等待某些资源的原因。 Queriesmig 等待数据提取或保存到数据库文件，因为已达到某些资源限制等待，等等。
 - **IO 使用情况**-您需要检查你是否到达基础存储的 IO 限制。
 - **内存使用情况**-您的数据库或实例已 Vcore 数成正比，则需要检查可用内存量是它并不足以支持工作负荷。 页生存期是可以指示您的页面快速从内存中删除的参数之一。

Azure SQL 数据库**提供了通知可帮助您排查并解决潜在的性能问题**。 可以轻松确定改善和优化查询性能，而无需通过查看更改资源机会[性能调整建议](sql-database-advisor.md)。 缺少索引与查询优化不足是数据库性能不佳的常见原因。 可以应用这些优化建议来提高工作负荷的性能。 此外，可以通过应用所有已确定的建议并验证它们是否可以提高数据库的性能，让 Azure SQL 数据库[自动优化查询的性能](sql-database-automatic-tuning.md)。

可使用以下选项来监视数据库性能以及对其进行故障排除：

- 在 [Azure 门户](https://portal.azure.com)中单击“SQL 数据库”，选择数据库，然后使用“监视”图表查找接近其上限的资源。  默认情况下将显示 DTU 消耗量。 单击“**编辑**”更改显示的时间范围和值。
- SQL Server Management Studio 等工具提供许多有用的报表，例如[性能仪表板](https://docs.microsoft.com/sql/relational-databases/performance/performance-dashboard?view=sql-server-2017)可以监视资源使用情况和识别资源消耗查询，排名靠前的位置或[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#Regressed)其中可以确定查询退化的性能。
- 使用[Query Performance Insight](sql-database-query-performance.md) [Azure 门户](https://portal.azure.com)以确定花费最多的资源的查询。 此功能仅提供单一数据库和弹性池。
- 使用 [Azure SQL 数据库顾问](sql-database-advisor-portal.md)查看有关创建和删除索引、参数化查询，以及解决架构问题的建议。 此功能仅提供单一数据库和弹性池。
- 使用 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) 自动监视数据库性能。 检测到性能问题后，将生成一个包含问题的详细信息和根本原因分析 (RCA) 的诊断日志。 在可能的情况下提供性能改善建议。
- [启用自动优化](sql-database-automatic-tuning-enable.md)，并让 Azure SQL 数据库自动修复已识别到的性能问题。
- 使用[动态管理视图 (DMV)](sql-database-monitoring-with-dmvs.md)、[扩展事件](sql-database-xevent-db-diff-from-svr.md)和[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)更细致地排查性能问题。

> [!TIP]
> 请参阅[性能指南](sql-database-performance-guidance.md)，了解在使用上述一种或多种方法识别性能问题后，可以利用哪些技术来提高 Azure SQL 数据库的性能。

## <a name="troubleshoot-performance-issues"></a>排查性能问题

若要诊断并解决性能问题，请先了解每个活动查询的状态，以及哪些条件导致出现了与每种工作负荷状态相关的性能问题。 若要提高 Azure SQL 数据库的性能，请了解来自应用程序的每个活动查询请求是处于运行状态还是等待状态。 在 Azure SQL 数据库中排查性能问题时，请在阅读本文以诊断并解决性能问题时注意以下图表。

![工作负荷状态](./media/sql-database-monitor-tune-overview/workload-states.png)

对于出现性能问题的工作负荷，问题的原因可能是 CPU 争用（**运行相关的**条件）或单个查询正在等待某个资源（**等待相关的**条件）。

原因或**运行相关**问题可能是：
- **编译问题**-SQL 查询优化器可能会生成非最优计划，因为过时的统计信息、 不正确将处理的行数的估计或所需的内存的估计值。 如果您知道在过去或其他实例 （托管实例或 SQL Server 实例） 上更快地执行该查询，采用的实际执行计划，并将其以查看它们不同。 尝试将应用查询提示或重新生成统计信息或要获得更好的计划的索引。 启用 Azure SQL 数据库，以自动缓解这些问题中的自动计划更正。
- **执行问题**-如果是最优查询计划则可能达到数据库，例如，日志写入吞吐量中的一些资源限制或它可能会使用，应重新生成碎片的索引。 大量的并发查询，就可以把资源还可能执行问题的原因。 **等待相关**问题是在大多数情况下的问题相关的执行，因为未在有效地执行的查询可能正在等待某些资源。

原因或**等待相关**问题可能是：
- **阻止**-一个查询可能会持有锁在数据库中的某些对象时其他人尝试访问相同的对象。 您可以轻松地识别阻塞查询使用 DMV 或监视工具。
- **IO 问题**-查询可能会等待要写入的数据或日志文件的页面。 在这种情况下，您将看到`INSTANCE_LOG_RATE_GOVERNOR`， `WRITE_LOG`，或`PAGEIOLATCH_*`等待统计信息 DMV 中的。
- **TempDB 问题**-如果您正在使用大量的临时表或您看到大量 TempDB 溢出在计划中可能存在问题，TempDB 吞吐量查询。 
- **与内存相关的问题**-您可能没有足够的内存的工作负荷使您的页生存期可能删除或查询获得比所需较少的内存授予。 在某些情况下，查询优化器中的内置智能可以解决这些问题。
 
以下各节中将介绍如何确定并解决这些问题。

## <a name="running-related-performance-issues"></a>运行相关的性能问题

一般原则是，如果 CPU 利用率一贯达到或超过 80%，则表示出现了运行相关的性能问题。 如果出现运行相关的问题，原因可能是 CPU 资源不足，或者出现了与以下条件之一相关的问题：

- 运行查询过多
- 编译查询过多
- 一个或多个执行查询使用了欠佳的查询计划

如果确定遇到了运行相关的性能问题，则目标应是使用一种或多种方法识别确切的问题。 用于标识运行相关问题的最常见方法包括：

- 使用 [Azure 门户](sql-database-manage-after-migration.md#monitor-databases-using-the-azure-portal)监视 CPU 利用率百分比。
- 使用以下[动态管理视图](sql-database-monitoring-with-dmvs.md)：

  - [sys.dm_db_resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 返回 Azure SQL 数据库中数据库的 CPU、I/O 和内存消耗量。 即使数据库中没有活动，也会每 15 秒存在一行。 历史数据将保留一小时。
  - [sys.resource_stats](sql-database-monitoring-with-dmvs.md#monitor-resource-use) 返回 Azure SQL 数据库的 CPU 使用率和存储数据。 在五分钟的间隔内收集和聚合数据。

> [!IMPORTANT]
> 请参阅[识别 CPU 性能问题](sql-database-monitoring-with-dmvs.md#identify-cpu-performance-issues)，其中提供了一组使用这些 DMV 排查 CPU 利用率问题的 T-SQL 查询。

### <a name="ParamSniffing"></a> 使用参数敏感查询执行计划问题对查询进行故障排除

参数敏感计划 (PSP) 问题是指查询优化器生成的查询执行计划仅适用于某个或某组特定的参数值，而缓存计划对于连续执行操作中所用的参数值并非最佳。 这样一来，非最佳计划可能会导致整体工作负荷吞吐量下降并出现查询性能问题。 参数截取和查询处理的详细信息，请参阅[查询处理体系结构指南](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing)。

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

- 这篇 [I smell a parameter](https://blogs.msdn.microsoft.com/queryoptteam/2006/03/31/i-smell-a-parameter/)（探查参数）博客文章
- 这篇[动态 sql 与参数化查询的计划质量](https://blogs.msdn.microsoft.com/conor_cunningham_msft/2009/06/03/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries/)博客文章
- 这篇 [SQL Query Optimization Techniques in SQL Server:Parameter Sniffing](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)（SQL Server 中的 SQL 查询优化技术：参数探查）博客文章

### <a name="troubleshooting-compile-activity-due-to-improper-parameterization"></a>排查因参数化不当而导致的编译活动问题

当查询包含文本时，为了减少编译次数，要么数据库引擎选择自动参数化语​​句，要么用户可以将其显式参数化。 使用相同模式但不同文本值的大量查询编译可能会导致 CPU 利用率较高。 同样，如果仅将查询部分参数化，导致该查询仍包含文本，则数据库引擎不会将其进一步参数化。  以下是部分参数化查询的示例：

```sql
SELECT * FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F'
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

确定不存在 CPU 消耗量较高的运行相关性能问题后，则表示出现了等待相关的性能问题。 即，由于 CPU 正在等待其他某个资源，因此 CPU 资源未得到有效利用。 对于这种情况，下一步是识别 CPU 资源正在等待哪个资源。 用于显示最常见的等待类型类别的最常用方法：

- [查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)每个查询在不同时间段的等待统计信息。 在查询存储中，等待类型合并成等待类别。 等待类别到等待类型的映射在 [sys.query_store_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table) 中提供。
- [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) 返回有关操作期间执行的线程遇到的所有等待的信息。 可以使用此聚合视图来诊断 Azure SQL 数据库以及特定查询和批的性能问题。
- [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) 返回有关正在等待某个资源的任务的等待队列的信息。

在高 CPU 方案中，查询存储和等待统计信息并不一定反映 CPU 利用率，原因有二：

- CPU 消耗量较高的查询可能仍在执行，查询尚未完成
- 发生故障转移时，正在运行 CPU 消耗量较高的查询

查询存储和等待统计信息跟踪动态管理视图仅显示成功完成的查询和超时查询的结果，而不显示当前正在执行的语句的数据（直到其完成）。 动态管理视图 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 可用于跟踪当前正在执行的查询以及相关的工作线程时间。

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

最后，如果没有可行的措施可以提高数据库的性能，可以更改 Azure SQL 数据库中可用的资源量。 随时可以通过更改单一数据库的 [DTU 服务层级](sql-database-service-tiers-dtu.md)或者增加弹性池的 eDTU 数目，来分配更多资源。 或者，如果使用[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)，则可更改服务层级或增加分配给数据库的资源。

1. 对于单一数据库，可以根据需要[更改服务层级](sql-database-single-database-scale.md)或[计算资源](sql-database-single-database-scale.md)以提高数据库性能。
2. 对于多个数据库，请考虑使用[弹性池](sql-database-elastic-pool-guidance.md)自动缩放资源。

## <a name="tune-and-refactor-application-or-database-code"></a>优化和重构应用程序或数据库代码

可以更改应用程序代码来最好地利用数据库、更改索引、强制计划，或使用提示来手动让数据库适应工作负荷。 [性能指南主题](sql-database-performance-guidance.md)文章中提供了一些有关手动优化和重写代码的指导与提示。

## <a name="next-steps"></a>后续步骤

- 若要在 Azure SQL 数据库中启用自动优化并使自动优化功能完全管理工作负荷，请参阅[启用自动优化](sql-database-automatic-tuning-enable.md)。
- 若要使用手动优化，请参阅 [Azure 门户中的优化建议](sql-database-advisor-portal.md)，然后手动应用一些可提高查询性能的建议。
- 通过更改 [Azure SQL 数据库服务层级](sql-database-performance-guidance.md)来更改数据库中可用的资源
