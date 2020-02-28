---
title: Azure 应用程序 Insights 代理概述 |Microsoft Docs
description: Application Insights 代理的概述。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671165"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>为本地服务器部署 Azure Monitor Application Insights 代理

> [!IMPORTANT]
> 对于 Application Insights 代理的本地和非 Azure 云部署，建议使用本指南。 下面是适用于[Azure 虚拟机和虚拟机规模集部署](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)的建议方法。

Application Insights 代理（以前称为状态监视器 V2）是发布到[PowerShell 库](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)的 PowerShell 模块。
它将替换[状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)。
遥测将发送到 Azure 门户，你可以在其中[监视](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)应用。

> [!NOTE]
> 该模块当前只支持托管了 IIS 的 .NET web 应用的无代码置备检测。 使用 SDK 检测 ASP.NET Core、Java 和 node.js 应用程序。

## <a name="powershell-gallery"></a>PowerShell 库

Application Insights 代理位于此处： https://www.powershellgallery.com/packages/Az.ApplicationMonitor。

![PowerShell 库](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- 请参阅[入门说明](status-monitor-v2-get-started.md)，开始学习简洁的代码示例。
- 有关如何开始使用的详细说明，请参阅[详细说明](status-monitor-v2-detailed-instructions.md)。

## <a name="powershell-api-reference"></a>PowerShell API 参考
- [ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>故障排除
- [故障排除](status-monitor-v2-troubleshoot.md)
- [已知问题](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>常见问题解答

- Application Insights 代理是否支持代理安装？

  *是*。 有多种方法可下载 Application Insights 代理。 如果你的计算机可以访问 internet，则可以通过使用 `-Proxy` 参数来载入 PowerShell 库。
您还可以手动下载该模块，并将其安装在您的计算机上或直接使用。
[详细说明](status-monitor-v2-detailed-instructions.md)中对每个选项进行了介绍。

- 状态监视器 v2 支持 ASP.NET Core 应用程序吗？

  *否*。 有关启用对 ASP.NET Core 应用程序的监视的说明，请参阅[ASP.NET Core 应用程序的 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)。 无需为 ASP.NET Core 的应用程序安装 StatusMonitor。 即使在 IIS 中承载 ASP.NET Core 应用程序，也是如此。

- 如何实现验证启用是否成功？

  - [ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) cmdlet 可用于验证启用是否成功。
  - 建议使用[实时指标](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)来快速确定应用是否正在发送遥测数据。

  - 你还可以使用[Log Analytics](../log-query/get-started-portal.md)列出当前发送遥测数据的所有云角色：
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>后续步骤

查看遥测：

* [探索指标](../../azure-monitor/app/metrics-explorer.md)来监视性能和使用情况。
* [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
* [将分析](../../azure-monitor/app/analytics.md)用于更高级的查询。
* [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。

添加更多遥测：

* [创建 web 测试](monitor-web-app-availability.md)，确保网站保持活动。
* [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常并启用跟踪调用。
* [将 APPLICATION INSIGHTS SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)中，以便可以插入跟踪和日志调用。

