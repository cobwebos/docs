---
title: Azure 状态监视器 v2 概述 |Microsoft Docs
description: 状态监视器 v2 的概述。 监视网站性能，无需重新部署该网站。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734163"
---
# <a name="status-monitor-v2"></a>状态监视器 v2

状态监视器 v2 是模块发布到 PowerShell [PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)。
它将替换[状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
此模块提供与 IIS 托管的.NET web 应用程序的无代码检测。
遥测数据发送到 Azure 门户中，您可以在其中[监视器](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)您的应用程序。

> [!IMPORTANT]
> 状态监视器 v2 目前处于公共预览状态。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="powershell-gallery"></a>PowerShell 库

PowerShell 库位于此处： https://www.powershellgallery.com/packages/Az.ApplicationMonitor。


## <a name="instructions"></a>说明
- 请参阅[入门说明](status-monitor-v2-get-started.md)入门的简洁的代码示例。
- 请参阅[详细说明](status-monitor-v2-detailed-instructions.md)深入了解如何开始。

## <a name="powershell-api-reference"></a>PowerShell API 参考
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>故障排除
- [故障排除](status-monitor-v2-troubleshoot.md)
- [已知问题](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常见问题解答

- 状态监视器 v2 是否支持代理安装？

  *是*。 有多种方法来下载状态监视器 v2。 如果您的计算机具有 internet 访问权限，则可以上架到 PowerShell 库使用`-Proxy`参数。
您可以手动下载该模块，并可以将其安装在计算机上或直接使用它。
每个选项所述[详细说明](status-monitor-v2-detailed-instructions.md)。
  
- 如何验证启用是否成功？

   没有任何 cmdlet 来验证已成功启用。
我们建议你使用[实时指标](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)来快速确定您的应用程序是否正在发送遥测数据。

   此外可以使用[Log Analytics](../log-query/get-started-portal.md)若要列出当前发送遥测数据的所有云角色：
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>后续步骤

查看遥测：

* [浏览指标](../../azure-monitor/app/metrics-explorer.md)监视性能和使用情况。
* [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)来诊断问题。
* [使用 Analytics](../../azure-monitor/app/analytics.md)获取更多高级查询。
* [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

添加更多遥测：

* [创建 web 测试](monitor-web-app-availability.md)以确保你的站点保持活动状态。
* [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常，并启用跟踪调用。
* [将 Application Insights SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)以便插入跟踪和记录调用。

