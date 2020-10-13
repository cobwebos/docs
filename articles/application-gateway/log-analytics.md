---
title: 使用 Azure Log Analytics 检查 WAF 日志
titleSuffix: Azure Application Gateway
description: 本文介绍如何使用 Azure Log Analytics (WAF) 日志来检查应用程序网关 Web 应用程序防火墙。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 881dbd02a6bf069fbe68108cc6eab0cb6b909a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91362644"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>使用 Log Analytics 来检查应用程序网关 Web 应用程序防火墙 (WAF) 日志

应用程序网关 WAF 正常运行之后，你可以启用日志来检查每一个请求所发生的情况。 通过防火墙日志可深入了解 WAF 所评估、匹配和阻止的内容。 通过 Log Analytics，可以检查防火墙日志内的数据，从而获得更多见解。 若要详细了解如何创建 Log Analytics 工作区，请参阅[在 Azure 门户中创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)。 有关日志查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>导入 WAF 日志

若要将防火墙日志导入 Log Analytics，请参阅[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md#diagnostic-logging)。 当 Log Analytics 工作区中有防火墙日志时，你可以查看数据、编写查询、创建可视化效果，并将这些内容添加到门户仪表板。

## <a name="explore-data-with-examples"></a>通过运行示例浏览数据

若要查看防火墙日志中的原始数据，可运行以下查询：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

这样可获得与以下查询类似的效果：

![Log Analytics 查询](media/log-analytics/log-query.png)

可以向下钻取数据，并在此绘制图表或创建可视化效果。 请参阅以下查询作为入门示例：

### <a name="matchedblocked-requests-by-ip"></a>按 IP 匹配/阻止的请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>按 URI 匹配/阻止的请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>排名靠前的匹配的规则

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

## <a name="add-to-your-dashboard"></a>添加到仪表板

创建查询后，可将其添加到仪表板。  选择日志分析工作区右上方的“固定到仪表板”。 上述四个查询固定到示例仪表板后，这些数据就可以一目了然：

![屏幕截图显示了可在其中添加查询的 Azure 仪表板。](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>后续步骤

[应用程序网关的后端运行状况、诊断日志和指标](application-gateway-diagnostics.md)
