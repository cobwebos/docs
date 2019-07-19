---
title: 用于 Azure 警报中的日志警报的 Webhook 操作
description: 本文介绍如何使用 "Log Analytics" 工作区或 "Application Insights" 创建日志警报规则, 以及如何将数据作为 HTTP webhook 推送, 以及可能的不同自定义的详细信息。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 8bdd0d5230feeeb4c80775ce63aa7e4eaccb601c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226797"
---
# <a name="webhook-actions-for-log-alert-rules"></a>用于日志警报规则的 Webhook 操作
[在 Azure 中创建日志警报](alerts-log.md)时, 可以选择[通过使用操作组](action-groups.md)来执行一个或多个操作。 本文介绍可用的不同 webhook 操作, 并演示如何配置基于 JSON 的自定义 webhook。

> [!NOTE]
> 你还可以将[常见的警报架构](https://aka.ms/commonAlertSchemaDocs)用于 webhook 集成。 常见的警报架构提供了在 Azure Monitor 中的所有警报服务之间具有单个可扩展的统一警报负载的优点。 [了解常见的警报架构定义。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook 操作

使用 webhook 操作, 可以通过单个 HTTP POST 请求调用外部进程。 调用的服务应支持 webhook, 并确定如何使用它收到的任何有效负载。

Webhook 操作需要下表中的属性。

| 属性 | 描述 |
|:--- |:--- |
| **Webhook URL** |Webhook 的 URL。 |
| **自定义 JSON 有效负载** |在警报创建过程中选择此选项时, 要与 webhook 一起发送的自定义负载。 有关详细信息, 请参阅[管理日志警报](alerts-log.md)。|

> [!NOTE]
> "查看日志的**自定义 JSON 负载**" 选项旁边的 "**查看 webhook** " 选项会显示提供的自定义项的示例 webhook 负载。 它不包含实际数据, 而是代表用于日志警报的 JSON 架构。 

Webhook 包括以 JSON 格式发送到外部服务的 URL 和有效负载。 默认情况下，有效负载包括下表中的值。 可以选择将此负载替换成自己的自定义负载。 在这种情况下, 请将表中的变量用于每个参数, 以在自定义负载中包含它们的值。


| 参数 | 变量 | 描述 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |警报规则的名称。 |
| *Severity* |#severity |为触发的日志警报设置的严重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警报规则的阈值运算符, 它使用大于或小于。 |
| *AlertThresholdValue* |#thresholdvalue |警报规则的阈值。 |
| *LinkToSearchResults* |#linktosearchresults |指向分析门户的链接, 该门户从创建警报的查询返回记录。 |
| *ResultCount* |#searchresultcount |搜索结果中的记录数。 |
| *搜索间隔结束时间* |#searchintervalendtimeutc |UTC 格式的结束时间, 采用 mm/dd/yyyy HH: mm: ss AM/PM 格式。 |
| *搜索间隔* |#searchinterval |警报规则的时间范围, 格式为 HH: mm: ss。 |
| *搜索间隔 StartTime* |#searchintervalstarttimeutc |UTC 格式的开始时间, 采用 mm/dd/yyyy HH: mm: ss AM/PM 格式。 
| *SearchQuery* |#searchquery |警报规则所使用的日志搜索查询。 |
| *SearchResults* |"IncludeSearchResults": true|如果将 "IncludeSearchResults": true 作为顶级属性添加到自定义 JSON webhook 定义中, 则查询以 JSON 表形式返回的记录将限制为前1000个记录。 |
| *警报类型*| #alerttype | 配置为[指标度量](alerts-unified-log.md#metric-measurement-alert-rules)或[结果数](alerts-unified-log.md#number-of-results-alert-rules)的日志警报规则的类型。|
| *WorkspaceID* |#workspaceid |Log Analytics 工作区的 ID。 |
| *应用程序 ID* |#applicationid |Application Insights 应用的 ID。 |
| *订阅 ID* |#subscriptionid |使用的 Azure 订阅的 ID。 

> [!NOTE]
> *LinkToSearchResults*将 URL 中的*SearchQuery*、*搜索间隔 StartTime*和*搜索间隔结束时间*等参数传递到 Azure 门户以便在分析部分中查看。 Azure 门户的 URI 大小限制为大约2000个字符。 如果参数值超出限制, 门户将*不*会打开警报中提供的链接。 你可以手动输入详细信息以在分析门户中查看结果。 或者, 您可以使用[Application Insights 分析 REST API](https://dev.applicationinsights.io/documentation/Using-the-API)或[Log Analytics REST API](/rest/api/loganalytics/)以编程方式检索结果。 

例如，可以指定以下自定义负载，其中包含名为 *text* 的单一参数。 此 webhook 调用的服务需要此参数。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
此示例负载在发送到 webhook 时解析为类似于以下内容:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
由于自定义 webhook 中的所有变量都必须在 JSON 机箱内指定, 如 "#searchinterval", 因此, 生成的 webhook 还会在机箱内包含变量数据, 如 "00:05:00"。

若要在自定义有效负载中包含搜索结果, 请确保将**IncludeSearchResults**设置为 JSON 有效负载中的顶级属性。 

## <a name="sample-payloads"></a>示例有效负载
此部分显示日志警报的 webhook 的示例负载。 如果负载是标准的并且是自定义的, 则示例负载包括示例。

### <a name="standard-webhook-for-log-alerts"></a>日志警报的标准 webhook 
这两个示例都有一个只有两列和两行的虚拟有效负载。

#### <a name="log-alert-for-log-analytics"></a>Log Analytics 的日志警报
下面的示例负载适用于*不带自定义 JSON 选项*的标准 webhook 操作, 用于基于 Log Analytics 的警报:

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
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
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> 如果已在 Log Analytics 上切换了针对日志警报的[API 首选项](alerts-log-api-switch.md), 则 "严重性" 字段值可能会发生变化。


#### <a name="log-alert-for-application-insights"></a>Application Insights 的日志警报
以下示例负载适用于*不带自定义 JSON 选项*的标准 webhook, 因为它用于基于 Application Insights 的日志警报:
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
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
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>包含自定义 JSON 有效负载的日志警报
例如, 若要创建只包含警报名称和搜索结果的自定义负载, 可以使用以下内容: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下示例有效负载适用于任何日志警报的自定义 webhook 操作:
    
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
- 了解[Azure 警报中的日志警报](alerts-unified-log.md)。
- 了解如何[管理 Azure 中的日志警报](alerts-log.md)。
- [在 Azure 中](action-groups.md)创建和管理操作组。
- 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。 

