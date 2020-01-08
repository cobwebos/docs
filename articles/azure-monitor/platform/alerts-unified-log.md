---
title: Azure Monitor 中的日志警报
description: 当满足指定的分析查询条件时，Azure 警报会触发电子邮件、通知、调用网站 URL (Webhook) 或自动化。
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: b8cae9f7c43098b713d0d5d8f74e46cb0386600c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75396484"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor 中的日志警报

本文提供日志警报的详细信息，该警报是 [Azure 警报](../../azure-monitor/platform/alerts-overview.md)中支持的警报类型之一，允许用户使用 Azure 分析平台作为警报的基础。

日志警报包含为 [Azure Monitor 日志](../../azure-monitor/learn/tutorial-viewdata.md)或 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) 创建的日志搜索规则。 若要详细了解其用法，请参阅[在 Azure 中创建日志警报](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> [Azure Monitor 日志](../../azure-monitor/learn/tutorial-viewdata.md)中的常见日志数据现在也可以在 Azure Monitor 中的指标平台上查看。 有关详细信息，请查看[日志的指标警报](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>日志搜索警报规则 - 定义和类型

日志搜索规则由 Azure 警报创建，用于定期自动运行指定的日志查询。  如果日志查询的结果符合特定条件，则会创建警报记录。 然后，该规则可以使用[操作组](../../azure-monitor/platform/action-groups.md)自动运行一个或多个操作。 可能需要具有用于创建、修改和更新日志警报的 [Azure 监视参与者](../../azure-monitor/platform/roles-permissions-security.md)角色，以及在警报规则或警报查询中分析目标的访问和查询执行权限。 如果用户创建无法访问 "警报规则" 或 "警报查询" 中的所有分析目标，则规则创建可能会失败，或者将使用部分结果执行日志警报规则。

日志搜索规则由以下详细信息定义：

- **日志查询**。  这是每次触发预警规则时都会运行的查询。  此查询返回的记录用于确定是否将触发某个警报。 分析查询可以针对特定的 Log Analytics 工作区或 Application Insights 应用，如果用户有权访问和查询所有资源，则分析查询甚至可以跨[多个 Log Analytics 和 Application Insights 资源](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)。 
    > [!IMPORTANT]
    > 仅限[使用 SCHEDULEDQUERYRULES API 配置 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) Application Insights 和日志警报的日志警报中的[跨资源查询](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)支持。

    某些分析命令和组合不适合在日志警报中使用；有关更详细的视图，请参阅 [Azure Monitor 中的日志警报查询](../../azure-monitor/platform/alerts-log-query.md)。

- **时间段**。  指定查询的时间范围。 查询仅返回在当前时间的这个范围内创建的记录。 时间段限制为日志查询提取的数据以防止滥用，并规避日志查询中使用的任何时间命令（如 ago）。 <br>*例如，如果将时间段设置为60分钟，并且在 1:15 PM 运行查询，则仅返回在 12:15 PM 和 1:15 PM 之间创建的记录。现在，如果日志查询使用时间命令（如前（7d）），则只会对 12:15 PM 和 1:15 PM 之间的数据运行日志查询，就像在过去的60分钟内存在数据一样。而不是记录查询中指定的七天的数据。*

- **频率**。  指定应运行查询的频率。 可以是介于 5 分钟到 24 小时之间的任何值。 应等于或小于时间段。  如果该值大于时间段，则会有记录缺失的风险。<br>*例如，假设时间段为30分钟，频率为60分钟。 如果查询在1:00 运行，则它将返回12:30 和 1:00 PM 之间的记录。 下一次运行查询时，该查询将在1:30 和2:00 之间返回记录2:00。 在1:00 和1:30 之间创建的任何记录永远都不会进行评估。*

- **阈值**。  对日志搜索的结果进行评估，确定是否应创建警报。  不同类型的日志搜索警报规则的阈值不同。

针对 [Azure Monitor 日志](../../azure-monitor/learn/tutorial-viewdata.md)或 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events) 的日志搜索规则可以分为两种类型。 这些类型中的每一种都在随后的相应部分进行了详细介绍。

- **[结果数](#number-of-results-alert-rules)** 。 当日志搜索返回的记录数超出指定数目时，将创建单个警报。
- **[指标度量值](#metric-measurement-alert-rules)** 。  为日志搜索结果中其值超出指定阈值的每个对象创建警报。

警报规则类型之间的差异如下所示。

- “结果数”警报规则始终创建单个警报，而“指标度量”预警规则将为超出阈值的每个对象创建一个警报。
- “结果数”预警规则会在超出阈值一次时创建一个警报。 当阈值在特定的时间间隔内超出特定的次数时，“指标度量”警报规则即可创建一个警报。

### <a name="number-of-results-alert-rules"></a>“结果数”警报规则

当搜索查询返回的记录数超出指定的阈值时，“结果数”警报规则将创建一个警报。 此类警报规则适用于处理 Windows 事件日志、Syslog、WebApp Response 和自定义日志等事件。  生成特定错误事件时，或在特定时间段内生成多个错误事件时，就可能需要创建警报。

**阈值**：“结果数”警报规则的阈值要么超出某个特定值，要么低于该值。  如果日志搜索返回的记录数与此条件匹配，则创建警报。

若要针对单个事件发出警报，请将结果数设置为大于 0 并检查自上次运行查询起创建的某事件的出现次数。 某些应用程序可能会记录不一定引发警报的偶然错误。  例如，应用程序可能会重试导致错误事件的进程，而下一次就会成功。  在这种情况下，可能不想创建警报，除非在特定时间段内生成多个事件。  

某些情况下，可能需要在没有事件的情况下创建警报。  例如，进程可能记录常规事件以指明其运行正常。  如果它不在特定时间段内记录某个事件，则应创建警报。  在这种情况下，应将阈值设置为**小于 1**。

#### <a name="example-of-number-of-records-type-log-alert"></a>记录类型日志警报数目的示例

假设你希望知道你的基于 web 的应用何时向用户返回代码为 500 的响应，即内部服务器错误。 可以创建一个警报规则，详情如下：  

- **查询：** requests | where resultCode == "500"<br>
- **时间段：** 30 分钟<br>
- **警报频率：** 五分钟<br>
- **阈值：** 大于 0<br>

然后，警报将每隔 5 分钟运行一次查询，在 30 分钟的数据中查找结果代码为 500 的任何记录。 即使找到一条这样的记录，它也会引发警报并触发所配置的操作。

### <a name="metric-measurement-alert-rules"></a>指标度量警报规则

**指标度量**警报规则为查询中的每个对象创建一个警报，其值超出了指定的阈值和指定的触发条件。 与 "**结果数**" 警报规则不同，"分析结果" 提供时序时，"**指标度量**" 警报规则将起作用。 这些规则具有下述不同于“结果数”警报规则的差异。

- **聚合函数**：确定要执行的计算以及可能要聚合的数字字段。  例如，**count()** 返回查询中的记录数，**avg(CounterValue)** 返回 CounterValue 字段在特定时间间隔内的平均值。 查询中的聚合函数必须名为：AggregatedValue 并提供数值。 

- **分组字段**：将为此字段的每个实例创建包含聚合值的记录，并可为每个实例生成警报。  例如，如果需要为每台计算机生成一个警报，则可使用“按计算机”。 如果在警报查询中指定了多个分组字段，则用户可以使用**聚合依据** (metricColumn) 参数指定要使用哪个字段对结果进行排序。

    > [!NOTE]
    > *聚合依据* (metricColumn) 选项仅适用于 Application Insights 的指标度量类型日志警报和[使用 scheduledQueryRules API 配置的 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) 的日志警报。

- **间隔**：定义一个时间间隔，在该间隔内对数据进行聚合。  例如，如果指定“五分钟”，则会在为警报指定的时间段内，为分组字段（按 5 分钟间隔进行聚合）的每个实例创建一个记录。

    > [!NOTE]
    > 必须在查询中使用 Bin 函数来指定间隔。 由于 Bin() 可能生成不相等的时间间隔，警报会在运行时使用相应的时间自动将 bin 命令转换为 bin_at 命令，以确保结果包含固定点。 日志警报的指标度量类型设计为用于最多具有三个 bin() 命令实例的查询
    
- **阈值**：“指标度量”警报规则的阈值通过一个聚合值和一个违规次数来定义。  如果日志搜索中的某数据点超出该值，则被视为违规。  如果结果中某对象的违规次数超出指定值，则会针对该对象创建警报。

*聚合依据*或 *metricColumn* 选项配置错误可能会导致警报规则误触发。 有关详细信息，请参阅[当指标度量警报规则不正确时进行故障排除](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)。

#### <a name="example-of-metric-measurement-type-log-alert"></a>指标度量类型日志警报的示例

考虑一下这样一种情形：如果任何计算机的处理器利用率在 30 分钟内超出 90% 三次，则需发出警报。  可以创建一个警报规则，详情如下：  

- **查询：** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **时间段：** 30 分钟<br>
- **警报频率：** 五分钟<br>
- **警报逻辑-条件 & 阈值：** 大于90<br>
- **组字段（聚合）：** 计算机
- **触发警报的标准：** 总违规次数大于 2 次<br>

查询将按 5 分钟的时间间隔为每台计算机创建一个平均值。  对于在前 30 分钟 内收集的数据，此查询将每隔 5 分钟运行一次。 由于所选“组字段(聚合)”为纵栏式“计算机”，因此针对“计算机”的各种值对 AggregatedValue 进行了拆分，而每个计算机的平均处理器利用率在 5 分钟的时间段内是确定的。  例如，三台计算机的查询结果示例将如下所示。


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

如果绘制查询结果，该结果将如下所示。

![示例查询结果](media/alerts-unified-log/metrics-measurement-sample-graph.png)

在此示例中，我们看到的是三台计算机中的每台计算机在 5 分钟的时间范围内计算出来的平均处理器利用率。 srv01 只有一次（在 1:25 处）超出了阈值 90。 如果进行比较，则会发现 srv02 在 1:10、1:15 和 1:25 处超出了阈值 90，而 srv03 则在 1:10、1:15、1:20 和 1:30 处超出了阈值 90。
由于已将警报配置为超出阈值两次以上才触发，因此我们看到只有 srv02 和 srv03 符合此标准。 因此，将为 srv02 和 srv03 创建单独的警报，因为它们在多个时间箱内违反了90% 阈值。 如果*触发器警报基于：* 参数已配置为*连续泄露*选项，则**仅**对 srv03 引发警报，因为它将三个连续的时间箱的阈值从1:10 改为1:20。 不会为 srv02 触发警报，因为它只在从 1:10 到 1:15 这个时间范围内连续两个时间段超出阈值。

## <a name="log-search-alert-rule---firing-and-state"></a>日志搜索警报规则 - 触发和状态

日志搜索警报规则仅适用于在查询中生成的逻辑。 警报系统没有系统状态的任何其他上下文、意图或查询隐含的根本原因。 在这种情况下，日志警报被称为不太状态。 每次运行时，条件都将计算为 "TRUE" 或 "FALSE"。  当警报条件的评估为 "TRUE" 时，无论先前是否激发，都将触发警报。    

让我们使用一个实际的示例来查看此行为。 假设有一个名为 " *Contoso-日志警报*" 的日志警报规则，该规则已配置为 "[结果数" 类型日志警报](#example-of-number-of-records-type-log-alert)中所示的示例。 条件是一个自定义警报查询，旨在查找日志中的500结果代码。 如果在日志中找到一个或多个500结果代码，则警报的条件为 true。 

在下面的每个间隔中，Azure 警报系统将评估*Contoso 日志警报*的条件。


| 时间    | 日志搜索查询返回的记录数 | 日志条件评估 | 结果 
| ------- | ----------| ----------| ------- 
| 1:05 PM | 0条记录 | 0不 > 0，因此为 FALSE |  不会触发警报。 未调用任何操作。
| 1:10 PM | 2条记录 | 2 > 0，因此为 TRUE  | 触发警报和调用的操作组。 警报状态处于活动状态。
| 下午 1:15 | 5条记录 | 5 > 0，因此为 TRUE  | 触发警报和调用的操作组。 警报状态处于活动状态。
| 1:20 PM | 0条记录 | 0不 > 0，因此为 FALSE |  不会触发警报。 未调用任何操作。 警报状态保持为活动状态。

使用上例作为示例：

在下午1:15，Azure 警报无法确定1:10 上出现的基础问题是否持久存在，如果记录是新的失败，或在1：10PM 上重复出现了较旧的故障。 用户所提供的查询可能会或不会考虑先前的记录，并且系统不知道。 Azure 警报系统会在一定的时间发出错误，并在下午1:15 重新生成警报和关联的操作。 

在下午1:20 时，如果出现包含500结果代码的零条记录，Azure 警报将无法确定在 1:10 PM 和 1:15 PM 上发现的500结果代码的原因现在已解决。 它不知道是否会再次出现500错误问题。 因此， *Contoso-日志警报*不会在 Azure 警报仪表板中更改为 "**已解决**"，并且/或者不会发出通知，表明警报已解决。 如果你知道在分析查询中嵌入逻辑的确切条件或原因，则可以根据需要[将警报标记为已关闭](alerts-managing-alert-states.md)。

## <a name="pricing-and-billing-of-log-alerts"></a>日志警报的定价和计费

适用于日志警报的定价在 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)页中有说明。 在 Azure 帐单中，日志警报表示为 `microsoft.insights/scheduledqueryrules`，并且：

- Application Insights 上的日志警报显示确切的警报名称以及资源组和警报属性
- 如果是使用 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 创建的，则 Log Analytics 上的日志警报显示确切的警报名称以及资源组和警报属性

[旧 Log Analytics API](../../azure-monitor/platform/api-alerts.md) 将警报操作和计划作为 Log Analytics 保存的搜索的一部分，而不是相应 [Azure 资源](../../azure-resource-manager/management/overview.md)的一部分。 因此，若要针对使用 Azure 门户为 Log Analytics 创建的此类旧日志警报启用计费，**而无需**[切换到新 API](../../azure-monitor/platform/alerts-log-api-switch.md)或通过[旧 Log Analytics API](../../azure-monitor/platform/api-alerts.md)隐藏的伪警报规则，则会在 `microsoft.insights/scheduledqueryrules` 上针对 Azure 上的计费创建。 在 `microsoft.insights/scheduledqueryrules` 上创建的用于计费的隐藏伪警报规则将随资源组和警报属性一起显示，格式为 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`。

> [!NOTE]
> 如果存在无效字符（例如 `<, >, %, &, \, ?, /`），则它们在隐藏的伪警报规则名称以及 Azure 帐单中会被替换为 `_`。

若要删除使用[旧 Log Analytics API](api-alerts.md) 为警报规则的计费创建的隐藏 scheduleQueryRules 资源，用户可以执行以下任一操作：

- 用户可以[在 Log Analytics 工作区上切换警报规则的 API 首选项](../../azure-monitor/platform/alerts-log-api-switch.md)，并且可以切换到 Azure 资源管理器兼容的 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 而不丢失其警报规则或监视功能。 因此，这不需要创建用于计费的隐藏伪警报规则。
- 或者，如果用户不希望切换 API 首选项，则用户将需要使用[旧 Log Analytics API](api-alerts.md)**删除**原始计划和警报操作，或者[在 Azure 门户中删除原始日志警报规则](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

此外，对于使用[旧版 LOG ANALYTICS API](api-alerts.md)为警报规则计费创建的隐藏 scheduleQueryRules 资源，任何修改操作（如 PUT）都将失败。 作为 `microsoft.insights/scheduledqueryrules` 类型，伪规则用于对使用[旧 LOG ANALYTICS API](api-alerts.md)创建的警报规则进行计费。 任何警报规则修改都应使用[旧的 LOG ANALYTICS api](api-alerts.md) （或）用户可以[切换 api 首选项，以便警报规则](../../azure-monitor/platform/alerts-log-api-switch.md)改用[scheduledQueryRules api](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 。

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 中创建日志警报](../../azure-monitor/platform/alerts-log.md)。
* 了解 [Azure 日志警报中的 Webhook](alerts-log-webhook.md)。
* 了解 [Azure 警报](../../azure-monitor/platform/alerts-overview.md)。
* 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)。
* 详细了解 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)。
