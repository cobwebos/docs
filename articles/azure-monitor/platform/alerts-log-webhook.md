---
title: 用于 Azure 警报中日志警报的 Webhook 操作
description: 描述如何使用 webhook 操作和可用自定义配置日志警报推送
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 9a074be9bcc62d8c20635400f462f52fb796d2fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294302"
---
# <a name="webhook-actions-for-log-alert-rules"></a>用于日志警报规则的 Webhook 操作

[日志警报](alerts-log.md) 支持 [配置 webhook 操作组](action-groups.md#webhook)。 本文介绍了哪些属性可用以及如何配置自定义 JSON webhook。

> [!NOTE]
> API 版本当前不支持基于 JSON 的自定义 webhook `2020-05-01-preview`

> [!NOTE]
> 建议为 webhook 集成使用 [常见的警报架构](alerts-common-schema.md) 。 通用警报架构的优点是可以跨 Azure Monitor 中的所有警报服务提供单个可扩展且统一的警报有效负载。 对于定义了自定义 JSON 有效负载的日志警报规则，启用 "公共架构" 会将负载架构恢复到 [此处](alerts-common-schema-definitions.md#log-alerts)所述的规则。 对于启用了通用架构的警报，每个警报的大小上限为 256 KB，较大的警报不包含搜索结果。 如果不包含搜索结果，则应使用 `LinkToFilteredSearchResultsAPI` 或 `LinkToSearchResultsAPI` 通过 Log Analytics API 访问查询结果。

## <a name="webhook-payload-properties"></a>Webhook 负载属性

Webhook 操作允许调用单个 HTTP POST 请求。 所调用的服务应支持 webhook，并知道如何使用它接收的有效负载。

默认 webhook 操作属性及其自定义 JSON 参数名称：

| 参数 | 变量 | 说明 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |警报规则的名称。 |
| *严重性* |#severity |为触发的日志警报设置的严重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警报规则的阈值运算符。 |
| *AlertThresholdValue* |#thresholdvalue |警报规则的阈值。 |
| *LinkToSearchResults* |#linktosearchresults |指向 Analytics 门户的链接，该门户会从创建警报的查询返回记录。 |
| LinkToSearchResultsAPI |#linktosearchresultsapi |指向 Analytics API 的链接，该 API 会从创建警报的查询返回记录。 |
| LinkToFilteredSearchResultsUI |#linktofilteredsearchresultsui |指向 Analytics 门户的链接，该门户返回按创建警报的维度值组合筛选的查询中的记录。 |
| LinkToFilteredSearchResultsAPI |#linktofilteredsearchresultsapi |指向 Analytics API 的链接，该 API 返回按创建警报的维度值组合筛选的查询中的记录。 |
| *ResultCount* |#searchresultcount |搜索结果中的记录数。 |
| 搜索时间间隔结束时间 |#searchintervalendtimeutc |查询结束时间 (UTC)，格式为 mm/dd/yyyy HH:mm:ss AM/PM。 |
| 搜索时间间隔 |#searchinterval |警报规则的时间范围，格式为 HH:mm:ss。 |
| 搜索时间间隔开始时间 |#searchintervalstarttimeutc |查询开始时间 (UTC)，格式为 mm/dd/yyyy HH:mm:ss AM/PM。 
| *SearchQuery* |#searchquery |警报规则所使用的日志搜索查询。 |
| *SearchResults* |"IncludeSearchResults": true|查询以 JSON 表形式返回的记录，仅限于前 1,000 条记录。 在自定义 JSON Webhook 定义中添加 "IncludeSearchResults": true 作为顶级属性。 |
| *Dimensions* |"IncludeDimensions": true|将该警报作为 JSON 部分触发的维度值组合。 在自定义 JSON Webhook 定义中添加 "IncludeDimensions": true 作为顶级属性。 |
| 警报类型| #alerttype | 配置为[指标度量 或 结果数](alerts-unified-log.md#measure)的日志警报规则的类型。|
| *WorkspaceID* |#workspaceid |Log Analytics 工作区的 ID。 |
| *应用程序 ID* |#applicationid |Application Insights 应用的 ID。 |
| *订阅 ID* |#subscriptionid |使用的 Azure 订阅的 ID。 |

## <a name="custom-webhook-payload-definition"></a>自定义 webhook 负载定义

可以使用以上参数中 **包含的自定义 json 有效负载** 来获取自定义 json 有效负载。 还可以生成其他属性。
例如，可以指定以下自定义负载，其中包含名为 *text* 的单一参数。 此 webhook 调用的服务需要此参数：

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
此示例的有效负载会在发送到 Webhook 时解析如下：

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
必须在 JSON 机箱中指定自定义 webhook 中的变量。 例如，在上述 webhook 示例中引用 "#searchresultcount" 将基于警报结果进行输出。

若要包括搜索结果，请在自定义 JSON 中添加 **IncludeSearchResults** 作为顶级属性。 搜索结果以 JSON 结构的形式提供，因此不能在自定义的字段中引用结果。 

> [!NOTE]
> "**包括 webhook 的自定义 JSON 负载**" 选项旁边的 "**查看 webhook** " 按钮显示提供的内容的预览。 它不包含实际数据，而是要使用的 JSON 架构的代表。 

## <a name="sample-payloads"></a>示例有效负载
本部分显示用于日志警报的 Webhook 的示例有效负载。 示例有效负载包括有效负载是标准有效负载时以及是自定义有效负载时的示例。

### <a name="log-alert-for-log-analytics"></a>Log Analytics 的日志警报
以下示例有效负载适用于基于 Log Analytics 的警报的标准 webhook 操作：

> [!NOTE]
> 如果已从[旧的 Log Analytics 警报 api](api-alerts.md)[切换到当前的 scheduledQueryRules api](alerts-log-api-switch.md) ，则 "严重性" 字段值会发生更改。

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Application Insights 的日志警报
以下示例负载适用于基于 Application Insights 资源的日志警报使用的标准 webhook：
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>其他资源的日志警报 (API 版本 `2020-05-01-preview`) 

> [!NOTE]
> 对于 API 版本 `2020-05-01-preview` 和以资源为中心的日志警报，当前没有额外的费用。  未来将公布预览版中的功能的定价，以及开始计费之前提供的通知。 如果你选择在通知期后继续使用新的 API 版本和以资源为中心的日志警报，则将按适用的费率向你收费。

以下示例有效负载适用于基于其他资源的日志警报，而不包括工作区和 Application Insights) 的日志警报 (：

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>使用自定义 JSON 有效负载的日志警报
例如，若要创建只包含警报名称和搜索结果的自定义负载，请使用此配置： 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

下面是用于任何日志警报的自定义 Webhook 操作的示例有效负载：
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>后续步骤
- 了解 [Azure 警报中的日志警报](alerts-unified-log.md)。
- 了解如何[管理 Azure 中的日志警报](alerts-log.md)。
- 创建和管理 [Azure 中的操作组](action-groups.md)。
- 详细了解 [Application Insights](../log-query/log-query-overview.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。 
