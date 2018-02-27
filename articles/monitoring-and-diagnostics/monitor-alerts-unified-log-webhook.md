---
title: "用于 Azure 警报（预览版）中日志警报的 Webhook 操作 | Microsoft Docs"
description: "本文介绍使用 Log Analytics 或 application insights 的日志警报规则如何作为 HTTP Webhook 推送数据，以及可能的不同自定义设置的详细信息。"
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 49905638-f9f2-427b-8489-a0bcc7d8b9fe
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: vinagara
ms.openlocfilehash: 4af1bb61888810011ce64fde7931cabfefe76ab6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="webhook-actions-for-log-alert-rules"></a>用于日志警报规则的 Webhook 操作
[在 Azure（预览版）中创建警报](monitor-alerts-unified-usage.md)时，可以选择[使用操作组配置](monitoring-action-groups.md)以执行一个或多个操作。  本文介绍可用的不同 Webhook 操作，以及有关配置基于 JSON 的自定义 Webhook 的详细信息。


## <a name="webhook-actions"></a>Webhook 操作

使用 Webhook 操作可通过单个 HTTP POST 请求调用外部进程。  被调用的服务应支持 Webhook，并确定将如何使用接收的任何负载。  只要此请求采用 API 理解的格式，还可以调用不专门支持 Webhook 的 REST API。  使用 webhook 响应警报的示例是在 [Slack](http://slack.com) 中发送消息或在 [PagerDuty](http://pagerduty.com/) 中创建事件。  有关使用 webhook 来调用 Slack，以创建警报规则的完整演练步骤，请参阅 [Log Analytics 警报中的 Webhook](../log-analytics/log-analytics-alerts-webhooks.md)。

Webhook 操作需要下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| Webhook URL |Webhook 的 URL。 |
| 自定义 JSON 负载 |要使用 webhook 发送的自定义负载。  （如果警报创建期间选择了此选项）。 有关详细信息，请参阅[使用 Azure 警报（预览版）管理警报](monitor-alerts-unified-usage.md) |

> [!NOTE]
> 单击日志警报的“包括 Webhook 的自定义 JSON 有效负载”选项旁边的“测试 Webhook”按钮可触发虚拟调用以测试 Webhook URL。 它不包含实际数据，也不代表用于日志警报的 JSON 架构。 虽然可以使用典型自定义 JSON 测试任一个 Webhook，但操作组中配置的所有 Webhook 将与自定义 JSON 有效负载一起发送。

Webhooks 包括 URL 和 JSON 格式的负载（即发送到外部服务的数据）。  默认情况下，负载将包含下表中的值。  可以选择将此负载替换成自己的自定义负载。  在这种情况下，可以使用下表中每个参数的变量，将其值包含在自定义负载中。


| 参数 | 变量 | 说明 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |警报规则的名称。 |
| AlertThresholdOperator |#thresholdoperator |警报规则的阈值运算符。  *大于*或*小于*。 |
| AlertThresholdValue |#thresholdvalue |警报规则的阈值。 |
| LinkToSearchResults |#linktosearchresults |指向 Log Analytics 日志搜索的链接，该搜索会从创建警报的查询返回记录。 |
| ResultCount |#searchresultcount |搜索结果中的记录数。 |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |查询结束时间（UTC 格式）。 |
| SearchIntervalInSeconds |#searchinterval |警报规则的时间范围。 |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |查询开始时间（UTC 格式）。 |
| SearchQuery |#searchquery |警报规则所使用的日志搜索查询。 |
| SearchResults |"IncludeSearchResults":true|如果在自定义 JSON Webhook 定义中添加了 "IncludeSearchResults":true 作为顶级属性，则查询以 JSON 表形式返回的记录将限制为前 1,000 个记录/行 |
| WorkspaceID |#workspaceid |Log Analytics 工作区的 ID。 |
| Severity |#severity |为触发的日志警报设置的严重性。 |

例如，可以指定以下自定义负载，其中包含名为 *text* 的单一参数。  该 Webhook 调用的服务将需要此参数。

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

此示例的有效负载会在发送到 Webhook 时解析如下。

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

若要在自定义有效负载中包含搜索结果，请确保在 json 有效负载中将 **IncudeSearchResults** 设置为顶级属性。


可以在 [在 Log Analytics 中创建警报 webhook 操作以将消息发送到 Slack](../log-analytics/log-analytics-alerts-webhooks.md) 中使用 webhook 启动外部服务，逐步完成创建警报规则的完整示例。

## <a name="sample-payload"></a>示例有效负载
此部分显示用于日志警报的 Webhook 的示例有效负载，包括有效负载是标准有效负载时以及是自定义有效负载时。

> [!NOTE]
> 为了确保向后兼容性，使用 Azure Log Analytics 的警报的标准 Webhook 有效负载与 [OMS 警报管理](../log-analytics/log-analytics-solution-alert-management.md)相同。 但对于使用 [Application Insights](../application-insights/app-insights-analytics.md) 的日志警报，标准 Webhook 有效负载基于操作组架构

### <a name="standard-webhook-for-log-alerts"></a>用于日志警报的标准 Webhook
这两个示例都声明了仅包含两列和两行的虚拟有效负载。

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log-Analytics 的日志警报
下面是用于基于 Log Analytics 的日志警报时不带自定义 Json 的标准 Webhook 操作的示例有效负载。

    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }



#### <a name="log-alert-for-azure-application-insights"></a>Application Insights 的日志警报
下面是用于基于 Application Insights 的日志警报时不带自定义 Json 的标准 Webhook 操作的示例有效负载。


    {
    "schemaId":"LogAlert","data":
    {
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }
    }


#### <a name="log-alert-with-custom-json-payload"></a>带自定义 JSON 有效负载的日志警报
例如，若要创建只包含警报名称和搜索结果的自定义负载，则可以使用以下代码。

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }

下面是用于任何日志警报的自定义 Webhook 操作的示例有效负载。


    {
    "AlertRuleName":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"},
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




## <a name="next-steps"></a>后续步骤
- 创建和管理 [Azure 门户中的操作组](monitoring-action-groups.md)
- [了解 Azure 警报（预览版）中的日志警报](monitor-alerts-unified-log.md)
