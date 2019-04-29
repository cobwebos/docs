---
title: Azure web 应用程序防火墙监视和日志记录
description: 了解使用前门监视和日志记录的 web 应用程序防火墙 (WAF)
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459723"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure web 应用程序防火墙监视和日志记录 

Azure web 应用程序防火墙 (WAF) 监视和日志记录是通过提供日志记录以及与 Azure Monitor 和 Azure Monitor 集成日志。

> [!IMPORTANT]
> WAF 监视和日志记录功能的 Azure 第一道防线目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="azure-monitor"></a>Azure Monitor

与集成与前门日志 WAF [Azure Monitor](../azure-monitor/overview.md)。 Azure 监视器可以跟踪包括 WAF 警报和日志的诊断信息。 你可以配置在下的门户中的第一道防线资源监视 WAF**诊断**选项卡上，或直接通过 Azure Monitor 服务。

从 Azure 门户中，转到第一道防线资源类型。 从**监视**/**指标**选项卡在左侧，可以添加**WebApplicationFirewallRequestCount**来跟踪 WAF 规则匹配的请求数。 自定义筛选器可以创建根据操作类型以及规则名称。

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>日志和诊断

与第一道防线 WAF 会提供详细报告检测到每个威胁。 日志记录与 Azure 诊断日志集成，并且警报以 JSON 格式记录。 这些日志可与 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)集成。

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 记录所有请求转发到客户的后端。 FrontdoorWebApplicationFirewallLog 记录的任何请求都与 WAF 规则相匹配。

下面的示例查询获取 WAF 日志上阻止的请求：

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

下面的示例查询获取 AccessLogs 条目：

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[第一道防线](front-door-overview.md)。

