---
title: Application Insights：语言、平台和集成 |Microsoft Docs
description: 适用于 Application Insights 的语言、平台和集成
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d87b1d0aa74263f249db2030f2366b93b8c2b421
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669822"
---
# <a name="supported-languages"></a>支持的语言

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python（预览版）](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>支持的平台和框架

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署的应用程序（无代码置备、基于代理）的检测
* [Azure VM 和 Azure 虚拟机规模集](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure 应用服务](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 适用于已处于活动状态的应用](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 云服务](../../azure-monitor/app/cloudservices.md)（包括 web 角色和辅助角色）
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>通过代码进行检测（Sdk）
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python（预览版）](../../azure-monitor/app/opencensus-python.md)
* [通用 Windows 应用](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows 桌面应用程序、服务和辅助角色](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>记录框架
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net、NLog 或 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash 插件](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>导出和数据分析
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [流分析](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>不受支持的 SDK
我们注意到，还有其他几个社区支持的 Sdk。 但是，Azure Monitor 仅在使用本页上列出的支持 Sdk 时提供支持。 我们正在不断地评估增加对其他语言的支持的机会，因此请按照[GitHub 公告](https://github.com/microsoft/ApplicationInsights-Announcements/issues)页面，接收最新的 SDK 新闻。 
