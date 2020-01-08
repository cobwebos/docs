---
title: 在 Application Insights 中浏览 .NET 跟踪日志
description: 搜索 Trace、NLog 或 Log4Net 生成的日志。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/08/2019
ms.openlocfilehash: 33dc415e06b7f49f75697abb05248750444fea7c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432646"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>在 Application Insights 中浏览 .NET/.NET Core 和 Python 跟踪日志

将 ASP.NET/ASP.NET Core 应用程序的诊断跟踪日志从 ILogger、NLog、log4Net 或发送到[Azure 应用程序 Insights][start]。 对于 Python 应用程序，请使用 AzureLogHandler 在 OpenCensus Python 中发送诊断跟踪日志，以便 Azure Monitor。 然后，你可以浏览并搜索它们。 这些日志与应用程序中的其他日志文件合并，因此，你可以标识与每个用户请求关联的跟踪，并将它们与其他事件和异常报告关联起来。

> [!NOTE]
> 是否需要日志捕获模块？ 对于第三方记录器，它是一个有用的适配器。 但如果尚未使用 NLog、log4Net 或，请考虑仅直接调用[**Application Insights TrackTrace （）** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 。
>
>
## <a name="install-logging-on-your-app"></a>在应用上安装记录
在项目中安装所选的日志记录框架，这会导致 app.config 或 web.config 中的条目。

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>配置 Application Insights 以收集日志
如果尚未执行此操作，请[将 Application Insights 添加到项目](../../azure-monitor/app/asp-net.md)。 会看到一个选项以包括日志收集器。

或者右键单击中的项目，解决方案资源管理器**配置 Application Insights**。 选择 "**配置跟踪集合**" 选项。

> [!NOTE]
> 无 Application Insights 菜单或日志收集器选项？ 尝试[故障排除](#troubleshooting)。

## <a name="manual-installation"></a>手动安装
如果项目类型（例如 Windows 桌面项目）不受 Application Insights 安装程序支持，请使用此方法。

1. 如果计划使用 log4Net 或 NLog，请在项目中安装它。
2. 在解决方案资源管理器中，右键单击你的项目，然后选择 "**管理 NuGet 包**"。
3. 搜索 "Application Insights"。
4. 选择以下包之一：

   - 对于 ILogger： [applicationinsights.config](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - 对于 NLog： [NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - 对于 Log4Net： [Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 对于[applicationinsights.config： TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 包将安装所需的程序集，并修改 web.config 或 app.config （如果适用）。

## <a name="ilogger"></a>ILogger

有关将 Application Insights ILogger 实现与控制台应用程序和 ASP.NET Core 结合使用的示例，请参阅[ApplicationInsightsLoggerProvider for .Net Core ILogger 日志](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入诊断日志调用
如果使用 System.Diagnostics.Trace，则典型的调用将是：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果希望使用 log4net 或 NLog，请使用：

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>使用 EventSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件。 首先，安装 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 包。 然后编辑 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 文件的 `TelemetryModules` 部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * **名称**指定要收集的 EventSource 的名称。
 * **Level**指定要收集的日志记录级别：*严重*、*错误*、*信息性*、 *LogAlways*、*详细*或*警告*。
 * **关键字**（可选）指定要使用的关键字组合的整数值。

## <a name="use-diagnosticsource-events"></a>使用 DiagnosticSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件。 首先，安装 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 包。 然后编辑[applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)文件的 "TelemetryModules" 部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

对于要跟踪的每个 DiagnosticSource，添加**名称**属性设置为 DiagnosticSource 名称的条目。

## <a name="use-etw-events"></a>使用 ETW 事件
你可以配置要作为跟踪发送到 Application Insights 的 Windows 事件跟踪（ETW）事件。 首先，安装 `Microsoft.ApplicationInsights.EtwCollector` NuGet 包。 然后编辑[applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)文件的 "TelemetryModules" 部分。

> [!NOTE] 
> 仅当承载 SDK 的进程在作为性能日志用户或管理员的成员的标识下运行时，才能收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * **ProviderName**是要收集的 ETW 提供程序的名称。
 * **ProviderGuid**指定要收集的 ETW 提供程序的 GUID。 它可用于代替 `ProviderName`。
 * **级别**设置要收集的日志记录级别。 它可以是*严重*、*错误*、*信息性*、 *LogAlways*、*详细*或*警告*。
 * **关键字**（可选）设置要使用的关键字组合的整数值。

## <a name="use-the-trace-api-directly"></a>直接使用跟踪 API
可以直接调用 Application Insights 跟踪 API。 日志记录适配器使用此 API。

例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的一个优势是可将相对较长的数据放置在消息中。 例如，可在此处对 POST 数据进行编码。

你还可以将严重性级别添加到消息中。 与其他遥测一样，可以添加属性值来帮助筛选或搜索不同的跟踪集。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

这使您可以轻松筛选出与特定数据库相关的特定严重级别[的所有消息][diagnostic]。

## <a name="azureloghandler-for-opencensus-python"></a>AzureLogHandler for OpenCensus Python
Azure Monitor 日志处理程序允许您将 Python 日志导出到 Azure Monitor。

通过用于 Azure Monitor 的[OpenCensus PYTHON SDK](../../azure-monitor/app/opencensus-python.md)检测应用程序。

此示例演示如何将警告等级日志发送到 Azure Monitor。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>浏览日志
在调试模式下运行你的应用程序或将其部署在一起。

在应用的 "概述" 窗格中[的 Application Insights 门户][portal]中，选择 "[搜索][diagnostic]"。

例如，可以：

* 筛选日志跟踪或具有特定属性的项。
* 检查详细信息中的特定项。
* 查找与相同用户请求（具有相同的 OperationId）相关的其他系统日志数据。
* 将页的配置保存为收藏项。

> [!NOTE]
>如果你的应用程序发送大量数据，并且你使用的是用于 ASP.NET 版本 2.0.0-beta3 或更高版本的 Application Insights SDK，则*自适应采样*功能可能会运行并仅发送部分遥测数据。 [了解有关采样的详细信息。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>故障排除
### <a name="how-do-i-do-this-for-java"></a>对于 Java，我该怎么做？
使用 [Java 日志适配器](../../azure-monitor/app/java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>项目上下文菜单上没有 Application Insights 选项
* 请确保在开发计算机上安装了 Developer Analytics Tools。 在 Visual Studio **Tools** > **扩展和更新**中，查找**Developer Analytics Tools**。 如果未在 "**已安装**" 选项卡上，请打开 "**联机**" 选项卡并安装。
* 这可能是 Devloper 分析工具不支持的项目类型。 使用[手动安装](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>配置工具中没有日志适配器选项
* 首先安装日志记录框架。
* 如果使用的是，请确保已[在 web.config 中配置](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)它。
* 请确保使用最新版本的 Application Insights。 在 Visual Studio 中，中转到 "**工具**" > "**扩展和更新**"，并打开 "**更新**" 选项卡。如果**Developer Analytics Tools**存在，请选择它以对其进行更新。

### <a name="emptykey"></a>我收到 "检测密钥不能为空" 错误消息
您可能安装了日志记录适配器 Nuget 包，但没有安装 Application Insights。 在解决方案资源管理器中，右键单击*applicationinsights.config*，然后选择 "**更新 Application Insights**"。 系统将提示你登录到 Azure，并创建 Application Insights 资源或重复使用现有资源。 这会解决此问题。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我可以在诊断搜索中看到跟踪，而不是其他事件
所有事件和请求需要一段时间才能通过管道。

### <a name="limits"></a>保留多少数据？
多个因素会影响保留的数据量。 有关详细信息，请参阅 "客户事件指标" 页的 "[限制](../../azure-monitor/app/api-custom-events-metrics.md#limits)" 部分。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>我看不到所需的一些日志条目
如果你的应用程序发送 voluminous 的数据量，并且你使用的是适用于 ASP.NET 版本 2.0.0-beta3 或更高版本的 Application Insights SDK，则自适应采样功能可能会运行，并且只发送一部分遥测数据。 [了解有关采样的详细信息。](../../azure-monitor/app/sampling.md)

## <a name="add"></a>后续步骤

* [诊断 ASP.NET 中的故障和异常][exceptions]
* [了解有关搜索的详细信息][diagnostic]
* [设置可用性和响应能力测试][availability]
* [故障排除][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
