---
title: "Intelligent Insights 监视数据库使用情况 - Azure SQL 数据库 | Microsoft Docs"
description: "使用 Intelligent Insights 可以知道数据库发生了什么情况"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c3b11dd50fa8c94d3bf80e02a8a319030c375133
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="intelligent-insights"></a>Intelligent Insights

***“使用 Intelligent Insights 可以知道数据库发生了什么情况。”***

Azure SQL 数据库内置智能将通过人工智能持续监视数据库使用情况，并检测导致性能不佳的中断事件。 检测后，将执行详细分析，并生成包含对问题的智能评估的诊断日志。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议，即***“智能见解”***。 

## <a name="what-can-intelligent-insights-do-for-you"></a>Intelligent Insights 能为你做什么？

Intelligent Insights 是 Azure 内置智能的一项独特功能，提供以下功能价值：

- 主动监视
- 定制的性能见解
- 数据库性能下降的早期检测
- 已检测出问题的根本原因分析 (RCA)
- 性能改进建议
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="how-does-intelligent-insights-work"></a>Intelligent Insights 的工作原理是什么？

Intelligent Insights 可分析 Azure SQL 数据库性能，从而比较前 1 个小时的数据库工作负载和前 7 天的基线工作负载。 数据库工作负载由确定为对数据库性能最为重要的查询（例如重复最多和最大的查询）组成。 每个数据库基于其结构、数据、使用情况和应用程序都是唯一的，这是因为每个生成的工作负载基线对于单个实例而言都具有特定性和唯一性。 Intelligent Insights 因为独立于工作负载基线，还可监视绝对操作阈值，并检测过长的等待时间问题、关键异常和查询参数化问题，这些问题可能会对性能造成影响。

在使用人工智能根据多个观察的指标检测到性能下降后，将执行分析，并输出包含数据库状况的智能见解的诊断日志。 Intelligent Insights 可对数据库性能问题从其首次出现到解决全程轻松展开跟踪。 通过从初始问题检测、性能改进验证和完成的生命周期中跟踪每个检测到的问题的状态，可以实现此目的。 更新在诊断日志中每 15 分钟提供一次。 

<center>![服务器](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

用于衡量和检测数据库性能问题的指标基于查询持续时间、超时请求、过长的等待时间和出错的请求制定，并在本文档的[检测指标](sql-database-intelligent-insights.md#detection-metrics)部分作进一步说明。

## <a name="degradations-detected"></a>检测到性能下降

确定的 Azure SQL 数据库性能下降记录在诊断日志中，并包含由以下属性组成的智能项：

| 属性             | 详细信息              |
| :------------------- | ------------------- |
| 数据库信息  | 关于在其上检测到见解的数据库的元数据，例如资源 URI |
| 观察的时间范围 | 检测到的见解时段的开始和结束时间 |
| 受影响的指标 | 导致生成某个见解的指标： <ul><li>查询持续时间增加 [秒]</li><li>过长等待 [秒]</li><li>超时的请求 [百分比]</li><li>出错的请求 [百分比]</li></ul>|
| 影响值 | 某个指标测量出的值 |
| 受影响的查询和错误代码 | 查询哈希或错误代码。 这些属性可用于轻松关联到受影响的查询。 提供包括查询持续时间增加、等待时间、超时计数或错误代码的指标。 |
| 检测 | 在数据库中发生事件时确定的检测。 有 15 种检测模式。 请参阅[使用 Intelligent Insights 解决数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)。 |
| 根本原因分析 | 对已确定问题的根本原因分析 (RCA) 采用人眼可读的格式。 一些见解可能包含可行的性能改进建议。 |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>问题状态生命周期：活动、正在验证和已完成

在诊断日志中记录的性能问题通过问题生命周期的三种状态（活动、正在验证和已完成）中的一种进行标记。 检测到性能问题后，只要该问题由 Azure SQL 内置智能认定为存在，即将其标记为“活动”。 当系统认为该问题得到缓解时，它将进行验证，并且问题状态更改为“正在验证”。 在 Azure SQL 内置智能认为问题得到解决后，问题状态将标记为“已完成”。

## <a name="using-intelligent-insights"></a>使用 Intelligent Insights

如 [Azure SQL 数据库指标和诊断日志记录](sql-database-metrics-diag-logging.md)中所述，Intelligent Insights 诊断日志可以发送到 Azure Log Analytics、Azure 事件中心和 Azure 存储。 在日志发送到这三个目标中的一个后，使用 Microsoft 或第三方工具可将该日志用于自定义警报和监视开发。 有关使用 Intelligent Insights 的 Azure SQL 数据库性能故障排除操作指南，请参阅[使用 Intelligent Insights 解决 Azure SQL 数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)。

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>使用 Azure Log Analytics 的内置 Intelligent Insights 分析 

Azure Log Analytics 解决方案在提供 Intelligent Insights 诊断日志数据之外还提供报告和警报功能。 下面是 Azure SQL Analytics 中 Intelligent Insights 报告的示例。

![服务器](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

在 Intelligent Insights 诊断日志配置为将数据流式传输到 Azure SQL Analytics 后，可以[使用 Azure SQL Analytics 监视 Azure SQL 数据库](../log-analytics/log-analytics-azure-sql.md)。

## <a name="custom-integrations-of-intelligent-insights-log"></a>Intelligent Insights 日志的自定义集成

有关使用 Microsoft 或第三方工具自定义警报和监视开发，请参阅[使用 Intelligent Insights 数据库性能诊断日志](sql-database-intelligent-insights-use-diagnostics-log.md)。

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>如何使用 Azure 事件中心设置 Intelligent Insights

- 通过[将 Azure 诊断日志流式传输到事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)，配置 Intelligent Insights 以将日志事件流式传输到 Azure 事件中心。
- 通过[如何在事件中心内处理指标和诊断日志](sql-database-metrics-diag-logging.md#stream-into-azure-storage)，将 Azure 事件中心用于自定义监视和警报。 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>如何使用 Azure 存储设置 Intelligent Insights

- 通过[流式传输到 Azure 存储](sql-database-metrics-diag-logging.md#stream-into-azure-storage)，将 Intelligent Insights 配置为存储到 Azure 存储中。

## <a name="detection-metrics"></a>检测指标

用于生成 Intelligent Insights 的检测模型的指标基于监视以下内容得出：

- 查询持续时间
- 超时请求
- 过长的等待时间 
- 出错的请求

在检测数据库工作负载性能问题时，查询持续时间和超时请求将用作主要模型。 这是因为它们直接测量工作负载发生的情况。 为检测工作负载性能下降的所有可能情况，过长的等待时间和出错的请求将用作附加模型，以指出影响工作负载性能的问题。

系统会自动将工作负载更改和对数据库所做的查询请求数更改纳入考虑，以动态决定正常和异常的数据库性能阈值。

所有指标都将通过科学派生的数据模型在各种关系中纳入考虑，并且这种模型会对每个检测到的性能问题进行归类。 通过智能见解提供的信息包括检测到的性能问题的详细信息、所发生问题的根本原因分析以及在可能的情况下如何改进所监视 Azure SQL 数据库性能的建议。

## <a name="query-duration"></a>查询持续时间

查询持续时间下降模型分析单独的查询，并检测与性能基线相比，编译和执行查询增加的时间。

当 Azure SQL 数据库内置智能检测到查询编译或查询执行时间显著增加并影响工作负载性能时，这些查询将标记为具有查询持续时间性能下降问题。 

Intelligent Insights 诊断日志将输出性能下降查询的查询哈希，性能下降是否与查询编译或执行时间增加有关的指示以及增加的查询持续时间。

## <a name="timeout-requests"></a>超时请求

超时请求下降模型分析单独的查询，并检测查询执行级别增加的超时，以及与性能基线持续时间相比，数据库级别的总体请求超时。

由于一些查询在到达执行阶段前就可能超时，所以通过比较中止的辅助角色数和所提请求数的方式，Azure SQL 数据库内置智能还测量和分析到达数据库的所有查询，而不管它们有没有到达执行阶段。 

在执行查询超时数或中止的请求辅助角色数超出系统管理阈值后，使用智能见解填充诊断日志。

生成的见解包含超时请求数、超时查询数，并指示性能下降是否与执行阶段或总体数据库级别的超时增加有关。 当系统认为超时增加对数据库性能至关重要时，这些查询将标记为具有超时性能下降问题。 

## <a name="excessive-wait-times"></a>过长的等待时间

过长的等待时间模型监视单独的数据库查询，并检测通常高于系统管理的绝对阈值的查询等待统计信息。 下面的查询过长等待时间指标使用新的 SQL Server 功能（即“查询存储等待统计信息” (sys.query_store_wait_stats) 功能）进行观察：

- 达到资源上限
- 达到弹性池资源上限
- 过多的辅助角色或会话线程数
- 过多的数据库锁定
- 内存压力
- 其他等待统计信息

达到资源上限或弹性池资源上限表示某一订阅或弹性池中的可用资源消耗已经超过指示工作负载性能下降的绝对阈值。 过多的辅助角色或会话线程数表示如下情况：启动的辅助角色线程或会话数超过指示工作负载性能下降的绝对阈值。

过多的数据库锁定表示如下情况：数据库锁定计数超过指示工作负载性能下降的绝对阈值。 内存压力表示如下情况：请求内存授予的线程数超过指示工作负载性能下降的绝对阈值。

其他等待统计信息指示如下情况：通过查询存储等待统计信息测量的其他指标超过指示工作负载性能下降的绝对阈值。

检测到过长的等待时间后，根据可用数据的情况，Intelligent Insights 诊断日志将输出性能下降正在影响和已经影响的查询的哈希，以及导致查询在执行和测量等待时间中等待的指标的详细信息。

## <a name="errored-requests"></a>出错的请求

出错的请求下降模型监视单独的查询，并检测与基线持续时间相比，增加的出错查询数。 此模型还监视达到由 Azure 数据库内置智能管理的绝对阈值的关键异常。 系统将自动考虑向数据库提出的查询请求数，并在受监视时期说明任何工作负载更改。

当系统认为测量出的出错请求数增加（与提出的总请求数有关）对工作负载性能至关重要时，受影响的查询将标记为具有出错请求的性能下降问题。

Intelligent Insights 日志将输出出错请求计数，性能下降是否与出错请求数增加或达到受监视的关键异常阈值之一有关，以及性能下降的测量时间。 

在任何受监视的关键异常超出系统管理的绝对阈值的情况下，将使用关键异常详细信息生成智能见解。

## <a name="next-steps"></a>后续步骤
* 了解[使用 Intelligent Insights 解决 Azure SQL 数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)
* 使用 [Intelligent Insights Azure SQL 数据库性能诊断日志](sql-database-intelligent-insights-use-diagnostics-log.md)
* 了解[使用 Azure SQL Analytics 监视 Azure SQL 数据库](../log-analytics/log-analytics-azure-sql.md)
* 了解[从 Azure 资源收集和使用日志数据](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)



