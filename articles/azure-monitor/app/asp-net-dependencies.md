---
title: 在 Azure Application Insights 中跟踪依赖项 | Microsoft Docs
description: 从你的本地或 Microsoft Azure web 应用程序使用 Application Insights 监视依赖项调用。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: 479b810c5a66917bde5754d32991fb489ea26c9b
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299276"
---
# <a name="dependency-tracking-in-azure-application-insights"></a>在 Azure Application Insights 中跟踪依赖项 

*依赖项*是应用调用的外部组件。 它通常是使用 HTTP、数据库或文件系统调用的服务。 [Application Insights](../../azure-monitor/app/app-insights-overview.md)测量的依赖项调用持续时间是否其失败与否，以及类似名称的依赖关系的其他信息，等等。 可以调查特定的依赖项调用，并将它们与请求和异常相关联。

## <a name="automatically-tracked-dependencies"></a>自动跟踪依赖项

.NET 和.NET Core 附随的 application Insights Sdk`DependencyTrackingTelemetryModule`这是依赖项将自动收集的遥测模块。 为自动启用此依赖项收集[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)并[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)应用程序中，根据链接的官方文档配置。`DependencyTrackingTelemetryModule`是否为随附[这](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)NuGet 包，并使用 NuGet 包时自动变为`Microsoft.ApplicationInsights.Web`或`Microsoft.ApplicationInsights.AspNetCore`。

 `DependencyTrackingTelemetryModule` 当前自动跟踪以下依赖项：

|依赖项 |详细信息|
|---------------|-------|
|Http/Https | 本地或远程 http/https 调用 |
|WCF 调用| 如果使用基于 Http 的绑定将只跟踪自动。|
|SQL | 使用进行的调用`SqlClient`。 请参阅[这](##advanced-sql-tracking-to-get-full-sql-query)用于捕获 SQL 查询。  |
|[Azure 存储 （Blob、 表、 队列）](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 存储客户端进行的调用。 |
|[事件中心客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 版及更高版本。 |
|[ServiceBus 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3.0.0 版及更高版本。 |
|Azure Cosmos DB | 如果使用 HTTP/HTTPS，仅跟踪自动。 Application Insights 不会捕获 TCP 模式。 |


## <a name="setup-automatic-dependency-tracking-in-console-apps"></a>安装程序自动跟踪在控制台应用程序中的依赖项

若要从.NET/.NET Core 控制台应用程序会自动跟踪依赖项，安装 Nuget 包`Microsoft.ApplicationInsights.DependencyCollector`，并初始化`DependencyTrackingTelemetryModule`，如下所示：

```csharp
    DependencyTrackingTelemetryModule depModule = new DependencyTrackingTelemetryModule();
    depModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="how-automatic-dependency-monitoring-works"></a>如何自动依赖项监视正常工作？

使用以下方法之一自动收集依赖项：

* 使用选择的方法周围的字节代码检测。 (InstrumentationEngine 从 StatusMonitor 或 Azure Web 应用扩展)
* EventSource 回调
* DiagnosticSource 回调 （在最新的.NET/.NET Core Sdk)

## <a name="manually-tracking-dependencies"></a>手动跟踪依赖关系

以下是一些示例的依赖项，它不会自动收集，并因此需要手动跟踪。

* 仅当使用 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking) 时，才会自动跟踪 Azure Cosmos DB。 Application Insights 不会捕获 TCP 模式。
* Redis

对于 SDK 不会自动收集这些依赖项，你可以跟踪它们使用手动[TrackDependency API](api-custom-events-metrics.md#trackdependency)由标准自动收集模块。

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

或者，`TelemetryClient`提供的扩展方法`StartOperation`并`StopOperation`其可用于手动跟踪依赖项，如所示[此处](custom-operations-tracking.md#outgoing-dependencies-tracking)

如果你想要关闭标准依赖项跟踪模块，删除对 DependencyTrackingTelemetryModule 中的引用[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)为 ASP.NET 应用程序。 对于 ASP.NET Core 应用程序，按照说明进行操作[此处](asp-net-core.md#configuring-or-removing-default-telemetrymodules)。

## <a name="tracking-ajax-calls-from-web-pages"></a>跟踪来自网页的 AJAX 调用

对于 web 页面，Application Insights JavaScript SDK 自动收集 AJAX 调用作为依赖项所述[此处](javascript.md#ajax-performance)。 本文档重点介绍从服务器组件的依赖项。

## <a name="advanced-sql-tracking-to-get-full-sql-query"></a>高级 SQL 跟踪以获取完整的 SQL 查询

对于 SQL 调用，服务器和数据库的名称始终收集并存储为名称的收集`DependencyTelemetry`。 没有名为数据，可以包含完整的 SQL 查询文本的附加字段。

对于 ASP.NET Core 应用程序，没有完整的 SQL 查询所需任何额外的步骤。

对于 ASP.NET 应用程序，完整 SQL 查询收集字节代码检测，这需要检测引擎的帮助。 其他特定于平台的步骤，如下所述是必需的。

| 平台 | 若要获取完整的 SQL 查询所需的步骤 |
| --- | --- |
| Azure Web 应用 |在 web 应用控件面板中，[打开 Application Insights 边栏选项卡](../../azure-monitor/app/azure-web-apps.md)并启用在.NET 下的 SQL 命令 |
| IIS 服务器 （Azure VM、 在本地，依次类推。） | [在应用程序正在运行的服务器上安装状态监视器](../../azure-monitor/app/monitor-performance-live-website-now.md)并重新启动 IIS。
| Azure 云服务 |[使用启动任务](../../azure-monitor/app/cloudservices.md)到[安装状态监视器](monitor-performance-live-website-now.md#download) |
| IIS Express | 不支持

在上述情况下，验证该检测引擎的正确方式是正确安装了它是通过验证的 SDK 版本收集`DependencyTelemetry`是 rddp。 rdddsd 或 rddf 指示依赖项收集通过 DiagnosticSource 或 EventSource 回调，并因此不会捕获完整 SQL 查询。

## <a name="where-to-find-dependency-data"></a>在何处查找依赖项数据

* [应用程序地图](app-map.md)直观显示应用与相邻组件之间的依赖关系。
* [事务诊断](transaction-diagnostics.md)显示统一、 相关服务器数据。
* [浏览器边栏选项卡](javascript.md#ajax-performance)显示从用户浏览器发出的 AJAX 调用。
* 单击缓慢或失败的请求可以检查其依赖性调用。
* [Analytics](#analytics) 可用于查询依赖性数据。

## <a name="diagnosis"></a>诊断慢速请求

每个请求事件是应用处理请求时跟踪到的依赖项调用、异常和其他事件相关联。 因此如果某些请求格式不执行操作，您可以找出是否是由于某个依赖项的响应速度慢。

让我们演练一个相关的示例。

### <a name="tracing-from-requests-to-dependencies"></a>从发往依赖项的请求开始跟踪

打开“性能”边栏选项卡，并查看请求网格：

![包含平均值和计数的请求列表](./media/asp-net-dependencies/02-reqs.png)

上面的那个时间长。 让我们看看是否可以查明花费时间的部分。

单击该行，查看单独的请求事件：

![请求事件的列表](./media/asp-net-dependencies/03-instances.png)

单击任何一个长时间运行的实例以做进一步调查，并向下滚动到与此请求相关的远程依赖项调用：

![查找对远程依赖项的调用，标识异常持续时间](./media/asp-net-dependencies/04-dependencies.png)

似乎处理此请求的大部分时间都花在对本地服务的调用上。

选择该行，获取详细信息：

![单击该远程依赖项，标识原因](./media/asp-net-dependencies/05-detail.png)

看起来此依赖项是问题所在。 我们已经查明了问题，现在只需调查该调用为何花费了这么长时间。

### <a name="request-timeline"></a>请求时间线

在不同的情况下，依赖项调用的持续时间都不是很长。 但切换到时间线视图后，可以发现内部进程中发生的延迟：

![查找对远程依赖项的调用，标识异常持续时间](./media/asp-net-dependencies/04-1.png)

似乎在第一次依赖项调用后经过了一个较长的时间间隔，我们应在代码中调查原因是什么。

### <a name="profile-your-live-site"></a>分析实时站点

不知道时间花到哪去了？ [Application Insights 探查器](../../azure-monitor/app/profiler.md)跟踪 HTTP 调用对实时站点，并显示您在代码中花费了最长时间的函数。

## <a name="failed-requests"></a>失败的请求

失败的请求还可能与依赖项的失败调用相关联。 同样，我们可以单击相应的项来跟踪问题。

![单击失败的请求图表](./media/asp-net-dependencies/06-fail.png)

单击某个失败的请求，并查看其关联的事件。

![单击请求类型、单击实例以访问同一实例的不同视图、单击它以获取异常详细信息。](./media/asp-net-dependencies/07-faildetail.png)

## <a name="analytics"></a>分析

可以跟踪 [Kusto 查询语言](/azure/kusto/query/)中的依赖项。 下面是一些示例。

* 查找所有失败的依赖项调用：

```

    dependencies | where success != "True" | take 10
```

* 查找 AJAX 调用：

```

    dependencies | where client_Type == "Browser" | take 10
```

* 查找与请求关联的依赖项调用：

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 查找与页面视图关联的 AJAX 调用：

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```

## <a name="video"></a>视频

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-does-automatic-dependency-collector-report-failed-calls-to-dependencies"></a>*如何执行自动依赖项收集器报告无法调用依赖项？*

* 失败的依赖项调用将会有成功字段设置为 False。 `DependencyTrackingTelemetryModule` 不会报告`ExceptionTelemetry`。 依赖项的完整数据模型所述[此处](data-model-dependency-telemetry.md)。

## <a name="open-source-sdk"></a>开源 SDK
每个 Application Insights SDK，如依赖项集合模块也是开源的。 阅读和参与代码，或报告问题[官方 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet-server)。


## <a name="next-steps"></a>后续步骤

* [异常](../../azure-monitor/app/asp-net-exceptions.md)
* [用户和页面数据](../../azure-monitor/app/javascript.md)
* [可用性](../../azure-monitor/app/monitor-web-app-availability.md)
