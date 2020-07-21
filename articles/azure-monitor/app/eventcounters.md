---
title: Application Insights 中的事件计数器 | Microsoft Docs
description: 监视 Application Insights 中的系统和自定义的 .NET/.NET Core EventCounters。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 06bf15bf60b1ee5e2c301935a30b3981d5233a08
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539935"
---
# <a name="eventcounters-introduction"></a>EventCounters 简介

`EventCounter` 是用于发布和使用计数器或统计信息的 .NET/.NET Core 机制。 [本文档](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)概述了 `EventCounters` 以及有关如何发布和使用它们的示例。 所有 OS 平台（Windows、Linux 和 macOS）都支持 EventCounters。 可以将其视为仅在 Windows 系统中受支持的 [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) 的等效跨平台。

尽管用户可以根据需要发布任何自定义 `EventCounters`，但 .NET Core 3.0 运行时默认情况下会发布一组此类计数器。 本文档将指导你完成在 Azure Application Insights 中收集和查看 `EventCounters`（系统定义或用户定义）所需的步骤。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用 Application Insights 收集 EventCounters

Application Insights 支持使用 `EventCounterCollectionModule` 收集 `EventCounters`，该模块是新发布的 nuget 包 [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector) 的一部分。 使用 [AspNetCore](asp-net-core.md) 或 [WorkerService](worker-service.md) 时，将自动启用 `EventCounterCollectionModule`。 `EventCounterCollectionModule` 会以不可配置的收集频率（60 秒）收集计数器。 收集 EventCounters 时，不需要具备特殊的权限。

## <a name="default-counters-collected"></a>已收集默认计数器

对于在 .NET Core 3.0 中运行的应用，SDK 会自动收集以下计数器。 计数器的名称将采用“类别|计数器”格式。

|类别 | 计数器|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |
|`Microsoft.AspNetCore.Hosting` | `requests-per-second` |
|`Microsoft.AspNetCore.Hosting` | `total-requests` |
|`Microsoft.AspNetCore.Hosting` | `current-requests` |
|`Microsoft.AspNetCore.Hosting` | `failed-requests` |

> [!NOTE]
> Microsoft.AspNetCore.Hosting 类别的计数器仅添加到 ASP.NET Core 应用程序中。

## <a name="customizing-counters-to-be-collected"></a>自定义要收集的计数器

以下示例介绍如何添加/删除计数器。 使用 `AddApplicationInsightsTelemetry()` 或 `AddApplicationInsightsWorkerService()` 启用 Application Insights 遥测收集后，将在应用程序的 `ConfigureServices` 方法中完成此自定义。 下面是 ASP.NET Core 应用程序的示例代码。 对于其他类型的应用程序，请参阅[此文档](worker-service.md#configuring-or-removing-default-telemetrymodules)。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>Metric Explorer 中的事件计数器

若要在 [Metric Explorer](../platform/metrics-charts.md) 中查看 EventCounter 指标，请选择 Application Insights 资源，然后选择基于日志的指标作为指标命名空间。 EventCounter 指标随即显示在“自定义”类别下。

> [!div class="mx-imgBorder"]
> ![Application Insights 中报告的事件计数器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics 中的事件计数器

还可以在 [Analytics](../log-query/log-query-overview.md) 的“customMetrics”表中搜索和显示事件计数器报表。

例如，运行以下查询，以查看收集了哪些计数器并可用于查询：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights 中报告的事件计数器](./media/event-counters/analytics-event-counters.png)

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
与其他指标一样，可以[设置警报](../../azure-monitor/platform/alerts-log.md)以便在事件计数器超出指定的限制时收到警报。 打开“警报”窗格，并单击“添加警报”。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-see-eventcounters-in-live-metrics"></a>是否能在实时指标中看到 EventCounters？

实时指标目前不显示 EventCounters。 使用 Metric Explorer 或 Analytics 来查看遥测数据。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>可以在哪些平台上查看 .NET Core 3.0 计数器的默认列表？

EventCounter 无需任何特殊权限，并且在支持 .NET Core 3.0 的所有平台中受支持。 这包括：

* **操作系统**：Windows、Linux 和 macOS。
* 托管方法：进程内或进程外。
* 部署方法：框架依赖或自包含。
* Web 服务器：IIS（Internet 信息服务器）或 Kestrel。
* 托管平台：Azure 应用服务、Azure VM、Docker、Azure Kubernetes 服务 (AKS) 等的 Web 应用功能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已从 Azure Web 应用门户启用 Application Insights。 但看不到 EventCounters，这是什么原因？

 ASP.NET Core 的 [Application Insights 扩展](./azure-web-apps.md)尚不支持此功能。 支持此功能后，本文档会更新。

## <a name="next-steps"></a><a name="next"></a>后续步骤

* [依赖关系跟踪](../../azure-monitor/app/asp-net-dependencies.md)
