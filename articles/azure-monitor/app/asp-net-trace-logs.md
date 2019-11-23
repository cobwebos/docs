---
title: 在 Application Insights 中浏览 .NET 跟踪日志
description: 搜索 Trace、NLog 或 Log4Net 生成的日志。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/08/2019
ms.openlocfilehash: 352e31e2a2f1a88a33e82134460e6df0911dbd2e
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677636"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>在 Application Insights 中浏览 .NET/.NET Core 和 Python 跟踪日志

将 ILogger、NLog、log4Net 或 System.Diagnostics.Trace 生成的 ASP.NET/ASP.NET Core 应用程序诊断跟踪日志发送到 [Azure Application Insights][start]。 对于 Python 应用程序，在用于 Azure Monitor 的 OpenCensus Python 中使用 AzureLogHandler 发送诊断跟踪日志。 然后，可以浏览和搜索这些日志。 这些日志将与应用程序中的其他日志文件合并，因此，你可以识别与每个用户请求关联的跟踪，并将其关联到其他事件和异常报告。

> [!NOTE]
> 是否需要日志捕获模块？ 它是第三方记录器的有用适配器。 但如果你未使用 NLog、log4Net 或 System.Diagnostics.Trace，只需考虑直接调用 [**Application Insights TrackTrace()** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)。
>
>
## <a name="install-logging-on-your-app"></a>在应用上安装记录
在项目中安装所选的日志记录框架，这应该会在 app.config 或 web.config 中生成一个条目。

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
[将 Application Insights 添加到项目](../../azure-monitor/app/asp-net.md)（如果尚未这样做）。 会看到一个选项以包括日志收集器。

或者在解决方案资源管理器中右键单击项目并选择“配置 Application Insights”。 选择“配置跟踪集合”选项。

> [!NOTE]
> 未看到 Application Insights 菜单或日志收集器选项？ 尝试[故障排除](#troubleshooting)。

## <a name="manual-installation"></a>手动安装
如果项目类型（例如 Windows 桌面项目）不受 Application Insights 安装程序支持，请使用此方法。

1. 如果计划使用 log4Net 或 NLog，请在项目中安装它。
2. 在解决方案资源管理器中右键单击项目，然后选择“管理 NuGet 包”。
3. 搜索“Application Insights”。
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

NuGet 包会安装必要的程序集，并在适用情况下修改 web.config 或 app.config。

## <a name="ilogger"></a>ILogger

有关将 Azure Application Insights ILogger 实现与控制台应用程序及 ASP.NET Core 配合使用的示例，请参阅 [.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入诊断日志调用
如果使用 System.Diagnostics.Trace，则典型的调用将是：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果首选 log4net 或 NLog，请使用：

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>使用 EventSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件。 首先，安装 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 包。 然后编辑 `TelemetryModules`ApplicationInsights.config[ 文件的 ](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * **Name** 指定要收集的 EventSource 的名称。
 * **Level**指定要收集的日志记录级别：*严重*、*错误*、*信息性*、 *LogAlways*、*详细*或*警告*。
 * **Keywords**（可选）指定要使用的关键字组合的整数值。

## <a name="use-diagnosticsource-events"></a>使用 DiagnosticSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件。 首先，安装 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 包。 然后编辑 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 文件的“TelemetryModules”节。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

对于要跟踪的每个 DiagnosticSource，请添加一个 **Name** 属性设置为 DiagnosticSource 名称的条目。

## <a name="use-etw-events"></a>使用 ETW 事件
可将 Windows 事件跟踪 (ETW) 事件配置为以跟踪的形式发送到 Application Insights。 首先，安装 `Microsoft.ApplicationInsights.EtwCollector` NuGet 包。 然后编辑 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 文件的“TelemetryModules”节。

> [!NOTE] 
> 仅当托管 SDK 的进程以“性能日志用户”或“管理员”成员身份运行时，才可以收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * **ProviderName** 是要收集的 ETW 提供程序的名称。
 * **ProviderGuid** 指定要收集的 ETW 提供程序的 GUID。 它可以代替 `ProviderName`。
 * **Level** 设置要收集的日志记录级别。 值可为 *Critical*、*Error*、*Informational*、*LogAlways*、*Verbose* 或 *Warning*。
 * **Keywords**（可选）设置要使用的关键字组合的整数值。

## <a name="use-the-trace-api-directly"></a>直接使用跟踪 API
可以直接调用 Application Insights 跟踪 API。 日志记录适配器使用此 API。

例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的一个优势是可将相对较长的数据放置在消息中。 例如，可在此处对 POST 数据进行编码。

还可向消息添加严重性级别。 此外，像其他遥测一样，可以添加属性值以帮助筛选或搜索不同的跟踪集。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

这样便可以在[搜索][diagnostic]中轻松筛选出与特定数据库相关的所有特定严重性级别的消息。

## <a name="azureloghandler-for-opencensus-python"></a>用于 OpenCensus Python 的 AzureLogHandler
使用 Azure Monitor 日志处理程序可以将 Python 日志导出到 Azure Monitor。

使用用于 Azure Monitor 的 [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md) 检测应用程序。

此示例演示如何将警告级别日志发送到 Azure Monitor。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>浏览日志
在调试模式下运行应用，或者实时部署它。

在 [Application Insights 门户][portal]中应用的概述窗格中，选择[“搜索”][diagnostic]。

例如，可以：

* 对日志跟踪或具有特定属性的项目进行筛选。
* 检查详细信息中的特定项。
* 找到与同一用户请求（具有相同的 OperationId）相关的其他系统日志数据。
* 将页面的配置保存到收藏夹。

> [!NOTE]
>如果应用程序发送大量数据，并且使用的是 Application Insights SDK for ASP.NET 2.0.0-beta3 或更高版本，则*自适应采样*功能可能会正常运行，但只发送一部分遥测数据。 [了解有关采样的详细信息。](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>故障排除
### <a name="how-do-i-do-this-for-java"></a>对于 Java，我该怎么做？
使用 [Java 日志适配器](../../azure-monitor/app/java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>项目上下文菜单上没有 Application Insights 选项
* 确保 Developer Analytics Tools 已安装在开发计算机上。 在 Visual Studio 的“工具” **“扩展和更新”中，找到“Developer Analytics Tools”。**  >  如果它不在“已安装”选项卡中，请打开“联机”选项卡并安装它。
* 这可能是 Developer Analytics Tools 不支持的项目类型。 使用[手动安装](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>配置工具中没有日志适配器选项
* 先安装日志记录框架。
* 如果使用 System.Diagnostics.Trace，请确保[在 *web.config* 中对其进行配置](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)。
* 确保已安装最新版本的 Application Insights。 在 Visual Studio 中，中转到 "**工具**" > "**扩展和更新**"，并打开 "**更新**" 选项卡。如果**Developer Analytics Tools**存在，请选择它以对其进行更新。

### <a name="emptykey"></a>我收到 "检测密钥不能为空" 错误消息
你可能在未安装 Application Insights 的情况下安装了日志记录适配器 Nuget 包。 在解决方案资源管理器中，右键单击“ApplicationInsights.config”并选择“更新 Application Insights”。 系统会提示你登录到 Azure 并创建 Application Insights 资源，或重复使用现有的资源。 遵照提示应该可以解决问题。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我在诊断搜索中可以看到跟踪，但看不到其他事件
所有事件和请求都通过管道可能需要花费一段时间。

### <a name="limits"></a>保留多少数据？
有多个因素会影响保留的数据量。 有关详细信息，请参阅客户事件指标页的[限制](../../azure-monitor/app/api-custom-events-metrics.md#limits)部分。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>有些日志条目未按预期出现
如果应用程序发送大量数据，并且使用的是 Application Insights SDK for ASP.NET 2.0.0-beta3 或更高版本，则自适应采样功能可能会正常运行，但只发送一部分遥测数据。 [了解有关采样的详细信息。](../../azure-monitor/app/sampling.md)

## <a name="add"></a>后续步骤

* [ASP.NET 中的诊断故障和异常][exceptions]
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