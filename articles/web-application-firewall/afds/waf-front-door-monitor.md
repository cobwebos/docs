---
title: Azure Web 应用程序防火墙监视和日志记录
description: 通过前门监控和日志记录了解 Web 应用程序防火墙 （WAF）
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284137"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 应用程序防火墙监视和日志记录 

Azure Web 应用程序防火墙 （WAF） 监视和日志记录是通过日志记录和与 Azure 监视器和 Azure 监视器日志集成提供的。

## <a name="azure-monitor"></a>Azure Monitor

具有前门日志的 WAF 与[Azure 监视器](../../azure-monitor/overview.md)集成。 Azure 监视器允许您跟踪诊断信息，包括 WAF 警报和日志。 您可以在 **"诊断"** 选项卡下的门户前门资源中或通过 Azure 监视器服务直接配置 WAF 监视。

从 Azure 门户转到前门资源类型。 从左侧的 **"监视**/**指标"** 选项卡中，可以添加**Web 应用程序防火墙请求计数**以跟踪与 WAF 规则匹配的请求数。 可以基于操作类型和规则名称创建自定义筛选器。

![WAFMetric](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>日志和诊断

带前门的 WAF 提供有关其检测到的每个威胁的详细报告。 日志记录与 Azure 诊断日志集成，并且警报以 JSON 格式记录。 这些日志可与 [Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)集成。

![瓦夫迪亚格](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

前门访问日志记录转发到客户后端的所有请求。 前门Web应用程序防火墙日志记录任何与WAF规则匹配的请求。

以下示例查询获取阻止请求上的 WAF 日志：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

下面是 WAF 日志中记录的请求的示例：

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

以下示例查询获取 AccessLogs 条目：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

下面是访问日志中记录的请求的示例：

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>后续步骤

- 了解有关[前门](../../frontdoor/front-door-overview.md)的更多。
