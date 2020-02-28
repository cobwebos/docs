---
title: Azure Monitor 中的日志警报进行故障排除 |Microsoft Docs
description: Azure 中日志警报规则的常见问题、错误和解决方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668496"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor 中的日志警报进行故障排除  

本文说明如何解决在 Azure Monitor 中设置日志警报时可能出现的常见问题。 它还提供有关功能或日志警报配置的常见问题的解决方案。

术语*日志警报*介绍了基于[Azure Log Analytics 工作区](../learn/tutorial-viewdata.md)中的日志查询或[Azure 应用程序 Insights](../../azure-monitor/app/analytics.md)触发的规则。 详细了解 Azure Monitor 中的[日志警报](../platform/alerts-unified-log.md)中的功能、术语和类型。

> [!NOTE]
> 本文不会考虑以下情况： Azure 门户显示触发的警报规则，并且关联的操作组不执行通知。 对于这种情况，请参阅在[Azure 门户中创建和管理操作组](../platform/action-groups.md)中的详细信息。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

下面是[Azure Monitor 中配置的日志警报规则](../platform/alerts-log.md)的状态在不显示时所显示的[*一些常见原因*](../platform/alerts-managing-alert-states.md)。

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间

日志警报会定期根据[Log Analytics](../learn/tutorial-viewdata.md)或[Application Insights](../../azure-monitor/app/analytics.md)来运行查询。 由于 Azure Monitor 处理来自世界各地不同源的数千个客户的数 tb 的数据，因此该服务容易发生变化的时间延迟。 有关详细信息，请参阅[Azure Monitor 日志中的数据引入时间](../platform/data-ingestion-time.md)。

为了缓解延迟，如果发现所需的数据尚未引入，系统会等待多次重试警报查询。 为系统设置的等待时间呈指数级递增。 日志警报仅在数据可用后触发，因此延迟可能是由于日志数据的引入缓慢导致的。

### <a name="incorrect-time-period-configured"></a>配置了错误的时间段

如[日志警报术语](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文中所述，配置中指定的时间段指定查询的时间范围。 查询仅返回在此范围内创建的记录。

时间段限制为日志查询提取的数据以防滥用，并避开日志查询中使用的任何时间命令（如**前**）。 例如，如果时间段设置为 60 分钟，且在下午 1:15 运行查询，则在中午 12:15 和下午 1:15 之间创建的记录将用于日志查询。 如果日志查询使用类似于**前（1d）** 的时间命令，则该查询仍只使用 12:15 pm 和 1:15 pm 之间的数据，因为该时间段设置为该间隔。

检查配置中的时间段是否与查询匹配。 对于之前显示的示例，如果日志查询使用带有绿色标记的**前（1d）** ，则应将时间段设置为24小时或1440分钟（以红色表示）。 此设置可确保查询按预期方式运行。

![时间段](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>设置“抑制警报”选项

如在[Azure 门户中创建日志警报规则](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)一文中所述的步骤8所述，日志警报提供了 "**禁止显示警报**" 选项，以在配置的时间内抑制触发和通知操作。 因此，你可能会认为不会触发警报。 事实上，它已激发但已被取消。  

![阻止警报](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>指标度量警报规则不正确

*指标度量日志警报*是一种具有特殊功能和受限警报查询语法的日志警报的子类型。 指标度量日志警报的规则要求查询输出为指标时间序列。 也就是说，输出是一个表，其中包含不同的、大小相同的时间段以及相应的聚合值。

您可以选择在表中另外添加一些变量，还可以**AggregatedValue**。 这些变量可用于对表进行排序。

例如，假设度量值度量日志警报的规则配置为：

- `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)` 查询  
- 6小时的时间段
- 阈值50
- 三次连续违规的警报逻辑
- 选择为 **$Table** **时聚合**

因为该命令包含**汇总 .。。由**和提供了两个变量（**时间戳**和 **$table**），系统会选择 "**聚合**" **$table** 。 系统按 **$table**字段对结果表进行排序，如以下屏幕截图所示。 然后，查看每个表类型的多个**AggregatedValue**实例（例如**availabilityResults**），以查看是否存在三个或更多的连续漏洞。

![具有多个值的指标度量查询执行](media/alert-log-troubleshoot/LogMMQuery.png)

由于 **$table**上定义了**聚合**，因此数据按 **$table**列排序（以红色表示）。 然后 **，对字段进行**分组和查找类型。

例如，对于 **$table**， **availabilityResults**的值将被视为一个绘图/实体（用橙色表示）。 在此值绘图/实体中，警报服务将检查三个连续违规（以绿色表示）。 此违规会触发表值**availabilityResults**的警报。

同样，如果对任何其他 **$table**值发生了三次连续违规，则会为同一事情触发另一个警报通知。 警报服务按时间对一个绘图/实体（以橙色表示）中的值进行自动排序。

现在，假设度量度量日志警报的规则已被修改，查询已 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`。 配置的其余部分保持不变，包括三次连续泄露的警报逻辑。 默认情况下，在此示例中，"**聚合依据**" 选项为**timestamp** 。 在 "汇总 ..." 查询中仅提供一个值**by** （即**timestamp**）。 与前面的示例一样，执行结束时的输出将如下所示。

   ![用单值度量度量值查询执行](media/alert-log-troubleshoot/LogMMtimestamp.png)

由于在**时间戳**上定义了**聚合**，因此数据在**时间戳**列上排序（以红色表示）。 然后按**时间戳**分组。 例如，`2018-10-17T06:00:00Z` 的值将被视为一个绘图/实体（用橙色表示）。 在此值绘图/实体中，警报服务将找不到任何连续违规（因为每个**时间戳**值只有一个条目）。 因此永远不会触发警报。 在这种情况下，用户必须：

- 添加一个虚变量或现有变量（如 **$table**），以使用 "**聚合依据**" 字段正确排序。
- 重新配置警报规则，以改用**违反全部**的警报逻辑。

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

当你在[Azure 警报](../platform/alerts-managing-alert-states.md)中查看时，可能会意外触发[Azure Monitor 中配置的日志警报规则](../platform/alerts-log.md)。 以下各节描述了一些常见的原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

Log Analytics 和 Application Insights 可能会引入延迟和处理。 当你运行日志警报查询时，可能会发现没有数据可用或只有部分数据可用。 有关详细信息，请参阅[中的日志数据引入时间 Azure Monitor](../platform/data-ingestion-time.md)。

如果在执行警报时日志中没有数据或部分数据，则可能会发生 misfiring，具体取决于如何配置警报规则。 在这种情况下，我们建议更改警报查询或配置。

例如，如果将分析查询的结果数小于5时要触发日志警报规则，则当没有数据（零记录）或部分结果（一条记录）时，将触发该警报。 但在数据引入延迟后，包含完整数据的同一查询可能会产生10条记录。

### <a name="alert-query-output-is-misunderstood"></a>警报查询输出的误解

你在分析查询中提供日志警报的逻辑。 分析查询可以使用各种大数据和数学函数。 警报服务按指定时间段内使用数据指定的时间间隔来运行查询。 警报服务根据警报类型对查询进行细微的更改。 可以在 "**配置信号逻辑**" 屏幕上的 "**要执行的查询**" 部分查看此更改：

![要执行的查询](media/alert-log-troubleshoot/LogAlertPreview.png)

"**要执行的查询**" 框是日志警报服务的运行情况。 如果你想要了解在创建警报之前警报查询输出可能是什么，则可以通过[分析门户](../log-query/portals.md)或[分析 API](https://docs.microsoft.com/rest/api/loganalytics/)运行所述的查询和 timespan。

## <a name="log-alert-was-disabled"></a>已禁用日志警报

以下部分列出了 Azure Monitor 可能禁用[日志警报规则](../platform/alerts-log.md)的一些原因。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>创建警报的资源不再存在

Azure Monitor 在特定资源（如 Azure Log Analytics 工作区、Azure 应用程序 Insights 应用和 Azure 资源）中创建的日志警报规则。 然后，日志警报服务将为指定的目标运行规则中提供的分析查询。 但在规则创建后，用户通常会继续从 Azure 中删除，或在 Azure 内部移动-日志警报规则的目标。 由于警报规则的目标不再有效，因此执行规则失败。

在这种情况下，Azure Monitor 会禁用日志警报，并确保在规则不能连续运行（如一周）的情况下，不需要付费。 通过[Azure 活动日志](../../azure-resource-manager/management/view-activity-logs.md)Azure Monitor 禁用日志警报时，可以找到准确的时间。 在 Azure 活动日志中，当 Azure Monitor 禁用日志警报规则时，会添加一个事件。

Azure 活动日志中的以下示例事件适用于由于连续失败而禁用的警报规则。

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>日志警报中使用的查询无效

在 Azure Monitor 中创建的每个日志警报规则都必须指定警报服务将定期运行的分析查询。 在创建或更新规则时，分析查询可能有正确的语法。 但有时，在一段时间内，日志警报规则中提供的查询会导致语法问题并导致规则执行失败。 日志警报规则中提供的分析查询可能导致错误的一些常见原因是：

- 该查询将写入到[跨多个资源运行](../log-query/cross-workspace-query.md)。 和一个或多个指定的资源不再存在。
- 配置的[度量值类型日志警报](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)中的警报查询不符合语法规范
- 没有流向分析平台的数据流。 [查询执行](https://dev.loganalytics.io/documentation/Using-the-API/Errors)会导致错误，因为提供的查询没有数据。
- [查询语言](https://docs.microsoft.com/azure/kusto/query/)中的更改包括命令和函数的修订格式。 因此，先前在警报规则中提供的查询不再有效。

[Azure 顾问](../../advisor/advisor-overview.md)会警告你这一行为。 在 Azure 顾问上，针对特定的日志警报规则添加了一个建议，该规则位于具有中等影响的高可用性类别下，以及有关 "修复日志警报规则以确保监视" 的说明。 如果在 Azure 顾问提供了七天的建议后，不会更正日志警报规则中的警报查询，Azure Monitor 将禁用日志警报，并确保在规则不能连续运行的情况下不会产生不必要的计费（如一周）。

可以通过在[Azure 活动日志](../../azure-resource-manager/management/view-activity-logs.md)中查找事件来找到 Azure Monitor 禁用日志警报规则的确切时间。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 中的日志警报](../platform/alerts-unified-log.md)。
- 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 详细了解[日志查询](../log-query/log-query-overview.md)。
