---
title: 在 Azure Application Insights 中跟踪依赖项 | Microsoft Docs
description: 使用 Application Insights 监视来自本地或 Microsoft Azure Web 应用程序的依赖项调用。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 2b7a20731fa5eae8313adcf07d877626fcaa4dce
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980841"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>在 Azure Application Insights 中跟踪依赖项 

*依赖项*是应用程序调用的外部组件。 它通常是使用 HTTP、数据库或文件系统调用的服务。 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 可以度量依赖项调用的持续时间、调用结果是否失败，以及依赖项名称等附加信息。 可以调查特定的依赖项调用，并将其与请求和异常相关联。

## <a name="automatically-tracked-dependencies"></a>自动跟踪的依赖项

适用于 .NET 和 .NET Core 的 Application Insights SDK 随附了 `DependencyTrackingTelemetryModule`：一个自动收集依赖项的遥测模块。 根据链接的官方文档进行配置后，将自动为 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 和 [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 应用程序启用此依赖项收集功能。`DependencyTrackingTelemetryModule` 作为[此](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) NuGet 包附送，使用 NuGet 包 `Microsoft.ApplicationInsights.Web` 或 `Microsoft.ApplicationInsights.AspNetCore` 时会自动打开它。

 `DependencyTrackingTelemetryModule` 目前会自动跟踪以下依赖项：

|依赖项 |详细信息|
|---------------|-------|
|Http/Https | 本地或远程 http/https 调用 |
|WCF 调用| 仅当使用基于 Http 的绑定时，才会自动跟踪。|
|SQL | 使用 `SqlClient` 发出的调用。 请参阅[此文](#advanced-sql-tracking-to-get-full-sql-query)来捕获 SQL 查询。  |
|[Azure 存储（Blob、表、队列）](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 存储客户端发出的调用。 |
|[事件中心客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 和更高版本。 |
|[ServiceBus 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 和更高版本。 |
|Azure Cosmos DB | 仅当使用 HTTP/HTTPS 时，才会自动跟踪。 Application Insights 不会捕获 TCP 模式。 |

如果缺少依赖项，或使用不同的 SDK，请确保它位于[自动收集的依赖项](https://docs.microsoft.com/azure/application-insights/auto-collect-dependencies)的列表中。 如果依赖关系不是自动收集的，则仍可以使用[跟踪依赖项调用](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)手动跟踪。

## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>在控制台应用中设置自动依赖项跟踪

若要从 .NET 控制台应用自动跟踪依赖项，请安装`Microsoft.ApplicationInsights.DependencyCollector`Nuget 包， `DependencyTrackingTelemetryModule`并按如下所示进行初始化：

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

对于 .NET Core 控制台应用 TelemetryConfiguration 已过时。 请参阅[辅助角色服务文档](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)和[ASP.NET Core 监视文档](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)中的指南

### <a name="how-automatic-dependency-monitoring-works"></a>自动依赖项监视的工作原理

使用以下方法之一自动收集依赖项：

* 围绕 select 方法使用字节代码检测。 （StatusMonitor 或 Azure Web 应用扩展中的 InstrumentationEngine）
* EventSource 回调
* 最新 .NET/.NET Core SDK 中的 DiagnosticSource 回调

## <a name="manually-tracking-dependencies"></a>手动跟踪依赖项

下面是不会自动收集的一些依赖项示例，需要手动跟踪它们。

* 仅当使用 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) 时，才会自动跟踪 Azure Cosmos DB。 Application Insights 不会捕获 TCP 模式。
* Redis

对于 SDK 不会自动收集的依赖项，可以通过标准自动收集模块使用的 [TrackDependency API](api-custom-events-metrics.md#trackdependency) 手动跟踪它们。

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

或者，可以使用 `TelemetryClient` 提供的扩展方法 `StartOperation` 和 `StopOperation` 来手动跟踪依赖项，如[此处](custom-operations-tracking.md#outgoing-dependencies-tracking)所示

若要关闭标准依赖项跟踪模块，请在 ASP.NET 应用程序的 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 中删除对 DependencyTrackingTelemetryModule 的引用。 对于 ASP.NET Core 应用程序，请遵照[此处](asp-net-core.md#configuring-or-removing-default-telemetrymodules)的说明操作。

## <a name="tracking-ajax-calls-from-web-pages"></a>跟踪来自网页的 AJAX 调用

对于网页，Application Insights JavaScript SDK 以依赖项的形式自动收集 AJAX 调用。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>使用高级 SQL 跟踪获取完整的 SQL 查询

对于 SQL 调用，始终会收集服务器和数据库的名称，并将其存储为收集的 `DependencyTelemetry` 的名称。 有一个名为“data”的附加字段，其中可以包含完整的 SQL 查询文本。

对于 ASP.NET Core 应用程序，无需执行额外的步骤即可获取完整的 SQL 查询。

对于 ASP.NET 应用程序，将使用需要检测引擎或[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 包而不是 SqlClient 库的字节代码检测帮助收集完整的 SQL 查询。 这需要执行其他特定于平台的步骤，如下所述。

| 平台 | 获取完整 SQL 查询所要执行的步骤 |
| --- | --- |
| Azure Web 应用 |在 Web 应用控制面板中，[打开“Application Insights”边栏选项卡](../../azure-monitor/app/azure-web-apps.md)并启用“.NET”下的“SQL 命令” |
| IIS 服务器（Azure VM、本地服务器，等等。） | 使用[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 包，或使用状态监视器 PowerShell 模块[安装检测引擎](../../azure-monitor/app/status-monitor-v2-api-reference.md)，并重新启动 IIS。 |
| Azure 云服务 | 添加[启动任务以安装 StatusMonitor](../../azure-monitor/app/cloudservices.md#set-up-status-monitor-to-collect-full-sql-queries-optional) <br> 您的应用程序应在生成时通过安装[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)或[ASP.NET Core 应用程序](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)的 NuGet 包载入到 applicationinsights.config SDK |
| IIS Express | 使用[SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient) NuGet 包

在上述情况下，验证是否已正确安装该检测引擎的适当方法是验证收集的 `DependencyTelemetry` 的 SDK 版本是否为“rddp”。 “rdddsd”或“rddf”表示依赖项是通过 DiagnosticSource 或 EventSource 回调收集的，因此不会捕获完整的 SQL 查询。

## <a name="where-to-find-dependency-data"></a>在何处查找依赖项数据

* [应用程序地图](app-map.md)直观显示应用与相邻组件之间的依赖关系。
* [事务诊断](transaction-diagnostics.md)显示统一的关联服务器数据。
* [“浏览器”选项卡](javascript.md)显示从用户浏览器发出的 AJAX 调用。
* 单击缓慢或失败的请求可以检查其依赖性调用。
* [Analytics](#logs-analytics) 可用于查询依赖性数据。

## <a name="diagnose-slow-requests"></a><a name="diagnosis"></a>诊断慢速请求

每个请求事件都与应用处理请求时跟踪的依赖项调用、异常和其他事件相关联。 因此，如果某些请求的执行状态不佳，可以判断其原因是否为某个依赖项的响应速度缓慢。

### <a name="tracing-from-requests-to-dependencies"></a>从发往依赖项的请求开始跟踪

打开“性能”选项卡，导航到顶部的操作旁边的“依赖项”选项卡。********

单击整个选项卡下面的某个**依赖项名称**。 选择一个依赖项后，右侧会显示该依赖项的持续时间分布图。

![在“性能”选项卡中，单击顶部的“依赖项”选项卡，然后单击图表中的某个依赖项名称](./media/asp-net-dependencies/2-perf-dependencies.png)

单击右下方的蓝色“示例”按钮，然后单击某个示例以查看端到端的事务详细信息。****

![单击示例查看端到端的事务详细信息](./media/asp-net-dependencies/3-end-to-end.png)

### <a name="profile-your-live-site"></a>分析实时站点

不知道时间花到哪去了？ [Application Insights 探查器](../../azure-monitor/app/profiler.md)跟踪对实时站点的 HTTP 调用，并显示代码中花费最长时间的函数。

## <a name="failed-requests"></a>失败的请求

失败的请求还可能与依赖项的失败调用相关联。

我们可以转到左侧的“失败”选项卡，然后单击顶部的“依赖项”选项卡。********

![单击失败的请求图表](./media/asp-net-dependencies/4-fail.png)

在此处可以查看失败的依赖项计数。 若要获取有关某个失败依赖项的更多详细信息，请尝试单击底部表中的依赖项名称。 可以单击右下方的蓝色“依赖项”按钮获取端到端的事务详细信息。****

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

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*自动依赖项收集器如何报告依赖项的失败调用？*

* 失败依赖项调用的“success”字段设置为 False。 `DependencyTrackingTelemetryModule` 不会报告 `ExceptionTelemetry`。 [此处](data-model-dependency-telemetry.md)介绍了依赖项的完整数据模型。

## <a name="open-source-sdk"></a>开源 SDK
与每个 Application Insights SDK 一样，依赖项收集模块也是开源的。 请在[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet-server)中阅读和贡献代码，或者报告问题。

## <a name="next-steps"></a>后续步骤

* [异常](../../azure-monitor/app/asp-net-exceptions.md)
* [用户和页面数据](../../azure-monitor/app/javascript.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
