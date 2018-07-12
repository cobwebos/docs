---
title: Azure Monitor 中的日志警报
description: 当满足指定的分析查询条件时，Azure 警报会触发电子邮件、通知、调用网站 URL (Webhook) 或自动化。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f36f05789424cfd3213525dd501333f852a0d9c2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971714"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Azure Monitor - 警报中的日志警报 
本文提供日志警报的详细信息，该警报是新版 [Azure 警报](monitoring-overview-unified-alerts.md)中支持的警报类型之一，允许用户使用 Azure 的分析平台作为警报的基础。 有关使用日志的指标警报的详细信息，请参阅[准实时指标警报](monitoring-near-real-time-metric-alerts.md)


日志警报包含为 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 创建的日志搜索规则


## <a name="log-search-alert-rule---definition-and-types"></a>日志搜索警报规则 - 定义和类型

日志搜索规则由 Azure 警报创建，用于定期自动运行指定的日志查询。  如果日志查询的结果符合特定条件，则会创建警报记录。 然后，该规则可以使用[操作组](monitoring-action-groups.md)自动运行一个或多个操作。 

日志搜索规则由以下详细信息定义：
- **日志查询**。  这是每次触发预警规则时都会运行的查询。  此查询返回的记录用于确定是否创建警报。 *Azure Application Insights* 查询还可以包含[跨应用程序调用](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)，前提是用户具有对外部应用程序的访问权限。 

    > [!IMPORTANT]
    > 对 [Application Insights 的跨应用程序查询](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery)的支持处于预览状态 - 该功能限制为用于 2 个或更多应用并且用户体验可能会发生更改。 目前，Azure 警报**不支持**使用 Log Analytics 的[跨工作区查询](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery)和[跨资源查询](../log-analytics/log-analytics-cross-workspace-search.md)。

- **时间段**。  指定查询的时间范围。 查询仅返回在当前时间的这个范围内创建的记录。 时间段限制为日志查询提取的数据以防止滥用，并规避日志查询中使用的任何时间命令（如 ago）。 <br>*例如，如果时间段设置为 60 分钟，且在下午 1:15 运行查询，则执行日志查询时仅返回中午 12:15 和下午 1:15 之间创建的记录。现在，如果日志查询使用时间命令（如 ago (7d)），则日志查询将仅针对中午 12:15 和下午 1:15 之间的数据运行 - 就像仅存在过去 60 分钟的数据一样。而不是按在日志查询中所指定针对七天的数据。*
- **频率**。  指定应运行查询的频率。 可以是介于 5 分钟到 24 小时之间的任何值。 应等于或小于时间段。  如果该值大于时间段，则会有记录缺失的风险。<br>*例如，假设时间段为 30 分钟，频率为 60 分钟。如果查询在下午 1:00 运行，则会返回中午 12:30 和下午 1:00 之间的记录。下次运行查询的时间是下午 2:00，会返回下午 1:30 到 2:00 之间的记录。在下午 1:00 和 1:30 之间创建的任何记录不会获得评估。*
- **阈值**。  对日志搜索的结果进行评估，确定是否应创建警报。  不同类型的日志搜索警报规则的阈值不同。

针对 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) 的日志搜索规则可以分为两种类型。 这些类型中的每一种都在随后的相应部分进行了详细介绍。

- **[结果数](#number-of-results-alert-rules)**。 当日志搜索返回的记录数超出指定数目时，将创建单个警报。
- **[指标度量值](#metric-measurement-alert-rules)**。  为日志搜索结果中其值超出指定阈值的每个对象创建警报。

警报规则类型之间的差异如下所示。

- “结果数”警报规则始终创建单个警报，而“指标度量”预警规则将为超出阈值的每个对象创建一个警报。
- “结果数”预警规则会在超出阈值一次时创建一个警报。 当阈值在特定的时间间隔内超出特定的次数时，“指标度量”警报规则即可创建一个警报。

### <a name="number-of-results-alert-rules"></a>“结果数”警报规则
当搜索查询返回的记录数超出指定的阈值时，“结果数”警报规则将创建一个警报。 此类预警规则适用于处理 Windows 事件日志、Syslog、WebApp Response 和自定义日志等事件。  生成特定错误事件时，或在特定时间段内生成多个错误事件时，就可能需要创建警报。

**阈值**：“结果数”警报规则的阈值要么超出某个特定值，要么低于该值。  如果日志搜索返回的记录数与此条件匹配，则创建警报。

若要针对单个事件发出警报，请将结果数设置为大于 0 并检查自上次运行查询起创建的某事件的出现次数。 某些应用程序可能会记录不一定引发警报的偶然错误。  例如，应用程序可能会重试导致错误事件的进程，而下一次就会成功。  在这种情况下，可能不想创建警报，除非在特定时间段内生成多个事件。  

某些情况下，可能需要在没有事件的情况下创建警报。  例如，进程可能记录常规事件以指明其运行正常。  如果它不在特定时间段内记录某个事件，则应创建警报。  在这种情况下，应将阈值设置为**小于 1**。

#### <a name="example"></a>示例
假设你希望知道你的基于 web 的应用何时向用户返回代码为 500 的响应，即内部服务器错误。 可以创建一个警报规则，详情如下：  
- **查询：** requests | where resultCode == "500"<br>
- **时间段：** 30 分钟<br>
- **警报频率：** 五分钟<br>
- **阈值：** 大于 0<br>

然后，警报将每隔 5 分钟运行一次查询，在 30 分钟的数据中查找结果代码为 500 的任何记录。 即使找到一条这样的记录，它也会引发警报并触发所配置的操作。

### <a name="metric-measurement-alert-rules"></a>指标度量警报规则

- “指标度量”警报规则为查询中其值超出指定阈值的每个对象创建一个警报。  这些规则具有下述不同于“结果数”警报规则的差异。
- **聚合函数**：确定要执行的计算以及可能要聚合的数字字段。  例如，**count()** 返回查询中的记录数，**avg(CounterValue)** 返回 CounterValue 字段在特定时间间隔内的平均值。 查询中的聚合函数必须名为：AggregatedValue 并提供数值。 
- **分组字段**：将为此字段的每个实例创建包含聚合值的记录，并可为每个实例生成警报。  例如，如果需要为每台计算机生成一个警报，则可使用“按计算机”。 

    > [!NOTE]
    > 对于基于 Application Insights 的指标度量警报规则，可以指定对数据分组的字段。 若要执行此操作，请使用规则定义中的“聚合基于”选项。   
    
- **间隔**：定义一个时间间隔，在该间隔内对数据进行聚合。  例如，如果指定“五分钟”，则会在为警报指定的时间段内，为分组字段（按 5 分钟间隔进行聚合）的每个实例创建一个记录。

    > [!NOTE]
    > 必须在查询中使用 Bin 函数来指定间隔。 由于 Bin() 可能生成不相等的时间间隔，警报会在运行时使用相应的时间自动将 bin 命令转换为 bin_at 命令，以确保结果包含固定点
    
- **阈值**：“指标度量”警报规则的阈值通过一个聚合值和一个违规次数来定义。  如果日志搜索中的某数据点超出该值，则被视为违规。  如果结果中某对象的违规次数超出指定值，则会针对该对象创建警报。

#### <a name="example"></a>示例
考虑一下这样一种情形：如果任何计算机的处理器利用率在 30 分钟内超出 90% 三次，则需发出警报。  可以创建一个警报规则，详情如下：  

- **查询：** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **时间段：** 30 分钟<br>
- **警报频率：** 五分钟<br>
- **聚合值：** 大于 90<br>
- **触发警报的标准：** 总违规次数大于 2 次<br>

查询将按 5 分钟的时间间隔为每台计算机创建一个平均值。  对于在前 30 分钟 内收集的数据，此查询将每隔 5 分钟运行一次。  下面显示的示例数据是针对三台计算机的。

![示例查询结果](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

在此示例中，将为 srv02 和 srv03 创建单独的警报，因为二者都在指定的时间段内超出 90% 的阈值 3 次。  如果将“触发警报的标准:”更改为“连续”，则只会为 srv03 创建警报，因为对于连续三个示例，它都超出了阈值。


## <a name="log-search-alert-rule---creation-and-modification"></a>日志搜索警报规则 - 创建和修改

可以通过以下方式查看、创建或修改日志警报及其组成的日志搜索警报规则：
- Azure 门户
- REST API（包括通过 PowerShell）
- Azure 资源管理器模板

### <a name="azure-portal"></a>Azure 门户
由于引入了[新的 Azure 警报](monitoring-overview-unified-alerts.md)，现在用户可以从单一位置以类似的步骤管理 Azure 门户中所有类型的警报。 详细了解如何[使用新的 Azure 警报](monitor-alerts-unified-usage.md)。

另外，用户可以在 Azure 中精选的 Analytics 平台中完善其查询，然后通过保存查询将它们导入以在警报中使用。 要遵循的步骤如下：
- *对于 Application Insights*：转到 Analytics 门户，验证查询及其结果。 然后，以唯一名称将其保存到“共享查询”。
- *对于 Log Analytics*：转到“日志搜索”，验证查询及其结果。 然后，以唯一名称将其保存到任何类别。

[在警报中创建日志警报](monitor-alerts-unified-usage.md)时，可以看到保存的查询作为信号类型“日志(已保存的查询)”列出；如以下示例中所示：![导入到警报的已保存查询](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> 使用“日志(已保存的查询)”会导致导入到警报中。 因此，之后在 Analytics 中所做的任何更改都不会反映在日志搜索警报规则中，反之亦然。

### <a name="rest-apis"></a>REST API
为日志警报提供的 API 为 RESTful API，可通过 Azure 资源管理器 REST API 来访问。 因此，可以通过 PowerShell 以及利用 API 的其他选项进行访问。

有关使用 REST API 的详细信息和示例，请参阅：
- [Log Analytics 警报 REST API](../log-analytics/log-analytics-api-alerts.md) - 为 Azure Log Analytics 创建和管理日志搜索警报规则
- [Azure Monitor 计划查询规则 REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) - 为 Azure Application Insights 创建和管理日志搜索警报规则

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
用户还可以利用由 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)提供的灵活性来创建和更新资源 - 用于创建或更新日志警报。

有关使用资源管理器模板的详细信息和示例，请参阅：
- 用于基于 Azure Log Analytics 的日志警报的[已保存搜索和警报管理](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics)
- 用于基于 Azure Application Insights 的日志警报的[计划查询规则](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights)
 

## <a name="next-steps"></a>后续步骤
* 了解 [Azure 中的日志警报](monitor-alerts-unified-log-webhook.md)。
* 了解新的 [Azure 警报](monitoring-overview-unified-alerts.md)。
* 详细了解 [Application Insights](../application-insights/app-insights-analytics.md)。
* 详细了解 [Log Analytics](../log-analytics/log-analytics-overview.md)。    
