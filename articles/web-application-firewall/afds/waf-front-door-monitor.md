---
title: Azure Web 应用程序防火墙监视和日志记录
description: 了解具有 FrontDoor 监视和日志记录的 Web 应用程序防火墙（WAF）
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512478"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web 应用程序防火墙监视和日志记录 

Azure Web 应用程序防火墙（WAF）监视和日志记录通过日志记录和与 Azure Monitor 和 Azure Monitor 日志集成提供。

## <a name="azure-monitor"></a>Azure Monitor

带有 FrontDoor 日志的 WAF 与[Azure Monitor](../../azure-monitor/overview.md)集成。 Azure Monitor 允许你跟踪包括 WAF 警报和日志在内的诊断信息。 可以在门户中的 "**诊断**" 选项卡下或直接通过 Azure Monitor 服务配置 WAF 监视。

从 Azure 门户中转到前门资源类型。 从左侧的 "**监视**/**度量值**" 选项卡中，你可以添加**WebApplicationFirewallRequestCount**以跟踪与 WAF 规则匹配的请求数。 可以基于操作类型和规则名称创建自定义筛选器。

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>日志和诊断

带有前门的 WAF 提供了有关它检测到的每个威胁的详细报告。 日志记录与 Azure 诊断日志集成，并且警报以 JSON 格式记录。 这些日志可与 [Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)集成。

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 记录转发给客户后端的所有请求。 FrontdoorWebApplicationFirewallLog 记录与 WAF 规则匹配的任何请求。

下面的示例查询将获取阻止的请求的 WAF 日志：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

下面的示例查询将获取 AccessLogs 条目：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>后续步骤

- 详细了解[前门](../../frontdoor/front-door-overview.md)。

