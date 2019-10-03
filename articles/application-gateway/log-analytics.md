---
title: 使用 Azure Log Analytics 来检查应用程序网关 Web 应用程序防火墙日志
description: 本文介绍如何使用 Azure Log Analytics 来检查应用程序网关 Web 应用程序防火墙日志
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712171"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>使用 Log Analytics 来检查应用程序网关 Web 应用程序防火墙日志

应用程序网关 WAF 操作后，可以启用日志，以检查每个请求发生了什么。 对哪些 WAF 的防火墙日志提供见解是评估、 匹配和阻塞。 使用 Log Analytics，可以检查防火墙日志，以提供更多见解中的数据。 有关创建 Log Analytics 工作区的详细信息，请参阅[Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。 有关日志查询的详细信息，请参阅[日志概述在 Azure Monitor 中查询](../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>导入 WAF 日志

若要在防火墙日志导入 Log Analytics，请参阅[后端运行状况、 诊断日志和应用程序网关指标](application-gateway-diagnostics.md#diagnostic-logging)。 当在 Log Analytics 工作区中有防火墙日志时，可以查看数据、 编写查询、 创建可视化效果，和将其添加到门户仪表板。

## <a name="explore-data-with-examples"></a>浏览示例数据

若要查看防火墙日志中的原始数据，可以运行以下查询：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

这看起来类似于以下查询：

![Log Analytics 查询](media/log-analytics/log-query.png)

可以深化到数据，并绘制关系图或从此处创建可视化效果。 作为起始点，请参阅下面的查询：

### <a name="matchedblocked-requests-by-ip"></a>通过 IP 匹配/已阻止请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>匹配/已阻止请求通过 URI

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>顶部匹配规则

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>前五个匹配的规则组

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>将添加到你的仪表板

一旦创建查询，可以将其添加到你的仪表板。  选择**固定到仪表板**右上方的 log analytics 工作区。 与上述四个查询中已固定到仪表板示例，这是你可以快速查看的数据：

![仪表板](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>后续步骤

[后端运行状况、 诊断日志和指标的应用程序网关](application-gateway-diagnostics.md)
