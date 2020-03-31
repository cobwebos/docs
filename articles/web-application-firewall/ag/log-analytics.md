---
title: 使用 Azure 日志分析检查应用程序网关 Web 应用程序防火墙日志
description: 本文介绍如何使用 Azure 日志分析检查应用程序网关 Web 应用程序防火墙日志
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: e1bc3b58f425b374e4cae1da6e9800579e503f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73516586"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>使用 Log Analytics 来检查应用程序网关 Web 应用程序防火墙日志

应用程序网关 WAF 运行后，您可以启用日志来检查每个请求发生的情况。 防火墙日志可以深入了解 WAF 正在评估、匹配和阻止的内容。 使用日志分析，您可以检查防火墙日志中的数据，以提供更多见解。 有关创建日志分析工作区的详细信息，请参阅[在 Azure 门户中创建日志分析工作区](../../azure-monitor/learn/quick-create-workspace.md)。 有关日志查询的详细信息，请参阅 Azure[监视器 中的日志查询概述](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="import-waf-logs"></a>导入 WAF 日志

要将防火墙日志导入日志分析，请参阅[应用程序网关的后端运行状况、诊断日志和指标](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)。 当您的日志分析工作区中包含防火墙日志时，您可以查看数据、编写查询、创建可视化效果以及将它们添加到门户仪表板。

## <a name="explore-data-with-examples"></a>使用示例浏览数据

要查看防火墙日志中的原始数据，可以运行以下查询：

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

这类似于以下查询：

![日志分析查询](../media/log-analytics/log-query.png)

您可以向下钻取数据，并在此处绘制图形或创建可视化效果。 请参阅以下查询作为起点：

### <a name="matchedblocked-requests-by-ip"></a>按 IP 匹配/阻止的请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI 匹配/阻止的请求

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>顶级匹配规则

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>匹配的规则组前五个

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>添加到仪表板

创建查询后，可以将其添加到仪表板。  选择日志分析工作区右上角的 **"固定到仪表板**"。 将前四个查询固定到示例仪表板，您可以一目了然地查看这些数据：

![仪表板](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>后续步骤

[应用程序网关的后端运行状况、诊断日志和指标](../../application-gateway/application-gateway-diagnostics.md)