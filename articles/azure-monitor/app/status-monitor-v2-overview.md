---
title: Azure Application Insights 代理概述 | Microsoft Docs
description: Application Insights 代理的概述。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275706"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>为本地服务器部署 Azure Monitor Application Insights 代理

> [!IMPORTANT]
> 对于 Application Insights 代理的本地和非 Azure 云部署，建议使用本指南。 下面是建议用于 [Azure 虚拟机和虚拟机规模集部署](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)的方法。

Application Insights 代理（以前称为状态监视器 V2）是发布到 [PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模块。
它将替换[状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
遥测数据将发送到 Azure 门户，你可以在其中[监视](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)应用。

> [!NOTE]
> 目前，该模块仅支持使用 IIS 托管的 .NET Web 应用的无代码检测。 使用 SDK 检测 ASP.NET Core、Java 和 Node.js 应用程序。

## <a name="powershell-gallery"></a>PowerShell 库

Application Insights 代理位于此处：https://www.powershellgallery.com/packages/Az.ApplicationMonitor。

![PowerShell 库](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- 请参阅[入门说明](status-monitor-v2-get-started.md)，从简明的代码示例开始学习。
- 请参阅[详细说明](status-monitor-v2-detailed-instructions.md)，以深入了解如何开始使用。

## <a name="powershell-api-reference"></a>PowerShell API 参考
- [禁用应用程序见解监控](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [启用应用程序洞察监控](status-monitor-v2-api-enable-monitoring.md)
- [启用检测引擎](status-monitor-v2-api-enable-instrumentation-engine.md)
- [获取应用程序洞察监控配置](status-monitor-v2-api-get-config.md)
- [获取应用程序见解监视状态](status-monitor-v2-api-get-status.md)
- [设置应用程序洞察监控配置](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>疑难解答
- [疑难解答](status-monitor-v2-troubleshoot.md)
- [已知问题](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- Application Insights 代理是否支持代理安装？

  *有*。 可以通过多种方式下载 Application Insights 代理。 如果计算机可以访问 Internet，则可以使用 `-Proxy` 参数登录到 PowerShell 库。
还可以手动下载此模块，并将其安装到计算机上或直接使用它。
上述每个选项都在[详细说明](status-monitor-v2-detailed-instructions.md)中进行了说明。

- 状态监视器 v2 是否支持 ASP.NET Core 应用程序？

  *No*。 有关启用对 ASP.NET Core 应用程序的监视的说明，请参阅[适用于 ASP.NET Core 应用程序的 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)。 无需为 ASP.NET Core 应用程序安装 StatusMonitor。 即使 ASP.NET Core 应用程序托管在 IIS 中，也是如此。

- 如何验证启用是否成功？

  - [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) cmdlet 可用于验证启用是否成功。
  - 我们建议你使用[实时指标](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)来快速确定应用是否正在发送遥测数据。

  - 还可以使用 [Log Analytics](../log-query/get-started-portal.md) 列出当前正在发送遥测数据的所有云角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>后续步骤

查看遥测：

* [浏览指标](../../azure-monitor/app/metrics-explorer.md)以监视性能和使用情况。
* [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
* 对更高级的查询[使用分析](../../azure-monitor/app/analytics.md)。
* [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

添加更多遥测：

* [创建 Web 测试](monitor-web-app-availability.md)，确保站点保持活动状态。
* [添加 Web 客户端遥测](../../azure-monitor/app/javascript.md)，以查看网页代码中的异常并启用跟踪调用。
* [将 Application Insights SDK 添加到代码](../../azure-monitor/app/asp-net.md)，以便插入跟踪和日志调用。

