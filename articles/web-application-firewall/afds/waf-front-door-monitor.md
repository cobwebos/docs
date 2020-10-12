---
title: Azure Web 应用程序防火墙监视和日志记录
description: 了解 Web 应用程序防火墙 (WAF) 与 FrontDoor 监视和日志记录
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808953"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 应用程序防火墙监视和日志记录

Azure Web 应用程序防火墙 (WAF) 监视和日志记录通过日志记录和与 Azure Monitor 和 Azure Monitor 日志集成提供。

## <a name="azure-monitor"></a>Azure Monitor

带有 FrontDoor 日志的 WAF 与 [Azure Monitor](../../azure-monitor/overview.md)集成。 Azure Monitor 允许你跟踪包括 WAF 警报和日志在内的诊断信息。 可以在门户中的 " **诊断** " 选项卡下或直接通过 Azure Monitor 服务配置 WAF 监视。

从 Azure 门户中转到前门资源类型。 从左侧的 "**监视** / **指标**" 选项卡中，你可以添加**WebApplicationFirewallRequestCount**以跟踪与 WAF 规则匹配的请求数。 可以基于操作类型和规则名称创建自定义筛选器。

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>日志和诊断

带有前门的 WAF 提供了有关它检测到的每个威胁的详细报告。 日志记录与 Azure 诊断日志集成，并且警报以 JSON 格式记录。 这些日志可与 [Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)集成。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) 记录所有请求。 FrontdoorWebApplicationFirewallLog 记录与具有以下架构的 WAF 规则匹配的任何请求：

| 属性  | 说明 |
| ------------- | ------------- |
|操作|针对请求执行的操作|
| ClientIp | 发出请求的客户端的 IP 地址。 如果请求中有一个 X 转发的标头，则将从 "标头" 字段中提取客户端 IP。 |
| ClientPort | 发出请求的客户端的 IP 端口。 |
| 详细信息|有关匹配请求的其他详细信息 |
|| matchVariableName：匹配的请求的 http 参数名称（例如，标头名称）|
|| matchVariableValue：触发匹配的值|
| 主机 | 匹配请求的主机标头 |
| 策略 | 请求匹配的 WAF 策略的名称。 |
| PolicyMode | WAF 策略的操作模式。 可能的值为 "阻止" 和 "检测" |
| RequestUri | 匹配请求的完全 URI。 |
| RuleName | 请求匹配的 WAF 规则的名称。 |
| SocketIp | WAF 发现的源 IP 地址。 此 IP 地址基于 TCP 会话，与任何请求标头无关。|
| TrackingReference | 标识由 Front Door 提供的请求的唯一引用字符串，该请求还会以 X-Azure-Ref 标头的形式发送到客户端。 是搜索特定请求访问日志中的详细信息必需的。 |

以下查询示例返回被阻止的请求上的 WAF 日志：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

下面是 WAF 日志中记录的请求的示例：

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

下面的示例查询返回 AccessLogs 条目：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

下面是访问日志中记录的请求的示例：

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>后续步骤

- 详细了解 [前门](../../frontdoor/front-door-overview.md)。
