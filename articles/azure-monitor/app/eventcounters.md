---
title: 应用程序见解中的事件计数器 |微软文档
description: 监视系统和自定义 .NET/.NET 核心事件计数器在应用程序见解中。
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 2094c012e86131073fc66be4f2ac2fb2e81ef4c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663583"
---
# <a name="eventcounters-introduction"></a>事件计数器介绍

`EventCounter`是 .NET/.NET 核心机制，用于发布和使用计数器或统计信息。 [本文档](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md)概述了`EventCounters`如何发布和使用它们。 所有操作系统平台都支持事件计数器 - Windows、Linux 和 macOS。 它可以被视为仅在 Windows 系统中支持[的性能计数器](https://docs.microsoft.com/dotnet/api/system.diagnostics.performancecounter)的跨平台等效项。

虽然用户可以发布任何自定义以满足`EventCounters`其需求，但 .NET Core 3.0 运行时默认发布一组这些计数器。 本文档将演练在 Azure 应用程序见解中收集和查看`EventCounters`（系统定义或用户定义）所需的步骤。

## <a name="using-application-insights-to-collect-eventcounters"></a>使用应用程序见解收集事件计数器

应用程序见解支持`EventCounters`收集其`EventCounterCollectionModule`，这是新发布的 nuget 包[Microsoft.应用程序见解.事件计数器收集器的一](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)部分。 `EventCounterCollectionModule`使用[AspNetCore](asp-net-core.md)或[辅助服务](worker-service.md)时，将自动启用 。 `EventCounterCollectionModule`收集不可配置收集频率为 60 秒的计数器。 收集事件计数器不需要特殊权限。

## <a name="default-counters-collected"></a>收集的默认计数器

对于在 .NET Core 3.0 中运行的应用，SDK 会自动收集以下计数器。 计数器的名称将为"类别"计数器"。

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
> 类别 Microsoft.AspNetCore.Hosting 的计数器仅在ASP.NET核心应用程序中添加。

## <a name="customizing-counters-to-be-collected"></a>自定义要收集的计数器

下面的示例演示如何添加/删除计数器。 此自定义将在应用程序`ConfigureServices`方法中使用 或`AddApplicationInsightsTelemetry()``AddApplicationInsightsWorkerService()`启用应用程序见解遥测集合后完成。 下面是来自ASP.NET核心应用程序的示例代码。 对于其他类型的应用程序，请参阅[本文档](worker-service.md#configuring-or-removing-default-telemetrymodules)。

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

要查看[指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)中的事件计数器指标，请选择"应用程序见解"资源，并选择基于日志的指标作为指标命名空间。 然后，事件计数器指标将显示在"自定义"类别下。

> [!div class="mx-imgBorder"]
> ![应用程序见解中报告的事件计数器](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>分析中的事件计数器

您还可以在**自定义指标**表中搜索和显示["分析](../../azure-monitor/app/analytics.md)"中的事件计数器报表。

例如，运行以下查询以查看收集哪些计数器并可供查询：

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![应用程序见解中报告的事件计数器](./media/event-counters/analytics-event-counters.png)

要获取最近一段时间内特定计数器的图表（例如： `ThreadPool Completed Work Item Count`），请运行以下查询。

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![在应用程序见解中聊天单个计数器](./media/event-counters/analytics-completeditems-counters.png)

与其他遥测数据一样 **，customMetrics**也有一`cloud_RoleInstance`列，指示应用正在其上运行的主机服务器实例的标识。 上述查询显示每个实例的计数器值，可用于比较不同服务器实例的性能。

## <a name="alerts"></a>警报
与其他指标一样，您可以[设置警报](../../azure-monitor/app/alerts.md)，以便在事件计数器超出您指定的限制时发出警告。 打开“警报”窗格，并单击“添加警报”。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="can-i-see-eventcounters-in-live-metrics"></a>我可以在实时指标中查看事件计数器吗？

截至今天，实时指标不显示事件计数器。 使用指标资源管理器或分析查看遥测数据。

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>我可以查看哪些平台的默认列表 .NET Core 3.0 计数器？

EventCounter 不需要任何特殊权限，并且在所有平台中都支持 .NET Core 3.0。 这包括：

* **操作系统**：Windows、Linux 或 macOS。
* **托管方法**：在进程或进程外。
* **部署方法**：框架相关或自包含。
* **网络服务器**：IIS（互联网信息服务器）或凯斯特雷尔。
* **托管平台**：Azure 应用服务、Azure VM、Docker、Azure 库伯奈斯服务 （AKS） 等的 Web 应用功能。

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>我已经从 Azure Web 应用门户启用了应用程序见解。 但我看不到事件计数器。

 ASP.NET核心[应用程序见解扩展](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps)尚未支持此功能。 此文档将在支持此功能时更新。

## <a name="next-steps"></a><a name="next"></a>后续步骤

* [依赖项跟踪](../../azure-monitor/app/asp-net-dependencies.md)
