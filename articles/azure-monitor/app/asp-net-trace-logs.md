---
title: 在 Application Insights 中浏览 .NET 跟踪日志
description: 由 Trace、NLog 或 Log4Net 生成的搜索日志。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: ab3b12bf0401c4060823c6ed1d20dd6385cc397f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90973842"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>在 Application Insights 中浏览 .NET/.NET Core 和 Python 跟踪日志

将 ASP.NET/ASP.NET Core 应用程序的诊断跟踪日志从 ILogger、NLog、log4Net 或 System.Diagnostics.Trace 发送到 [Azure Application Insights][start]。 对于 Python 应用程序，使用适用于 Azure Monitor 的 OpenCensus Python 中的 AzureLogHandler 发送诊断跟踪日志。 然后，可以浏览并搜索它们。 这些日志与应用程序中的其他日志文件合并，因此可以标识与每个用户请求关联的跟踪，并将它们与其他事件和异常报告关联。

> [!NOTE]
> 是否需要日志捕获模块？ 它是适用于第三方记录器的适配器。 但是如果未使用 NLog、log4Net 或 System.Diagnostics.Trace，只需考虑直接调用 [Application Insights TrackTrace()](./api-custom-events-metrics.md#tracktrace)。
>
>
## <a name="install-logging-on-your-app"></a>在应用上安装记录
在项目中安装所选的日志框架，这将导致在 app.config 或 web.config 中生成一个条目。

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
如果尚未执行此操作，请[向项目添加 Application Insights](./asp-net.md)。 会看到一个选项以包括日志收集器。

或者右键单击解决方案资源管理器中的项目以配置 Application Insights。 选择“配置跟踪集合”选项。

> [!NOTE]
> 没有 Application Insights 菜单或日志收集器选项？ 尝试[故障排除](#troubleshooting)。

## <a name="manual-installation"></a>手动安装
如果项目类型（例如 Windows 桌面项目）不受 Application Insights 安装程序支持，请使用此方法。

1. 如果计划使用 log4Net 或 NLog，请在项目中安装它。
2. 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包”.
3. 搜索“Application Insights”。
4. 选择以下包之一：

   - 对于 ILogger： [applicationinsights.config](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/) 
 [ ![ NuGet ILogger 横幅](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - 对于 NLog： [NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/) 
 [ ![ NuGet NLog 横幅](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - 对于 Log4Net： [Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/) 
 [ ![ NuGet Log4Net 横幅](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - 对于[applicationinsights.config： TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) 
 [ ![ NuGet 系统。诊断横幅](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Applicationinsights.config. DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) 
[ ![ NuGet 诊断源侦听器横幅](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Applicationinsights.config. EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) 
[ ![ NuGet Etw 收集器横幅](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Applicationinsights.config. EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) 
[ ![ NuGet 事件源侦听器横幅](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 包安装必要的程序集，并在适用情况下修改 web.config 或 app.config。

## <a name="ilogger"></a>ILogger

有关将 Azure Application Insights ILogger 实现与控制台应用程序及 ASP.NET Core 配合使用的示例，请参阅[适用于 .NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](ilogger.md)。

## <a name="insert-diagnostic-log-calls"></a>插入诊断日志调用
如果使用 System.Diagnostics.Trace，则典型的调用将是：

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

如果首选 log4net 或 NLog，请使用：

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>使用 EventSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.Tracing.EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) 事件。 首先，安装 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 包。 然后编辑 [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 文件的 `TelemetryModules` 部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * “名称”指定要收集的 EventSource 的名称。
 * “级别”指定要收集的日志记录级别：严重、错误、信息性、LogAlways、详细或警告     。
 * “关键字”（可选）指定要使用的关键字组合的整数值。

## <a name="use-diagnosticsource-events"></a>使用 DiagnosticSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件。 首先，安装 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 包。 然后编辑 [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 文件的“TelemetryModules”部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

对于要跟踪的每个 DiagnosticSource，请在 DiagnosticSource 名称中添加包含“名称”属性集的项。

## <a name="use-etw-events"></a>使用 ETW 事件
可配置要作为跟踪发送到 Application Insights 的 Windows 事件跟踪 (ETW) 事件。 首先，安装 `Microsoft.ApplicationInsights.EtwCollector` NuGet 包。 然后编辑 [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 文件的“TelemetryModules”部分。

> [!NOTE] 
> 托管 SDK 的进程以“性能日志用户”或管理员成员身份运行时才可收集 ETW 事件。

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * “ProviderName”是要收集的 ETW 提供程序的名称。
 * “ProviderGuid”指定要收集的 ETW 提供程序的 GUID。 可以使用它，而不是使用 `ProviderName`。
 * “级别”设置要收集的日志记录级别。 它可以是“严重”、“错误”、“信息性”、“LogAlways”、“详细”或“警告”     。
 * “关键字”（可选）设置要使用的关键字组合的整数值。

## <a name="use-the-trace-api-directly"></a>直接使用跟踪 API
可以直接调用 Application Insights 跟踪 API。 日志记录适配器使用此 API。

例如：

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow response - database01");
```

TrackTrace 的一个优势是可将相对较长的数据放置在消息中。 例如，可在此处对 POST 数据进行编码。

还可向消息添加严重性级别。 并像其他遥测一样，可以添加属性值以帮助筛选或搜索不同跟踪集。 例如：

  ```csharp
  var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
  telemetry.TrackTrace("Slow database response",
                 SeverityLevel.Warning,
                 new Dictionary<string,string> { {"database", db.ID} });
  ```

这样便可以在[搜索][diagnostic]中轻松筛选出与特定数据库相关的所有特定严重性级别的消息。

## <a name="azureloghandler-for-opencensus-python"></a>适用于 OpenCensus Python 的 AzureLogHandler
Azure Monitor 日志处理程序允许将 Python 日志导出到 Azure Monitor。

使用适用于 Azure Monitor 的 [OpenCensus Python SDK](./opencensus-python.md) 检测应用程序。

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

在 [Application Insights 门户][portal]中的应用的概述窗格中，选择[搜索][diagnostic]。

例如，可以：

* 对日志跟踪或具有特定属性的项目进行筛选。
* 检查详细信息中的特定项。
* 查找与相同用户请求（具有相同的操作 ID）相关的其他系统日志数据。
* 将页面的配置另存为收藏夹。

> [!NOTE]
>如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则*自适应采样*功能可以正常运行，只发送一部分遥测数据。 [了解有关采样的详细信息。](./sampling.md)
>

## <a name="troubleshooting"></a>疑难解答
### <a name="how-do-i-do-this-for-java"></a>对于 Java，我该怎么做？
在 Java 无代码检测（推荐）中，日志是现成收集的，使用 [Java 3.0 代理](./java-in-process-agent.md)。

如果使用的是 Java SDK，请使用 [Java 日志适配器](./java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>项目上下文菜单上没有 Application Insights 选项
* 请确保 Developer Analytics Tools 安装在开发计算机上。 在 Visual Studio“工具” > “扩展和更新”中，查找“Developer Analytics Tools”  。 如果它不在“已安装”选项卡中，请打开“联机”选项卡并安装它 。
* 这可能是 Developer Analytics Tools 不支持的项目类型。 使用[手动安装](#manual-installation)。

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>配置工具中没有日志适配器选项
* 首先安装记录框架。
* 如果使用的是 System.Diagnostics.Trace，请确保已[在 web.config 中对其进行配置](/dotnet/api/system.diagnostics.eventlogtracelistener?view=dotnet-plat-ext-3.1)。
* 确保已安装最新版本的 Application Insights。 在 Visual Studio中，转到“工具” > “扩展和更新”，并打开“更新”选项卡  。如果“Developer Analytics Tools”在此处，请选择以更新它。

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>遇到“检测密钥不能为空”错误消息
您可能安装了日志记录适配器 NuGet 包，但没有安装 Application Insights。 在解决方案资源管理器中，右键单击“ApplicationInsights.config”，然后选择“更新 Application Insights”。 系统将提示你登录到 Azure 并创建 Application Insights 资源或重复使用现有资源。 这会解决此问题。

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>我可以在诊断搜索中看到跟踪，但看不到其他事件
所有事件和请求都通过管道可能需要一些时间。

### <a name="how-much-data-is-retained"></a><a name="limits"></a>保留多少数据？
多个因素会影响保留的数据量。 有关详细信息，请参阅客户事件指标页的[限制](./api-custom-events-metrics.md#limits)部分。

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>没有看到所需的一些日志条目
如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则自适应采样功能可以正常运行，只发送一部分遥测数据。 [了解有关采样的详细信息。](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>后续步骤

* [ASP.NET 中的诊断故障和异常][exceptions]
* [了解有关搜索的详细信息][diagnostic]
* [设置可用性和响应能力测试][availability]
* [故障排除][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

