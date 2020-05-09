---
title: Azure Monitor 中的 Get 指标 Application Insights
description: 了解如何使用 Azure Monitor Application Insights 有效地使用 GetMetric （）调用来捕获适用于 .NET 和 .NET Core 应用程序的本地预聚合度量值
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/28/2020
ms.openlocfilehash: 94525ce901a89935c4ee7800ada44a9dff84b27a
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927898"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>.NET 和 .NET Core 中的自定义指标集合

.NET 和 .NET Core Sdk Azure Monitor Application Insights 有两种不同的方法来收集自定义`TrackMetric()`指标、 `GetMetric()`和。 这两种方法之间的主要区别在于本地聚合。 `TrackMetric()`在`GetMetric()`预先聚合之前缺少预聚合。 建议使用聚合，因此不再`TrackMetric()`是收集自定义指标的首选方法。 本文将指导你使用 GetMetric （）方法，以及它的工作原理。

## <a name="trackmetric-versus-getmetric"></a>TrackMetric 与 GetMetric

`TrackMetric()`发送用于表示指标的原始遥测数据。 为每个值发送单个遥测项是低效的。 `TrackMetric()`在性能方面也是低效的，因为`TrackMetric(item)`每个都要经历遥测初始值设定项和处理器的完整 SDK 管道。 与`TrackMetric()`不同`GetMetric()`的是，为您处理本地预聚合，然后仅提交一分钟固定时间间隔内的聚合汇总指标。 因此，如果你需要在第二个或每个毫秒级别密切监视一些自定义指标，则可以这样做，同时仅每分钟仅产生一次监视的存储和网络流量成本。 这还极大地降低了由于需要为聚合指标发送的遥测项总数大大降低，导致限制的风险。

在 Application Insights 中，通过`TrackMetric()`和`GetMetric()`收集的自定义度量值不受[采样](https://docs.microsoft.com/azure/azure-monitor/app/sampling)的限制。 采样重要指标可能会导致出现这样的情况：你可能围绕这些指标构建的警报可能会变得不可靠。 如果从不采样自定义指标，通常可以确信当警报阈值违例时，会触发警报。  但由于不会对自定义度量值进行采样，因此可能会出现一些问题。

如果每秒需要跟踪指标的趋势，或以更精细的间隔跟踪，则可能导致：

- 数据存储成本增加。 向 Azure Monitor 发送的数据量有一种相关的成本。 （发送的数据越多，总的监视成本就越大。）
- 增加了网络流量/性能开销。 （在某些情况下，这可能会产生金钱和应用程序的性能成本。）
- 引入限制的风险。 （当应用程序在短时间间隔内发送非常高的遥测速率时，Azure Monitor 服务会删除（"限制"）数据点。）

限制是特别要注意的，因为这种情况下，阻止可能会导致丢失警报，因为触发警报的条件可以在本地发生，然后由于发送的数据太多而被丢弃。 这就是我们不建议使用`TrackMetric()`的 .NET 和 .net Core 的原因，除非已实现自己的本地聚合逻辑。 如果尝试跟踪每个实例在给定时间段内发生的事件，可能会发现[`TrackEvent()`](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics#trackevent)更适合。 但请记住，与自定义指标不同，自定义事件会受到采样。 当然，即使没有编写您`TrackMetric()`自己的本地预聚合，您仍然可以使用它，但如果这样做，您就可以意识到这些缺陷。

总之`GetMetric()` ，我们建议使用此方法，因为它进行了预聚合，它将从所有跟踪（）调用中累积值并每分钟发送一次摘要/聚合。 这可以通过发送更少的数据点来大幅降低成本和性能开销，同时仍收集所有相关信息。

> [!NOTE]
> 只有 .NET 和 .NET Core Sdk 具有 GetMetric （）方法。 如果你使用的是 Java，则可以使用[Micrometer 度量值](https://docs.microsoft.com/azure/azure-monitor/app/micrometer-java)或`TrackMetric()`。 对于 Python，可以使用[OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#metrics)发送自定义指标。 对于 JavaScript 和 node.js，你仍会使用`TrackMetric()`，但请记住上一部分中所述的注意事项。

## <a name="getting-started-with-getmetric"></a>GetMetric 入门

对于我们的示例，我们将使用基本的 .NET Core 3.1 辅助角色服务应用程序。 如果要完全复制与这些示例一起使用的测试环境，请遵循[监视辅助角色服务一文](https://docs.microsoft.com/azure/azure-monitor/app/worker-service#net-core-30-worker-service-application)中的步骤1-6，将 Application Insights 添加到基本辅助角色服务项目模板。 这些概念适用于可使用 SDK 的任何一般应用程序，其中包括 web 应用和控制台应用。

### <a name="sending-metrics"></a>发送指标

将`worker.cs`文件的内容替换为以下内容：

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

如果运行上面的代码并观看通过 Visual Studio 的 "输出" 窗口或 Telerik 的 Fiddler 等工具发送的遥测数据，则会看到 while 循环反复执行（不发送遥测数据），然后将通过60秒标记发送单个遥测项，这种情况下，我们的测试如下所示:

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

此单个遥测项表示 41 distinct 度量度量值的聚合。 由于我们要反复发送相同的值，因此我们的*标准偏差（stDev）* 值为0，*最大*值（最大值）和*最小值（min）* 值相同。 *Value*属性表示已聚合的所有单个值的总和。

如果我们在日志（分析）经验中检查 Application Insights 资源，此单个遥测项目将如下所示：

![Log Analytics 查询视图](./media/get-metric/log-analytics.png)

> [!NOTE]
> 引入创建一个后，原始遥测项不包含显式 sum 属性/字段。 在这种情况下`value` ， `valueSum`和属性都表示相同的内容。

你还可以在门户的 "[_指标_](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)" 部分中访问自定义指标遥测。 同时作为[基于日志的指标和自定义指标](pre-aggregated-metrics-log-metrics.md)。 （下面的屏幕截图是基于日志的示例。）![指标资源管理器视图](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>高吞吐量使用情况的缓存指标引用

在某些情况下，会发现指标值非常频繁。 例如，处理每秒500请求的高吞吐量服务可能希望为每个请求发出20个遥测指标。 这意味着每秒跟踪10000个值。 在这种高吞吐量情况下，用户可能需要通过避免一些查找来帮助 SDK。

例如，在这种情况下，上面的示例执行了度量值 "ComputersSold" 的句柄的查找，然后跟踪观察到的值42。 相反，句柄可能会缓存多个磁道调用：

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

除了对度量值句柄进行缓存之外，上面的示例还`Task.Delay`减小到50毫秒，以便循环会更频繁地执行，导致`TrackValue()` 772 调用。

## <a name="multi-dimensional-metrics"></a>多维指标

上一部分中的示例显示了零维指标。 指标也可以是多维的。 目前最多支持10个维度。

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

运行此代码至少60秒将导致三个不同的遥测项发送到 Azure，每个项表示三种形式因素之一的聚合。 与可以在 "日志（分析）" 视图中检查它们之前一样：

![多维度量值的 Log analytics 视图](./media/get-metric/log-analytics-multi-dimensional.png)

以及指标资源管理器体验：

![自定义指标](./media/get-metric/custom-metrics.png)

不过，您会注意到，您不能通过新的自定义维度拆分度量值，也不能使用 "指标" 视图查看自定义维度：

![拆分支持](./media/get-metric/splitting-support.png)

默认情况下，指标资源管理器体验中的多维度量值未在 Application Insights 资源中打开。

### <a name="enable-multi-dimensional-metrics"></a>启用多维度量值

若要为 Application Insights 资源启用多维度量值，请选择 "**使用情况和估计成本** > **"** 自定义**指标** > **在自定义指标维度** > 下启用警报。 可在[此处](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)找到有关此内容的更多详细信息。

完成更改并发送新的多维遥测后，您将能够**应用拆分**。

> [!NOTE]
> 只有在门户中打开此功能后，新发送的度量值才会存储维度。

![应用拆分](./media/get-metric/apply-splitting.png)

并查看每个_FormFactor_维度的指标聚合：

![外形规格](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>当维度超过三个时如何使用 MetricIdentifier

目前支持10个维度，但大于三个维度需要使用`MetricIdentifier`：

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>自定义指标配置

如果要更改指标配置，则需要在指标初始化的位置执行此操作。

### <a name="special-dimension-names"></a>特殊维度名称

度量值不使用用于访问它们的`TelemetryClient`遥测上下文，类中`MetricDimensionNames`的常量可用的特殊维度名称是此限制的最佳解决方法。

以下 "特殊操作请求大小" 所发送的度量值聚合-指标**不**会将`Context.Operation.Name`其设置为 "特殊操作"。 而`TrackMetric()`或任何其他 TrackXXX （）将正确`OperationName`设置为 "特殊操作"。

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

在这种情况下，请使用`MetricDimensionNames`类中列出的特殊维度名称指定`TelemetryContext`值。

例如，在将下一条语句生成的指标聚合发送到 Application Insights 云终结点时，其`Context.Operation.Name`数据字段将设置为 "特殊操作"：

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

此特殊维度的值将复制到中`TelemetryContext` ，并且不会用作 "normal" 维度。 如果还希望为常规指标探索保留操作维度，则需要为此目的创建一个单独的维度：

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>维度和时间序列上限

 若要防止遥测子系统意外地使用资源，可以控制每个指标的最大数据序列数。 默认限制不超过1000的每个指标的数据系列总数，每个维度的值不超过100。

 在维度和时序上限的上下文中，我们使用`Metric.TrackValue(..)`来确保观察到限制。 如果已达到限制， `Metric.TrackValue(..)`将返回 "False"，且不会跟踪该值。 否则，它将返回 "True"。 如果某个指标的数据来自用户输入，则此方法很有用。

`MetricConfiguration`构造函数采用一些选项来了解如何管理各自指标内的不同序列，以及实现`IMetricSeriesConfiguration`的、为每个单独的度量序列指定聚合行为的类的对象：

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

* `seriesCountLimit`指标可以包含的数据时序的最大数目。 达到此限制后，调用`TrackValue()`。
* `valuesPerDimensionLimit`以类似的方式限制每个维度的非重复值的数目。
* `restrictToUInt32Values`确定是否只跟踪非负整数值。

下面的示例演示如何发送消息以了解是否超出上限限制：

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>后续步骤

* [详细了解](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)如何监视 worker 服务应用程序。
* 有关[基于日志和预聚合指标](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics)的更多详细信息。
* [指标资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)
* 如何为[ASP.NET Core 应用程序](asp-net-core.md)启用 Application Insights
* 如何为[ASP.NET 应用程序](asp-net.md)启用 Application Insights
