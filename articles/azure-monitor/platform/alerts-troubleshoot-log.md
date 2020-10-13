---
title: 在 Azure Monitor 中排查日志警报问题 | Microsoft Docs
description: Azure 中日志警报规则的常见问题、错误和解决方法。
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: ec2ffe71a32781a855da258f3621738f1a5f6be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294285"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>在 Azure Monitor 中排查日志警报问题  

本文介绍如何解决 Azure Monitor 中日志警报的常见问题。 它还提供了有关日志警报功能和配置的常见问题的解决方法。

日志警报允许用户使用 [Log Analytics](../log-query/get-started-portal.md) 查询来评估每个设置频率的资源日志，并根据结果触发警报。 规则可使用 [操作组](./action-groups.md)触发一个或多个操作。 [详细了解日志警报的功能和术语](alerts-unified-log.md)。

> [!NOTE]
> 本文不考虑 Azure 门户中显示警报规则已触发以及不是通过关联的操作组执行通知的情况。 对于这种情况，请参阅 [此处](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)的疑难解答详细信息。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间

Azure Monitor 处理来自世界各地的数 tb 客户的日志，这可能会导致 [日志引入延迟](./data-ingestion-time.md)。

日志为半结构化数据，本质上比指标更具延迟。 如果在触发警报时遇到超过4分钟的延迟，应考虑使用 [指标警报](alerts-metric-overview.md)。 可以使用 [日志的指标警报](alerts-metric-logs.md)，将数据从日志发送到指标存储。

系统多次重试警报评估以降低延迟。 数据到达后，将触发警报，这在大多数情况下不会等于日志记录时间。

### <a name="incorrect-query-time-range-configured"></a>配置了不正确的查询时间范围

在规则条件定义中设置查询时间范围。 此字段称为工作区和 Application Insights 的 **时间段** ，称为替代所有其他资源类型的 **查询时间范围** 。 与在 log analytics 中一样，时间范围限制查询数据到指定时间段。 即使查询中使用了 **前** 一个命令，也会应用时间范围。 

例如，当时间范围为60分钟时，查询将扫描60分钟，即使文本中 ** (1d) **也是如此。 时间范围和查询时间筛选需要匹配。 在此示例中，将 "**期间**"  /  **重写查询时间范围**更改为一天将按预期方式工作。

![时间段](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>警报规则中的 "操作" 静音

日志警报提供了一个选项，用于在设置的时间内对警报操作进行静音。 此字段称为 **禁止显示** 工作区和 Application Insights 中的警报。 在所有其他资源类型中，这称为 " **静音操作**"。 

常见的问题是，由于服务问题，警报不会触发操作。 通常，规则配置还会将其静音。

![阻止警报](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>使用旧 Log Analytics API 进行拆分的指标度量警报规则

[指标度量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 是一种基于汇总时序结果的日志警报。 这些规则允许按列分组以 [拆分警报](alerts-unified-log.md#split-by-alert-dimensions)。 如果使用的是旧的 Log Analytics API，则拆分操作不会按预期方式工作。 不支持在旧版 API 中选择分组。

使用当前的 ScheduledQueryRules API，可以在[指标度量](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)规则中设置**聚合**，这将按预期方式工作。 [详细了解如何切换到当前的 SCHEDULEDQUERYRULES API](alerts-log-api-switch.md)。

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

可能会意外触发 [Azure Monitor 中配置的日志警报规则](./alerts-log.md) 。 以下部分描述了某些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

Azure Monitor 处理来自世界各地的数 tb 客户的日志，这可能会导致 [日志引入延迟](./data-ingestion-time.md)。

日志为半结构化数据，本质上比指标更具延迟。 如果在触发警报时遇到了很多 misfires，则应考虑使用 [指标警报](alerts-metric-overview.md)。 可以使用 [日志的指标警报](alerts-metric-logs.md)，将数据从日志发送到指标存储。

尝试检测日志中的数据时，日志警报最适用。 当你尝试检测日志中缺少的数据时，它的工作效率会降低。 例如，对虚拟机检测信号发出警报。 

尽管存在可防止误报的内置功能，但这些功能仍可能在大约30分钟 (超过30分钟) 和数据，延迟延迟。

### <a name="query-optimization-issues"></a>查询优化问题

警报服务会更改查询，以优化较低负载和警报滞后时间。 生成警报流以将指示问题的结果转换为警报。 例如，在以下情况下，查询如下所示：

``` Kusto
SecurityEvent
| where EventID == 4624
```

如果用户的目的是发出警报，则当此事件类型发生时，警报逻辑将追加 `count` 到查询。 将运行的查询将是：

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

无需向查询添加警报逻辑，甚至可能会导致问题。 在上面的示例中，如果您 `count` 在查询中包括，它将始终生成值1，因为警报服务将执行的操作 `count` `count` 。

优化查询是日志警报服务运行的内容。 可以在 Log Analytics [门户](../log-query/log-query-overview.md) 或 [API](/rest/api/loganalytics/)中运行修改后的查询。

对于工作区和 Application Insights，它被称为在 "条件" 窗格中 **执行的查询** 。 在所有其他资源类型中，在 "条件" 选项卡中选择 " **查看最终警报查询** "。

![要执行的查询](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>已禁用日志警报

以下部分列出了 Azure Monitor 可能禁用日志警报规则的一些原因。 我们还包括了在 [禁用规则时发送的活动日志的示例](#activity-log-example-when-rule-is-disabled)。

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>警报范围不再存在或已被移动

当警报规则的作用域资源不再有效时，规则的执行将失败。 在这种情况下，还会停止计费。

Azure Monitor 将在一周后禁用日志警报。

### <a name="query-used-in-a-log-alert-isnt-valid"></a>日志警报中使用的查询无效

创建日志警报规则时，将验证查询的语法是否正确。 但有时，日志警报规则中提供的查询可能会失败。 常见的原因有：

- 规则是通过 API 创建的，用户跳过了验证。
- 此查询 [运行多个资源](../log-query/cross-workspace-query.md) ，一个或多个资源已删除或移动。
- [查询失败](https://dev.loganalytics.io/documentation/Using-the-API/Errors)，因为：
    - 日志记录解决方案未 [部署到工作区](../insights/solutions.md#install-a-monitoring-solution)，因此不会创建表。
    - 数据在30天内停止流向查询中的表。
    - [自定义日志表](data-sources-custom-logs.md) 尚未创建，因为尚未启动数据流。
- [查询语言](/azure/kusto/query/)的更改包含命令和函数的已修改格式。 因此，先前提供的查询不再有效。

[Azure 顾问](../../advisor/advisor-overview.md)会警告此类行为。 它添加了有关受影响的日志警报规则的建议。 使用的类别为 "高可用性"，其中包含中等影响和 "修复日志警报规则以确保监视" 的说明。

## <a name="alert-rule-quota-was-reached"></a>已达到警报规则配额

每个订阅和资源的日志搜索预警规则数目受[此处](../service-limits.md)所述的配额限制约束。

### <a name="recommended-steps"></a>建议的步骤
    
如果已达到配额限制，可以执行以下步骤来帮助解决此问题。

1. 尝试删除或禁用不再使用的日志搜索预警规则。
1. 尝试 [按维度使用拆分警报](alerts-unified-log.md#split-by-alert-dimensions) 来减少规则计数。 这些规则可以监视许多资源和检测事例。
1. 如果需要增加配额限制，请继续打开支持请求，并提供以下信息：

    - 需要为其增加配额限制的订阅 Id 和资源 Id。
    - 配额增加的原因。
    - 配额增加的资源类型： **Log Analytics**、 **Application Insights**等。
    - 请求的配额限制。


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>检查新日志预警规则的当前使用情况
    
#### <a name="from-the-azure-portal"></a>通过 Azure 门户

1. 打开 "*警报*" 屏幕，然后选择 "*管理警报规则*"
2. 使用“订阅”下拉列表控件筛选到相关订阅
3. 请勿筛选到特定的资源组、资源类型或资源
4. 在“信号类型”下拉列表控件中，选择“日志搜索”
5. 验证“状态”下拉列表控件是否设置为“已启用”
6. 日志搜索预警规则总数将显示在规则列表上方

#### <a name="from-api"></a>通过 API

- PowerShell - [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [按订阅列出](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>禁用规则时的活动日志示例

如果查询连续七天失败，Azure Monitor 将禁用该规则的日志警报和停止计费。 你可以在 [Azure 活动日志](../../azure-resource-manager/management/view-activity-logs.md)中查看 Azure Monitor 禁用日志警报时的准确时间。 请参阅以下示例：

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

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 中的日志警报](./alerts-unified-log.md)。
- 详细了解如何 [配置日志警报](../log-query/log-query-overview.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。
