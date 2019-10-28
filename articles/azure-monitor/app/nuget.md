---
title: Azure Application Insights - 依赖项自动收集 | Microsoft Docs
description: Application Insights 自动收集和可视化依赖项
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/16/2018
ms.openlocfilehash: 1d3259c32de7f937d4e5ac8e21c8cab60b9cc635
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819169"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet 包

下面是 Application Insights NuGet 包稳定版本的当前列表。

## <a name="common-packages-for-aspnet"></a>适用于 ASP.NET 的常用包

| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | 提供用于所有 Application Insights 遥测类型传输的核心功能，且作为所有其他 Application Insights 包的依赖包 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | 可拦截方法调用 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | 适用于 .NET 应用程序的 Application Insights 依赖项收集器。 这是一个用于 Application Insights 平台专用包的依赖包，可自动收集依赖项遥测数据。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights 性能计数器，可用于将性能收集器收集的数据发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | 适用于 .NET Web 应用程序的 Application Insights | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet 包可自动收集 .NET 应用程序的 Application Insights 遥测数据。 此包可用作 Application Insights 平台专用包的依赖包，也可用作平台专用包未涵盖的 .NET 应用程序独立包（例如用于 .NET 辅助角色的包）。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | 向 Application Insights Windows Server SDK 提供遥测渠道，此 SDK 将保留脱机方案中的遥测数据。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>适用于 ASP.NET Core 的常用包

| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | 适用于 ASP.NET Core Web 应用程序的 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | 此包提供用于所有 Application Insights 遥测类型传输的核心功能，且作为所有其他 Application Insights 包的依赖包 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | 适用于 .NET 应用程序的 Application Insights 依赖项收集器。 这是一个用于 Application Insights 平台专用包的依赖包，可自动收集依赖项遥测数据。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights 性能计数器，可用于将性能收集器收集的数据发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet 包可自动收集 .NET 应用程序的 Application Insights 遥测数据。 此包可用作 Application Insights 平台专用包的依赖包，也可用作平台专用包未涵盖的 .NET 应用程序独立包（例如用于 .NET 辅助角色的包）。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | 向 Application Insights Windows Server SDK 提供遥测渠道，此 SDK 将保留脱机方案中的遥测数据。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>使用 OpenCensus 的 Python 的常用包
| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| opencensus-azure | 1.0.0 | Application Insights 通过 OpenCensus Azure Monitor 下的 Python 应用程序。 | [下载包](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-django | 0.7.2 | 此包提供与 Python [django](https://pypi.org/project/django/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-flask | iisnode-inspector-0.7.3.dll | 此包提供与 Python [flask](https://pypi.org/project/flask/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-httplib | 0.7.2 | 此包提供与 Python [http.](https://docs.python.org/3/library/http.client.html) Python3 和[httplib](https://docs.python.org/2/library/httplib.html) for Python2 的集成。 | [下载包](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-未进行日志记录 | 0.1.0 | 此包丰富日志记录和跟踪数据。 | [下载包](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-外部-mysql | 0.1.2 | 此包提供与 Python [mysql 连接器](https://pypi.org/project/mysql-connector/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-postgresql | 0.1.2 | 此包提供与 Python [psycopg2](https://pypi.org/project/psycopg2/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-pymongo | 0.7.1 | 此包提供与 Python [pymongo](https://pypi.org/project/pymongo/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-pymysql | 0.1.2 | 此包提供与 Python [PyMySQL](https://pypi.org/project/PyMySQL/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-棱锥图 | 0.7.1 | 此包提供与 Python[棱锥](https://pypi.org/project/pyramid/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-请求 | 0.7.2 | 此包提供与 Python[请求](https://pypi.org/project/requests/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-sqlalchemy | 0.1.2 | 此包提供与 Python [SQLAlchemy](https://pypi.org/project/SQLAlchemy/)库的集成。 | [下载包](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>侦听器/收集器/追加器

| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  允许将 DiagnosticSource 中的事件转发给 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener 可用于将 EventSource 事件中的数据发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector 可用于将 Windows 事件跟踪 (ETW) 中的数据发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | 自定义的 TraceListener，可用于将跟踪日志消息发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | 自定义追加器，可用于将 Log4Net 日志消息发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  自定义目标，可用于将 NLog 日志消息发送到 Application Insights。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | 监视应用程序中的异常，并自动收集快照用于脱机分析。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | 此包使用运行应用程序的 Service Fabric 上下文自动修饰遥测数据。 请不要对 Service Fabric 本机应用程序使用此 NuGet。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | 适用于 Service Fabric 应用程序的 Application Insights 模块。 此 NuGet 仅用于 Service Fabric 本机应用程序。 对于在容器中运行的应用程序，请使用 Microsoft.ApplicationInsights.ServiceFabric 包。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>状态监视器

| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  为 x64 应用程序启用运行时数据收集功能 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  为 x86 应用程序启用运行时数据收集功能。 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

这些包属于[状态监视器](../../azure-monitor/app/monitor-performance-live-website-now.md)中运行时监视的核心功能。 无需直接下载这些包，只需使用状态监视器安装程序即可。 如果要详细了解这些包如何在后台工作，请参阅我们的一位开发人员撰写的这篇[博客文章](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)。

## <a name="additional-packages"></a>其他包

| 包名称 | 稳定版本 | 描述 | 下载 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | 此扩展将在 Azure 应用服务上启用 Application Insights 监视功能。 SDK 版本 2.6.1。 说明：使用 ikey 添加“APPINSIGHTS_INSTRUMENTATIONKEY”应用程序设置，再重启 Web 应用使其生效。| [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | 此包具有在不使用代码的情况下注入 Application Insights 时所需的文件 | [下载包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>后续步骤

- 监视 [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)。
- 分析 ASP.NET Core [Azure Linux Web 应用](../../azure-monitor/app/profiler-aspnetcore-linux.md)。
- 调试 ASP.NET [快照](../../azure-monitor/app/snapshot-debugger.md)。