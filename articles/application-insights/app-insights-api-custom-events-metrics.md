---
title: 用于处理自定义事件和指标的 Application Insights API | Microsoft 文档
description: 在设备、桌面应用、网页或服务中插入几行代码，即可跟踪使用情况和诊断问题。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: 072ce2952e3cdea47b02ef7656ca67d4bc0ae8f1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>用于处理自定义事件和指标的 Application Insights API

在应用程序中插入几行代码，即可了解用户在该应用程序中执行的操作或帮助诊断问题。 可以从设备和桌面应用、Web 客户端和 Web 服务器发送遥测数据。 使用 [Visual Studio Application Insights](app-insights-overview.md) 核心遥测 API 发送自定义事件和指标，以及自己的标准遥测版本。 此 API 与标准 Application Insights 数据收集器使用的 API 相同。

## <a name="api-summary"></a>API 摘要
API 在所有平台中是一致的，只有一些微小的差异。

| 方法 | 用途 |
| --- | --- |
| [`TrackPageView`](#page-views) |页面、屏幕、边栏选项卡或窗体。 |
| [`TrackEvent`](#trackevent) |用户操作和其他事件。 用于跟踪用户行为或监视性能。 |
| [`TrackMetric`](#trackmetric) |性能度量，例如与特定事件不相关的队列长度。 |
| [`TrackException`](#trackexception) |记录诊断的异常。 跟踪与其他事件的相关性，以及检查堆栈跟踪。 |
| [`TrackRequest`](#trackrequest) |记录服务器请求的频率和持续时间以进行性能分析。 |
| [`TrackTrace`](#tracktrace) |诊断日志消息。 还可以捕获第三方日志。 |
| [`TrackDependency`](#trackdependency) |记录对应用依赖的外部组件的调用持续时间和频率。 |

可以[将属性和指标附加到](#properties)其中的大多数遥测调用。

## <a name="prep"></a>开始之前
如果还没有 Application Insights SDK 引用：

* 将 Application Insights SDK 添加到项目：

  * [ASP.NET 项目](app-insights-asp-net.md)
  * [Java 项目](app-insights-java-get-started.md)
  * [Node.js 项目](app-insights-nodejs.md)
  * [每个网页中的 JavaScript](app-insights-javascript.md) 
* 在设备或 Web 服务器代码中包含以下内容：

    *C#：* `using Microsoft.ApplicationInsights;`

    Visual Basic：`Imports Microsoft.ApplicationInsights`

    *Java：* `import com.microsoft.applicationinsights.TelemetryClient;`
    
    *Node.js：*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>获取 TelemetryClient 实例
获取 `TelemetryClient` 的实例（网页中的 JavaScript 除外）：

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

Visual Basic

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient 是线程安全的。

对于 ASP.NET 和 Java 项目，可自动捕获传入的 HTTP 请求。 可能需要为应用的其他模块创建 TelemetryClient 的其他实例。 例如，可以在中间件类中使用一个 TelemetryClient 实例报告业务逻辑事件。 可以设置属性（如 UserId 和 DeviceId）来标识计算机。 此信息会附加到实例发送的所有事件。 

*C#*

    TelemetryClient.Context.User.Id = "...";
    TelemetryClient.Context.Device.Id = "...";

*Java*

    telemetry.getContext().getUser().setId("...);
    telemetry.getContext().getDevice().setId("...");

在 Node.js 项目中，可以使用 `new applicationInsights.TelemetryClient(instrumentationKey?)` 创建新实例，但这建议仅用于需要与单一实例 `defaultClient` 隔离的配置的方案。

## <a name="trackevent"></a>TrackEvent
在 Application Insights 中，自定义事件是一个数据点，它可在[指标资源管理器](app-insights-metrics-explorer.md)中显示为聚合计数，在[诊断搜索](app-insights-diagnostic-search.md)中显示为单个事件。 （它与 MVC 或其他框架“事件”不相关。）

在代码中插入 `TrackEvent` 调用来统计各种事件。 用户选择特定功能的频率、实现特定目标的频率，或可能制造特定类型的错误的频率。

例如，在游戏应用中，每当用户获胜时会发送事件：

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

Visual Basic

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>在 Microsoft Azure 门户中查看事件
要查看事件计数，请打开[“指标资源管理器”](app-insights-metrics-explorer.md)边栏选项卡、添加新图表，并选择“事件”。  

![查看自定义事件计数](./media/app-insights-api-custom-events-metrics/01-custom.png)

要比较不同事件的计数，请将图表类型设置为“网格”，并按事件名称分组：

![设置图表类型和分组](./media/app-insights-api-custom-events-metrics/07-grid.png)

在网格中逐个单击事件名称，查看该事件的发生次数。 单击列表中的任何一项事件可查看详细信息。

![钻取事件](./media/app-insights-api-custom-events-metrics/03-instances.png)

要在“搜索”或“指标资源管理器”中专注查看特定事件，请将边栏选项卡筛选器设置为所需的事件名称：

![打开“筛选器”，展开“事件名称”，并选择一个或多个值](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Analytics 中的自定义事件

[Application Insights Analytics](app-insights-analytics.md) 的 `customEvents` 表格提供了遥测。 每行表示对应用中 `trackEvent(..)` 的调用。 

如果正在进行[采样](app-insights-sampling.md)，那么 itemCount 属性将显示大于 1 的值。 例如，itemCount==10 表明对 trackEvent() 调用了 10 次，采样进程只传输其中一次。 若要获取自定义事件的正确计数，应使用 `customEvent | summarize sum(itemCount)` 之类的代码。


## <a name="trackmetric"></a>TrackMetric

Application Insights 可绘制未附加到特定事件的指标。 例如，可以定期监视队列长度。 对指标而言，变化和趋势比单个度量值更具价值，因此统计图表非常实用。

若要将指标发送到 Application Insights，可以使用 `TrackMetric(..)` API。 可通过两种方式发送指标： 

* 单个值。 每次在应用中执行测量时，会发送相应的值到 Application Insights。 例如，假设有个指标用于描述容器中项的数量。 在特定时间段，先将 3 个项放入容器中，再从容器中移除 2 个项。 相应地，将会调用 `TrackMetric` 两次：首先传递值 `3`，然后传递值 `-2`。 Application Insights 会替你存储这两个值。 

* 聚合。 使用指标时，每个单次测量几乎无关紧要。 反而特定时间段内发生活动的摘要很重要。 此类摘要名为聚合。 在上一示例中，该时间段的聚合指标总数为 `1`，同时指标值的计数为 `2`。 使用聚合方法时，每个时间段只调用一次 `TrackMetric` 并发送聚合值。 建议采用此方法是因为它可以通过发送更少的数据点到 Application Insights 同时仍然收集所有相关信息来显著降低成本和性能开销。

### <a name="examples"></a>示例:

#### <a name="single-values"></a>单个值

发送单一指标值：

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

*Java*

```Java
    
    telemetry.trackMetric("queueLength", 42.0);

```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>聚合指标

建议在应用发送指标前聚合指标，以减少带宽和成本同时提升性能。
以下是一个聚合代码示例：

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>指标资源管理器中的自定义指标

若要查看结果，请打开指标资源管理器并添加新图表。 编辑图表以显示指标。

> [!NOTE]
> 可能需要几分钟，自定义指标才会显示在可用指标列表中。
>

![添加新图表或选择图表，并在“自定义”下面选择指标](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>分析中的自定义指标

[Application Insights Analytics](app-insights-analytics.md) 的 `customMetrics` 表格提供了遥测。 每行表示对应用中 `trackMetric(..)` 的调用。
* `valueSum` - 这是度量值的总和。 若要获取平均值，请除以 `valueCount`。
* `valueCount` - 聚合到此 `trackMetric(..)` 调用中的度量值个数。

## <a name="page-views"></a>页面视图
在设备或网页应用中，加载每个屏幕或页面时默认将发送页面视图遥测数据。 但是，可以更改为在其他时间或不同时间跟踪页面视图。 例如，在显示选项卡或边栏选项卡的应用中，可以在用户每次打开新边栏选项卡时跟踪一个页面。

![“概述”边栏选项卡上的用法可重用功能区](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

用户和会话数据与页面视图作为属性一起发送，以便在有页面视图遥测数据时显示用户与会话图表。

### <a name="custom-page-views"></a>自定义页面视图
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Java*

    telemetry.trackPageView("GameReviewPage");

Visual Basic

    telemetry.TrackPageView("GameReviewPage")


如果不同的 HTML 网页中有多个选项卡，还可以指定 URL：

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>计时页面视图
默认情况下，报告为“页面视图加载时间”的时间测量是从浏览器发送请求开始、调用浏览器的页面加载事件为止的时间。

可以：

* 在 [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) 调用中设置显式持续时间：`appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`。
* 使用页面视图计时调用 `startTrackPage` 和 `stopTrackPage`。

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

用作第一个参数的名称会将开始调用与停止调用相关联。 该名称默认为当前页面名称。

显示在指标资源管理器中的最终页面加载持续时间派生自开始调用与停止调用的间隔时间。 实际时间间隔由你决定。

### <a name="page-telemetry-in-analytics"></a>Analytics 中的页面遥测

[Analytics](app-insights-analytics.md)中有两个表展示了浏览器操作的数据：

* `pageViews` 表包含关于 URL 和页标题的数据
* `browserTimings` 表包含关于客户端性能的数据，例如处理传入数据所用的时间

查找浏览器处理不同页面需要的时间：

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

发现不同浏览器的热门程度：

```
pageViews | summarize count() by client_Browser
```

若要将页面视图关联到 AJAX 调用，请联接依赖项：

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
服务器 SDK 使用 TrackRequest 记录 HTTP 请求。

如果想要在没有 Web 服务模块运行的上下文中模拟请求，也可以自行调用。

但是，发送请求遥测的建议方式是将请求用作<a href="#operation-context">操作上下文</a>。

## <a name="operation-context"></a>操作上下文
可以通过将遥测项与操作上下文关联来将遥测项关联在一起。 标准的请求跟踪模块针对在处理 HTTP 请求时发送的异常和其他事件执行此操作。 在[搜索](app-insights-diagnostic-search.md)和[分析](app-insights-analytics.md)中，可以使用此操作 ID 轻松找到与请求关联的任何事件。

有关关联的更多详细信息，请参阅 [Application Insights 中的遥测关联](application-insights-correlation.md)。

手动跟踪遥测时，使用此模式确保遥测关联的最简单方法：

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...
    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

除了设置操作上下文之外，`StartOperation` 还会创建一个所指定类型的遥测项。 在处理操作时，或如果显式调用 `StopOperation`，它将发送遥测项。 如果使用 `RequestTelemetry` 作为遥测类型，其持续时间将设置为开始与停止的间隔时间。

在操作范围内报告的遥测项将成为此类操作的“子级”。 操作上下文可以嵌套。 

在搜索中，操作上下文可用于创建“相关项”列表：

![相关项](./media/app-insights-api-custom-events-metrics/21.png)

有关自定义操作跟踪的详细信息，请参阅[使用 Application Insights .NET SDK 跟踪自定义操作](application-insights-custom-operations-tracking.md)。

### <a name="requests-in-analytics"></a>Analytics 中的请求 

在 [Application Insights Analytics](app-insights-analytics.md) 中，请求出现在 `requests` 表中。

如果正在进行[采样](app-insights-sampling.md)，那么 itemCount 属性将会显示大于 1 的值。 例如，itemCount==10 表明对 trackRequest() 调用了 10 次，采样进程只传输其中一次。 若要按请求名称获取正确的请求数和平均持续时间，请使用如下所示的代码：

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
将异常发送到 Application Insights：

* 用于[对其计数](app-insights-metrics-explorer.md)，作为问题发生频率的指示。
* 用于[检查单个事件](app-insights-diagnostic-search.md)。

报告包含堆栈跟踪。

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*Java*

    try {
        ...
    } catch (Exception ex) {
        telemetry.trackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

SDK 会自动捕获许多异常，因此不一定需要显式调用 TrackException。

* ASP.NET：[编写代码来捕获异常](app-insights-asp-net-exceptions.md)。
* J2EE：[自动捕获异常](app-insights-java-get-started.md#exceptions-and-request-failures)。
* JavaScript：自动捕获异常。 若要禁用自动收集，请在插入网页的代码片段中添加一行：

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Analytics 中的异常

在 [Application Insights Analytics](app-insights-analytics.md) 中，异常出现在 `exceptions` 表中。

如果正在进行[采样](app-insights-sampling.md)，那么 `itemCount` 属性将显示大于 1 的值。 例如，itemCount==10 表明对 trackException() 调用了 10 次，采样进程只传输其中一次。 若要按异常类型获取正确的异常数，请使用如下所示的代码：

```
exceptions | summarize sum(itemCount) by type
```

虽然大部分重要的堆叠信息已提取到了单独的变量中，但可以扩展 `details` 结构，获取更多信息。 由于这个结构是动态的，应将结果转换为预期的类型。 例如：

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

若要将异常与相关请求关联，请使用联接：

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
使用 TrackTrace 可以通过将“痕迹导航跟踪”发送到 Application Insights 来帮助诊断问题。 可以发送诊断数据区块，并在[诊断搜索](app-insights-diagnostic-search.md)中检查。

使用 .NET 时，[日志适配器](app-insights-asp-net-trace-logs.md)使用此 API 将第三方日志发送到门户。

使用 Java 时，[标准记录器（如 Log4J、Logback）](app-insights-java-trace-logs.md)使用 Application Insights Log4j 或 Logback Appenders 将第三方日志发送到门户。

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

*Java*

    telemetry.trackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


可以搜索消息内容，但是（不同于属性值）无法在其中进行筛选。

`message` 上的大小限制比属性上的限制高得多。
TrackTrace 的一个优势是可将相对较长的数据放置在消息中。 例如，可在此处对 POST 数据进行编码。  

此外，可向消息添加严重性级别。 并像其他遥测一样，可以添加属性值以帮助筛选或搜索不同跟踪集。 例如：

*C#*

```C#
    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```Java

    Map<String, Integer> properties = new HashMap<>();
    properties.put("Database", db.ID);
    telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);

```

在[搜索](app-insights-diagnostic-search.md)中，可轻松筛选出与特定数据库相关的所有特定严重性级别的消息。


### <a name="traces-in-analytics"></a>Analytics 中的跟踪

在 [Application Insights Analytics](app-insights-analytics.md) 中，对 TrackTrace 的调用出现在 `traces` 表中。

如果正在进行[采样](app-insights-sampling.md)，那么 itemCount 属性将显示大于 1 的值。 例如，itemCount==10 表明对 `trackTrace()` 调用了 10 次，采样进程只传输其中一次。 若要获取正确的跟踪调用数，应使用 `traces | summarize sum(itemCount)` 之类的代码。

## <a name="trackdependency"></a>TrackDependency
可使用 TrackDependency 调用跟踪响应时间以及调用外部代码片段的成功率。 结果会显示在门户上的依赖项图表中。

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```Java
    boolean success = false;
    long startTime = System.currentTimeMillis();
    try {
        success = dependency.call();
    }
    finally {
        long endTime = System.currentTimeMillis();
        long delta = endTime - startTime;
        RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
        telemetry.setTimeStamp(startTime);
        telemetry.trackDependency(dependencyTelemetry);
    }

```

*JavaScript*

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

请记住，服务器 SDK 包含[依赖项模块](app-insights-asp-net-dependencies.md)，用于自动发现和跟踪特定的依赖项调用（例如，数据库和 REST API）。 必须在服务器上安装一个代理才能让模块正常运行。 

使用 Java 时，可以使用 [Java 代理](app-insights-java-agent.md)自动跟踪某些依赖项调用。

如果想要跟踪自动跟踪未捕获的调用，或不想安装代理，可以使用此调用。

要关闭 C# 中的标准依赖项跟踪模块，请编辑 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 并删除对 `DependencyCollector.DependencyTrackingTelemetryModule` 的引用。 使用 Java 时，如果不希望自动收集标准依赖项，请勿安装 java 代理。

### <a name="dependencies-in-analytics"></a>Analytics 中的依赖项

在 [Application Insights Analytics](app-insights-analytics.md) 中，trackDependency 调用出现在 `dependencies` 表中。

如果正在进行[采样](app-insights-sampling.md)，那么 itemCount 属性将显示大于 1 的值。 例如，itemCount==10 表明对 trackDependency() 调用了 10 次，采样进程只传输其中一次。 若要按目标组件获取正确的依赖项数，请使用如下所示的代码：

```
dependencies | summarize sum(itemCount) by target
```

若要将依赖项与相关请求关联，请使用联接：

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>刷新数据
通常，SDK 在选定的时间发送数据，以便最大程度地降低对用户的影响。 但是，在某些情况下，可能需要刷新缓冲区，例如，在关闭的应用程序中使用 SDK 时。

*C#*
 
 ```C#
    telemetry.Flush();
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(5000);
```

*Java*

```Java
    telemetry.flush();
    //Allow some time for flushing before shutting down
    Thread.sleep(5000);
```

    
*Node.js*

    telemetry.flush();

请注意，[服务器遥测通道](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)的函数是异步的。

理想情况下，应在应用程序的关闭活动中使用 flush() 方法。

## <a name="authenticated-users"></a>经过身份验证的用户
在 Web 应用中，默认按 Cookie 标识用户。 如果用户从不同的计算机或浏览器访问应用或删除 Cookie，则可能会多次统计它们。

如果用户登录到应用，可以通过在浏览器代码中设置经过身份验证的用户 ID 来获取更准确的计数：

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

例如，在 ASP.NET Web MVC 应用程序中：

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

不需要使用用户的实际登录名。 只需使用该用户的唯一 ID 即可。 该 ID 不能包含空格或任何 `,;=|` 字符。

还可在会话 Cookie 中设置用户 ID 并将其发送到服务器。 如果安装了服务器 SDK，则经过身份验证的用户 ID 将作为客户端和服务器遥测上下文属性的一部分发送。 可对其进行筛选和搜索。

如果应用将用户分组到帐户，则还可以传递该帐户的标识符（具有相同的字符限制）。

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

在[指标资源管理器](app-insights-metrics-explorer.md)中，可以创建统计“经身份验证的用户”和“用户帐户”的图表。

还可以[搜索](app-insights-diagnostic-search.md)具有特定用户名和帐户的客户端数据点。

## <a name="properties"></a>使用属性筛选、搜索和细分数据
可以将属性和度量值附加到事件（以及指标、页面视图、异常和其他遥测数据）。

*属性*是可以在使用情况报告中用来筛选遥测数据的字符串值。 例如，如果应用提供多种游戏，可以将游戏的名称附加到每个事件，了解哪些游戏更受欢迎。

字符串长度限制为 8192。 （如果想要发送大型数据区块，请使用消息参数 [TrackTrace](#track-trace)。）

*指标*是能够以图形方式呈现的数字值。 例如，可以查看玩家的分数是否逐渐增加。 可以根据连同事件一起发送的属性对图表进行分段，以便获取不同游戏的独立图形或堆积图。

这些值应大于或等于 0，以便正确显示指标值。

可[对属性、属性值和指标的数目使用一些限制](#limits)。

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


Visual Basic

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> 请注意不要在属性中记录个人身份信息。
>
>

如果使用了指标，请打开“指标资源管理器”，并从“自定义”组中选择指标：

![打开“指标资源管理器”，选择图表，并选择指标](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> 如果指标未显示或“自定义”标题未出现，请关闭选项边栏选项卡并稍后重试。 有时，通过管道聚合指标可能需要花费一个小时。

*如果使用了属性和指标*，请按属性将指标分段：

![设置“分组”，并在“分组依据”下面选择属性](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*在诊断搜索中*，可以查看发生的每个事件的属性和指标。

![选择一个实例，并选择“...”](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

使用“搜索”字段查看具有特定属性值的事件匹配项。

![在“搜索”中键入搜索词](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[详细了解搜索表达式](app-insights-diagnostic-search.md)。

### <a name="alternative-way-to-set-properties-and-metrics"></a>设置属性和指标的替代方法
如果更方便的话，可以收集不同对象中的事件的参数：

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> 请不要重复使用相同的遥测项实例（本示例中为 `event`）来调用 Track*() 多次。 这可能会导致使用不正确的配置发送遥测数据。
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>在 Analytics 中自定义度量值和属性

在 [Analytics](app-insights-analytics.md) 中，自定义指标和属性显示在每个遥测记录的 `customMeasurements` 和 `customDimensions` 属性中。

例如，如果已向请求遥测添加名为“game”的属性，此查询将计算“game”不同值的出现次数，同时显示自定义指标“score”的平均值：

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

请注意：

* 从 customDimensions 或 customMeasurements JSON 中提取值的时候，会有动态类型，所以必须将其转换为 `tostring` 或 `todouble`。
* 考虑到[采样](app-insights-sampling.md)的可能性，需要使用 `sum(itemCount)` 而非 `count()`。



## <a name="timed"></a>计时事件
有时，需要绘制图表来呈现执行某个操作花费了多少时间。 例如，你可能想要知道用户在游戏中考虑如何选择时花费了多少时间。 为此，可以使用度量参数。

*C#*

```C#
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string>
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```Java
    long startTime = System.currentTimeMillis();

    // perform timed action

    long endTime = System.currentTimeMillis();
    Map<String, Double> metrics = new HashMap<>();
    metrics.put("ProcessingTime", endTime-startTime);

    // Setup some propereties
    Map<String, String> properties = new HashMap<>();
    properties.put("signalSource", currentSignalSource.getName());

    //send the event
    telemetry.trackEvent("SignalProcessed", properties, metrics);

```


## <a name="defaults"></a>自定义遥测的默认属性
如果想要为编写的一些自定义事件设置默认属性值，可以在 TelemetryClient 实例中设置。 这些值将附加到从该客户端发送的每个遥测项。

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

Visual Basic

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



单个遥测调用可以重写其属性字典中的默认值。

*对于 JavaScript Web 客户端*，请[使用 JavaScript 遥测初始值设定项](#js-initializer)。

若要向所有遥测数据（包括来自标准收集模块的数据）添加属性，请[实现 `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties)。

## <a name="sampling-filtering-and-processing-telemetry"></a>采样、筛选和处理遥测数据
可以先通过编写代码来处理遥测数据，再从 SDK 发送该数据。 处理包括从标准遥测模块（如 HTTP 请求收集和依赖项收集）发送的数据。

通过实现 `ITelemetryInitializer` [将属性添加到](app-insights-api-filtering-sampling.md#add-properties)遥测。 例如，可添加版本号或从其他属性计算得出的值。

[筛选](app-insights-api-filtering-sampling.md#filtering)可以先修改或丢弃遥测数据，然后通过实现 `ITelemetryProcesor` 从 SDK 发送遥测数据。 可以控制要发送或丢弃的项，但必须考虑到这会给指标造成怎样的影响。 根据丢弃项的方式，有时你可能无法在相关项之间导航。

[采样](app-insights-api-filtering-sampling.md)是减少从应用发送到门户的数据量的打包解决方案。 它不会影响显示的指标。 且不影响通过在相关项（如异常、请求和页面视图）之间导航来诊断问题。

[了解详细信息](app-insights-api-filtering-sampling.md)。

## <a name="disabling-telemetry"></a>禁用遥测
*动态停止和启动*收集与传输遥测数据：

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```Java
    
    telemetry.getConfiguration().setTrackingDisabled(true);

```

若要*禁用选定的标准收集器*（例如性能计数器、HTTP 请求或依赖项），请删除或注释掉 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中的相关行。例如，如果想要发送自己的 TrackRequest 数据，则可以这样做。

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

若要*禁用所选的标准收集器*（例如，性能计数器、HTTP 请求或依赖项），初始化时请将配置方法链接到 SDK 初始化代码：

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

若要在初始化后禁用这些收集器，请使用配置对象：`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>开发人员模式
在调试期间，通过管道加速遥测会很有效，这样可以立即看到结果。 此外，还可以获得其他消息来帮助跟踪任何遥测问题。 在生产环境中请关闭此模式，因为它可能会拖慢应用。

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

Visual Basic

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a>设置所选自定义遥测的检测密钥
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> 动态检测密钥
若要避免混合来自开发、测试和生产环境的遥测，可以[创建单独的 Application Insights 资源](app-insights-create-new-resource.md)，并根据环境更改其密钥。

无需从配置文件获取检测密钥，可以在代码中设置密钥。 在初始化方法中设置密钥，如 ASP.NET 服务中的 global.aspx.cs：

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



在网页中，可能需要通过 Web 服务器的状态设置密钥，而不是以文本方式将它编码到脚本中。 例如，在 ASP.NET 应用中生成的网页中：

*使用 Razor 的 JavaScript*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey;
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient 具有上下文属性，其中包含与所有遥测数据一起发送的值。 它们通常由标准遥测模块设置，但你也可以自行设置。 例如：

    telemetry.Context.Operation.Name = "MyOperationName";

如果自行设置这些值，请考虑从 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中删除相关的代码行，以便值与标准值不会造成混淆。

* **Component**：应用及其版本。
* **Device**：有关正在运行应用的设备的数据。 （在 Web 应用中，是指从其中发送遥测的服务器或客户端设备。）
* **InstrumentationKey**：Azure 中显示遥测数据的 Application Insights 资源。 通常可从 ApplicationInsights.config 中选择。
* **Location**：设备的地理位置。
* **Operation**：在 Web 应用中，为当前的 HTTP 请求。 在其他应用类型中，可将此属性设置为将事件分组在一起。
  * **Id**：一个生成的值，它将不同的事件关联在一起，以便在诊断搜索中检查任何事件时，可以发现相关项。
  * **Name**：一个标识符，通常是 HTTP 请求的 URL。
  * **SyntheticSource**：如果不为 null 或空，则此字符串表示请求的源已标识为傀儡或 Web 测试。 默认情况下，该属性会从指标资源管理器的计算中排除。
* **Properties**：与所有遥测数据一起发送的属性。 可在单个 Track* 调用中重写。
* **Session**：用户的会话。 ID 设置为生成的值，当用户有一段时间处于非活动状态时，此值会更改。
* **User**：用户信息。

## <a name="limits"></a>限制
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

若要避免达到数据速率限制，请使用[采样](app-insights-sampling.md)。

若要确定保留数据的时间期限，请参阅[数据保留和隐私](app-insights-data-retention-privacy.md)。

## <a name="reference-docs"></a>参考文档
* [ASP.NET 参考](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 参考](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 参考](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK 代码
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server 包](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [所有平台](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>问题
* *Track_() 调用可能会引发哪些异常？*

    无。 不需要将它们包装在 try-catch 子句中。 如果 SDK 遇到问题，它会在调试控制台输出中记录消息，如果消息已传入，可在诊断搜索中查看。
* *是否可以使用某个 REST API 从门户获取数据？*

    是的，可以使用[数据访问 API](https://dev.applicationinsights.io/)。 提取数据的其他方法包括[从 Analytics 导出到 Power BI](app-insights-export-power-bi.md) 和[连续导出](app-insights-export-telemetry.md)。

## <a name="next"></a>后续步骤
* [搜索事件和日志](app-insights-diagnostic-search.md)

* [故障排除](app-insights-troubleshoot-faq.md)


