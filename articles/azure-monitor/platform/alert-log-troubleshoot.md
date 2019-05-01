---
title: 排查 Azure Monitor 中的日志警报 |Microsoft Docs
description: 常见的问题、 错误和日志警报规则在 Azure 中的解决方法。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683388"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>排查 Azure Monitor 中的日志警报  

本文介绍如何解决当你要设置 Azure Monitor 中的日志警报可能会发生的常见问题。 它还提供功能或配置的日志警报的常见问题的解决方案。 

术语*日志警报*介绍了激发，基于一个日志查询中的警报[Azure Log Analytics 工作区](../learn/tutorial-viewdata.md)中或在[Azure Application Insights](../../azure-monitor/app/analytics.md)。 了解更多有关功能、 术语和中的类型[Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)。

> [!NOTE]
> 本文不会考虑 Azure 门户显示警报规则触发的和通知不由关联的操作组。 这种情况下，请参阅中的详细信息[创建和管理在 Azure 门户中的操作组](../platform/action-groups.md)。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

以下是一些常见原因为什么一个已配置的状态[Azure Monitor 中的日志警报规则](../platform/alerts-log.md)不会显示[作为*触发*时预期](../platform/alerts-managing-alert-states.md)。 

### <a name="data-ingestion-time-for-logs"></a>日志数据引入时间

日志警报会定期运行查询基于[Log Analytics](../learn/tutorial-viewdata.md)或[Application Insights](../../azure-monitor/app/analytics.md)。 由于 Azure 监视器在世界各地处理数万亿字节的数千个客户从各种源中的数据，该服务是易受不同的时间延迟。 有关详细信息，请参阅[Azure Monitor 日志中的数据引入时间](../platform/data-ingestion-time.md)。

若要缓解延迟，系统等待，并会多次重试警报的查询，如果找到未尚未引入所需的数据。 为系统设置的等待时间呈指数级递增。 仅后数据仍然可用，因此延迟可能会导致日志数据引入速度缓慢，则会触发日志警报。 

### <a name="incorrect-time-period-configured"></a>配置了错误的时间段

如在本文中所述[日志警报的术语](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)，在配置中所述的时间段指定时间范围，用于查询。 查询将返回该范围内创建的记录。 

时间段内限制日志查询以防止滥用，提取的数据，并绕过任何时间命令 (如**前**) 的日志查询中使用。 例如，如果时间段设置为 60 分钟，且在下午 1:15 运行查询，则在中午 12:15 和下午 1:15 之间创建的记录将用于日志查询。 如果日志查询使用类似于时间命令**前 (1 d)**，查询仍然只使用 12:15 和 1:15 PM 之间的数据，因为时间段设置为该时间间隔内。

检查在配置中的时间段与查询匹配。 显示更早版本，例如如果日志查询使用**前 (1 d)** 使用绿色的标记，时间段应设置为 24 小时或 1440 分钟 （以红色表示）。 此设置可确保查询按预期运行。

![时间段](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>设置“抑制警报”选项

按上一文的步骤 8 中所述[在 Azure 门户中创建的日志警报规则](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)，日志警报提供**阻止警报**选项可用于配置内禁止触发和通知操作时间。 因此，您可能认为不触发警报。 事实上，它未触发，但被取消。  

![阻止警报](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>指标度量警报规则不正确

*公制度量单位日志警报*是具有特殊功能和限制警报查询语法的日志警报的子类型。 公制度量单位日志警报的规则需要在查询输出成为一个度量值时间序列。 也就是说，输出是具有明确的、 可与其大小相等的时间段以及相应的聚合值的表。 

你可以选择允许其他变量以及表中**AggregatedValue**。 可以使用这些变量对表进行排序。 

例如，假设公制度量单位日志警报的规则已配置为：

- 查询 `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 为 6 小时的时间段
- 阈值为 50
- 警报逻辑的三个连续违规数
- **聚合后**选作 **$table**

因为该命令包含**总结...通过**，并提供了两个变量 (**时间戳**并 **$table**)，系统会选择 **$table**对于**聚合后**. 系统对通过结果表进行排序 **$table**字段，如以下屏幕截图中所示。 然后它会查看多个**AggregatedValue**每个表类型的实例 (如**availabilityResults**) 以查看是否有三个或多个连续违规数。

![指标度量查询执行使用多个值](media/alert-log-troubleshoot/LogMMQuery.png)

因为**聚合后**上定义 **$table**，对数据进行排序上 **$table**列 （以红色表示）。 然后，组并查找类型的**聚合后**字段。 

例如，对于 **$table**，值为**availabilityResults**将被视为一个绘图/实体 （以橙色指示）。 在此值绘图/实体中，警报服务将检查三个连续违规数 （以绿色表示）。 安全违规事件触发的表值的警报**availabilityResults**。 

同样，如果三个连续违规数会发生的任何其他值的 **$table**，为相同的操作触发其他警报通知。 警报服务会自动按时间排列 （以橙色指示） 的一个绘图/实体中的值。

现在假设公制度量单位日志警报规则的修改时间和查询是`search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`。 配置的其余部分也仍然相同，包括前三个连续违规数的警报逻辑。 **聚合后**选项在这种情况下是**时间戳**默认情况下。 在查询中提供一个值**总结...通过**(即**时间戳**)。 如前面的示例中，在末尾执行输出将如下所示。

   ![指标度量查询执行使用单值](media/alert-log-troubleshoot/LogMMtimestamp.png)

因为**聚合后**上定义**时间戳**，对数据进行排序上**时间戳**列 （以红色表示）。 然后我们分组依据**时间戳**。 例如，值为`2018-10-17T06:00:00Z`将被视为一个绘图/实体 （以橙色指示）。 警报服务将此值绘图/实体，在找到任何连续违规数 (因为每个**时间戳**值具有只有一个条目)。 因此永远不会触发警报。 在这种情况下，用户必须：

- 添加一个虚拟变量或现有变量 (如 **$table**) 进行正确排序通过使用**聚合后**字段。
- 重新配置警报规则，以使用基于警报逻辑**总违规**相反。

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

一个已配置[Azure Monitor 中的日志预警规则](../platform/alerts-log.md)中查看时可能会意外触发[Azure 警报](../platform/alerts-managing-alert-states.md)。 以下部分介绍的一些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

Log Analytics 和 Application Insights 受到引入延迟和处理。 当您运行的日志警报查询后，您可能会发现，没有数据可用时，或只有某些数据可用。 有关详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](../platform/data-ingestion-time.md)。

根据警报规则的配置方式，misfiring 可能情况下会出现任何数据或日志中的部分数据时的警报执行。 在这种情况下，我们建议你更改警报查询或配置。 

例如，如果您配置日志预警规则触发时从分析查询的结果数小于 5，没有任何数据 （零个记录） 或部分结果 （一条记录） 时触发警报。 但数据引入延迟后，使用完整的数据相同的查询可能提供的 10 条记录的结果。

### <a name="alert-query-output-is-misunderstood"></a>警报查询输出被误解

你在分析查询中提供日志警报的逻辑。 分析查询可以使用各种大数据和数学函数。 警报服务在指定的时间段内指定的数据与时间间隔运行查询。 警报服务向基于警报类型的查询的细微的更改。 你可以查看在此更改**要执行的查询**部分**配置信号逻辑**屏幕：

![要执行的查询](media/alert-log-troubleshoot/LogAlertPreview.png)

**要执行的查询**框，则日志警报服务的运行。 如果你想要了解哪些警报查询输出之前创建该警报，可能需要可以运行指定的查询和通过的时间跨度[分析门户](../log-query/portals.md)或[分析 API](https://docs.microsoft.com/rest/api/loganalytics/)。

## <a name="log-alert-was-disabled"></a>已禁用日志警报

以下各节列出了为什么可能会禁用 Azure Monitor 的一些原因[日志预警规则](../platform/alerts-log.md)。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>不能再创建警报时所在的资源已存在

在 Azure Monitor 中创建的日志警报规则的目标 Azure Log Analytics 工作区、 Azure Application Insights 应用等 Azure 资源的特定资源。 日志警报服务将运行 analytics 查询提供规则中指定的目标。 但是，规则在创建后，用户通常转到从 Azure 中删除或移动在 Azure 日志预警规则的目标。 警报规则的目标不再有效，因为该规则的执行会失败。

在这种情况下，Azure Monitor 禁用日志警报，并确保，则不计费不必要地时该规则不能持续运行 （如一周） 的可调时间。 您可以查找了 Azure Monitor 禁用通过日志警报时的确切时间[Azure 活动日志](../../azure-resource-manager/resource-group-audit.md)。 在 Azure 活动日志中，当 Azure Monitor 禁用日志预警规则时添加事件。

Azure 活动日志中的以下示例事件是已因持续故障而被禁用的警报规则。

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

在 Azure Monitor 中为其配置的一部分创建的每个日志预警规则必须指定警报服务将定期运行的分析查询。 分析查询的规则创建或更新时可能会具有正确的语法。 但有时，通过一段时间，日志预警规则中提供的查询可以开发语法问题，会导致规则执行失败。 为什么日志预警规则中提供的分析查询可以开发错误的一些常见原因是：

- 该查询将写入[运行跨多个资源](../log-query/cross-workspace-query.md)。 和一个或多个指定的资源不再存在。
- 已分析平台，平台无数据流。 [执行查询时出现错误](https://dev.loganalytics.io/documentation/Using-the-API/Errors)因为没有提供的查询的数据。
- 中的更改[查询语言](https://docs.microsoft.com/azure/kusto/query/)包括命令和函数的修改后的格式。 因此在警报规则的前面提供的查询将不再有效。

[Azure 顾问](../../advisor/advisor-overview.md)有关此行为会发出警告。 建议添加特定的日志警报规则上 Azure 顾问下具有中等程度的影响的高可用性的类别和说明的"修复你的日志警报规则以确保监视。" Azure 监视器中日志警报规则的警报查询不纠正后 Azure 顾问提供了七天的建议，如果要禁用日志警报和确保您不计费不必要地为可调整大小的时间段 （不能持续运行规则时如一周）。

您可以找到 Azure Monitor 禁用日志预警规则通过查找中的事件时的确切时间[Azure 活动日志](../../azure-resource-manager/resource-group-audit.md)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 中的日志警报](../platform/alerts-unified-log.md)。
- 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。
