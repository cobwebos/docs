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
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: cce112929ff2f4fb48c2c6e2ddc2d4eee743b790
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>使用 Intelligent Insights 排查 Azure SQL 数据库性能问题

本页提供有关通过 [Intelligent Insights](sql-database-intelligent-insights.md) 数据库性能诊断日志检测到的 Azure SQL 数据库性能问题的信息。 可将此诊断日志发送到 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure 事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure 存储](sql-database-metrics-diag-logging.md#stream-into-storage)或第三方解决方案，用于自定义 DevOps 警报和报告功能。

> [!NOTE]
> 有关通过 Intelligent Insights 快速排查 SQL 数据库性能问题的指导，请参阅本文档中的[建议的故障排除流程](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)流程图。
>

## <a name="detectable-database-performance-patterns"></a>可检测的数据库性能模式

Intelligent Insights 可根据查询执行等待时间、错误或超时自动检测 SQL 数据库的性能问题， 然后将检测到的性能模式输出到诊断日志。 下表汇总了可检测的性能模式：

| 可检测的性能模式 | 输出的详细信息 |
| :------------------- | ------------------- |
| [达到资源限制](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | 受监视订阅上的可用资源 (DTU)、数据库工作线程或数据库登录会话消耗量已达到限制，导致 SQL 数据库性能出现问题。 |
| [工作负荷增大](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | 检测到工作负荷增大，或数据库上的工作负荷持续累积，导致 SQL 数据库性能出现问题。 |
| [内存压力](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | 请求内存授予的工作线程必须等待内存分配相当长的时间。 否则，随着请求内存授予的工作线程数不断增加，SQL 数据库的性能就会受到影响。 |
| [锁定](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | 检测到过度的数据库锁定，这影响 SQL 数据库性能。 |
| [MAXDOP 提升](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | 最大并行度选项 (MAXDOP) 发生更改，影响查询执行效率。 |
| [Pagelatch 争用](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | 检测到 Pagelatch 争用，这影响 SQL 数据库性能。 多个线程同时尝试访问同一内存中数据缓冲区页。 这导致等待时间增加，影响 SQL 数据库性能。 |
| [缺少索引](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | 检测到缺少索引的问题，这影响 SQL 数据库性能。 |
| [新建查询](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | 检测到新建查询，这影响 SQL 数据库总体性能。 |
| [异常等待统计信息](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | 检测到异常的数据库等待时间，这影响 SQL 数据库性能。 |
| [TempDB 争用](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | 多个线程尝试访问相同的 TempDB 资源，导致出现影响 SQL 数据库性能的瓶颈。 |
| [弹性池 DTU 不足](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | 弹性池中的可用 eDTU 不足，影响 SQL 数据库性能。 |
| [计划回归](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | 检测到新计划，或现有计划的工作负荷发生更改，这影响 SQL 数据库性能。 |
| [数据库范围的配置值更改](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | 数据库中的配置更改影响 SQL 数据库性能。 |
| [客户端缓慢](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | 检测到应用程序客户端运行缓慢，无法以足够快的速度使用 SQL 数据库的输出，这影响 SQL 数据库性能。 |
| [定价层降级](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | 定价层降级操作减少了可用资源，这影响 SQL 数据库性能。 |

> [!TIP]
> 若要持续进行 SQL 数据库性能优化，请启用 [Azure SQL 数据库自动优化](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning)。 SQL 数据库内置智能的这项独特功能可以持续监视 SQL 数据库、自动优化索引，并应用查询执行计划更正。
>

以下部分更详细地描述了前面列出的可检测性能模式。

## <a name="reaching-resource-limits"></a>达到资源上限

### <a name="what-is-happening"></a>发生了什么

这种可检测性能模式合并了各种性能问题，涉及达到可用资源限制、工作线程限制和会话限制。 检测到这种性能问题之后，诊断日志的说明字段就会指示性能问题是否与资源、工作线程或会话限制相关。

SQL 数据库上的资源通常称为 [DTU 资源](https://docs.microsoft.com/azure/sql-database/sql-database-what-is-a-dtu)， 包含 CPU 和 I/O（数据和事务日志 I/O）资源的混合度量值。 如果检测到的查询性能下降是由于达到所度量资源限制而造成的，则认为出现了“达到资源限制”模式。

会话限制资源表示系统只允许一定数量的 SQL 数据库并发登录。 如果连接到 SQL 数据库的应用程序达到允许的数据库并发登录数，则认为出现了此性能模式。 如果应用程序尝试使用的会话数超过了可在数据库中使用的数目，则会影响查询性能。

达到工作线程限制是一种特殊的达到资源限制的情况，因为可用工作线程数不会计入 DTU 用量。 数据库中达到工作线程限制可能会导致资源特定的等待时间延长，从而导致查询性能降低。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出影响性能和资源消耗百分比的查询的查询哈希。 可以使用此信息作为优化数据库工作负荷的入手点。 具体而言，可以通过添加索引优化那些导致性能降低的查询。 也可以让工作负荷分配更均衡，从而优化应用程序。 如果无法减少工作负荷或进行优化，可以考虑提高 SQL 数据库订阅的定价层，以增加可用的资源量。

如果已达到可用会话限制，可以通过减少数据库登录次数来优化应用程序。 如果无法减少从应用程序到数据库的登录数，可以考虑提高数据库的定价层。 也可以将数据库拆分成多个数据库并进行移动，使工作负荷的分配更为均衡。

有关解决会话限制的更多建议，请参阅 [How to deal with the limits of SQL Database maximum logins](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/)（如何处理 SQL 数据库最大登录数的限制）。 若要了解订阅层的可用资源限制，请参阅 [SQL 数据库资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits)。

## <a name="workload-increase"></a>工作负载增加

### <a name="what-is-happening"></a>发生了什么

此性能模式可识别由于工作负荷增大（更严重者，工作负荷堆积）而导致的问题。

这项检测是通过多个指标的组合来执行的。 度量的基本指标会根据以往的工作负荷基线检测工作负荷的增大情况。 另一种形式的检测基于对活动工作线程数的大幅提升的检测，这种提升率足以影响查询性能。

更严重时，由于 SQL 数据库无法处理工作负荷，导致工作负荷不断堆积。 结果就是工作负荷不断增大，即出现工作负荷堆积情况。 因此，工作负荷等待执行的时间也增加。 这种情况代表最严重的数据库性能问题之一。 可通过监视已中止工作线程数的增加情况来检测此问题。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出执行时间已延长的查询数，以及对工作负荷增加影响最大的查询的查询哈希。 可以使用此信息作为优化工作负荷的入手点。 一开始可以确定对工作负荷增加影响最大的查询，这很有用。

可以考虑将工作负荷更均衡地分配到数据库。 考虑通过添加索引来优化影响性能的查询。 也可将工作负荷分配到多个数据库中。 如果此类解决方案不可行，可以考虑提高 SQL 数据库订阅的定价层，以增加可用的资源量。

## <a name="memory-pressure"></a>内存压力

### <a name="what-is-happening"></a>发生了什么

此性能模式指示由于出现内存压力（更严重者，出现内存堆积情况），当前数据库性能与过去七天的性能基线相比已降低。

内存压力表示这样一种性能状况：SQL 数据库中有大量的工作线程在请求内存授予。 大量工作线程导致内存利用率高的状况：SQL 数据库无法有效地将内存分配给请求内存的所有工作线程。 此问题最常见的一个原因涉及到可供 SQL 数据库使用的内存量，这是一方面。 另一方面，工作负荷增加导致工作线程数增加和内存压力增大。

内存压力增大时，更严重者会出现内存堆积的情况。 这种情况表明，请求内存授予的工作线程数超出释放内存的查询数。 这个请求内存授予的工作线程数还可能在此数字的基础上不断增加（堆积），因为 SQL 数据库引擎无法以足够高的效率分配内存，因而无法满足需求。 内存堆积的情况代表最严重的数据库性能问题之一。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出内存对象存储详细信息，并输出已标记为内存使用率高最大原因的分配器（即工作线程）和相关时间戳。 可以将此信息用作故障排除的基础。 

可以优化或删除与导致内存使用率最高的分配器相关的查询。 还可以确保不查询那些不打算使用的数据。 合理的做法是始终在查询中使用 WHERE 子句。 此外，建议创建非聚集索引来搜寻数据，而不是扫描数据。

还可以减少工作负荷，方法是对其进行优化，或者将其分配到多个数据库。 也可将工作负荷分配到多个数据库中。 如果此类解决方案不可行，可以考虑提高 SQL 数据库订阅的定价层，以增加可供数据库使用的内存资源量。

有关其他故障排除建议，请参阅 [Memory grants meditation: The mysterious SQL Server memory consumer with many names](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/)（内存授予探幽：有多个名称的神秘 SQL Server 内存消耗者）。

## <a name="locking"></a>锁定

### <a name="what-is-happening"></a>发生了什么

此性能模式表示当前数据库性能降低，相比过去七天的性能基线，在这种性能情况下检测到的数据库锁定过多。 

在现代 RDBMS 中，锁定对于实现多线程系统至关重要，这样可以通过尽可能地运行多个同步工作线程和并行数据库事务，最大程度地提高性能。 此上下文中的锁定是指一种内置访问机制，即只允许单个事务以独占方式访问所需的行、页、表和文件，不允许其他事务与之争用资源。 当锁定资源进行独占使用的事务用完资源后，对这些资源的锁定就会取消，允许其他事务访问所需资源。 有关锁定的详细信息，请参阅[数据库引擎中的锁定](https://msdn.microsoft.com/library/ms190615.aspx)。

如果 SQL 引擎执行的事务长时间等待访问已被锁定供独占使用的资源，这段等待时间会导致工作负荷在执行起来时变慢。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出锁定详细信息，可将这些信息用作故障排除的基础。 可以分析报告的阻塞查询（即造成锁定性能降低的查询）并将其删除。 在某些情况下，可以成功优化阻塞查询。

缓解问题的最简单安全方法是保持较短的事务运行时间，并减少开销最高的查询的锁占用时间。 可以将大批操作分成小批操作。 合理的做法是尽量提高查询效率，减少查询时的锁定时间。 减少大型扫描，因为这些扫描会增大死锁的可能性，并对数据库总体性能造成负面影响。 对于识别出的导致锁定的查询，可以通过创建新索引或将列添加到现有索引来避免表扫描。 

有关更多建议，请参阅 [How to resolve blocking problems that are caused by lock escalation in SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in)（如何解决 SQL Server 中的锁升级造成的阻塞问题）。

## <a name="increased-maxdop"></a>MAXDOP 提升

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示所选查询执行计划的并行化程度超出了预期。 SQL 数据库查询优化器可以并行执行查询，尽量加速处理进度，从而增强工作负荷的性能。 在某些情况下，处理查询的并行工作线程等待相互同步和合并结果所花费的时间，比使用较少的并行工作线程（有时甚至是一个工作线程）执行相同查询的时间还要长。

专家系统会对比基线期间来分析当前的数据库性能。 它会确定此前运行的某个查询是否运行得比以前更慢，因为查询执行计划的并行化程度比本来应有的程度更高。

SQL 数据库的 MAXDOP 服务器配置选项用于控制并行执行同一查询时可以使用的 CPU 核心数。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出与查询相关的查询哈希，这些查询由于并行化程度超出预期，其执行持续时间延长。 日志还会输出 CXP 等待时间。 此时间表示单个组织器/协调器线程（线程 0）在合并结果并继续运行之前，等待其他所有线程完成的时间。 此外，诊断日志还会输出性能不佳的查询在执行过程中等待的总时间。 可以将此信息用作故障排除的基础。

首先，优化或简化复杂的查询。 合理的做法是将长时间运行的批处理作业分解成较小的作业。 此外，确保创建了用于支持查询的索引。 对于被标记为性能不佳的查询，也可通过手动方式强制实现最大并行度 (MAXDOP)。 若要使用 T-SQL 配置此操作，请参阅 [Configure the MAXDOP server configuration option](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option)（配置 MAXDOP 服务器配置选项）。

将 MAXDOP 服务器配置选项设置为 零 (0)（作为默认值）表示 SQL 数据库可以使用所有可用的逻辑 CPU 核心将执行单个查询的线程并行化。 将 MAXDOP 设置为一 (1) 表示只能将一个核心用于单个查询的执行。 实际上，这意味着并行功能处于关闭状态。 根据具体的情况、数据库的可用核心数与诊断日志信息，可将 MAXDOP 选项优化成用于并行查询执行且能够解决具体问题的核心数。

## <a name="pagelatch-contention"></a>Pagelatch 争用

### <a name="what-is-happening"></a>发生了什么

此性能模式表示由于 Pagelatch 争用，当前数据库工作负荷的性能与过去七天的工作负荷基线相比有所降级。

Latch（闩锁）是一种轻量同步机制，允许 SQL 数据库启用多线程处理。 该机制可保证内存中结构（包括索引、数据页以及其他内部结构）的一致性。

可在 SQL 数据库中使用许多类型的闩锁。 为了简单起见，可以使用缓冲区闩锁来保护缓冲池中的内存中页。 I/O 闩锁用于保护尚未加载到缓冲池中的页。 每当在缓冲池的页中写入或读取数据时，工作线程需要首先获取该页的缓冲区闩锁。 每当工作线程尝试访问尚未在内存中缓冲池中提供的页时，就会发出 I/O 请求，以便从存储中加载所需的信息。 这种事件序列指示更严重形式的性能降低。

当多个线程同时尝试获取同一内存中结构上的闩锁，从而造成查询执行的等待时间延长时，就会发生页闩锁争用。 如果在需要从存储访问数据时发生 Pagelatch I/O 争用，这段等待时间甚至会更长， 并可能会显著影响工作负荷性能。 Pagelatch 争用是线程相互等待，在多个 CPU 系统上争用资源的最常见情景。

### <a name="troubleshooting"></a>故障排除

诊断日志输出 Pagelatch 争用详细信息。 可以将此信息用作故障排除的基础。

由于 Pagelatch 是 SQL 数据库的内部控制机制，因此 SQL 数据库会自动确定何时使用它。 应用程序决策（包括架构设计）可以影响因闩锁的确定性行为而导致的 Pagelatch 行为。

处理闩锁争用的一个方法是将有序索引键替换为无序键，以便在索引范围内均匀分配插入内容。 通常情况下，索引中的前导列可按比例分配工作负荷。 可考虑的另一种方法是表分区。 在分区表中创建一个包含计算列的哈希分区方案，是缓解过度闩锁争用的常用方法。 如果发生 Pagelatch I/O 争用，可以引入索引来缓解此性能问题。 

有关详细信息，请参阅 [Diagnose and resolve latch contention on SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf)（诊断和解决 SQL Server 上的闩锁争用）（PDF 下载）。

## <a name="missing-index"></a>缺失的索引

### <a name="what-is-happening"></a>发生了什么

此性能模式表示由于缺少索引，当前数据库工作负荷的性能与过去七天的基线相比有所降级。

索引用于加速查询性能。 使用索引可以减少需要访问或扫描的数据集页数，因此能够快速访问表数据。

可以通过此项检测来识别导致性能降低的特定查询。创建索引对性能有利。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出查询的查询哈希，这些查询已确定会影响工作负荷性能。 可以为这些查询生成索引。 还可以优化查询，或者删除不需要的那些查询。 在性能方面，合理的做法是避免查询不使用的数据。

> [!TIP]
> 是否知道，SQL 数据库内置智能可以自动管理数据库的最佳性能索引？
>
> 若要持续进行 SQL 数据库性能优化，建议启用 [SQL 数据库自动优化](sql-database-automatic-tuning.md)。 SQL 数据库内置智能的这项独特功能可以持续监视 SQL 数据库并自动优化和创建数据库的索引。
>

## <a name="new-query"></a>新查询

### <a name="what-is-happening"></a>发生了什么

这种性能模式表示检测到一个性能不佳并影响工作负荷性能（与七天性能基线相比）的新查询。

有时，编写性能良好的查询很有难度。 有关编写查询的详细信息，请参阅 [Writing SQL queries](https://msdn.microsoft.com/library/bb264565.aspx)（编写 SQL 查询）。 若要优化现有的查询性能，请参阅[查询优化](https://msdn.microsoft.com/library/ms176005.aspx)。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出最多两个 CPU 消耗量最大的新查询的信息，包括其查询哈希。 由于检测到的查询影响工作负荷性能，可以优化查询。 最好是只检索需要使用的数据。 另外，建议使用带 WHERE 子句的查询， 并简化复杂的查询，将其分解成更小的查询。 将大批查询分解成小批查询也是好办法。 为新查询引入索引通常是缓解此性能问题的好办法。

考虑使用 [Azure SQL 数据库查询性能见解](sql-database-query-performance.md)。

## <a name="unusual-wait-statistic"></a>异常等待统计信息

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示确定的性能不佳的查询导致工作负荷的性能相比过去七天的工作负荷基线有所降级。

在这种情况下，系统无法将性能不佳的查询归到任何其他标准的可检测性能类别下，但确实检测到导致性能回归的等待统计信息。 因此，系统会将其视为具有异常等待统计信息的查询，并会在其中公开导致性能回归的异常等待统计信息。 

### <a name="troubleshooting"></a>故障排除

诊断日志会输出异常等待时间详情、受影响查询的查询哈希、等待时间等方面的信息。

由于系统无法成功识别查询性能不佳的根本原因，在手动故障排除时，诊断信息是很好的入手点。 可以优化这些查询的性能。 合理的做法是只提取需使用的数据，简化复杂的查询，将其分解成较小的查询。 

有关优化查询性能的详细信息，请参阅[查询优化](https://msdn.microsoft.com/library/ms176005.aspx)。

## <a name="tempdb-contention"></a>TempDB 争用

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示这样一种数据库性能状况：尝试访问 tempDB 资源的线程存在瓶颈。 （这种状况与 I/O 不相关。）此性能问题的典型情景是数百个并发查询都在创建、使用然后删除小型 tempDB 表。 系统已检测到使用相同 tempDB 表的并发请求数出现了统计学意义上的大幅增长，影响了数据库的性能（与过去七天的性能基线相比）。

### <a name="troubleshooting"></a>故障排除

诊断日志输出 tempDB 争用详细信息。 可以将此信息用作故障排除的入手点。 可以通过两项操作来减轻此类争用并提高整个工作负荷的吞吐量：一是停止使用临时表， 二是使用内存优化表。 

有关详细信息，请参阅[内存优化表简介](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)。 

## <a name="elastic-pool-dtu-shortage"></a>弹性池 DTU 不足

### <a name="what-is-happening"></a>发生了什么

此可检测的性能模式表示当前数据库工作负荷的性能与过去七天的基线相比有所降级。 这是由于在订阅的弹性池中缺少可用的 DTU。 

SQL 数据库中的资源通常称为 [DTU 资源](sql-database-what-is-a-dtu.md)，由 CPU 和 I/O（数据和事务日志 I/O）资源的混合度量值构成。 [Azure 弹性池资源](sql-database-elastic-pool.md)用作出于缩放目的而在多个数据库之间共享的可用 eDTU 资源的池。 如果弹性池中的可用 eDTU 资源不够大，无法支持池中的所有数据库，则系统就会检测到“弹性池 DTU 不足”性能问题。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出有关弹性池的信息，列出 DTU 消耗量最大的数据库，并提供消耗量最大的数据库所使用的池 DTU 的百分比。

由于性能状况与使用弹性池中同一池 eDTU 的多个数据库相关，因此故障排除步骤侧重于 DTU 消耗量最大的数据库。 可以减少消耗量最大的数据库的工作负荷，包括优化这些数据库中消耗量最大的查询。 还可以确保不查询那些不使用的数据。 另一种做法是使用 DTU 消耗量最大的数据库来优化应用程序，在多个数据库之间重新分配工作负荷。

如果无法减少和优化 DTU 消耗量最大的数据库中的当前工作负荷，可以考虑提高弹性池定价层。 提高定价层可以增加弹性池中的可用 DTU。

## <a name="plan-regression"></a>计划回归

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示 SQL 数据库使用了欠佳的查询执行计划。 欠佳的计划通常会导致查询执行时间延长，从而导致当前查询和其他查询的等待时间变长。

SQL 数据库可以确定查询执行开销最低的查询执行计划。 由于查询和工作负荷的类型会发生变化，有时现有的计划不再有效，或者 SQL 数据库未能做出合理的评估。 作为一种纠正措施，可以手动强制查询执行计划。

这种可检测的性能模式合并了三种不同的计划回归情况：新计划回归、旧计划回归和现有计划更改的工作负荷。 诊断日志的“详细信息”属性中提供了出现的计划回归的特定类型。

新计划回归状况表示这样一种状态：SQL 数据库开始执行不如旧计划那么有效的新查询执行计划。 旧计划回归状况表示这样一种状态：SQL 数据库弃用更有效的新计划，改用不如新计划那么有效的旧计划。 现有计划更改的工作负荷回归表示这样一种状态：不断交替使用旧计划和新计划，天平逐渐倾向于性能不佳的计划。

有关计划回归的详细信息，请参阅 [What is plan regression in SQL Server?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/)（SQL Server 中的计划回归是什么？）。 

### <a name="troubleshooting"></a>故障排除

诊断日志输出查询哈希、正确计划 ID、错误计划 ID 和查询 ID。 可以将此信息用作故障排除的基础。

可以使用提供的查询哈希来分析哪个计划对于可识别的特定查询而言性能更好。 确定哪个计划更适合自己的查询后，可以手动强制该计划。 

有关详细信息，请参阅 [Learn how SQL Server prevents plan regressions](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/)（了解 SQL Server 如何阻止计划回归）。

> [!TIP]
> 是否知道，SQL 数据库内置智能可以自动管理数据库的最佳性能查询执行计划？
>
> 若要持续进行 SQL 数据库性能优化，建议启用 [SQL 数据库自动优化](sql-database-automatic-tuning.md)。 SQL 数据库内置智能的这项独特功能可以持续监视 SQL 数据库并自动优化和创建数据库的最佳性能查询执行计划。
>

## <a name="database-scoped-configuration-value-change"></a>数据库范围的配置值更改

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示数据库范围的配置发生更改，导致检测到性能回归（与过去七天的数据库工作负荷行为相比）。 此模式意味着，最近对数据库范围的配置所做的更改似乎对数据库性能不利。

可以针对每个数据库设置数据库范围的配置更改。 根据具体的情况使用此配置可以优化数据库的个体性能。 可为每个数据库配置以下选项： MAXDOP、LEGACY_CARDINALITY_ESTIMATION、PARAMETER_SNIFFING、QUERY_OPTIMIZER_HOTFIXES 和 CLEAR PROCEDURE_CACHE。

### <a name="troubleshooting"></a>故障排除

诊断日志会输出最近进行的、导致性能相比过去七天的工作负荷行为有所降级的数据库范围配置更改。 可以将配置更改还原为以前的值。 也可对值逐个进行优化，直到达到所需性能级别。 可以从性能令人满意的类似数据库中复制数据库范围配置值。 如果无法排查性能问题，可以还原为 SQL 数据库默认值，并尝试从此基线开始进行微调。

有关优化数据库范围配置的详细信息以及更改配置时使用的 T-SQL 语法，请参阅 [Alter database-scoped configuration (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx)（更改数据库范围的配置 (Transact SQL)）。

## <a name="slow-client"></a>客户端缓慢

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示使用 SQL 数据库的客户端使用数据库输出的速度赶不上数据库发送结果的速度。 由于 SQL 数据库不会在缓冲区中存储所执行查询的结果，因此它的速度会变慢，先等待客户端使用传输的查询输出，然后继续。 此状况也可能与网络相关 - 网络无法以足够快的速度将输出从 SQL 数据库传输到使用方客户端。

仅当检测到性能回归（与过去七天的数据库工作负荷行为相比）时，才会产生这种状况。 仅当性能与过去的性能行为相比，在统计学上有显著的降级时，才会检测到这种性能问题。

### <a name="troubleshooting"></a>故障排除

这种可检测的性能模式指示客户端的情况。 必须在客户端应用程序或客户端网络上进行故障排除。 诊断日志会输出在过去两小时内让客户端等待最长时间来使用的查询哈希和等待时间。 可以将此信息用作故障排除的基础。

可以优化应用程序在使用这些查询时的性能。 还可以考虑可能的网络延迟问题。 由于性能降低问题的检出是基于最近七天性能基线的更改，因此可以调查此性能回归事件是否因最近的应用程序或网络状况变化而导致。 

## <a name="pricing-tier-downgrade"></a>定价层降级

### <a name="what-is-happening"></a>发生了什么

这种可检测的性能模式表示 SQL 数据库订阅的定价层已降级。 由于可供数据库使用的资源 (DTU) 减少，系统检测到当前数据库性能相比过去七天的基线有所下降。

此外，可能 SQL 数据库订阅的定价层曾经降级，后来又在短时间内升级到更高的层。 检测到这种暂时性的性能降低时，会将检测结果作为定价层降级和升级输出到诊断日志的 details 节中。

### <a name="troubleshooting"></a>故障排除

如果降低了定价层，因而减少了可供 SQL 数据库使用的 DTU 数，但同时对性能感到满意，则不需采取任何措施。 如果降低定价层后对 SQL 数据库的性能不满意，请减少数据库工作负荷，或考虑将定价层提升到更高的级别。

## <a name="recommended-troubleshooting-flow"></a>建议的故障排除流程

 请遵循流程图，通过建议的方法使用 Intelligent Insights 排查性能问题。

通过在 Azure 门户中转到 Azure SQL Analytics 来访问 Intelligent Insights。 尝试找到传入的性能警报并选择它。 在检测页上确定发生了什么情况。 观察提供的问题根本原因分析、查询文本、查询时间趋势和事件演变情况。 使用用于缓解性能问题的 Intelligent Insights 建议来尝试解决问题。 

[![故障排除流程图](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> 选择可下载 PDF 版本的流程图。

Intelligent Insights 通常需要花费一小时来针对性能问题执行根本原因分析。 如果在 Intelligent Insights 中找不到问题，而该问题又很重要，则请使用查询存储手动确定性能问题的根本原因。 （通常情况下，这些问题是不到一小时之前的问题。）有关详细信息，请参阅[使用查询存储监视性能](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)。

## <a name="next-steps"></a>后续步骤
- 了解 [Intelligent Insights](sql-database-intelligent-insights.md) 概念。
- 使用 [Intelligent Insights Azure SQL 数据库性能诊断日志](sql-database-intelligent-insights-use-diagnostics-log.md)。
- [使用 Azure SQL Analytics 监视 Azure SQL 数据库](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)。
- 了解如何[从 Azure 资源收集和使用日志数据](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。
