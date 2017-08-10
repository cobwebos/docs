---
title: "了解 Azure Log Analytics 中的警报 | Microsoft Docs"
description: "Log Analytics 中的警报标识 OMS 存储库中的重要信息，还可主动通知你存在问题或调用操作以尝试更正问题。  本文介绍不同类型的警报规则及其定义方式。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 951e76d3fb18d9e433b148e82d4d6cee9417ce6d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---
# <a name="understanding-alerts-in-log-analytics"></a>了解 Log Analytics 中的警报

Log Analytics 中的警报标识 Log Analytics 存储库中的重要信息。  本文详细说明了 Log Analytics 中警报规则的作用原理，并介绍了不同类型警报规则的差异。

有关创建预警规则的过程，请参阅以下文章：

- 使用 [Azure 门户](log-analytics-alerts-creating.md)创建警报规则
- 使用[资源管理器模板](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)创建警报规则
- 使用 [REST API](log-analytics-api-alerts.md) 创建警报规则


## <a name="alert-rules"></a>警报规则

警报通过警报规则创建，警报规则定期自动运行日志搜索。  如果日志搜索的结果符合特定条件，则会创建警报记录。  然后，该规则可自动运行一个或多个操作以主动通知你存在警报或调用另一个进程。  不同类型的警报规则使用不同的逻辑来执行此分析。

![Log Analytics 警报](media/log-analytics-alerts/overview.png)

预警规则通过以下详细信息定义：

- **日志搜索**。  这是每次触发预警规则时都会运行的查询。  此查询返回的记录用于确定是否创建警报。
- **时间窗口**。  指定查询的时间范围。  查询仅返回在当前时间的这个范围内创建的记录。  这可以是介于 5 分钟到 24 小时之间的任何值。 例如，如果时间范围设置为 60 分钟，且在下午 1:15 运行查询，则仅返回中午 12:15 和下午 1:15 之间创建的记录。
- **频率**。  指定应运行查询的频率。 可以是介于 5 分钟到 24 小时之间的任何值。 应等于或小于时间范围。  如果该值大于时间范围，则会有记录缺失的风险。<br>例如，假设时间范围为 30 分钟，频率为 60 分钟。  如果查询在下午 1:00 运行，则会返回中午 12:30 和下午 1:00 之间的记录。  下次运行查询的时间是下午 2:00，会返回下午 1:30 到 2:00 之间的记录。  在下午 1:00 和 1:30 之间创建的任何记录不会获得评估。
- **阈值**。  对日志搜索的结果进行评估，确定是否应创建警报。  不同类型的警报规则的阈值不同。

Log Analytics 中的预警规则均为以下两种类型之一。  这些类型中的每一种都在随后的相应部分进行了详细介绍。

- **[结果数](#number-of-results-alert-rules)**。 当日志搜索返回的记录数超出指定数目时，将创建单个警报。
- **[指标度量](#metric-measurement-alert-rules)**。  为日志搜索结果中其值超出指定阈值的每个对象创建警报。

警报规则类型之间的差异如下所示。

- “结果数”预警规则将始终创建单个警报，而“指标度量”预警规则将为超出阈值的每个对象创建一个警报。
- “结果数”预警规则会在超出阈值一次时创建一个警报。 当阈值在特定的时间间隔内超出特定的次数时，“指标度量”警报规则即可创建一个警报。

## <a name="number-of-results-alert-rules"></a>“结果数”警报规则
当搜索查询返回的记录数超出指定的阈值时，“结果数”警报规则将创建一个警报。

### <a name="threshold"></a>阈值
“结果数”警报规则的阈值要么超出某个特定值，要么低于该值。  如果日志搜索返回的记录数与此条件匹配，则创建警报。

### <a name="scenarios"></a>方案

#### <a name="events"></a>事件
此类预警规则适用于处理 Windows 事件日志、Syslog 和自定义日志等事件。  生成特定错误事件时，或在特定时间范围内生成多个错误事件时，就可能需要创建警报。

要对单个事件发出警报，请将结果数设置为大于 0，并将频率和时间范围都设置为 5 分钟。  这样，每 5 分钟运行一次查询，并检查自上次运行查询起创建的某事件的出现次数。  较长的频率可能会延长收集事件和创建警报之间的时间。

某些应用程序可能会记录不一定引发警报的偶然错误。  例如，应用程序可能会重试导致错误事件的进程，而下一次就会成功。  在这种情况下，可能不想创建警报，除非在特定时间范围内创建多个事件。  

某些情况下，可能需要在没有事件的情况下创建警报。  例如，进程可能记录常规事件以指明其运行正常。  如果它不在特定时间范围内记录某个事件，则应创建警报。  在这种情况下，应将阈值设置为“小于 1”。

#### <a name="performance-alerts"></a>性能警报
与事件类似，[性能数据](log-analytics-data-sources-performance-counters.md)存储为 OMS 存储库中的记录。  如果想要在性能计数器超过特定阈值时发出警报，则该阈值应包括在查询中。

例如，若希望处理器超过 90% 时发出警报，可以使用如下查询，并将警报规则的阈值设置为“大于 0”。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

若希望处理器在特定时间范围平均超过 90% 时发出警报，则可使用如下所示的 [measure 命令](log-analytics-search-reference.md#commands)进行查询，并将警报规则的阈值设置为“大于 0”。

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

>[!NOTE]
> 如果工作区已升级到[新的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则上述查询将更改为如下所示：`Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90`
> `Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90`


## <a name="metric-measurement-alert-rules"></a>指标度量警报规则

>[!NOTE]
> “指标度量”警报规则目前为公共预览版。

“指标度量”警报规则为查询中其值超出指定阈值的每个对象创建一个警报。  这些规则具有下述不同于“结果数”警报规则的差异。

#### <a name="log-search"></a>日志搜索
虽然可以对“结果数”警报规则使用任何查询，但对适用于“指标度量”警报规则的查询存在特定的要求。  该查询必须包含[指标命令](log-analytics-search-reference.md#commands)，以便将特定字段的结果组合在一起。 该命令必须包含以下元素。

- **聚合函数**。  确定要执行的计算以及可能要聚合的数字字段。  例如，**count()** 将返回查询中的记录数，**avg(CounterValue)** 将返回 CounterValue 字段在特定时间间隔内的平均值。
- **组字段**。  将为此字段的每个实例创建包含聚合值的记录，并可为每个实例生成警报。  例如，如果需要为每台计算机生成一个警报，则可使用“按计算机”。   
- **时间间隔**。  定义一个时间间隔，在该间隔内对数据进行聚合。  例如，如果指定“5 分钟”，则会在为警报指定的时间范围内，为组字段（按 5 分钟间隔进行聚合）的每个实例创建一个记录。

#### <a name="threshold"></a>阈值
“指标度量”警报规则的阈值通过一个聚合值和一个违规次数来定义。  如果日志搜索中的某数据点超出该值，则被视为违规。  如果结果中某对象的违规次数超出指定值，则会针对该对象创建警报。

#### <a name="example"></a>示例
考虑一下这样一种情形：如果任何计算机的处理器利用率在 30 分钟内超出 90% 三次，则需发出警报。  可以创建一个警报规则，详情如下。  

**查询：**Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
时间范围：30 分钟<br>
警报频率：5 分钟<br>
聚合值：大于 90<br>
触发警报的标准：总违规次数大于 5 次<br>

查询将按 5 分钟的时间间隔为每台计算机创建一个平均值。  对于在前 30 分钟 内收集的数据，此查询将每隔 5 分钟运行一次。  下面显示的示例数据是针对三台计算机的。

![示例查询结果](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

在此示例中，将为 srv02 和 srv03 创建单独的警报，因为二者都在指定的时间范围内超出 90% 的阈值 3 次。  如果将“触发警报的标准:”更改为“连续”，则只会为 srv03 创建警报，因为连续 3 个示例它都超出了阈值。

## <a name="alert-records"></a>警报记录
Log Analytics 中警报规则创建的警报记录的“**类型**”为“**警报**”，“**SourceSystem**”为“**OMS**”。  它们具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*OMS* |
| *Object*  | [“指标度量”警报](#metric-measurement-alert-rules)将有一个针对组字段的属性。  例如，如果日志搜索按“计算机”进行组合，则警报记录会有一个“计算机”字段，其值为计算机的名称。
| AlertName |警报的名称。 |
| AlertSeverity |警报的严重级别。 |
| LinkToSearchResults |指向 Log Analytics 日志搜索的链接，该搜索会从创建警报的查询返回记录。 |
| 查询 |运行的查询的文本。 |
| QueryExecutionEndTime |查询的时间范围结束点。 |
| QueryExecutionStartTime |查询的时间范围开始点。 |
| ThresholdOperator | 警报规则所使用的运算符。 |
| ThresholdValue | 警报规则所使用的值。 |
| TimeGenerated |警报的创建日期和时间。 |

还有其他类型的由[警报管理解决方案](log-analytics-solution-alert-management.md)和 [Power BI 导出内容](log-analytics-powerbi.md)创建的警报记录。  它们的“**类型**”都为“**警报**”，但其“**SourceSystem**”不同。


## <a name="next-steps"></a>后续步骤
* 安装[警报管理解决方案](log-analytics-solution-alert-management.md)，用于分析 Log Analytics 中创建的警报以及从 System Center Operations Manager 收集的警报。
* 深入了解有关可生成警报的[日志搜索](log-analytics-log-searches.md)。
* 完成[配置 Webook](log-analytics-alerts-webhooks.md) 和警报规则的演练步骤。  
* 了解如何[在 Azure 自动化中编写 Runbook](https://azure.microsoft.com/documentation/services/automation)，以修正警报标识的问题。

