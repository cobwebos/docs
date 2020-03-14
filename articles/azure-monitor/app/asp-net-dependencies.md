---
title: 在 Azure Application Insights 中跟踪依赖项 | Microsoft Docs
description: 通过 Application Insights 监视本地或 Microsoft Azure web 应用程序的依赖项调用。
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 8fb1550a3f1d4b3336384139b049b60e23e648d7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276291"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>Azure 应用程序 Insights 中的依赖关系跟踪 

*依赖项*是应用程序调用的外部组件。 它通常是使用 HTTP、数据库或文件系统调用的服务。 [Application Insights](../../azure-monitor/app/app-insights-overview.md)度量依赖项调用的持续时间（不管是失败还是失败）以及附加信息（如依赖项的名称等）。 你可以调查特定的依赖项调用，并将它们与请求和异常相关联。

## <a name="automatically-tracked-dependencies"></a>自动跟踪的依赖项

适用于 .NET 和 .NET Core Application Insights Sdk 附带 `DependencyTrackingTelemetryModule`，这是一个自动收集依赖项的遥测模块。 根据链接的官方文档配置[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)和[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)应用程序时，将自动启用此依赖关系集合。`DependencyTrackingTelemetryModule` 以[此](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)nuget 包的形式提供，并且在使用任何一个 nuget 包 `Microsoft.ApplicationInsights.Web` 或 `Microsoft.ApplicationInsights.AspNetCore`时，会自动将其关闭。

 `DependencyTrackingTelemetryModule` 当前自动跟踪以下依赖项：

|依赖项 |详细信息|
|---------------|-------|
|Http/Https | 本地或远程 http/https 调用 |
|WCF 调用| 仅当使用基于 Http 的绑定时才自动跟踪。|
|SQL | 用 `SqlClient`进行的调用。 请参阅[此](#advanced-sql-tracking-to-get-full-sql-query)了解捕获 SQL 查询。  |
|[Azure 存储（Blob、表、队列）](https://www.nuget.org/packages/WindowsAzure.Storage/) | 通过 Azure 存储客户端发出的调用。 |
|[EventHub 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 版本1.1.0 和更高版本。 |
|[ServiceBus 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 版本3.0.0 和更高版本。 |
|Azure Cosmos DB | 仅在使用 HTTP/HTTPS 时自动跟踪。 Application Insights 不会捕获 TCP 模式。 |

如果缺少依赖项，或使用不同的 SDK，请确保它位于[自动收集的依赖项](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)的列表中。 如果依赖关系不是自动收集的，则仍可以使用[跟踪依赖项调用](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)手动跟踪。

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>在控制台应用程序中设置自动依赖项跟踪

若要自动跟踪 .NET/.NET Core 控制台应用中的依赖项，请 `Microsoft.ApplicationInsights.DependencyCollector`中安装 Nuget 包，并按如下所示初始化 `DependencyTrackingTelemetryModule`：

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>自动依赖项监视的工作原理

使用以下方法之一自动收集依赖项：

* 围绕 select 方法使用字节代码检测。 （InstrumentationEngine 为 StatusMonitor 或 Azure Web 应用扩展）
* EventSource 回调
* DiagnosticSource 回调（在最新的 .NET/.NET Core Sdk 中）

## <a name="manually-tracking-dependencies"></a>手动跟踪依赖项

下面是依赖项的一些示例，它们不会自动收集，因此需要手动跟踪。

* 仅当使用 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) 时，才会自动跟踪 Azure Cosmos DB。 Application Insights 不会捕获 TCP 模式。
* Redis

对于不是由 SDK 自动收集的依赖项，你可以使用标准自动收集模块使用的[TRACKDEPENDENCY API](api-custom-events-metrics.md#trackdependency)手动跟踪这些依赖项。

例如，如果使用未自行编写的程序集生成代码，可以对其所有调用进行计时，以了解它对响应时间所做的贡献。 若要使此数据显示在 Application Insights 中的依赖项图表中，请使用 `TrackDependency` 发送它。

```csharp

    var startTime = DateTime.UtcNow;
    var timer = System.Diagnostics.Stopwatch.StartNew();
    try
    {
        // making dependency call
        success = dependency.Call();
    }
    finally
    {
        timer.Stop();
        telemetryClient.TrackDependency("myDependencyType", "myDependencyCall", "myDependencyData",  startTime, timer.Elapsed, success);
    }
```

另外，`TelemetryClient` 提供 `StartOperation` 和 `StopOperation` 的扩展方法，这些方法可用于手动跟踪依赖项，[如下所示](custom-operations-tracking.md#outgoing-dependencies-tracking)

如果要关闭标准依赖项跟踪模块，请在 ASP.NET 应用程序的[applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)中删除对 DependencyTrackingTelemetryModule 的引用。 对于 ASP.NET Core 应用程序，请按照[此处](asp-net-core.md#configuring-or-removing-default-telemetrymodules)的说明进行操作。

## <a name="tracking-ajax-calls-from-web-pages"></a>跟踪网页中的 AJAX 调用

对于网页，Application Insights JavaScript SDK 会自动将 AJAX 调用作为依赖项收集。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>用于获取完整的 SQL 查询的高级 SQL 跟踪

对于 SQL 调用，始终收集服务器和数据库的名称并将其存储为所收集 `DependencyTelemetry`的名称。 还有一个名为 "data" 的字段，它可以包含完整的 SQL 查询文本。

对于 ASP.NET Core 应用程序，获取完整的 SQL 查询无需执行其他步骤。

对于 ASP.NET 应用程序，将收集完整的 SQL 查询，并附带需要检测引擎的字节代码检测。 需要其他特定于平台的步骤（如下所述）。

| 平台 | 获取完整的 SQL 查询所需的步骤 |
| --- | --- |
| Azure Web 应用 |在 web 应用控制面板中，[打开 "Application Insights" 边栏选项卡](../../azure-monitor/app/azure-web-apps.md)，并在 .net 下启用 SQL 命令。 |
| IIS 服务器（Azure VM、本地等。） | 使用状态监视器 PowerShell 模块[安装检测引擎](../../azure-monitor/app/status-monitor-v2-api-enable-instrumentation-engine.md)，并重新启动 IIS。 |
| Azure 云服务 | 添加[启动任务以安装 StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 您的应用程序应在生成时通过安装[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)或[ASP.NET Core 应用程序](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)的 NuGet 包载入到 applicationinsights.config SDK |
| IIS Express | 不支持

在上述情况下，正确安装检测引擎的正确方法是验证所收集 `DependencyTelemetry` 的 SDK 版本是否为 "rddp"。 "rdddsd" 或 "rddf" 指示依赖项是通过 DiagnosticSource 或 EventSource 回调收集的，因此不会捕获完整的 SQL 查询。

## <a name="where-to-find-dependency-data"></a>在何处查找依赖项数据

* [应用程序地图](app-map.md)直观显示应用与相邻组件之间的依赖关系。
* [事务诊断](transaction-diagnostics.md)显示统一的关联服务器数据。
* [浏览器选项卡](javascript.md)显示来自用户浏览器的 AJAX 调用。
* 单击缓慢或失败的请求可以检查其依赖性调用。
* [Analytics](#logs-analytics) 可用于查询依赖性数据。

## <a name="diagnosis"></a>诊断慢速请求

每个请求事件都与在应用处理请求时跟踪的依赖项调用、异常和其他事件相关联。 因此，如果某些请求的操作错误，你可以确定其是否是由于依赖项的响应速度缓慢导致的。

### <a name="tracing-from-requests-to-dependencies"></a>从发往依赖项的请求开始跟踪

打开 "**性能**" 选项卡，然后导航到 "操作" 旁边的 "**依赖项**" 选项卡。

单击 "总体" 下的**依赖项名称**。 选择依赖项后，将在右侧显示该依赖项的分布的关系图。

![在 "性能" 选项卡中，单击顶部的 "依赖项" 选项卡，然后单击图表中的依赖关系名称](./media/asp-net-dependencies/2-perf-dependencies.png)

单击右下方的蓝色 "**示例**" 按钮，然后单击示例以查看端到端事务详细信息。

![单击示例以查看端到端事务详细信息](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>分析实时站点

不知道时间花到哪去了？ [Application Insights 探查器](../../azure-monitor/app/profiler.md)跟踪对实时站点的 HTTP 调用，并显示代码中花费最长时间的函数。

## <a name="failed-requests"></a>失败的请求

失败的请求还可能与依赖项的失败调用相关联。

可以跳到左侧的 "**失败**" 选项卡，然后单击顶部的 "**依赖项**" 选项卡。

![单击失败的请求图表](./media/asp-net-dependencies/4-fail.png)

可在此处查看失败的依赖项计数。 若要获取有关尝试失败的详细信息，请尝试单击底部表中的依赖关系名称。 你可以单击右下方的蓝色**依赖项**按钮，以获取端到端事务详细信息。

## <a name="logs-analytics"></a>日志（分析）

可以跟踪 [Kusto 查询语言](/azure/kusto/query/)中的依赖项。 下面是一些示例。

* 查找所有失败的依赖项调用：

``` Kusto

    dependencies | where success != "True" | take 10
```

* 查找 AJAX 调用：

``` Kusto

    dependencies | where client_Type == "Browser" | take 10
```

* 查找与请求关联的依赖项调用：

``` Kusto

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 查找与页面视图关联的 AJAX 调用：

``` Kusto 

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*自动依赖项收集器如何报告对依赖项的调用失败？*

* 失败的依赖项调用会将 "成功" 字段设置为 False。 `DependencyTrackingTelemetryModule` 不报告 `ExceptionTelemetry`。 [此处](data-model-dependency-telemetry.md)描述了依赖项的完整数据模型。

## <a name="open-source-sdk"></a>开源 SDK
与每个 Application Insights SDK 一样，依赖关系收集模块也是开源的。 阅读和参与代码，或在[官方 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet-server)存储库中报告问题。

## <a name="next-steps"></a>后续步骤

* [异常](../../azure-monitor/app/asp-net-exceptions.md)
* [用户和页面数据](../../azure-monitor/app/javascript.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
