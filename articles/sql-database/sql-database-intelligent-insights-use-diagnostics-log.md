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
ms.workload: Inactive
ms.date: 09/25/2017
ms.author: v-daljep
ms.openlocfilehash: 358986f58c431aebfe7b41daa8c40ba641dc408a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="use-the-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>使用 Intelligent Insights Azure SQL 数据库性能诊断日志

本页提供有关如何使用 [Intelligent Insights](sql-database-intelligent-insights.md) 生成的 Azure SQL 数据库性能诊断日志、它的格式和它为满足自定义开发需求所包含的数据的信息。 可将此诊断日志发送到 [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)、[Azure 事件中心](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)、[Azure 存储](sql-database-metrics-diag-logging.md#stream-into-storage)或第三方解决方案，以用于自定义 DevOps 警报和报告功能。

## <a name="log-header"></a>日志标头

诊断日志使用 JSON 标准格式输出 Intelligent Insights 的发现成果。 访问 Intelligent Insights 日志的确切类别属性是固定值“SQLInsights”。

日志的标头很常见，由时间戳 (TimeGenerated) 组成，它显示条目的创建时间。 它还包括一个引用条目所涉及的特定 SQL 数据库的资源 ID (ResourceId)。 类别 (Category)、级别 (Level) 和操作名称 (OperationName) 为固定属性，其值不会改变。 它们指示日志项目是信息性的，并表示日志项目来自 Intelligent Insights (SQLInsights)。

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>问题 ID 和受影响的数据库

问题标识属性 (issueId_d) 提供一种跟踪性能问题直至解决的独特方法。 Intelligent Insights 将每个问题的生命周期显示为：“活动”、“正在验证”或“已完成”。 使用所有这些状态阶段，Intelligent Insights 可以在日志中记录多个事件记录。 对于每个条目，问题 ID 编号始终是唯一的。 Intelligent Insights 会在问题的整个生命周期中对它进行跟踪，并且每 15 分钟在诊断日志中生成一个见解。

一旦检测到性能问题并且只要它还在持续发生，该问题在状态 (status_s) 属性下将报告为“活动”。 检测的问题得到缓解后，它将进行验证，并在状态 (status_s) 属性下报告为“正在验证”。 如果问题已不在，状态 (status_s) 属性会将此问题报告为“完成”。

除问题 ID 外，诊断日志还会报告与在诊断日志中所报告问题相关的特定事件的开始 (intervalStartTime_t) 和结束 (intervalEndTme_t) 时间戳。

弹性池 (elasticPoolName_s) 属性指示出问题数据库属于哪个弹性池。 如果数据库不属于弹性池，此属性将没有值。 检测到问题的数据库在数据库名称 (databaseName_s) 属性中公开。

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>检测到的问题

Intelligent Insights 性能日志的下一部分包括通过内置人工智能检测到的性能问题。 检测结果会在 JSON 诊断日志的属性中公开。 这些检测结果包含问题类别、问题的影响、受影响的查询和指标。 检测属性可能包含多个检测到的性能问题。

检测到的性能问题使用以下检测属性结构进行报告：

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

下表提供了输出到诊断日志的可检测性能模式和详细信息。

### <a name="detection-category"></a>检测类别

类别 (category) 属性介绍了可检测性能模式的类别。 请查看下表，了解可检测性能模式的所有可能类别。 有关详细信息，请参阅[使用 Intelligent Insights 排查数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)。

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
| TempDB 争用 | <li>导致争用的查询的查询哈希</li><li>致使整体数据库 pagelatch 争用等待时间增加的查询 [%]</li> |
| 弹性池 DTU 不足 | <li>弹性池</li><li>最高的 DTU 消耗数据库</li><li>最高使用者使用的池 DTU 百分比</li> |
| 计划回归 | <li>查询哈希</li><li>完善的计划 ID</li><li>错误的计划 ID</li> |
| 数据库范围的配置值更改 | <li>与默认值相比的数据库范围的配置更改</li> |
| 客户端缓慢 | <li>查询哈希</li><li>等待时间</li> |
| 定价层降级 | <li>文本通知</li> |

### <a name="impact"></a>影响

影响 (impact) 属性描述检测到的行为对数据库的问题造成了多大的影响。 影响范围从 1 到 3，3 影响最大、2 影响居中，1 影响最小。 影响值可以用作自定义警报自动化的输入，具体取决于特定需求。 受影响的属性查询 (QueryHashes) 提供受特定检测影响的查询哈希的列表。

### <a name="impacted-queries"></a>受影响的查询

Intelligent Insights 日志的下一部分提供关于受检测到的性能问题影响的特定查询信息。 此信息公开为嵌入 impact_s 属性的一组对象。 影响属性包含实体和指标。 实体引用特定查询（类型：Query）。 唯一的查询哈希值在值 (Value) 属性下公开。 此外，每个公开的查询后跟指标和值，指示检测到的性能问题。

在以下日志示例中，使用哈希 0x9102EXZ4 的 查询被检测到其执行持续时间延长（指标：DurationIncreaseSeconds）。 值 110 秒表示此特定查询的执行时间延长 110 秒。 因为可以检测到多个查询，所以此特定日志部分可能包含多个查询条目。

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

DurationIncreaseSeconds 属性提供以秒为单位的测量单位。 CriticalErrorCount 测量单位是一个数字，表示错误计数。

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>根本原因分析和改进建议

Intelligent Insights 性能日志的最后部分是对已确定的性能下降问题的根本原因进行自动分析。 以用户友好措辞形式出现的信息会显示在根本原因分析 (rootCauseAnalysis_s) 属性中。 日志中可能包含改进建议。

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Intelligent Insights 性能日志可与 [Azure Log Analytics]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) 或第三方解决方案结合使用，以用于自定义 DevOps 警报和报告功能。

## <a name="next-steps"></a>后续步骤
- 了解有关 [Intelligent Insights](sql-database-intelligent-insights.md) 的概念。
- 了解如何[使用 Intelligent Insights 解决 Azure SQL 数据库性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)。
- 了解如何[使用 Azure SQL Analytics 监视 Azure SQL 数据库](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql)。
- 了解如何[从 Azure 资源收集和使用日志数据](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)。



