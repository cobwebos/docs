---
title: "Microsoft Azure 中的警报概述 | Microsoft Docs"
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
ms.date: 09/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f80179a487d08244cbd5e57d34b10c4ab67b3cf5


---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure 中的警报概述
本文介绍什么是警报及其优点，以及如何开始使用警报。  

## <a name="what-are-alerts"></a>什么是警报？
警报是监视 Azure 资源指标、事件或日志并在符合指定条件时发出通知的一种方法。

可能根据以下条件接收警报：

* **指标值**：当指定指标的值在任一方向越过了指定的阈值时将触发警报。 也就是说，当条件先是满足以及之后不再满足该条件时，警报都会触发。
* **活动日志事件**：警报可以在发生每个事件时都触发，也可以仅在发生特定数量的事件时触发。

## <a name="alerts-in-different-azure-services"></a>不同 Azure 服务中的警报
警报可跨不同的服务使用，包括：

* **Application Insights**：启用 Web 测试和指标警报。 请参阅[在 Application Insights 中设置警报](../application-insights/app-insights-alerts.md)和[监视任何网站的可用性和响应能力](../application-insights/app-insights-monitor-web-app-availability.md)。
* **Log Analytics (Operations Management Suite)**：用于将诊断日志路由到 Log Analytics。 Operations Management Suite 允许指标、日志和其他警报类型。 有关详细信息，请参阅 [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)。  
* **Azure 监视器**：基于指标值和活动日志事件启用警报。 Azure 监视器包含 [Azure 监视器 REST API](https://msdn.microsoft.com/library/dn931943.aspx)。  有关详细信息，请参阅[使用 Azure 门户、PowerShell 或命令行接口创建警报](insights-alerts-portal.md)。

## <a name="alert-actions"></a>警报操作
可将警报配置为执行以下操作：

* 将电子邮件通知发送到服务管理员、共同管理员或指定的其他电子邮件。
* 调用 Webhook，以便用户启动其他自动化操作
  
  ![警报介绍](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)

## <a name="next-steps"></a>后续步骤
了解警报规则以及如何使用以下工具来配置这些规则：

* [Azure 门户](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [命令行接口 (CLI)](insights-alerts-command-line-interface.md)
* [Azure 监视器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)




<!--HONumber=Nov16_HO3-->


