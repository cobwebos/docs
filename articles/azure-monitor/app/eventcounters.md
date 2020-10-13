---
title: Application Insights 中的事件计数器 | Microsoft Docs
description: 监视 Application Insights 中的系统和自定义的 .NET/.NET Core EventCounters。
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a9af36f3c81ee52b41a8eed875c1a286b95bf838
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803637"
---
# <a name="eventcounters-introduction"></a>EventCounters 简介

[`EventCounter`](/dotnet/core/diagnostics/event-counters) 用于发布和使用计数器或统计信息的 .NET/.NET Core 机制。 所有 OS 平台（Windows、Linux 和 macOS）都支持 EventCounters。 可以将其视为仅在 Windows 系统中受支持的 [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) 的等效跨平台。

尽管用户可以发布任何自定义 `EventCounters` 以满足其需求，但 .Net Core 3.0 和更高版本的运行时默认情况下会发布一组这些计数器。 本文档将指导你完成 `EventCounters` 在 Azure 应用程序 Insights 中收集和查看 (系统定义的或用户定义的) 所需的步骤。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用 Application Insights 收集 EventCounters

Application Insights 支持 `EventCounters` 通过其 `EventCounterCollectionModule` （新发布的 NuGet 包 [EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)的一部分）进行收集。 使用 [AspNetCore](asp-net-core.md) 或 [WorkerService](worker-service.md) 时，将自动启用 `EventCounterCollectionModule`。 `EventCounterCollectionModule` 会以不可配置的收集频率（60 秒）收集计数器。 收集 EventCounters 时，不需要具备特殊的权限。

## <a name="default-counters-collected"></a>已收集默认计数器

从2.15.0 版本的 [ASPNETCORE sdk](asp-net-core.md) 或 [WorkerService sdk](worker-service.md)开始，默认情况下不收集任何计数器。 模块本身已启用，因此用户只需添加所需的计数器即可收集这些计数器。

若要获取由 .NET 运行时发布的已知计数器列表，请参阅 [可用计数器](/dotnet/core/diagnostics/event-counters#available-counters) 文档。

## <a name="customizing-counters-to-be-collected"></a>自定义要收集的计数器

以下示例介绍如何添加/删除计数器。 使用 `AddApplicationInsightsTelemetry()` 或 `AddApplicationInsightsWorkerService()` 启用 Application Insights 遥测收集后，将在应用程序的 `ConfigureServices` 方法中完成此自定义。 下面是 ASP.NET Core 应用程序的示例代码。 对于其他类型的应用程序，请参阅[此文档](worker-service.md#configuring-or-removing-default-telemetrymodules)。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>禁用 EventCounter 收集模块

`EventCounterCollectionModule` 可以使用禁用 `ApplicationInsightsServiceOptions` 。 下面显示了使用 ASP.NET Core SDK 的示例。

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

类似的方法也可用于 WorkerService SDK，但必须更改命名空间，如以下示例中所示。

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Metric Explorer 中的事件计数器

若要在 [Metric Explorer](../platform/metrics-charts.md) 中查看 EventCounter 指标，请选择 Application Insights 资源，然后选择基于日志的指标作为指标命名空间。 EventCounter 指标随即显示在“自定义”类别下。

> [!div class="mx-imgBorder"]
> ![Application Insights 指标资源管理器中报告的事件计数器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics 中的事件计数器

还可以在 [Analytics](../log-query/log-query-overview.md) 的“customMetrics”表中搜索和显示事件计数器报表。

例如，运行以下查询，以查看收集了哪些计数器并可用于查询：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights Analytics 中报告的事件计数器](./media/event-counters/analytics-event-counters.png)

若要在最近一段时间内获取特定计数器（例如：`ThreadPool Completed Work Item Count`）的图表，请运行以下查询。

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights 中的单个计数器的图表](./media/event-counters/analytics-completeditems-counters.png)

与其他遥测一样，customMetrics 同样也具有列 `cloud_RoleInstance`，指示正在其上运行应用的主机服务器实例的标识。 上述查询显示每个实例的计数器值，并可用于比较不同服务器实例的性能。

## <a name="alerts"></a>警报
与其他指标一样，可以[设置警报](../platform/alerts-log.md)以便在事件计数器超出指定的限制时收到警报。 打开“警报”窗格，并单击“添加警报”。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-see-eventcounters-in-live-metrics"></a>是否能在实时指标中看到 EventCounters？

实时指标目前不显示 EventCounters。 使用 Metric Explorer 或 Analytics 来查看遥测数据。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已从 Azure Web 应用门户启用 Application Insights。 但看不到 EventCounters，这是什么原因？

 ASP.NET Core 的 [Application Insights 扩展](./azure-web-apps.md)尚不支持此功能。 支持此功能后，本文档会更新。

## <a name="next-steps"></a><a name="next"></a>后续步骤

* [依赖关系跟踪](./asp-net-dependencies.md)

