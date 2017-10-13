---
title: "使用 Intelligent Insights 排查 Azure SQL 数据库性能问题 | Microsoft Docs"
description: "Intelligent Insights 可以帮助排查 Azure SQL 数据库性能问题。"
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>使用 Intelligent Insights 排查 Azure SQL 数据库性能问题

本页提供有关通过 [Intelligent Insights](sql-database-intelligent-insights.md) 数据库性能诊断日志检测到的 Azure SQL 数据库性能问题的信息。 可将此诊断日志发送到 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure 事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure 存储](sql-database-metrics-diag-logging.md#stream-into-azure-storage)或第三方解决方案，以开发自定义 DevOps 警报和报告功能。

> [!NOTE]
> 有关通过 Intelligent Insights 快速排查 Azure SQL 数据库性能问题的指导，请参阅本文档中的[建议的故障排除流程](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)流程图。
>

## <a name="detectable-database-performance-patterns"></a>可检测的数据库性能模式

Intelligent Insights 可以根据查询执行等待时间、错误或超时自动检测 Azure SQL 数据库的性能问题，并将检测到的性能模式输出到诊断日志。 下表汇总了可检测的性能模式：

| 可检测的性能模式 | 输出的详细信息 |
| :------------------- | ------------------- |
| [达到资源限制](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 受监视订阅上的可用资源 (DTU)、数据库工作线程或数据库登录会话消耗量已达到限制，导致 Azure SQL 数据库性能出现问题。 |
| [工作负荷增大](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | 检测到工作负荷增大，或数据库上的工作负荷持续累积，导致 Azure SQL 数据库性能出现问题。 |
| [内存压力](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | 请求内存授予的工作线程等待内存分配的时间在统计学上非常漫长，或者请求内存授予的工作线程增长率不断累积，从而影响了 Azure SQL 数据库的性能。 |
| [锁定](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | 检测到过度的数据库锁定，这影响了 Azure SQL 数据库的性能。 |
| [MAXDOP 提升](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | 最大并行度选项 (MAXDOP) 发生更改，影响了查询执行效率。  |
| [Pagelatch 争用](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | 检测到 Pagelatch 争用，这影响了 Azure SQL 数据库的性能。 多个线程同时尝试访问相同的内存中数据缓冲区页面，导致等待时间变长，影响了 Azure SQL 数据库的性能。 |
| [缺少索引](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | 检测到缺少索引的问题，这影响了 Azure SQL 数据库的性能。 |
| [新查询](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | 检测到新查询，这影响了 Azure SQL 数据库的总体性能。  |
| [异常等待统计信息](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | 检测到异常的数据库等待时间，这影响了 Azure SQL 数据库的性能。 |
| [TempDB 争用](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 多个线程尝试访问相同的 TempDB 资源，导致出现影响 Azure SQL 数据库性能的瓶颈。 |
| [弹性池 DTU 不足](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | 弹性池中的可用 eDTU 不足，影响了 Azure SQL 数据库的性能。 |
| [计划回归](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | 检测到新计划，或现有计划的工作负荷发生更改，这影响了 Azure SQL 数据库的性能。 |
| [数据库范围的配置值更改](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | 数据库中的配置更改影响了 Azure SQL 数据库的性能。 |
| [客户端缓慢](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | 检测到应用程序客户端运行缓慢，无法以足够快的速度使用 Azure SQL 数据库的输出，这影响了 Azure SQL 数据库的性能。 |
| [定价层降级](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 定价层降级操作减少了可用资源，从而影响了 Azure SQL 数据库的性能。 |

> [!TIP]
> 若要持续优化 Azure SQL 数据库的性能，可以考虑启用 [Azure SQL 数据库自动优化](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning) – 这是 Azure SQL 内置智能的独特功能，可以持续监视 Azure SQL 数据库，并自动优化索引以及应用查询执行计划更正。
>

以下部分更详细地描述了前面列出的可检测性能模式。

## <a name="reaching-resource-limits"></a>达到资源限制

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式合并了有关达到可用资源限制、工作线程限制和会话限制的性能问题。 一旦检测到这种性能问题，诊断日志的说明字段就会指示性能问题是否与资源、工作线程或会话限制相关。

Azure SQL 数据库中的资源通常称为 [DTU 资源](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu)，由 CPU 和 I/O（数据和事务日志 I/O）资源的混合度量值构成。 如果检测到的查询性能下降是由于达到任何所度量资源限制而造成的，则认为出现了“到达资源限制”模式。

会话限制资源表示可以同时在 Azure SQL 数据库中登录的数目。 如果正在连接到 Azure SQL 数据库的应用程序达到可以同时在数据库中登录的数目，则认为出现了此性能模式。 如果应用程序尝试使用的会话数超过了可在数据库中使用的数目，则会影响查询性能。

达到工作线程限制是一种特殊的达到资源限制情况，因为可用工作线程数不会计入 DTU 用量。 数据库中达到工作线程限制可能会导致资源特定的等待时间延长，因此导致查询性能降级。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出已影响性能和资源消耗百分比的查询的查询哈希。 可以使用此信息作为优化数据库工作负荷的入手点。 具体而言，可以考虑优化导致性能降级的查询、添加索引，或采用更均衡的工作负荷分配来优化应用程序。 如果无法减少工作负荷或进行优化，也许可以考虑提高 Azure SQL 数据库订阅的定价层，以增加可用的资源量。

如果已达到可用会话限制，可以考虑优化应用程序，减少数据库的登录次数。 如果无法减少从应用程序登录到数据库的次数，可以考虑提高数据库的定价层，也许还可以将数据库拆分并转移到多个数据库，使工作负荷分配更均衡。

有关解决会话限制的更多建议，请参阅 [How to deal with the limits of Azure SQL Database maximum logins](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)（如何处理 Azure SQL 数据库最大登录数的限制）。 若要了解订阅层的可用资源限制，请参阅 [Azure SQL 数据库资源限制](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits)。

## <a name="workload-increase"></a>工作负荷增大

### <a name="what-is-happening"></a>发生了什么

此性能模式可识别由于工作负荷增大（甚至工作负荷堆积，这是一种更严重的形式）导致的问题。

这项检测是通过多个指标的组合来执行的。 度量的基本指标会根据以往的工作负荷基线检测工作负荷的增大情况。 另一种形式的检测基于对活动工作线程数的大幅提升的检测，这种提升率足以影响查询性能。

还有一种更严重的形式：由于 Azure SQL 数据库无法处理工作负荷，导致工作负荷不断堆积。 结果就是工作负荷的大小不断增大 - 所谓的工作负荷堆积状况。 由于出现这种状况，工作负荷等待执行的时间会不断增加，这代表了最严重的数据库性能问题之一。 可通过监视已中止工作线程数的增加情况来检测此问题。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出执行时间已延长的查询数，以及作为工作负荷增大问题的最大促成因素的查询的查询哈希。 可以使用此信息作为优化工作负荷的入手点，尤其是针对识别为工作负荷增大问题最大促成因素的查询。

还可以考虑更均衡地将工作负荷分配到数据库。 还可以考虑优化对性能造成了影响的查询，以及添加索引。 另一种可以考虑的做法是将工作负荷分散在多个数据库之间。 如果这种做法不可行，可以考虑提高 Azure SQL 数据库订阅的定价层，以增加可用的资源量。

## <a name="memory-pressure"></a>内存压力

### <a name="what-is-happening"></a>发生了什么

此性能模式表示由于出现内存压力（或者内存堆积状况，这是一种更严重的形式），当前数据库性能与过去 7 天的性能基线相比出现降级。

内存压力表示这样一种性能状况：Azure SQL 数据库中有大量的工作线程在请求内存授予。 这导致了高内存利用率的状况：Azure SQL 数据库无法有效地将内存分配给请求内存的所有工作线程。 此问题的最常见原因之一与 Azure SQL 数据库的可用内存量相关，另一方面，也与工作负荷的增大有关，这会导致工作线程数增加和内存压力。

内存压力的更严重形式是内存堆积状况，这表示请求内存授予的工作线程数超过释放内存的查询数。 请求内存授予的工作线程数还可能在此数字的基础上不断增加（即堆积），因为引擎无法以足够高的效率分配内存，因而无法满足需求。 内存堆积状况代表最严重的数据库性能问题之一。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出内存对象存储详细信息，并输出已标记为高内存使用率最大原因的促成因素（即工作线程）和相关时间戳。 可将此信息用作故障排除的基础。 

可以考虑优化或删除与最高内存使用率促成因素相关的查询。 此外，最好是确保不要查询不打算使用的数据。 合理的做法是始终在查询中使用 WHERE 子句。 此外，建议创建非聚集索引来搜寻数据，而不是扫描数据。

还可以考虑减少工作负荷，进行优化，或者分配到多个数据库。 另一种可以考虑的做法是将工作负荷分散在多个数据库之间。 如果这种做法不可行，可以考虑提高 Azure SQL 数据库订阅的定价层，以增加数据库可用的内存资源量。

有关其他故障排除建议，请参阅 [Memory Grants Meditation: The mysterious SQL Server memory consumer with Many Names](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/)（内存授予探幽：有多个名称的神秘 SQL Server 内存消耗者）。

## <a name="locking"></a>锁定

### <a name="what-is-happening"></a>发生了什么

此性能模式表示当前数据库性能降级，相比过去 7 天的性能基线，在该数据库中检测到的数据库锁定过多。 

在现代 RDBMS 中，锁定对于实现多线程系统至关重要，这样可以通过尽可能地运行多个同步工作线程和并行数据库事务，最大程度地提高性能。 简单而言，此上下文中的锁定是指一种内置访问机制，其中只有单个事务可以独占访问所需的行、页、表和文件，而不会与其他事务争用资源。 锁定资源供独占使用的事务在用完资源后，会释放这些资源上的锁，使其他事务能够访问所需的资源。 有关锁定的详细信息，请参阅 [Locking in the Database Engine](https://msdn.microsoft.com/library/ms190615.aspx)（数据库引擎中的锁定）。

如果 SQL 引擎执行的事务长时间等待访问已被锁定供独占使用的资源，这段等待时间会导致工作负荷的执行性能变慢。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出锁定详细信息，可将这些信息用作故障排除的基础。 可以分析报告的阻塞查询（即，造成锁定性能降级的查询）并将其删除。 在某些情况下，可以成功优化阻塞查询。
缓解问题的最简单安全方法是保持较短的事务运行时间，并减少开销最高的查询的锁占用时间。 可以考虑将较大的操作批分解成较小的操作。 合理的做法是通过尽量提高查询的效率，减少查询的锁占用时间。 考虑减少大型扫描，因为这些扫描会增大死锁的可能性，并对数据库总体性能造成负面影响。 对于识别出的导致锁定的查询，可以考虑创建新索引，或者将列添加到现有索引来避免表扫描。 有关更多建议，请参阅 [How to resolve blocking problems that are caused by lock escalation in SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)（如何解决 SQL Server 中的锁升级造成的阻塞问题）。

## <a name="increased-maxdop"></a>MAXDOP 提升

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示所选查询执行计划的并行化程度超出了预期。 Azure SQL 数据库查询优化器可以针对并行执行查询做出决策，尽量加速处理进度，从而可以增强工作负荷的性能。 但是，在某些情况下，处理查询的并行工作线程等待相互同步和合并结果所花费的时间，比使用更少的并行工作线程（有时甚至是一个工作线程）执行相同查询的时间还要长。

专业系统可分析当前数据库的性能并将其与基线时限进行比较，确定是否由于查询执行计划的并行化程度超出了预期，导致前面运行的查询比以前运行速度更慢。

Azure SQL 数据库中的“服务器配置选项”MAXDOP 用于控制可以使用多少个 CPU 核心来并行执行同一个查询。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出由于并行化程度超出预期，其执行持续时间延长的查询相关的查询哈希。 日志还会输出 CXP 等待时间。 此时间表示单个组织器/协调器线程（线程 0）在合并结果并继续运行之前，等待其他所有线程完成的时间。 此外，诊断日志还会输出性能不佳的查询在执行过程中等待的总时间。 可将此信息用作排查问题的基础。

可以先研究一下，是否有优化或简化复杂查询的可能性。 合理的做法是将长时间运行的批处理作业分解成较小的作业。 此外，确保创建了用于支持查询的索引。 还可以考虑针对标记为性能不佳的特定查询手动强制 MAXDOP（最大并行度）。 可通过 T-SQL 配置此操作，具体请参阅 [Configure the max degree of parallelism Server Configuration Option](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)（配置最大并行度服务器配置选项）。

将服务器配置选项 MAXDOP 零 (0) 设置为默认值表示 Azure SQL 数据库可以使用所有可用的逻辑 CPU 核心将执行单个查询的线程并行化。 将 MAXDOP 设置为一 (1) 表示只能为单个查询执行使用一个核心 – 实际上，这意味着关闭了并行化。 根据具体的情况、数据库的可用核心数与诊断日志信息，可将 MAXDOP 选项优化成用于并行查询执行的、能够解决具体问题的核心数。

## <a name="pagelatch-contention"></a>Pagelatch 争用

### <a name="what-is-happening"></a>发生了什么

此性能模式表示由于 Pagelatch（页闩锁）争用，当前数据库工作负荷的性能与过去 7 天的工作负荷基线相比有所降级。

闩锁是一种轻型同步机制，Azure SQL 数据库使用它来启用多线程，以及保证内存中结构（包括索引、数据页和其他内部结构）的一致性。

可在 Azure SQL 数据库中使用许多类型的闩锁。 为简单起见，可以使用缓冲区闩锁在缓冲池中保护内存中页，使用 I/O 闩锁来保护尚未载入缓冲池的页。 每当在缓冲池中的页中写入或读取数据时，工作线程需要首先获取该页的缓冲区闩锁。 每当工作线程尝试访问尚未在内存中缓冲池中提供的页时，会发出 I/O 请求以便从存储中加载所需的信息，这是一种更严重形式的性能降级问题。

当多个线程同时尝试获取同一内存中结构上的闩锁，从而造成查询执行的等待时间延长时，就会发生页闩锁争用。 如果在需要从存储访问数据时发生 Pagelatch I/O 争用，这段等待时间甚至会更长，并可能会显著影响工作负荷的性能。 Pagelatch 争用是线程相互等待，在多个 CPU 系统上争用资源的最常见情景。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出 Pagelatch 争用详细信息，可将这些信息用作排查问题的基础。

由于 Pagelatch 是 Azure SQL 数据库的内部控制机制，因此 SQL 数据库会自动确定何时使用它。 应用程序决策（包括架构设计）可以会根据闩锁的确定性行为影响 Pagelatch 的行为。
处理闩锁争用的方法之一是将有序索引键替换为无序键，以便在索引范围内均匀分配插入内容。 通常，可以通过在索引中创建一个可按比例分配工作负荷的前导列来实现此目的。 可考虑的另一种方法是表分区。 在分区表中创建一个包含计算列的哈希分区方案，是缓解过度闩锁争用的常用方法。 如果发生 Pagelatch I/O 争用，我们建议引入索引，因为这有利于缓解此性能问题。 有关详细信息，请参阅 [Diagnosing and Resolving Latch Contention on SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf)（诊断和解决 SQL Server 上的闩锁争用）（PDF 下载）。

## <a name="missing-index"></a>缺少索引

### <a name="what-is-happening"></a>发生了什么

此性能模式表示由于缺少索引，当前数据库工作负荷的性能与过去 7 天的基线相比有所降级。

索引用于加速查询性能。 使用索引可以快速访问表数据，同时减少需要访问或扫描的数据集页数。

可以通过此项检测来识别导致性能降级的特定查询。创建索引对性能有利。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出已识别到会影响工作负荷性能的查询的查询哈希。 可以考虑针对这些查询生成索引。 还可以考虑优化这些查询；如果不需要这些查询，可以将其删除。 在性能方面，合理的做法是避免查询不使用的数据。

> [!TIP]
> 是否知道，Azure 内置智能可以自动管理数据库的最佳性能索引？
>
> 为了持续优化 Azure SQL 数据库的性能，我们建议启用 [Azure SQL 数据库自动优化](sql-database-automatic-tuning.md) – 这是 Azure SQL 内置智能的独特功能，可以持续监视 Azure SQL 数据库，并自动优化以及创建数据库的索引。
>

## <a name="new-query"></a>新建查询

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示检测到一个性能不佳，并且影响了工作负荷性能（与 7 天性能基线相比）的新查询。

有时，编写性能良好的查询很有难度。 有关编写查询的详细信息，请参阅 [Writing SQL Queries](https://msdn.microsoft.com/library/bb264565.aspx)（编写 SQL 查询）；若要优化现有的查询性能，请参阅 [Query Tuning](https://msdn.microsoft.com/library/ms176005.aspx)（查询优化）。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出最多两 (2) 个 CPU 消耗量最大的新查询的信息，包括其查询哈希。 由于检测到的查询影响了工作负荷性能，因此，我们建议考虑优化该查询。 合理的做法是不要检索不使用的数据。 另外，建议使用包含 WHERE 子句的查询。 我们还建议简化复杂的查询，并将其分解成更小的查询。 将较大的批处理查询分解成较小的批处理查询也是值得考虑的好办法。 为新查询引入索引通常是缓解此性能问题的好办法。

可以考虑使用 [Azure SQL 数据库查询性能见解](sql-database-query-performance.md)。

## <a name="unusual-wait-statistic"></a>异常等待统计信息

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示识别出的性能不佳的查询导致工作负荷的性能相比过去 7 天的工作负荷基线有所降级。

在这种情况下，系统无法将性能不佳的查询分类到其他任何标准的可检测性能类别，但检测到负责回归的等待统计信息，因此，将这些查询视为具有“异常等待统计信息”的查询，其中也会公开负责回归的异常等待统计信息。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出异常等待时间详细信息、受影响的查询的查询哈希和等待时间。

在这种情况下，由于系统无法成功识别查询性能不佳的根本原因，在手动故障排除时，有关性能不佳的查询的诊断信息可以充当很好的入手点。 可以考虑优化这些查询的性能。 通常，合理的做法是不要提取不使用的数据，简化复杂的查询并将其分解成较小的查询。 有关优化查询性能的详细信息，请参阅 [Query Tuning](https://msdn.microsoft.com/library/ms176005.aspx)（查询优化）。

## <a name="tempdb-contention"></a>TempDB 争用

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示这样一种数据库性能状况：尝试访问 TempDB 资源的线程存在瓶颈（这种状况与 I/O 无关）。 此性能问题的典型情景是数百个并发查询都在创建、使用然后删除小型 TempDB 表，即所谓的“TempDB 争用”。 系统已检测到使用相同 TempDB 表的并发请求数出现了统计学上的大幅增长，影响了数据库的性能（与过去 7 天的性能基线相比）。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出 TempDB 争用详细信息，可将这些信息用作排查性能问题的入手点。 为了缓解这种争用并提高总体工作负荷的吞吐量，请遵循以下几种做法。 可以停止使用临时表。 还可以考虑使用内存优化表，具体请参阅 [Introduction to Memory-Optimized Tables](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)（内存优化表简介）。 

## <a name="elastic-pool-dtu-shortage"></a>弹性池 DTU 不足

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示由于订阅的弹性池中可用 DTU 不足，当前数据库工作负荷的性能相比过去 7 天的基线有所降级。 

Azure SQL 数据库中的资源通常称为 [DTU 资源](sql-database-what-is-a-dtu.md)，由 CPU 和 I/O（数据和事务日志 I/O）资源的混合度量值构成。 [Azure 弹性池资源](sql-database-elastic-pool.md)用作出于缩放目的而在多个数据库之间共享的可用 eDTU 资源的池。 如果弹性池中的可用 eDTU 资源不够大，无法支持池中的所有数据库，则系统就会检测到“弹性池 DTU 不足”性能问题。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出有关弹性池的信息，列出 DTU 消耗量最大的数据库，并提供消耗量最大的数据库使用的池 DTU 百分比。

由于性能状况与使用弹性池中相同 eDTU 池的多个数据库相关，因此故障排除步骤需要侧重于 DTU 消耗量最大的数据库。 可以考虑减少消耗量最大的数据库中的工作负荷，包括优化这些数据库中消耗量最大的查询。 此外，最好是确保不要查询不使用的数据。 另一种做法是优化那些使用 DTU 消耗量最大的数据库的应用程序，并在多个数据库之间重新分配工作负荷。

如果无法减少和优化 DTU 消耗量最大的数据库中的当前工作负荷，可以考虑提高弹性池定价层。 提高定价层可以增加弹性池中的可用 DTU。

## <a name="plan-regression"></a>计划回归

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示 Azure SQL 数据库开始利用欠佳的查询执行计划。 欠佳的计划通常会导致查询执行时间延长，从而导致当前和其他查询的等待时间变长。

Azure SQL 数据库可以确定查询执行开销最低的查询执行计划。 但是，由于查询和工作负荷的类型会发生变化，有时现有的计划不再有效，或者 Azure SQL 数据库未能做出合理的评估。 作为一种纠正措施，可以手动强制查询执行计划。

这种可检测的性能模式合并了三种不同的计划回归情况：新计划回归、旧计划回归和现有计划更改的工作负荷。 诊断日志中的“*details*”属性内提供了发生的特定计划回归的类型。

新计划回归状况表示这样一种状态：Azure SQL 数据库开始执行不如旧计划那么有效的新查询执行计划。 旧计划回归状况表示这样一种状态：Azure SQL 数据库弃用更有效的新计划，改用不如新计划那么有效的旧计划。 现有计划更改的工作负荷回归表示这样一种状态：不断交替使用两种计划（旧计划和新计划），天平逐渐倾向于性能不佳的计划。

有关计划回归的详细信息，请参阅 [What is plan regression in SQL server](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/)（SQL Server 中的计划回归是什么）。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出查询哈希、良好计划的 ID、不佳计划的 ID 和查询 ID，可将这些信息用作排查此性能状况的基础。

可以使用提供的查询哈希来分析哪个计划对于可识别的特定查询而言性能更好。 确定哪个计划更适合自己的查询后，可以手动强制该计划。 有关详细信息，请参阅 [How SQL Server prevents plan regressions](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)（SQL Server 如何阻止计划回归）。

> [!TIP]
> 是否知道，Azure 内置智能可以自动管理数据库的最佳性能查询执行计划？
>
> 为了持续优化 Azure SQL 数据库的性能，我们建议启用 [Azure SQL 数据库自动优化](sql-database-automatic-tuning.md) – 这是 Azure SQL 内置智能的独特功能，可以持续监视 Azure SQL 数据库，并自动为数据库优化和创建具有最佳性能的查询执行计划。
>

## <a name="database-scoped-configuration-value-change"></a>数据库范围的配置值更改

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示数据库范围的配置发生更改，导致检测到性能回归（与过去 7 天的数据库工作负荷行为相比）。 这意味着，最近对数据库范围的配置所做的更改似乎对数据库性能不利。

可以针对每个数据库设置数据库范围的配置更改。 根据具体的情况使用此配置可以优化数据库的个体性能。 可为每个数据库配置以下选项： MAXDOP、LEGACY_CARDINALITY_ESTIMATION、PARAMETER_SNIFFING、QUERY_OPTIMIZER_HOTFIXES 和 CLEAR PROCEDURE_CACHE。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出最近进行的、导致性能相比过去 7 天的工作负荷行为有所降级的数据库范围配置更改。 可以考虑将配置更改还原为以前的值，或者逐值进行优化，直至达到所需的性能级别。 还可以考虑从性能令人满意的类似数据库中复制数据库范围配置值。 如果无法排查性能问题，可以考虑还原为 Azure SQL 数据库默认值，并尝试从此基线开始进行微调。

有关优化数据库范围配置的详细信息以及更改配置时使用的 T-SQL 语法，请参阅 [Alter database scoped configuration (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx)（更改数据库范围的配置 (Transact SQL)）。

## <a name="slow-client"></a>客户端缓慢

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示在数据库能够发送结果的情况下，使用 Azure SQL 数据库的客户端无法以足够快的速度使用数据库的输出。 由于 Azure SQL 数据库不会在缓冲区中存储所执行查询的结果，因此它的速度会变慢，先等待客户端使用传输的查询输出，然后继续。 此状况也可能与网速缓慢相关 - 网络无法以足够快的速度将输出从 Azure SQL 数据库传输到使用方客户端。

仅当检测到性能回归（与过去 7 天的数据库工作负荷行为相比）时，才会产生这种状况。 这可以确保仅当性能与过去的性能行为相比，在统计学上有显著的降级时，才会检测到这种性能问题。

### <a name="troubleshooting"></a>故障排除

这种可检测的性能模式表示客户端出现状况，需要对客户端应用程序或客户端网络进行故障排除。 诊断日志会输出在过去 2 小时内让客户端等待最长时间来使用的查询哈希和等待时间。 可将这些信息用作故障排除的基础。

可以考虑优化应用程序在使用这些查询时的性能。 还可以考虑是否存在网络延迟问题。 由于性能降级问题基于过去 7 天的性能基线变化，因此可以调查最近一段时间内是否发生了应用程序更改或网络条件更改，从而导致这种性能回归事件。 

## <a name="pricing-tier-downgrade"></a>定价层降级

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示 Azure SQL 数据库的定价层已降级。 由于数据库可用的资源 (DTU) 减少，系统检测到当前测得的数据库性能相比过去 7 天的基线有所下降。

此外，可能 Azure SQL 数据库订阅的定价层曾经降级，后来又在短时间内升级到更高的层。 这表示检测到的是一种暂时性的性能降级。定价层降级和升级时，诊断日志的 details 节中会输出相关信息。

### <a name="troubleshooting"></a>故障排除

如果降低了定价层，因而减少了 Azure SQL 数据库可用的 DTU 数，但同时对性能感到满意，那么，暂时就不需要采取任何措施。 如果降低定价层后对 Azure SQL 数据库的性能不满意，可以考虑减少数据库工作负荷，或考虑将定价层提升到更高的级别。

## <a name="recommended-troubleshooting-flow"></a>建议的故障排除流程

请遵循以下流程图，通过建议的方法使用 Intelligent Insights 排查性能问题。

在 Azure 门户中导航到“Azure SQL Analytics”来访问 Intelligent Insights。 尝试找到传入的性能警报并单击它。 在检测页上确定发生了什么情况。 观察提供的问题根本原因分析、查询文本、查询时间趋势和事件演变情况。 使用有关缓解性能问题的 Intelligent Insights 建议来尝试解决问题。 

[![故障排除流程图](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> 单击流程图下载此流程图的 PDF 版本。

Intelligent Insights 通常需要花费 1 小时来针对性能问题执行根本原因分析。 如果在 Intelligent Insights 找不到自己遇到的问题（大多数情况下，这些找不到的问题都是在 1 小时以内发生的），而此问题又很关键，可以使用查询数据存储 (QDS) 来手动识别性能问题的根本原因。 有关详细信息，请参阅[使用 Query Store 监视性能](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

## <a name="next-steps"></a>后续步骤
- 了解 [Intelligent Insights](sql-database-intelligent-insights.md) 的概念
- 使用 [Intelligent Insights Azure SQL 数据库性能诊断日志](sql-database-intelligent-insights-use-diagnostics-log.md)
- [使用 Azure SQL Analytics 监视 Azure SQL 数据库](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- 了解[从 Azure 资源收集和使用日志数据](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
