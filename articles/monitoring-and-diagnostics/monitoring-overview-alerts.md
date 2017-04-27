---
title: "Microsoft Azure 和 Azure Monitor 中的警报概述 | Microsoft 文档"
description: "使用警报可以监视 Azure 资源指标、事件或日志，并在符合指定的条件时接收通知。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 76c8feb077cca27dc96f43e708cdef4fbb0f824c
ms.lasthandoff: 03/31/2017


---
# <a name="what-are-alerts-in-microsoft-azure"></a>什么是 Microsoft Azure 中的警报？
本文介绍什么是警报及其优点，以及如何开始使用警报。 本文具体适用于 Azure Monitor，但提供了其他服务的链接。  

警报是用来监视 Azure 资源指标、事件或日志并在符合指定条件时发出通知的一种方法。  

## <a name="alerts-in-different-azure-services"></a>不同 Azure 服务中的警报
警报可跨不同的服务使用，包括：

* **Application Insights**：启用 Web 测试和指标警报。 请参阅[在 Application Insights 中设置警报](../application-insights/app-insights-alerts.md)和[监视任何网站的可用性和响应能力](../application-insights/app-insights-monitor-web-app-availability.md)。
* **Log Analytics (Operations Management Suite)**：用于将活动日志和诊断日志路由到 Log Analytics。 Operations Management Suite 允许指标、日志和其他警报类型。 有关详细信息，请参阅 [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)。  
* **Azure 监视器**：基于指标值和活动日志事件启用警报。 可以使用 [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) 来管理警报。  有关详细信息，请参阅[使用 Azure 门户、PowerShell 或命令行接口创建警报](insights-alerts-portal.md)。

## <a name="visual-summary"></a>可视化汇总
以下关系图汇总了警报以及在 Azure Monitor 中具体可以对其执行的操作。 其他操作可能在前面列出的服务中提供。 例如，当前，有关诊断日志的警报只在 Log Analytics 中提供。

![警报介绍](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="what-can-trigger-alerts-in-azure-monitor"></a>在 Azure Monitor 中什么可以触发警报

可能根据以下条件接收警报：

* **指标值**：当指定指标的值在任一方向越过了指定的阈值时将触发警报。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。 有关 Azure Monitor 支持的可用指标（不断增加中）的列表，请参阅 [Azure Monitor 支持的指标的列表](monitoring-supported-metrics.md)。
* **活动日志事件**：在对资源发生特定事件时，或是在服务通知发布到订阅时，可以触发此警报。

## <a name="what-can-metric-alerts-do"></a>指标警报可以做什么？
你可以配置警报来执行以下操作：

* 将电子邮件通知发送到服务管理员、共同管理员或指定的其他电子邮件。
* 调用 Webhook，以便用户启动其他自动化操作 示例包括调用以下各项：
    - Azure 自动化 Runbook
    - Azure 函数
    - Azure 逻辑应用
    - 第三方服务

## <a name="what-can-activity-log-alerts-do"></a>活动日志警报可以做什么？
你可以配置警报来执行以下操作：
* 每当对订阅下的资源之一发生特定事件时触发
* 每当服务通知发布到订阅时触发
* 通过以下方式向操作组的成员发出警报
    * SMS
    * 电子邮件
    * Webhook

## <a name="next-steps"></a>后续步骤
了解警报规则以及如何使用以下工具来配置这些规则：

* 详细了解[指标](monitoring-overview-metrics.md)
* [通过 Azure 门户配置指标警报](insights-alerts-portal.md)
* 配置[指标警报 PowerShell](insights-alerts-powershell.md)
* 配置[指标警报命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* 配置[指标警报 Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* 详细了解[活动日志](monitoring-overview-activity-logs.md)
* [通过 Azure 门户配置活动日志警报](monitoring-activity-log-alerts.md)
* [通过 Resource Manager 配置活动日志警报](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* 查看[活动日志警报 webhook 架构](monitoring-activity-log-alerts-webhook.md)
* 详细了解[服务通知](monitoring-service-notifications.md)
* 详细了解[操作组](monitoring-action-groups.md)

