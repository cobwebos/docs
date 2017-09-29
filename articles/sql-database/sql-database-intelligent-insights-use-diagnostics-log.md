---
title: "Intelligent Insights 性能诊断日志 - Azure SQL 数据库 | Microsoft Docs"
description: "Intelligent Insights 提供 Azure SQL 数据库性能问题的诊断日志"
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
ms.openlocfilehash: 4890baa4ead3323834a82b3f9340cf751bf0c755
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="use-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>使用 Intelligent Insights Azure SQL 数据库性能诊断日志

本页提供使用 [Intelligent Insights](sql-database-intelligent-insights.md) 生成的 Azure SQL 数据库性能诊断日志、它的格式和它为满足自定义开发需求所包含的数据的信息。 可将此诊断日志发送到 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure 事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure 存储](sql-database-metrics-diag-logging.md#stream-into-azure-storage)或第三方解决方案，以用于自定义 DevOps 警报和报告功能。

## <a name="log-header"></a>日志标头

诊断日志使用 JSON 标准格式输出 Intelligent Insights 的发现成果。 访问 Intelligent Insights 日志的确切类别属性是固定值“SQLInsights”。

日志标头很常见，并且在创建所含资源 ID (ResourceId) 是指与本身有关联的特定 Azure SQL 数据库的条目时，标头由时间戳组成 (TimeGenerated)。 类别 (Category)、级别 (Level) 和操作名称 (OperationName) 是值不变的固定属性，它们指示日志条目可以提供信息，并且来自于 Intelligent Insights (SQLInsights)。

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>问题 ID 和受影响的数据库

问题标识属性 (issueId_d) 提供一种跟踪性能问题直至解决的独特方法。 Intelligent Insights 将每个问题的生命周期显示为：活动、正在验证和已完成。 使用所有这些状态阶段，Intelligent Insights 可以在日志中记录多个事件记录，并且每个条目都始终有一个独特的问题 ID 编号。 Intelligent Insights 会在问题生命周期中跟踪它，并且每 15 分钟在诊断日志中生成一个见解。

一旦检测到性能问题并且只要它还在持续发生，该问题在状态 (Status) 属性下将报告为“活动”。 检测的问题得到缓解后，它将进行验证，并在状态 (Status) 属性下报告为“正在验证”。 如果问题已不在，状态 (Status) 属性会将此问题报告为“已完成”。
除问题 ID 外，诊断日志还会报告与在诊断日志中所报告问题相关的特定事件的开始 (intervalStartTime_t) 和结束 (intervalEndTme_t) 时间戳。

属性弹性池 (elasticPoolName_s) 指示出问题数据库属于哪个弹性池。 如果数据库不属于弹性池，此属性将没有值。 检测到问题的数据库在数据库名称 (databaseName_s) 属性中公开。

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported timestamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported timestamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: Active, Verifying and Complete
```

## <a name="detected-issues"></a>检测到的问题

Intelligent Insights 性能日志的下一部分包括通过内置人工智能检测到的性能问题。 检测结果在 JSON 属性检测中公开，该属性检测由问题类别、问题影响、受影响的查询和各个指标组成。 应注意的是，检测属性可能包含多个检测到的性能问题。

检测到的性能问题使用以下检测属性结构进行报告：

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // tperformance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

下表提供输出到诊断日志的可检测性能模式和详细信息。

### <a name="detection-category"></a>检测类别

类别 (category) 属性介绍可检测性能模式的类别。 请查看下表，了解可检测性能模式的所有可能类别。 有关进一步详细信息，请参阅[使用 Intelligent Insights 解决数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)页。

相应地，诊断日志文件中输出的详细信息可能因检测到的性能问题而异。

| 可检测性能模式 | 输出的详细信息 |
| :------------------- | ------------------- |
| 达到资源上限 | <li>受影响的资源</li><li>查询哈希</li><li>资源消耗百分比</li> |
| 工作负载增加 | <li>执行增加的查询数量</li><li>对工作负载增加影响最大的查询的查询哈希</li> |
| 内存压力 | <li>内存分配器</li> |
| 锁定 | <li>受影响的查询哈希</li><li>阻止的查询哈希</li> |
| 增加的 MAXDOP | <li>查询哈希</li><li>CXP 等待时间</li><li>等待时间</li> |
| Pagelatch 争用 | <li>导致争用的查询的查询哈希</li> |
| 缺失的索引 | <li>查询哈希</li> |
| 新查询 | <li>新查询的查询哈希</li> |
| 异常等待统计信息 | <li>异常等待类型</li><li>查询哈希</li><li>查询等待时间</li> |
| TempDB 争用 | <li>导致争用的查询的查询哈希</li><li>致使整体数据库 pagelatch 争用等待时间增加的查询 [ % ]</li> |
| 弹性池 DTU 不足 | <li>弹性池</li><li>最高的 DTU 消耗数据库</li><li>最高使用者使用的池 DTU 百分比</li> |
| 计划回归 | <li>查询哈希</li><li>完善的计划 ID</li><li>错误的计划 ID</li><li>查询哈希</li> |
| 数据库范围的配置值更改 | <li>与默认值相比的数据库范围的配置更改</li> |
| 客户端缓慢 | <li>查询哈希</li><li>等待时间</li> |
| 定价层降级 | <li>文本通知</li> |

### <a name="impact"></a>影响

影响 (impact) 属性介绍检测到的行为对问题数据库具有多大的影响，范围从 1 到 3，3 代表影响最大，2 代表影响中等，1 代表影响最低。 影响值可以用作自定义警报自动化的输入，具体取决于特定需求。 受影响的属性查询 (QueryHashes) 提供受特定检测影响的查询哈希的列表。

### <a name="impacted-queries"></a>受影响的查询

Intelligent Insights 日志的下一部分提供关于受检测到的性能问题影响的特定查询信息。 此信息公开为嵌入 impact_s 属性的一组对象。 影响属性由实体和指标组成。 实体是指在属性值 (Value) 下公开的特定查询（类型：Query）和唯一的查询哈希。 此外，每个公开的查询后跟指标和值，指示检测到的性能问题。

在以下日志示例中，可以注意到哈希为 0x9102EXZ4 的查询经检测执行持续时间（指标：DurationIncreaseSeconds）的值增加了 110 秒，这指示此特定查询需要多花 110 秒用于执行。 应注意到的是，由于此特定日志部分可能包括多个查询条目，所以可能会检测到多个查询。

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>度量值

每个报告的指标的测量单位在指标 (metric) 属性下提供，可能的值有：秒、数字和百分比。 测量的指标值在值 (value) 属性中报告。

DurationIncreaseSeconds 属性提供的测量单位是秒，对于 CriticalErrorCount，测量单位是表示错误计数的数字。

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>根本原因分析和改进建议

Intelligent Insights 性能日志的最后一个部分关于通过根本原因分析 (rootCauseAnalysis_s) 属性，采用用户友好措辞自动分析已确定的性能下降问题的根本原因。 改进建议将包含在记录在日志中的措辞中（如果可能）。

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Intelligent Insights 性能日志可与 [Azure Log Analytics]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) 或第三方解决方案结合使用，以用于自定义 DevOps 警报和报告功能。

## <a name="next-steps"></a>后续步骤
- 了解 [Intelligent Insights](sql-database-intelligent-insights.md) 的概念
- 了解[使用 Intelligent Insights 解决 Azure SQL 数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)
- 了解[使用 Azure SQL Analytics 监视 Azure SQL 数据库](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)
- [从 Azure 资源收集和使用日志数据](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)




