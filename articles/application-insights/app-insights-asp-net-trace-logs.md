---
title: 在 Application Insights 中查看日志浏览 .NET 跟踪日志
description: 搜索使用 Trace、NLog 或 Log4Net 生成的日志。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: 5ffb758fe5fa42be6323de06afbfb38068ae1926
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296163"
---
# <a name="explore-net-trace-logs-in-application-insights"></a>在 Application Insights 中查看日志浏览 .NET 跟踪日志
如果针对 ASP.NET 应用程序中的诊断跟踪使用了 NLog、log4Net 或 System.Diagnostics.Trace，请将日志发送到 [Azure Application Insights][start]，然后可在其中浏览和搜索日志。 日志将与来自应用程序的其他遥测合并，以便可以标识与处理每个用户请求关联的跟踪，并将它们与其他事件和异常报告相关联。

> [!NOTE]
> 是否需要日志捕获模块？ 它是非常适用于第三方记录器的适配器，但是如果未使用 NLog、log4Net 或 System.Diagnostics.Trace，只需考虑直接调用 [Application Insights TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace)。
>
>

## <a name="install-logging-on-your-app"></a>在应用上安装记录
在项目中安装所选的记录框架。 这应使项目出现在 app.config 或 web.config 中。

如果使用的是 System.Diagnostics.Trace，需要将项目添加到 web.config：

```XML

    <configuration>
     <system.diagnostics>
       <trace autoflush="false" indentsize="4">
         <listeners>
           <add name="myListener"
             type="System.Diagnostics.TextWriterTraceListener"
             initializeData="TextWriterOutput.log" />
           <remove name="Default" />
         </listeners>
       </trace>
     </system.diagnostics>
   </configuration>
```
## <a name="configure-application-insights-to-collect-logs"></a>配置 Application Insights 收集日志
如果尚未执行此操作，**[向项目添加 Application Insights](app-insights-asp-net.md)**。 会看到一个选项以包括日志收集器。

或者通过在解决方案资源管理器中右键单击项目**配置 Application Insights**。 选择“配置跟踪集合”选项。

*没有 Application Insights 菜单或日志收集器选项？* 尝试[故障排除](#troubleshooting)。

## <a name="manual-installation"></a>手动安装
如果项目类型（例如 Windows 桌面项目）不受 Application Insights 安装程序支持，请使用此方法。

1. 如果计划使用 log4Net 或 NLog，请在项目中安装它。
2. 在解决方案资源管理器中，右键单击项目并选择“管理 NuGet 包”。
3. 搜索“Application Insights”
4. 选择相应的程序包 - 以下各项之一：

   * Microsoft.ApplicationInsights.TraceListener（用于捕获 System.Diagnostics.Trace 调用）
   * Microsoft.ApplicationInsights.EventSourceListener（用于捕获 EventSource 事件）
   * Microsoft.ApplicationInsights.EtwListener（用于捕获 ETW 事件）
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

NuGet 包安装必要的程序集，并且还修改 web.config 或 app.config。

## <a name="insert-diagnostic-log-calls"></a>插入诊断日志调用
如果使用 System.Diagnostics.Trace，则典型的调用将是：

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

如果首选 log4net 或 NLog：

    logger.Warn("Slow response - database01");

## <a name="using-eventsource-events"></a>使用 EventSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件。 首先，安装 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 包。 然后编辑 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 文件的 `TelemetryModules` 部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

可对每个资源设置以下参数：
 * `Name` 指定要收集的 EventSource 的名称。
 * `Level` 指定要收集的日志记录级别。 可为 `Critical`、`Error`、`Informational`、`LogAlways`、`Verbose`、`Warning` 中的一个。
 * `Keywords`（可选）指定要使用的关键字组合的整数值。

## <a name="using-diagnosticsource-events"></a>使用 DiagnosticSource 事件
可配置要作为跟踪发送到 Application Insights 的 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 事件。 首先，安装 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 包。 然后编辑 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 文件的 `TelemetryModules` 部分。

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

对于要跟踪的每个 DiagnosticSource，请在 DiagnosticSource 名称中添加包含 `Name` 属性集的项。

## <a name="using-etw-events"></a>使用 ETW 事件
可配置要作为跟踪发送到 Application Insights 的 ETW 事件。 首先，安装 `Microsoft.ApplicationInsights.EtwCollector` NuGet 包。 然后编辑 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 文件的 `TelemetryModules` 部分。

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
 * `ProviderName` 是要收集的 ETW 提供程序的名称。
 * `ProviderGuid` 指定要收集的 ETW 提供程序的 GUID，可用于代替 `ProviderName`。
 * `Level` 设置要收集的日志记录级别。 可为 `Critical`、`Error`、`Informational`、`LogAlways`、`Verbose`、`Warning` 中的一个。
 * `Keywords`（可选）设置要使用的关键字组合的整数值。

## <a name="using-the-trace-api-directly"></a>直接使用跟踪 API
可以直接调用 Application Insights 跟踪 API。 日志记录适配器使用此 API。

例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

TrackTrace 的一个优势是可将相对较长的数据放置在消息中。 例如，可在此处对 POST 数据进行编码。

此外，可向消息添加严重性级别。 并像其他遥测一样，可以添加用于帮助筛选或搜索不同跟踪集的属性值。 例如：

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

这样便可以在[“搜索”][diagnostic]中轻松筛选出与特定数据库相关的所有特定严重性级别的消息。

## <a name="explore-your-logs"></a>浏览日志
在调试模式下运行应用，或者实时部署它。

在 [Application Insights 门户][portal]中应用的概述边栏选项卡中，选择 [搜索][diagnostic]。

![在 Application Insights 中，选择“搜索”](./media/app-insights-asp-net-trace-logs/020-diagnostic-search.png)

![搜索](./media/app-insights-asp-net-trace-logs/10-diagnostics.png)

例如，可以：

* 对日志跟踪或具有特定属性的项目进行筛选
* 检查详细信息中的特定项。
* 查找与同一用户请求相关的其他遥测（即，具有相同的 OperationId）
* 将此页面的配置另存为收藏夹

> [!NOTE]
> **采样。** 如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则自适应采样功能可以正常运行，只发送一部分遥测数据。 [了解有关采样的详细信息。](app-insights-sampling.md)
>
>

## <a name="next-steps"></a>后续步骤
[ASP.NET 中的诊断故障和异常][exceptions]

[了解有关搜索的详细信息][diagnostic]。

## <a name="troubleshooting"></a>故障排除
### <a name="how-do-i-do-this-for-java"></a>对于 Java，我该怎么做？
使用 [Java 日志适配器](app-insights-java-trace-logs.md)。

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>项目上下文菜单上没有 Application Insights 选项
* 检查 Application Insights 工具是否已安装在此开发计算机上。 在 Visual Studio 菜单的工具、扩展和更新中，查找 Application Insights 工具。 如果它不在“已安装”选项卡中，请打开“联机”选项卡并安装它。
* 这可能是 Application Insights 工具不支持的项目类型。 使用[手动安装](#manual-installation)。

### <a name="no-log-adapter-option-in-the-configuration-tool"></a>配置工具中没有日志适配器选项
* 需要先安装记录框架。
* 如果使用的是 System.Diagnostics.Trace，请确保[已在 `web.config` 中配置它](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx)。
* 是否已获得最新版本的 Application Insights？ 在 Visual Studio“工具”菜单中，选择“扩展和更新”，并打开“更新”选项卡。如果开发人员分析工具在此处，请单击以更新它。

### <a name="emptykey"></a>遇到错误“检测密钥不能为空”
看起来你在未安装 Application Insights 的情况下安装了日志记录适配器 Nuget 包。

在解决方案资源管理器中，右键单击 `ApplicationInsights.config` 并选择“更新 Application Insights”。 将得到一个对话框，邀请你登录 Azure 并创建 Application Insights 资源或重复使用现有资源。 这应当解决此问题。

### <a name="i-can-see-traces-in-diagnostic-search-but-not-the-other-events"></a>我可以看到诊断搜索中的跟踪，但看不到其他事件。
有时，所有事件和请求都通过管道可能需要一些时间。

### <a name="limits"></a>保留多少数据？
有多个因素会影响保留的数据量。 有关详细信息，请参阅客户事件指标页的[限制](app-insights-api-custom-events-metrics.md#limits)部分。 

### <a name="im-not-seeing-some-of-the-log-entries-that-i-expect"></a>我没有看到预期的某些日志条目
如果应用程序发送大量数据，并且使用的是用于 ASP.NET 的 Application Insights SDK 2.0.0-beta3 或更高版本，则自适应采样功能可以正常运行，只发送一部分遥测数据。 [了解有关采样的详细信息。](app-insights-sampling.md)

## <a name="add"></a>后续步骤
* [设置可用性和响应能力测试][availability]
* [故障排除][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
