---
title: Azure Monitor 中的日志警报故障排除 |Microsoft Docs
description: 常见的问题、 错误和日志警报规则在 Azure 中的解决方法。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578707"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>在 Azure Monitor 中排查日志警报问题  

## <a name="overview"></a>概述

本文介绍如何解决常见过程设置 Azure Monitor 中的日志警报时的问题。 并提供有关日志警报功能或配置的常见问题的解决方法。 

术语**日志警报**介绍了激发，基于一个日志查询中的警报[Log Analytics 工作区](../learn/tutorial-viewdata.md)或[Application Insights](../../azure-monitor/app/analytics.md)。 在[日志警报 - 概述](../platform/alerts-unified-log.md)中详细了解功能、术语和类型。

> [!NOTE]
> 本文不考虑 Azure 门户中显示警报规则已触发以及通过关联的操作组执行通知的情况。 对于这种情况，请参阅有关[操作组](../platform/action-groups.md)的文章中的详细信息。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

下面 [Azure Monitor 中配置的日志警报规则](../platform/alerts-log.md)状态不按预期[显示为已激发](../platform/alerts-managing-alert-states.md)的部分常见原因。 

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间

日志警报基于 [Log Analytics](../learn/tutorial-viewdata.md) 或 [Application Insights](../../azure-monitor/app/analytics.md) 定期运行查询。 由于 Azure 监视器在世界各地处理数万亿字节的数千个客户从各种源中的数据，该服务是易受不同的时间延迟。 有关详细信息，请参阅[在 Azure Monitor 日志中的数据引入时间](../platform/data-ingestion-time.md)。

如果系统发现所需的数据尚未引入，为了缓解数据引入延迟，它会等待一段时间，并重试警报查询多次。 为系统设置的等待时间呈指数级递增。 日志警报只会在数据可用后才会触发，因此，延迟可能是日志数据引入速度缓慢造成的。 

### <a name="incorrect-time-period-configured"></a>配置了错误的时间段

根据[日志警报的术语](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文中所述，配置中规定的时间段指定查询的时间范围。 查询仅返回在此时间范围内创建的记录。 时间段限制为日志查询提取的数据以防止滥用，并规避日志查询中使用的任何时间命令（例如 *ago*）。 例如，如果时间段设置为 60 分钟，且在下午 1:15 运行查询，则在中午 12:15 和下午 1:15 之间创建的记录将用于日志查询。 如果日志查询使用类似于 *ago (1d)* 的时间命令，则查询仍只使用在中午 12:15 和下午 1:15 之间的创建数据，因为时间段设置为该间隔。*

因此，应该检查配置中的时间段是否与查询匹配。 对于前面所述的示例，如果日志查询使用 *ago (1d)*（如绿色标记所示），则时间段应设置为 24 小时或 1440 分钟（如红色标记所示），以确保查询按预期执行。

![时间段](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>设置“抑制警报”选项

根据[在 Azure 门户中创建日志警报规则](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)一文中的步骤 8 所述，日志警报提供一个“抑制警报”选项，用于在配置的一段时间内抑制触发和通知操作。 因此，你可能认为某个警报未激发，但实际上它已激发，只不过是抑制了而已。  

![抑制警报](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>指标度量警报规则不正确

**指标度量日志警报**是日志警报的子类型，具有特殊的功能和受限的警报查询语法。 指标度量日志警报规则要求查询输出是指标时序；指标时序是包含等量大小的不同时间段以及相应聚合值的表。 此外，用户可以选择在该表中包含其他变量以及 AggregatedValue。 可以使用这些变量来为表排序。 

例如，假设指标度量日志警报规则已配置为：

- 查询为：`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 时间段为 6 小时
- 阈值为 50
- 警报逻辑为三次连续违规
- 选择 $table 作为聚合依据

由于命令中包含“summarize … by”，并且提供了两个变量（timestamp 和 $table），因此系统将选择 $table 作为“聚合依据”。 它会按 *$table* 字段将结果表排序（如下所示），然后查看每个表类型（例如 availabilityResults）的多个 AggregatedValue，以确定是否发生了 3 次或更多次的连续违规。

![包含多个值的指标度量查询执行](media/alert-log-troubleshoot/LogMMQuery.png)

由于“聚合依据”在 $table 上定义，因此数据已按 $table 列排序（如红框所示）；然后我们进行分组并查看“聚合依据”字段（即 $table）的类型 - 例如：availabilityResults 的值将视为一个绘图/实体（在橙色框中突出显示）。 在此绘图/实体值中，警报服务将检查对表值“availabilityResults”触发的警报的三次连续违规（如绿框中所示）。 同样，如果任何其他 $table 值发生三次连续违规，同一事件会触发另一条警报通知；警报服务将自动按时间排序一个绘图/实体中的值（如橙色框中所示）。

现在，假设指标度量日志警报规则已修改，查询为 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`，但剩余的配置与前面相同，包括警报逻辑同样为三次连续违规。 在这种情况下，“聚合依据”选项默认为 timestamp。 由于在查询中只为“summarize ... by”提供了一个值（即 timestamp），因此与前面的示例类似，在执行结束时，输出将如下所示。

   ![包含单个值的指标度量查询执行](media/alert-log-troubleshoot/LogMMtimestamp.png)

由于“聚合依据”在 timestamp 上定义，因此数据已按 timestamp 列排序（如红框中所示）；然后我们按 timestamp 进行分组，例如：`2018-10-17T06:00:00Z` 的值将视为一个绘图/实体（如橙色框中突出显示）。 在此绘图/实体值中，警报服务找不到发生的连续违规（因为每个 timestamp 值只包含一个条目），因此永远不会触发警报。 因此，在这种情况下，用户必须：

- 添加一个虚拟变量或现有变量（例如 $table），以使用配置的“聚合依据”字段正确执行排序
- （或者）相应地将警报规则重新配置为使用基于违规总数的警报逻辑

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

下面详细描述了在 [Azure 警报](../platform/alerts-managing-alert-states.md)中查看 [Azure Monitor 中配置的日志警报规则](../platform/alerts-log.md)时，该规则意外激发的一些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

为 Log Analytics 和 Application Insights 提供功能支持的 Analytics 可能会发生引入和处理延迟；因此，在运行提供的日志警报查询时，可能存在没有任何数据可用或者只有部分数据可用的情况。 有关详细信息，请参阅[Azure Monitor 中的日志数据引入时间](../platform/data-ingestion-time.md)。

根据警报规则的配置方式，如果在执行警报时日志中没有数据或者只有部分数据，则可能会错误地激发警报。 在这种情况下，我们建议你更改警报查询或配置。 

例如，如果日志警报规则配置为当分析查询的结果数小于 5 时触发，则当没有任何数据（零个记录）或只有部分结果（一个记录）时，警报将会触发。 但是，在发生数据引入延迟后，具有完整数据的同一查询可能会提供包含 10 个记录的结果。

### <a name="alert-query-output-misunderstood"></a>警报查询输出令人误解

你在分析查询中提供日志警报的逻辑。 分析查询可以使用各种大数据和数学函数。  警报服务使用指定时间段内的数据按指定的间隔执行你的查询。 警报服务根据所选择的警报类型对提供的查询进行细微更改。 可以在"要执行的查询"中的部分中查看此更改*配置信号逻辑*屏幕上，如下所示：![要执行的查询](media/alert-log-troubleshoot/LogAlertPreview.png)

“要执行的查询”框中显示的内容是日志警报服务运行的内容。 如果你在实际创建警报之前想要了解警报查询输出可能会显示什么内容，请通过[分析门户](../log-query/portals.md)或[分析 API](https://docs.microsoft.com/rest/api/loganalytics/) 运行所陈述的查询以及时间范围。

## <a name="log-alert-was-disabled"></a>已禁用日志警报

下面列出了由于一些原因[Azure Monitor 中的日志警报规则](../platform/alerts-log.md)可能禁用 Azure monitor。

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>不能再创建警报后的资源已存在

在 Azure Monitor 中创建的日志警报规则的目标 Azure Log Analytics 工作区、 Azure Application Insights 应用和 Azure 资源等的特定资源。 日志警报服务然后运行分析查询提供规则中指定的目标。 但是，规则在创建后，通常，用户转到从 Azure 中删除或移动在 Azure 的警报规则的目标。 日志警报规则的目标不再有效，因为规则的执行将失败。

在这种情况下，Azure Monitor 将禁用日志警报，并确保当规则本身不能为一个如一周的相当大段不断地执行对客户不会发生误报，计费。 用户可以找出的确切时间的日志预警规则已禁用通过 Azure monitor [Azure 活动日志](../../azure-resource-manager/resource-group-audit.md)。 Azure 活动日志中日志警报规则处于禁用状态由 Azure 时 Azure 活动日志中添加事件。

由于其持续失败; 禁用警报规则在 Azure 活动日志示例事件如下所示。

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

### <a name="query-used-in-log-alert-is-not-valid"></a>日志警报中使用的查询无效

在 Azure Monitor 中为其配置的一部分创建的每个日志预警规则必须指定要由警报服务会定期执行的分析查询。 虽然分析查询可能在规则创建或更新时具有正确的语法。 在日志中的某些时间以在一段时间，此查询提供警报规则可以开发语法问题并使规则执行后启动失败。 为什么日志预警规则中提供的分析查询可以开发错误的一些常见原因是：

- 查询写入[运行跨多个资源](../log-query/cross-workspace-query.md)和一个或多个资源指定，现在不存在。
- 已分析平台，由于没有数据流向[执行查询时出现错误](https://dev.loganalytics.io/documentation/Using-the-API/Errors)因为没有提供的查询的数据。
- 中的更改[查询语言](https://docs.microsoft.com/azure/kusto/query/)已在哪些命令和函数具有修改后的格式。 因此警报规则中的前面提供的查询不再有效。

应将警告用户此行为的第一次通过[Azure 顾问](../../advisor/advisor-overview.md)。 建议会被添加为特定的日志警报规则上 Azure 顾问高可用性与中等程度的影响和说明，如"修复你的日志警报规则以确保监视"的类别下。 如果在 Azure 顾问提供的建议的七天后警报将查询中指定的日志警报规则是不在更正问题。 然后 Azure Monitor 将禁用日志警报，并确保当规则本身不能为一个如一周的相当大段不断地执行对客户不会发生误报，计费。

用户可以找出的确切时间的日志预警规则已禁用通过 Azure monitor [Azure 活动日志](../../azure-resource-manager/resource-group-audit.md)。 Azure 活动日志，日志预警规则禁用的 Azure-事件添加 Azure 活动日志中。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 警报中的日志警报](../platform/alerts-unified-log.md)
- 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)
- 详细了解[记录的查询](../log-query/log-query-overview.md)
