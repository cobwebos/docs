---
title: 使用智能见解监视数据库性能
description: 智能见解 Azure SQL 数据库和 Azure SQL 托管实例使用内置智能，通过人工智能持续监视数据库使用情况，并检测导致性能不佳的干扰性事件。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 06/12/2020
ms.openlocfilehash: 96557a6049b316a69c32e96012206eab128e024a
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986498"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>智能见解使用 AI 监视数据库性能并对其进行故障排除（预览版）
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

智能见解 Azure SQL 数据库和 Azure SQL 托管实例，你可以了解数据库性能发生了什么情况。

Intelligent Insights 使用内置智能，通过人工智能持续监视数据库使用情况，并检测导致性能不佳的干扰性事件。 检测到后，将执行详细分析，生成智能见解资源日志（称为 SQLInsights），并对问题进行智能评估。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议。

## <a name="what-can-intelligent-insights-do-for-you"></a>智能见解能为你做什么？

Intelligent Insights 是 Azure 内置智能的一项独特功能，提供以下功能价值：

- 主动监视
- 定制的性能见解
- 数据库性能下降的早期检测
- 已检测出问题的根本原因分析
- 性能改进建议
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="how-does-intelligent-insights-work"></a>智能见解的工作原理

Intelligent Insights 可分析数据库性能，方法是比较前一个小时的数据库工作负荷和前七天的基线工作负荷。 数据库工作负荷由确定为对数据库性能最为重要的查询（例如重复最多和最大的查询）组成。 由于每个数据库基于其结构、数据、使用情况和应用程序都是唯一的，因此，生成的每个工作负荷基线都是特定于该工作负荷的唯一且唯一的。 Intelligent Insights 因为独立于工作负荷基线，还可监视绝对操作阈值，并检测过长的等待时间问题、关键异常和查询参数化问题，这些问题可能会对性能造成影响。

在使用人工智能根据多个观察的指标检测到性能降低问题后，将执行分析。 此外还会生成包含数据库状况的智能见解的诊断日志。 Intelligent Insights 可对数据库性能问题从其首次出现到解决全程轻松展开跟踪。 从初始问题检测和性能改进验证到完成，跟踪每个检测到的问题的整个生命周期。

![数据库性能分析工作流](./media/intelligent-insights-overview/intelligent-insights-concept.png)

用于衡量和检测数据库性能问题的指标基于查询持续时间、超时请求、过长的等待时间和出错的请求制定。 有关指标的详细信息，请参阅[检测指标](#detection-metrics)。

标识的数据库性能下降记录在 SQLInsights 日志中，其中包含由以下属性组成的智能项：

| Property | 详细信息 |
| :------------------- | ------------------- |
| 数据库信息 | 关于在其上检测到见解的数据库的元数据，例如资源 URI。 |
| 观察的时间范围 | 检测到的见解时段的开始和结束时间。 |
| 受影响的指标 | 导致生成某个见解的指标： <ul><li>查询持续时间增加 [秒]。</li><li>过长等待 [秒]。</li><li>超时的请求 [百分比]。</li><li>出错的请求 [百分比]。</li></ul>|
| 影响值 | 某个指标测量出的值。 |
| 受影响的查询和错误代码 | 查询哈希或错误代码。 这些属性可用于轻松关联到受影响的查询。 提供包括查询持续时间增加、等待时间、超时计数或错误代码的指标。 |
| 检测 | 在数据库中发生事件时确定的检测。 有 15 种检测模式。 有关详细信息，请参阅[使用 Intelligent Insights 排查数据库性能问题](intelligent-insights-troubleshoot-performance.md)。 |
| 根本原因分析 | 对已识别问题的根本原因分析采用人类可读的格式。 一些见解可能包含可行的性能改进建议。 |
|||

有关将智能见解用于 Azure SQL Analytics 和典型使用方案的动手概述，请观看此视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

智能见解用于发现和解决数据库性能问题。 为了使用智能见解来解决数据库性能问题，请参阅[使用智能见解排除性能问题](intelligent-insights-troubleshoot-performance.md)。

## <a name="intelligent-insights-options"></a>智能见解选项

可用智能见解选项如下：

| 智能见解选项 | Azure SQL 数据库支持 | Azure SQL 托管实例支持 |
| :----------------------------- | ----- | ----- |
| **配置智能见解** - 配置针对数据库的智能见解分析。 | 是 | 是 |
| 将**见解流式处理到 Azure SQL Analytics** --将见解传递到 Azure SQL Analytics。 | 是 | 是 |
| **将见解流式处理到 Azure 事件中心**-将见解流式传输到事件中心，以便进行进一步的自定义集成。 | 是 | 是 |
| **将见解流式传输到 Azure 存储**-将见解流式传输到 azure 存储，以便进行进一步分析和长期存档。 | 是 | 是 |

> [!NOTE]
> 智能见解是一项预览功能，在以下区域中不可用：西欧、北欧、美国西部1和美国东部1。

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>配置智能见解日志的导出

可以将智能见解的输出流式传输到多个目标之一进行分析：

- 流式传输到 Log Analytics 工作区的输出可以与[Azure SQL Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql)结合使用，以便通过 Azure 门户的用户界面查看见解。 这是集成式 Azure 解决方案，是用于查看见解的最典型方式。
- 流式传输到 Azure 事件中心的输出可用于开发自定义监视和警报方案
- 流式传输到 Azure 存储的输出可用于自定义应用程序开发，例如自定义报表、长期数据存档等。

将 Azure SQL Analytics、Azure 事件中心、Azure 存储或第三方产品的集成通过首先在数据库的 "诊断设置" 边栏选项卡中启用智能见解日志记录（"SQLInsights" 日志），然后将智能见解日志数据配置为流式传输到这些目标之一。

有关如何启用智能见解日志记录以及配置要流式传输到使用产品的指标和资源日志数据的详细信息，请参阅[指标和诊断日志记录](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。

### <a name="set-up-with-azure-sql-analytics"></a>通过 Azure SQL Analytics 进行设置

Azure SQL Analytics 解决方案使用智能见解资源日志数据，为数据库性能提供图形用户界面、报告和警告功能。

从 marketplace 向 Azure 门户仪表板添加 Azure SQL Analytics，若要创建工作区，请参阅[配置 Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md#configuration)

若要将智能见解与 Azure SQL Analytics 一起使用，请将智能见解日志数据流式传输到你在上一步中创建 Azure SQL Analytics 工作区，请参阅[指标和诊断日志记录](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。

以下示例显示了通过 Azure SQL Analytics 查看的智能见解：

![智能见解报告](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>通过事件中心进行设置

若要将智能见解与事件中心一起使用，请将智能见解日志数据配置为流式传输到事件中心，请参阅[指标和诊断日志记录](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)，并[将 Azure 诊断日志流式传输到事件中心](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)。

若要使用事件中心设置自定义监视和警报，请参阅如何[在事件中心处理指标和诊断日志](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)。

### <a name="set-up-with-azure-storage"></a>通过 Azure 存储进行设置

若要将智能见解与存储配合使用，请将智能见解日志数据配置为流式传输到存储，请参阅[指标和诊断日志记录](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)，并[流式传输到 Azure 存储](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)。

### <a name="custom-integrations-of-intelligent-insights-log"></a>Intelligent Insights 日志的自定义集成

若要将智能见解与第三方工具一起使用，或者使用自定义警报和监视开发，请参阅[使用智能见解数据库性能诊断日志](intelligent-insights-use-diagnostics-log.md)。

## <a name="detection-metrics"></a>检测指标

用于生成 Intelligent Insights 的检测模型的指标基于监视以下内容得出：

- 查询持续时间
- 超时请求
- 过长的等待时间
- 出错的请求

在检测数据库工作负载性能问题时，查询持续时间和超时请求将用作主要模型。 这是因为它们直接测量工作负荷发生的情况。 为检测工作负荷性能降低的所有可能情况，过长的等待时间和出错的请求将用作附加模型，以指出影响工作负荷性能的问题。

系统会自动将工作负荷更改和对数据库所做的查询请求数更改纳入考虑，以动态决定正常和异常的数据库性能阈值。

所有指标都将通过科学派生的数据模型在各种关系中纳入考虑，并且这种模型会对每个检测到的性能问题进行归类。 通过智能见解提供的信息包括：

- 检测到的性能问题的详细信息。
- 检测到的问题的根本原因分析。
- 建议如何提高监视的数据库的性能（如果可能）。

## <a name="query-duration"></a>查询持续时间

查询持续时间下降模型分析单独的查询，并检测与性能基线相比，编译和执行查询增加的时间。

如果内置智能检测到查询编译或查询执行时间显著增加，这会影响工作负荷性能，这些查询将标记为查询持续时间性能降低问题。

Intelligent Insights 诊断日志会输出性能降低的查询的查询哈希。 查询哈希指出性能降低是否与查询编译或执行时间增加（使查询持续时间增加）有关。

## <a name="timeout-requests"></a>超时请求

超时请求下降模型分析单独的查询，并在查询执行级别检测任何超时增加情况，以及与性能基线持续时间相比，数据库级别的总体请求超时。

某些查询甚至在到达执行阶段之前就可能超时。 通过中止的工作线程与发出的请求的方法，内置智能度量和分析到达数据库的所有查询是否到达执行阶段。

在执行查询超时数或中止的请求辅助角色数超出系统管理阈值后，使用智能见解填充诊断日志。

生成的见解包含超时请求数和超时查询数。 指示性能降低是否与执行阶段的超时增加有关，或提供总体数据库级别。 当系统认为超时增加对数据库性能至关重要时，这些查询将标记为出现超时性能降低问题。

## <a name="excessive-wait-times"></a>过长的等待时间

过长的等待时间模型监视单个数据库查询。 它会检测高出系统管理的绝对阈值的异常查询等待统计信息。 下面的查询通过使用[查询存储等待统计信息（query_store_wait_stats）](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)来观察到过长的等待时间度量：

- 达到资源限制
- 达到弹性池资源上限
- 过多的辅助角色或会话线程数
- 过多的数据库锁定
- 内存压力
- 其他等待统计信息

达到资源限制或弹性池资源限制表示某一订阅或弹性池中的可用资源消耗已超出绝对阈值。 这些统计信息指示工作负荷性能降低。 过多的辅助角色或会话线程数表示如下情况：启动的工作线程数或会话数超出绝对阈值。 这些统计信息指示工作负荷性能降低。

过多的数据库锁定表示如下情况：数据库锁定计数超出绝对阈值。 此状态指示工作负荷性能降低。 内存压力表示如下情况：请求内存授予的线程数超出绝对阈值。 此状态指示工作负荷性能降低。

其他等待统计信息指示如下情况：通过查询存储等待统计信息测量的其他指标超出绝对阈值。 这些统计信息指示工作负荷性能降低。

检测到过长的等待时间后，根据可用数据的情况，Intelligent Insights 诊断日志将输出性能降低正在影响和已经影响的查询的哈希，以及导致查询在执行和测量等待时间中等待的指标的详细信息。

## <a name="errored-requests"></a>出错的请求

出错的请求下降模型监视单独的查询，并检测与基线持续时间相比，增加的出错查询数。 此模型还监视超过内置智能管理的绝对阈值的关键异常。 系统会自动考虑向数据库提出的查询请求数，并在受监视时期说明任何工作负荷更改。

当系统认为测量出的出错请求数增加（与提出的总请求数有关）对工作负荷性能至关重要时，受影响的查询将标记为具有出错请求的性能降低问题。

Intelligent Insights 日志输出出错请求计数。 它会指示性能降低是否与出错请求数增加或超出受监视的关键异常阈值有关，以及性能降低的测量时间。

在任何受监视的关键异常超出系统管理的绝对阈值的情况下，会使用关键异常详细信息生成智能见解。

## <a name="next-steps"></a>后续步骤

- 了解如何[使用 SQL Analytics 监视数据库](../../azure-monitor/insights/azure-sql.md)。
- 了解如何[通过智能见解排查性能问题](intelligent-insights-troubleshoot-performance.md)。
