---
title: Azure 状态监视器 v2 概述 |Microsoft Docs
description: 状态监视器 v2 的概述。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tilee
ms.openlocfilehash: 3060659c5f870be60f1ac02e432dd0a8333f0900
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057834"
---
# <a name="status-monitor-v2"></a>状态监视器 v2

状态监视器 v2 是发布到 [PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模块。
它将替换[状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
该模块提供了使用 IIS 托管的 .NET Web 应用的无代码检测。
遥测数据将发送到 Azure 门户，你可以在其中[监视](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)应用。

## <a name="powershell-gallery"></a>PowerShell 库

状态监视器 v2 位于此处： https://www.powershellgallery.com/packages/Az.ApplicationMonitor 。

![PowerShell 库](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>说明
- 请参阅[入门说明](status-monitor-v2-get-started.md)，从简明的代码示例开始学习。
- 请参阅[详细说明](status-monitor-v2-detailed-instructions.md)，以深入了解如何开始使用。

## <a name="powershell-api-reference"></a>PowerShell API 参考
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>疑难解答
- [故障排除](status-monitor-v2-troubleshoot.md)
- [已知问题](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常见问题

- 状态监视器 v2 是否支持代理安装？

  *是*。 可以通过多种方式下载状态监视器 v2。 如果计算机可以访问 Internet，则可以使用 `-Proxy` 参数登录到 PowerShell 库。
还可以手动下载此模块，并将其安装到计算机上或直接使用它。
上述每个选项都在[详细说明](status-monitor-v2-detailed-instructions.md)中进行了说明。

- 状态监视器 v2 支持 ASP.NET Core 应用程序吗？

  *否*。 有关启用对 ASP.NET Core 应用程序的监视的说明，请参阅[ASP.NET Core 应用程序的 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)。 无需为 ASP.NET Core 的应用程序安装 StatusMonitor。 即使在 IIS 中承载 ASP.NET Core 应用程序，也是如此。
  
状态监视器 v2 支持 ASP.NET Core 应用程序吗？ 

  *否*。 请按照[以下](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)说明启用对 ASP.NET Core 应用程序的监视。 不需要为 ASP.NET Core 的应用程序安装 StatusMonitor。 即使在 IIS 中承载 ASP.NET Core 应用程序，也是如此。

- 如何验证启用是否成功？

  - [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) cmdlet 可用于验证启用是否成功。
  - 我们建议你使用[实时指标](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)来快速确定应用是否正在发送遥测数据。

  - 还可以使用 [Log Analytics](../log-query/get-started-portal.md) 列出当前正在发送遥测数据的所有云角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>后续步骤

查看遥测：

* [浏览指标](../../azure-monitor/app/metrics-explorer.md)，以便监视性能和使用情况。
* [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
* [使用分析](../../azure-monitor/app/analytics.md)，以便进行更高级的查询。
* [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

添加更多遥测：

* [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
* [添加 Web 客户端遥测](../../azure-monitor/app/javascript.md)，以查看网页代码中的异常并启用跟踪调用。
* [将 Application Insights SDK 添加到代码](../../azure-monitor/app/asp-net.md)，以便插入跟踪和日志调用。

