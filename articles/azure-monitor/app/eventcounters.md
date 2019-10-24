---
title: Application Insights 中的事件计数器 |Microsoft Docs
description: 在 Application Insights 中监视系统和自定义 .NET/.NET Core EventCounters。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cithomas
ms.author: cithomas
ms.date: 09/20/2019
ms.openlocfilehash: 0762819239e8fd71a015f317776a94280806db53
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72677154"
---
# <a name="eventcounters-introduction"></a>EventCounters 简介

`EventCounter` 是用于发布和使用计数器或统计信息的 .NET/.NET Core 机制。 [本](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)文档提供有关如何发布和使用它们的 `EventCounters` 和示例的概述。 所有 OS 平台（Windows、Linux 和 macOS）都支持 EventCounters。 它可以被视为只在 Windows 系统中受支持的[PerformanceCounters](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter)的跨平台等效项。

尽管用户可以根据需要发布任何自定义 `EventCounters`，但 .NET Core 3.0 运行时默认情况下会发布一组这些计数器。 该文档将指导你完成在 Azure 应用程序 Insights 中收集和查看 `EventCounters` （系统定义的或用户定义的）所需的步骤。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用 Application Insights 收集 EventCounters

Application Insights 支持收集 `EventCounters` 及其 `EventCounterCollectionModule`，它是新发布的[EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)的一部分。 使用[AspNetCore](asp-net-core.md)或[WorkerService](worker-service.md)时，会自动启用 `EventCounterCollectionModule`。 `EventCounterCollectionModule` 收集计数器，这些计数器的收集频率为60秒。 收集 EventCounters 不需要任何特殊权限。

## <a name="default-counters-collected"></a>收集的默认计数器

对于在 .NET Core 3.0 中运行的应用程序，SDK 会自动收集以下计数器。 计数器的名称将采用 "Category |计数器 "。

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
> AspNetCore 类别的计数器仅添加到 Asp.Net Core 应用程序中。

## <a name="customizing-counters-to-be-collected"></a>自定义要收集的计数器

下面的示例演示如何添加/删除计数器。 使用 `AddApplicationInsightsTelemetry()` 或 `AddApplicationInsightsWorkerService()` 启用 Application Insights 遥测收集后，将在应用程序的 `ConfigureServices` 方法中完成此自定义。 下面是 ASP.NET Core 应用程序的示例代码。 对于其他类型的应用程序，请参阅[本](worker-service.md#configuring-or-removing-default-telemetrymodules)文档。

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

## <a name="event-counters-in-metric-explorer"></a>指标资源管理器中的事件计数器

若要在[指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)中查看 EventCounter 指标，请选择 Application Insights 资源，并选择 "基于日志的指标" 作为 "指标命名空间"。 然后，EventCounter 度量值将显示在 "PerformanceCounter" 类别下。

> [!div class="mx-imgBorder"]
> Application Insights ](./media/event-counters/metrics-explorer-counter-list.png) 中报告 ![Event 计数器

## <a name="event-counters-in-analytics"></a>分析中的事件计数器

你还可以在**performanceCounters**表的[分析](../../azure-monitor/app/analytics.md)中搜索和显示事件计数器报表。

例如，运行以下查询，以查看收集了哪些计数器并可用于查询：

```Kusto
performanceCounters | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> Application Insights ](./media/event-counters/analytics-event-counters.png) 中报告 ![Event 计数器

若要获取特定计数器的图表（例如： `ThreadPool Completed Work Item Count`），请运行以下查询。

```Kusto
performanceCounters 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> Application Insights 中单个计数器的 ![Chat ](./media/event-counters/analytics-completeditems-counters.png)

与其他遥测一样，**performanceCounters** 同样也具有列 `cloud_RoleInstance`，指示正在其上运行应用的主机服务器实例的标识。 上面的查询显示每个实例的计数器值，并可用于比较不同服务器实例的性能。

## <a name="alerts"></a>警报
与其他指标一样，可以[设置警报](../../azure-monitor/app/alerts.md)，以便在事件计数器超出您指定的限制时发出警告。 打开“警报”窗格，并单击“添加警报”。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-see-eventcounters-in-live-metrics"></a>我是否可以在实时指标中看到 EventCounters？

实时指标目前不显示 EventCounters。 使用指标资源管理器或分析来查看遥测数据。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>可以在哪些平台上查看 .NET Core 3.0 计数器的默认列表？

EventCounter 无需任何特殊权限，支持在所有平台中支持 .NET Core 3.0。 这包括：

* **操作系统**： Windows、Linux 或 macOS。
* **宿主方法**：进程内或进程外。
* **部署方法**：依赖于框架的或独立的。
* **Web 服务器**： IIS （Internet information server）或 Kestrel。
* **托管平台**： Azure App Service、azure VM、Docker、Azure Kubernetes SERVICE （AKS）等的 Web 应用功能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已在 Azure Web 应用门户中启用 Application Insights。 但看不到 EventCounters。

 ASP.NET Core 的[Application Insights 扩展](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps)尚不支持此功能。 当支持此功能时，将更新此文档。

## <a name="next"></a>后续步骤

* [依赖关系跟踪](../../azure-monitor/app/asp-net-dependencies.md)