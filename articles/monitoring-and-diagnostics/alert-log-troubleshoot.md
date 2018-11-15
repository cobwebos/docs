---
title: 在 Azure Monitor 中排查日志警报问题
description: Azure 中日志警报规则的常见问题、错误和解法方法。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5572c80879584e7f6df650263ae455a134ee4088
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283591"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>在 Azure Monitor 中排查日志警报问题  

## <a name="overview"></a>概述
本文介绍如何处理在 Azure Monitor 中设置日志警报时出现的常见问题， 并提供有关日志警报功能或配置的常见问题的解决方法。 术语**日志警报**用来描述警报，其中的信号是基于 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-analytics.md) 的自定义查询。 从[日志警报 - 概述](monitor-alerts-unified-log.md)中详细了解功能、术语和类型。

> [!NOTE]
> 本文不考虑警报规则在 Azure 门户中显示为已触发以及通过关联的操作组发出通知的情况。 对于这种情况，请参阅有关[操作组](monitoring-action-groups.md)的文章中的详细信息。


## <a name="log-alert-didnt-fire"></a>日志警报未激发

下面详细描述了在 [Azure 警报](monitoring-alerts-managing-alert-states.md)中查看 [Azure Monitor 中配置的日志警报规则](alert-log.md)时，该规则未按预期激发的一些常见原因。 

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间
日志警报的工作原理是基于 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 或 [Application Insights](../application-insights/app-insights-analytics.md) 定期运行客户提供的查询。 这两个服务都拥有 Analytics 的强大功能，可处理巨量的日志数据，并提供基本相同的功能。 由于 Log Analytics 服务需要处理来自数千个客户以及全球各种源的若干 TB 的数据，因此，该服务很容易发生时间延迟。 有关详细信息，请参阅 [Log Analytics 中的数据引入时间](../log-analytics/log-analytics-data-ingestion-time.md)。

为了克服 Log Analytics 或 Application Insights 日志中可能发生的数据引入延迟，当发现数据在警报时间段内尚未引入时，日志警报会等待一段时间后，然后重试。 为日志警报设置的等待时间呈指数级增大，目的是确保我们等待必要的时间，让 Log analytics 引入数据。 因此，如果日志警报规则查询的日志受引入延迟的影响，则只有在 Log Analytics 后期引入中提供了数据，并且由于日志警报服务在此期间多次重试而产生了指数时隙之后，才会触发日志警报。

### <a name="incorrect-time-period-configured"></a>配置了错误的时间段
如[日志警报术语](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types)文章中所述，配置中规定的时间段指定查询的时间范围。 查询仅返回在此时间范围内创建的记录。 时间段限制为日志查询提取的数据以防止滥用，并规避日志查询中使用的任何时间命令（如 ago）。 
*例如，如果时间段设置为 60 分钟，且在下午 1:15 运行查询，则执行日志查询时仅返回中午 12:15 和下午 1:15 之间创建的记录。现在，如果日志查询使用时间命令（如 ago (1d)），则日志查询将仅针对中午 12:15 和下午 1:15 之间的数据运行 - 就像仅存在过去 60 分钟的数据一样。而不是按在日志查询中所指定针对七天的数据。*

根据查询逻辑，检查是否在配置中提供了适当的时间段。 对于前面所述的示例，如果日志查询使用 ago (1d)（如绿色标记所示），则时间段应设置为 24 小时或 1440 分钟（如红色标记所示），以确保提供的查询根据设想正确执行。
    ![时间段](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>设置“抑制警报”选项
根据[在 Azure 门户中创建日志警报规则](alert-log.md#managing-log-alerts-from-the-azure-portal)文章的步骤 8 中所述，日志警报提供一个选项用于配置警报规则的自动抑制，并防止在规定的时间发出通知/触发该规则。 “抑制警报”选项可让日志警报执行，同时可防止在“抑制警报”选项中指定的时间触发操作组，因此，用户可能觉得该警报未激发，但实际上它只是按配置抑制了。
    ![抑制警报](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>指标度量警报规则不正确
日志警报规则的指标度量类型是日志警报的子类型，它具有特殊的功能，但又对警报查询语法采用限制。 指标度量日志警报规则需要使用警报查询的输出来提供指标时序 - 指标时序是包含等量大小的不同时间段以及相应 AggregatedValue 计算值的表。 此外，用户可以选择在该表中包含其他变量，以及 Computer、Node 等 AggregatedValue。 在表中使用的数据可排序。

例如，假设指标度量日志警报规则已配置为：
- 查询为：`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 时间段为 6 小时
- 阈值为 50
- 警报逻辑为三次连续违规
- 选择 $table 作为聚合依据

由于在命令中，我们使用了“summarize … by”， 并提供了两个变量 timestamp 和 $table；警报服务将选择“$table”作为“聚合依据”，- 基本上可按 $table 字段将结果表排序，如下所示。然后查看每个表类型（例如 availabilityResults）的多个 AggregatedValue，以确定是否发生了 3 次或更多的连续违规。

   ![包含多个值的指标度量查询执行](./media/monitor-alerts-unified/LogMMQuery.png)

由于“聚合依据”为 $table – 数据已按 $table 列排序（如红框所示），我们进行分组并查看“聚合依据”字段（即 $table）的类型 - 例如：availabilityResults 的值将视为一个绘图/实体（在橙色框中突出显示）。 在此绘图/实体值中 – 警报服务将检查对表值“availabilityResults”触发的警报的三次连续违规（如绿框中所示）。 同样，如果其他任何 $table 值发生三次连续违规 - 同时会触发另一条警报通知；警报服务将自动按时间排序一个绘图/实体中的值（如橙色框中所示）。

现在，假设指标度量日志警报规则已修改，查询为 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`，但剩余的配置与前面相同，包括警报逻辑同样为三次连续违规。 在这种情况下，“聚合依据”选项默认为 timestamp。 由于在查询中只为 summarize…by 提供了一个值（即 timestamp），与前面的示例类似，在执行结束时，输出将如下所示。 

   ![包含单个值的指标度量查询执行](./media/monitor-alerts-unified/LogMMtimestamp.png)

由于“聚合依据”为 timestamp – 数据已按 timestamp 列排序（如红框中所示），我们按 timestamp 进行分组 – 例如：`2018-10-17T06:00:00Z` 的值将视为一个绘图/实体（如橙色框中突出显示）。 在此绘图/实体值中 – 警报服务找不到发生的连续违规（因为每个 timestamp 值只包含一个条目），因此永远不会触发警报。 因此，在这种情况下，用户必须 -
- 添加一个虚拟变量或现有变量（例如 $table），以使用配置的“聚合依据”字段正确执行排序
- （或者）相应地将警报规则重新配置为使用基于违规总数的警报逻辑
 
## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报
下面详细描述了在 [Azure 警报](monitoring-alerts-managing-alert-states.md)中查看 [Azure Monitor 中配置的日志警报规则](alert-log.md)时，该规则意外激发的一些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报
为 Log Analytics 和 Application Insights 提供功能支持的 Analytics 可能会发生引入和处理延迟；因此，在运行提供的日志警报查询时，可能存在没有任何数据可用或者只有部分数据可用的情况。 有关详细信息，请参阅 [Log Analytics 中的数据引入时间](../log-analytics/log-analytics-data-ingestion-time.md)。

根据警报规则的配置方式，在执行警报时如果日志中没有数据或者只有部分数据，则可能会错误地激发警报。 在这种情况下，建议更改警报查询或配置。 例如，如果日志警报规则配置为当分析查询的结果数小于 5 时触发，则当没有任何数据（零个记录）或只有部分结果（一条记录）时，警报规则将会触发。发生引入延迟后，在 Analytics 中运行同一查询时，包含完整数据的查询可能会提供 10 条记录作为结果。

### <a name="alert-query-output-misunderstood"></a>警报查询输出令人误解
对于日志警报，警报逻辑由用户通过分析查询提供。 提供的分析查询可以采用各种大数据和数学函数来创建特定的构造。 警报服务将会根据指定的间隔和指定的时间段，针对数据执行客户提供的查询；警报服务根据所选的警报类型对提供的查询进行细微的更改，在“配置信号逻辑”屏幕中的“要执行的查询”部分也可以看到这种更改，如下图所示：![要执行的查询](./media/monitor-alerts-unified/LogAlertPreview.png)
 
“要执行的查询”部分显示的是要运行哪个日志警报服务；如果用户想要在创建警报之前了解警报查询输出的内容，可以通过 [Analytics 门户](../log-analytics/log-analytics-log-search-portals.md)或 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/) 运行指定的查询及时间跨度。
 
## <a name="next-steps"></a>后续步骤

* 了解 [Azure 警报中的日志警报](monitor-alerts-unified-log.md)
* 详细了解 [Application Insights](../application-insights/app-insights-analytics.md)
* 详细了解 [Log Analytics](../log-analytics/log-analytics-queries.md)。 