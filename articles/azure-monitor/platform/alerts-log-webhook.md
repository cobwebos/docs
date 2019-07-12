---
title: 用于 Azure 警报中日志警报的 Webhook 操作
description: 本文介绍如何使用 Log Analytics 工作区或 Application Insights 中，创建的日志警报规则警报如何将数据推送作为 HTTP webhook，以及可能的不同自定义的详细信息。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705194"
---
# <a name="webhook-actions-for-log-alert-rules"></a>用于日志警报规则的 Webhook 操作
当[在 Azure 中创建日志警报](alerts-log.md)，可以选择[使用操作组配置](action-groups.md)执行一个或多个操作。 本文介绍可用的不同 webhook 操作，并演示如何配置自定义基于 JSON 的 webhook。

> [!NOTE]
> 您还可以使用[常见警报架构](https://aka.ms/commonAlertSchemaDocs)的 webhook 集成。 常见警报架构提供了一个可扩展且统一警报有效负载的 Azure Monitor 中的所有警报服务的优势。 [了解常见的警报的架构定义。](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>Webhook 操作

使用 webhook 操作，可以调用外部进程通过单个 HTTP POST 请求。 调用的服务应支持 webhook，并确定如何使用它收到任何负载。

Webhook 操作需要下表中的属性。

| 属性 | 描述 |
|:--- |:--- |
| **Webhook URL** |Webhook 的 URL。 |
| **自定义 JSON 有效负载** |若要创建警报期间选择此选项时，通过 webhook 发送自定义有效负载。 有关详细信息，请参阅[管理日志警报](alerts-log.md)。|

> [!NOTE]
> **视图 Webhook**按钮旁边**包括自定义 JSON webhook 有效负载**选项的日志警报显示提供的自定义项示例 webhook 有效负载。 它不包含实际数据，但代表用于日志警报的 JSON 架构。 

Webhooks 包括 URL 和将数据发送到外部服务的 JSON 格式的负载。 默认情况下，有效负载包括下表中的值。 可以选择将此负载替换成自己的自定义负载。 在这种情况下，使用变量表中的每个参数以自定义有效负载中包含它们的值。


| 参数 | 变量 | 描述 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |警报规则的名称。 |
| *Severity* |#severity |为触发的日志警报设置的严重性。 |
| *AlertThresholdOperator* |#thresholdoperator |警报规则，可使用大于或小于阈值运算符比。 |
| *AlertThresholdValue* |#thresholdvalue |警报规则的阈值。 |
| *LinkToSearchResults* |#linktosearchresults |链接到创建该警报的查询返回的记录在分析门户。 |
| *ResultCount* |#searchresultcount |搜索结果中的记录数。 |
| *搜索时间间隔结束时间* |#searchintervalendtimeutc |结束时间 utc 格式，在查询中使用格式 mm/dd/yyyy hh: mm: ss AM/PM。 |
| *搜索时间间隔* |#searchinterval |具有格式 hh: mm： 的警报规则的时间窗口。 |
| *搜索时间间隔开始时间* |#searchintervalstarttimeutc |启动查询时间以 UTC 格式 mm/dd/yyyy hh: mm: ss AM/PM。 
| *SearchQuery* |#searchquery |警报规则所使用的日志搜索查询。 |
| *SearchResults* |"IncludeSearchResults": true|如果查询以 JSON 表，限制为前 1,000 条记录，返回的记录"IncludeSearchResults": true 作为顶级属性的自定义 JSON webhook 定义中添加。 |
| *警报类型*| #alerttype | 日志警报规则配置为的类型[公制度量单位](alerts-unified-log.md#metric-measurement-alert-rules)或[的结果数](alerts-unified-log.md#number-of-results-alert-rules)。|
| *WorkspaceID* |#workspaceid |Log Analytics 工作区的 ID。 |
| *应用程序 ID* |#applicationid |ID 的 Application Insights 应用。 |
| *订阅 ID* |#subscriptionid |所用 Azure 订阅的 ID。 

> [!NOTE]
> *LinkToSearchResults*传递参数，如*SearchQuery*，*搜索时间间隔开始时间*，并且*搜索时间间隔结束时间*中到 Azure 的 URL在分析部分中查看的门户。 在 Azure 门户的 URI 大小限制为大约 2000 个字符。 在门户将*不*打开如果参数值超过了限制在警报中提供的链接。 您可以手动输入详细信息，以在分析门户中查看结果。 或者，可以使用[Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API)或[Log Analytics REST API](/rest/api/loganalytics/)来以编程方式检索结果。 

例如，可以指定以下自定义负载，其中包含名为 *text* 的单一参数。 该 webhook 调用的服务要求将此参数。

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
发送到 webhook 时，此示例有效负载会解析为类似于以下内容：

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
因为自定义 webhook 中的所有变量必须在 JSON 机箱，如"#searchinterval，"中都指定生成 webhook 还具有变量数据内部存储设备，例如"00: 05:00。"

若要自定义有效负载中包含搜索结果，请确保**IncludeSearchResults**设置为 JSON 负载中的顶级属性。 

## <a name="sample-payloads"></a>示例有效负载
本部分介绍针对日志警报的 webhook 的示例有效负载。 示例有效负载包括标准和时，自定义有效负载时的示例。

### <a name="standard-webhook-for-log-alerts"></a>用于日志警报的标准 webhook 
这两个示例有只有两个列和两个行包含虚拟有效负载。

#### <a name="log-alert-for-log-analytics"></a>Log Analytics 的日志警报
以下示例有效负载是标准 webhook 操作*不带自定义 JSON 选项*用于基于 Log Analytics 的警报：

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
> "严重级别"字段值可能会更改，如果您已经[切换 API 首选项](alerts-log-api-switch.md)Log Analytics 日志警报。


#### <a name="log-alert-for-application-insights"></a>Application Insights 的日志警报
以下示例有效负载是的标准 webhook*不带自定义 JSON 选项*当用于日志警报基于 Application Insights:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>使用自定义 JSON 有效负载的日志警报
例如，若要创建只包含警报名称和搜索结果的自定义负载，可以使用以下： 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

以下示例有效负载是任何日志警报的自定义 webhook 操作：
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
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
        }
    }
```


## <a name="next-steps"></a>后续步骤
- 了解如何[Azure 警报中的日志警报](alerts-unified-log.md)。
- 了解如何[管理 Azure 中的日志警报](alerts-log.md)。
- 创建和管理[在 Azure 中的操作组](action-groups.md)。
- 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。 

