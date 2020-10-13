---
title: Azure Monitor Application Insights 中的 Get-Metric
description: 了解如何有效地使用 GetMetric() 调用在 Azure Monitor Application Insights 中捕获 .NET 和 .NET Core 应用程序的本地预聚合指标
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 345d0d31528f7bdc40be4400e783ad5be45df72f
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930554"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET 和 .NET Core 中的自定义指标集合

Azure Monitor Application Insights.NET 和 .NET Core SDK 有两种不同的方法来收集自定义指标，分别为 `TrackMetric()` 和 `GetMetric()`。 这两种方法的主要区别在于本地聚合。 `TrackMetric()` 缺少预聚合，而 `GetMetric()` 具有预聚合。 推荐的方法是使用聚合，因此，`TrackMetric()` 不再是收集自定义指标的首选方法。 本文将引导你使用 GetMetric() 方法并介绍它的一些基本原理。

## <a name="trackmetric-versus-getmetric"></a>TrackMetric 与 GetMetric

`TrackMetric()` 发送表示指标的原始遥测。 为每个值发送单个遥测项效率低。 `TrackMetric()` 在性能方面的效率也较低，因为每个 `TrackMetric(item)` 都要经过整个 SDK 管道，包括遥测初始化程序和处理器。 与 `TrackMetric()` 不同，`GetMetric()` 为你处理本地预聚合，然后仅以一分钟的固定间隔提交聚合汇总指标。 因此，如果你需要在秒级甚至毫秒级密切监视某些自定义指标，则可以这样做，同时只需要承担每分钟监视一次的存储和网络流量成本。 这也极大降低了发生限制的风险，因为需要为聚合指标发送的遥测项的总数大大减少。

在 Application Insights 中，通过 `TrackMetric()` 和 `GetMetric()` 收集的自定义指标不受[采样](./sampling.md)限制。 对重要指标进行采样可能会导致以下情况：围绕这些指标构建的警报可能变得不可靠。 通过从不采样自定义指标，通常可以确信，一旦超过警报阈值，便会触发警报。  但由于没有对自定义指标进行采样，因此存在一些潜在的问题。

如果需要每秒钟跟踪一个指标的趋势，或以更细粒度的间隔进行跟踪，这可能会导致：

- 数据存储成本增加。 向 Azure Monitor 发送的数据量会产生一定的成本。 （发送的数据越多，监视的总体成本就越高。）
- 网络流量/性能开销增加。 （在某些情况下，这可能会产生货币成本和应用程序性能成本。）
- 引入限制风险。 （当应用以短时间间隔发送速率很高的遥测时，Azure Monitor 服务会丢弃（“限制”）数据点。）

与采样一样，限制也需要特别关注，限制可能会导致错过警报，因为触发警报的条件可能在本地发生，然后由于发送的数据太多而被丢弃在引入终结点。 这就是对于 .NET 和 .NET Core，我们不建议使用 `TrackMetric()` 的原因，除非你已实现自己的本地聚合逻辑。 如果尝试跟踪事件在给定时间段内发生的每个实例，则可能会发现 [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) 更适合。 但请记住，与自定义指标不同，自定义事件受采样限制。 当然，即使不编写自己的本地预聚合，也可以使用 `TrackMetric()`，但如果这样做，请注意陷阱。

总之，推荐使用 `GetMetric()` 方法，因为它执行预聚合、从所有 Track() 调用中累积值，并每分钟发送一次汇总/聚合。 通过发送更少的数据点，同时仍然收集所有相关信息，这可以显著降低成本和性能开销。

> [!NOTE]
> 只有 .NET 和 .NET Core SDK 具有 GetMetric() 方法。 如果你使用的是 Java，则可以使用 [Micrometer 指标](./micrometer-java.md)或 `TrackMetric()`。 对于 Python，可以使用 [OpenCensus.stats](./opencensus-python.md#metrics) 发送自定义指标。 对于 JavaScript 和 Node.js，仍可以使用 `TrackMetric()`，但请记住上一部分总结的注意事项。

## <a name="getting-started-with-getmetric"></a>GetMetric 入门

在示例中，我们将使用基本的 .NET Core 3.1 辅助角色服务应用程序。 如果要完全复制这些示例使用的测试环境，请按照[监视辅助角色服务文章](./worker-service.md#net-core-30-worker-service-application)的步骤 1-6 将 Application Insights 添加到基本辅助角色服务项目模板。 这些概念适用于任何可以使用 SDK 的通用应用程序，包括 Web 应用和控制台应用。

### <a name="sending-metrics"></a>发送指标

将 `worker.cs` 文件的内容替换为以下内容：

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("computersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

如果运行上面的代码，并在 Visual Studio 输出窗口或 Telerik 的 Fiddler 等工具中观察发送的遥测，则会发现 while 循环重复执行而不发送遥测，然后将在大约 60 秒后发送一个遥测项，在我们的测试中如下所示：

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"computersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

此单个遥测项代表了 41 个不同指标度量的聚合。 由于我们反复发送相同的值，因此标准偏差 (stDev) 为 0，具有相同的最大值 (max) 和最小值 (min)  。 值属性表示聚合的所有单个值的总和。

如果我们在日志（分析）体验中检查 Application Insights 资源，此单独的遥测项将如下所示：

![Log Analytics 查询视图](./media/get-metric/log-analytics.png)

> [!NOTE]
> 虽然原始遥测项在引入后不包含显式的总和属性/字段，但我们会为你创建一个。 在本例中，`value` 和 `valueSum` 属性都表示相同的内容。

你还可以在门户的[指标](../platform/metrics-charts.md)部分访问自定义指标遥测。 同时作为[基于日志的指标和自定义指标](pre-aggregated-metrics-log-metrics.md)。 （以下屏幕截图是基于日志的指标示例。）![指标资源管理器视图](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>用于高吞吐量使用情况的缓存指标引用

在某些情况下，指标值的观察非常频繁。 例如，每秒处理 500 个请求的高吞吐量服务可能希望为每个请求发出 20 个遥测指标。 这意味着每秒跟踪 10,000 个值。 在这种高吞吐量方案中，用户可能需要通过避免一些查找来帮助 SDK。

例如，在本例中，上面的示例对指标“ComputersSold”的句柄执行了查找操作，然后跟踪观察到的值 42。 相反，用户应该缓存该句柄，以进行多次跟踪调用：

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

除了缓存指标句柄之外，上面的示例还将 `Task.Delay` 缩短到了 50 毫秒，这样循环将更频繁地执行，从而导致了 772 次 `TrackValue()` 调用。

## <a name="multi-dimensional-metrics"></a>多维指标

上一部分的示例显示零维度指标。 指标也可以是多维的。 目前最多支持 10 个维度。

 下面是如何创建一维指标的示例：

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

运行此代码至少 60 秒将导致向 Azure 发送三个不同的遥测项，每个项代表三种外形规格之一的聚合。 与之前一样，你可以在日志（分析）视图中检查这些项：

![多维指标的日志分析视图](./media/get-metric/log-analytics-multi-dimensional.png)

以及在指标资源管理器体验中：

![自定义指标](./media/get-metric/custom-metrics.png)

但是，你将注意到无法按新的自定义维度拆分指标，也无法使用指标视图查看自定义维度：

![拆分支持](./media/get-metric/splitting-support.png)

默认情况下，指标资源管理器体验中的多维指标未在 Application Insights 资源中启用。

### <a name="enable-multi-dimensional-metrics"></a>启用多维指标

要为 Application Insights 资源启用多维指标，请选择“使用情况和估计成本” > “自定义指标” > “启用自定义指标维度的警报” > “确定”   。 在[此处](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)可以找到关于此内容的更多详细信息。

完成更改并发送新的多维遥测后，你将能够“应用拆分”。

> [!NOTE]
> 只有在门户中启用此功能后，新发送的指标才会存储维度。

![应用拆分](./media/get-metric/apply-splitting.png)

以及查看每个 FormFactor 维度的指标聚合：

![外形规格](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>当维护超过三个时如何使用 Metrocidentifier

目前支持 10 个维度，但是大于 3 个维度需要使用 `MetricIdentifier`：

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>自定义指标配置

如果想要更改指标配置，则需要在初始化指标的位置执行此操作。

### <a name="special-dimension-names"></a>特殊维度名称

指标不使用用于访问它们的 `TelemetryClient` 的遥测上下文，`MetricDimensionNames` 类中作为常量使用的特殊维度名称是解决此限制的最佳方法。

以下“特殊操作请求大小”指标发送的指标聚合不会将其 `Context.Operation.Name` 设置为“特殊操作”。 而 `TrackMetric()` 或任何其他 TrackXXX() 会将 `OperationName` 正确设置为“特殊操作”。

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

在这种情况下，使用 `MetricDimensionNames` 类中列出的特殊维度名称来指定 `TelemetryContext` 值。

例如，当下一条语句生成的指标聚合发送到 Application Insights 云终结点时，其 `Context.Operation.Name` 数据字段将设置为“特殊操作”：

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

此特殊维度的值将复制到 `TelemetryContext` 中，并且不会用作“普通”维度。 如果还希望用于普通指标探索的操作维度，则需要针对此目的创建单独的维度：

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>维度和时序上限

 要防止遥测子系统意外耗尽资源，可以控制每个指标的最大数据系列数。 默认限制是每个指标的总数据系列不超过 1000 个，每个维度的不同值不超过 100 个。

 在维度和时序上限的上下文中，我们使用 `Metric.TrackValue(..)` 来确保遵守限制。 如果已经达到限制，`Metric.TrackValue(..)` 将返回“False”，并且不会跟踪该值。 否则将返回“True”。 如果指标的数据源于用户输入，这将很有用。

`MetricConfiguration` 构造函数可以选择如何在相应的指标以及实现 `IMetricSeriesConfiguration` 的类对象中管理不同的系列，该类为指标的每个单独系列指定聚合行为：

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` 是指标可以包含的最大数据时序数目。 达到此限制后，调用 `TrackValue()`。
* `valuesPerDimensionLimit` 以类似的方式限制每个维度的非重复值数目。
* `restrictToUInt32Values` 确定是否只跟踪非负整数值。

以下示例演示如何发送消息以了解是否超过上限：

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>后续步骤

* [详细了解](./worker-service.md)有关监视辅助角色服务应用程序的信息。
* 有关[基于日志的指标和预聚合指标](./pre-aggregated-metrics-log-metrics.md)的更多详细信息。
* [指标资源管理器](../platform/metrics-getting-started.md)
* 如何为 [ASP.NET Core 应用程序](asp-net-core.md)启用 Application Insights
* 如何为 [ASP.NET 应用程序](asp-net.md)启用 Application Insights
