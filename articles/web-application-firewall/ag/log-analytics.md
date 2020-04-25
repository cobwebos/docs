---
title: 使用 Azure Log Analytics 查看应用程序网关 Web 应用程序防火墙日志
description: 本文介绍如何使用 Azure Log Analytics 来检查应用程序网关 Web 应用程序防火墙日志
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: 4d6b984fd451a835afaa51482f0158e9f315f8fc
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82130918"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>使用 Log Analytics 来检查应用程序网关 Web 应用程序防火墙日志

应用程序网关 WAF 运行后，你可以启用日志来检查每个请求发生的情况。 防火墙日志可深入了解 WAF 的评估、匹配和阻止。 利用 Azure Monitor Log Analytics，你可以检查防火墙日志中的数据，以更好地了解更多见解。 有关创建 Log Analytics 工作区的详细信息，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。 有关日志查询的详细信息，请参阅[Azure Monitor 中的日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>导入 WAF 日志

若要将防火墙日志导入 Log Analytics，请参阅[应用程序网关的后端运行状况、资源日志和指标](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。 Log Analytics 工作区中有防火墙日志时，可以查看数据、编写查询、创建可视化效果，并将其添加到门户仪表板。

## <a name="explore-data-with-examples"></a>利用示例浏览数据

若要查看防火墙日志中的原始数据，可运行以下查询：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

这将类似于下面的查询：

![Log Analytics 查询](../media/log-analytics/log-query.png)

您可以向下钻取数据，并在此处绘制图形或创建可视化效果。 请参阅以下查询作为起点：

### <a name="matchedblocked-requests-by-ip"></a>按 IP 的匹配/阻止的请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>按 URI 匹配/被阻止的请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>排名靠前的规则

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>前五个匹配规则组

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>添加到仪表板

创建查询后，可以将其添加到仪表板。  选择 log analytics 工作区右上方的 "**固定到仪表板**"。 当前面的四个查询固定到示例仪表板时，这就是您可以一目了然的数据：

![仪表板](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>后续步骤

[应用程序网关的后端运行状况、资源日志和指标](../../application-gateway/application-gateway-diagnostics.md)