---
title: Azure Application Insights 中的遥测采样 | Microsoft 文档
description: 如何使受控制的遥测数据的卷。
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: f4989f8dce32e2340357e30541548b3e7e9d8a44
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508881"
---
# <a name="sampling-in-application-insights"></a>在 Application Insights 中采样

采样是 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 中的一项功能。 建议将它用于降低遥测流量、数据成本和存储成本，同时保留采用统计方式的应用程序数据的正确分析。 采样还有助于避免 Application Insights 限制遥测。 采样筛选器会选择相关项，以便可以在执行诊断调查时在不同项之间导航。

当指标计数显示在门户中时，它们会重新规范化以考虑到采样。 这样可以尽量减轻对统计的影响。

## <a name="brief-summary"></a>简短摘要

* 有三种不同类型的采样：自适应采样、固定速率采样和引入采样。
* 默认情况下，已在所有最新版本的 Application Insights ASP.NET 和 ASP.NET Core 软件开发工具包 (SDK) 中启用自适应采样。 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 也使用自适应采样。
* 最新版本的 Application Insights Sdk 中提供了固定速率采样，适用于 ASP.NET、ASP.NET Core、Java （代理和 SDK）以及 Python。
* 引入采样适用于 Application Insights 服务终结点。 仅当没有其他采样生效时，才能应用引入采样。 如果 SDK 为遥测数据采样，则会禁用引入采样。
* 对于 Web 应用程序，如果记录自定义事件，并需要确保事件集一同保留或一同丢弃，则事件必须具有相同的 `OperationId` 值。
* 如果要编写分析查询，应[考虑采样](../../azure-monitor/log-query/aggregations.md)。 特别是，应使用 `summarize sum(itemCount)`，而不是仅对记录进行计数。
* 某些遥测类型（包括性能指标和自定义指标）始终会保留，而不管是否启用采样。

下表汇总了适用于每个 SDK 和应用程序类型的采样类型：

| Application Insights SDK | 支持自适应采样 | 支持固定速率采样 | 支持引入采样 |
|-|-|-|-|
| ASP.NET | [是（默认已启用）](#configuring-adaptive-sampling-for-aspnet-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-applications) | 仅当没有其他采样生效时 |
| ASP.NET Core | [是（默认已启用）](#configuring-adaptive-sampling-for-aspnet-core-applications) | [是](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | 仅当没有其他采样生效时 |
| Azure Functions | [是（默认已启用）](#configuring-adaptive-sampling-for-azure-functions) | 否 | 仅当没有其他采样生效时 |
| Java | 否 | [是](#configuring-fixed-rate-sampling-for-java-applications) | 仅当没有其他采样生效时 |
| Python | 否 | [是](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | 仅当没有其他采样生效时 |
| 所有其他 | 否 | 否 | [是](#ingestion-sampling) |

> [!NOTE]
> 本页中的大部分信息适用于最新版本的 Application Insights SDK。 有关早期版本的 SDK 的信息，请[参阅下面的部分](#older-sdk-versions)。

## <a name="types-of-sampling"></a>采样类型

有三种不同的采样方法：

* **自适应采样**自动调整从 ASP.NET/ASP.NET Core 应用中的 SDK 以及从 Azure Functions 发送的遥测量。 这是使用 ASP.NET 或 ASP.NET Core SDK 时的默认采样。 自适应采样目前仅适用于 ASP.NET 服务器端遥测，以及 Azure Functions。

* **固定速率采样**会减少从 ASP.NET、ASP.NET Core 或 Java 服务器和用户浏览器发送的遥测量。 用户设定速率。 客户端和服务器将同步其采样，以便在“搜索”中可以在多个相关页面视图和请求之间导航。

* **引入采样**在 Application Insights 服务终结点上发生。 它会以设置的采样率丢弃一些来自应用的遥测数据。 它不会减少应用发送的遥测流量，但可帮助保持在每月配额内。 引入采样的大优点是，无需重新部署应用就可设置它。 引入采样统一适用于所有服务器和客户端，但在运行任何其他类型的采样时，不会应用引入采样。

> [!IMPORTANT]
> 如果自适应或固定速率采样方法正在运行，将禁用引入采样。

## <a name="adaptive-sampling"></a>自适应采样

自适应采样会影响从 Web 服务器应用发送至 Application Insights 服务终结点的遥测量。

> [!TIP]
> 使用 ASP.NET SDK 或 ASP.NET Core SDK 时，默认会启用自适应采样，并且默认会为 Azure Functions 启用它。

遥测量会自动调整以保持在指定的最大流量率，可通过 `MaxTelemetryItemsPerSecond` 设置对其进行控制。 如果应用程序产生的遥测数据很少（例如在调试时或由于用量较小），则只要数量低于 `MaxTelemetryItemsPerSecond`，采样处理器就不会丢弃项目。 随着遥测数据量的增加，采样率将会调整以实现目标量。 调整会定期重新计算，计算依据是传出的传输速率的移动平均线。

为了实现目标量，一些生成的遥测会被丢弃。 但与其他类型的采样一样，该算法会保留相关的遥测项。 例如，在“搜索”中检查遥测数据时，可以查找与特定异常相关的请求。

诸如请求速率和异常率等指标计数将进行调整以补偿采样率，以便它们在指标资源管理器中能够显示大约正确的值。

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>为 ASP.NET 应用程序配置自适应采样

> [!NOTE]
> 本部分适用于 ASP.NET 应用程序，而不适用于 ASP.NET Core 应用程序。 [稍后在本文档中了解如何为 ASP.NET Core 应用程序配置自适应采样。](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

在 [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 中，可以在 `AdaptiveSamplingTelemetryProcessor` 节点中调整多个参数。 显示的数字是默认值：

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    自适应算法的目标速率旨在针对**每个服务器主机**。 如果在多台主机上运行 Web 应用，应减小此值以保留在 Application Insights 门户的目标流量速率以内。

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    会重新评估当前遥测速率的间隔。 评估以移动平均线形式进行执行。 可能想要缩短此间隔（如果遥测很容易就激增）。

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    当采样百分比值更改时，多久后我们可以再次降低采样百分比以便捕获更少数据？

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    当采样百分比值更改时，多久后我们可以再次增加采样百分比以便捕获更多数据？

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    随着采样百分比的变化，我们可以设置的最小值是多少？

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    随着采样百分比的变化，我们可以设置的最大值是多少？

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    在移动平均的计算中，此参数指定要分配给最新值的权重。 使用等于或小于 1 的值。 较小的值会使算法不易受突然的更改影响。

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    应用刚刚启动时要采样的遥测量。 在调试时请不要减小此值。

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    不要采样的类型的分号分隔列表。 识别的类型为：`Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace`。 将传输指定类型的所有遥测；将对未指定的类型采样。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    要采样的类型的分号分隔列表。 识别的类型为：`Dependency`、`Event`、`Exception`、`PageView`、`Request`、`Trace`。 将对指定的类型采样；始终会传输其他类型的所有遥测。

**若要关闭**自适应采样，请从 `ApplicationInsights.config` 中删除 `AdaptiveSamplingTelemetryProcessor` 节点。

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>替换项：在代码中配置自适应采样

除了在 `.config` 文件中设置采样参数以外，还可以编程方式设置这些值。

1. 从 `.config` 文件中删除所有 `AdaptiveSamplingTelemetryProcessor` 节点。
2. 使用以下代码片段来配置自适应采样：

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    （[了解遥测处理器](../../azure-monitor/app/api-filtering-sampling.md#filtering)。）

也可以单独调整每个遥测类型的采样率，甚至可以完全排除特定类型的采取：

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>为 ASP.NET Core 应用程序配置自适应采样

ASP.NET Core 应用程序没有 `ApplicationInsights.config`，因此所有配置都是通过代码完成的。
默认情况下会为所有 ASP.NET Core 应用程序启用自适应采样。 你可以禁用或自定义采样行为。

#### <a name="turning-off-adaptive-sampling"></a>禁用自适应采样

添加 Application Insights 服务时，可以在 `Startup.cs` 文件中的方法 `ConfigureServices` 内使用 `ApplicationInsightsServiceOptions` 来禁用默认采样功能：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

以上代码将禁用自适应采样。 可以执行以下步骤使用更多自定义选项来添加采样。

#### <a name="configure-sampling-settings"></a>配置采样设置

如下所示使用 `TelemetryProcessorChainBuilder` 的扩展方法来自定义采样行为。

> [!IMPORTANT]
> 如果使用此方法配置采样，请确保在调用 `AddApplicationInsightsTelemetry()` 时将 `aiOptions.EnableAdaptiveSampling` 属性设置为 `false`。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>为 Azure Functions 配置自适应采样

按照[此页](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)中的说明，为 Azure Functions 中运行的应用配置自适应采样。

## <a name="fixed-rate-sampling"></a>固定速率采样

固定速率采样会减少从 Web 服务器和 Web 浏览器发送的流量。 与自适应采样不同，它会按用户确定的固定速率来降低遥测。 固定速率采样适用于 ASP.NET、ASP.NET Core、Java 和 Python 应用程序。

与其他采样方法一样，此方法也会保留相关的项。 它还将同步客户端和服务器采样，以便保留相关项，例如，如果在“搜索”中查看页面视图，可以查找其相关的服务器请求。 

在指标资源管理器中，诸如请求和异常计数等速率将乘以某个系数来补偿采样率，以便它们大致正确。

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>为 ASP.NET 应用程序配置固定速率采样

1. **禁用自适应采样**：在 [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 中，删除或注释掉 `AdaptiveSamplingTelemetryProcessor` 节点。

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **启用固定速率采样模块。** 将以下代码片段添加到 [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)：
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      如果不在 `ApplicationInsights.config` 文件中设置采样参数，可以编程方式设置这些值：

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    （[了解遥测处理器](../../azure-monitor/app/api-filtering-sampling.md#filtering)。）

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>为 ASP.NET Core 应用程序配置固定速率采样

1. **禁用自适应采样**：可以在 `ConfigureServices` 方法中使用 `ApplicationInsightsServiceOptions` 进行更改：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **启用固定速率采样模块。** 可以在方法 `Configure` 中进行更改，如以下代码片段中所示：

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>为 Java 应用程序配置固定速率采样

默认情况下，Java 代理和 SDK 中未启用任何采样。 目前，它仅支持固定速率采样。 Java 不支持自适应采样。

#### <a name="configuring-java-agent"></a>配置 Java 代理

1. 下载[applicationinsights-agent-3.0.0-preview](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.4/applicationinsights-agent-3.0.0-PREVIEW.4.jar)

1. 若要启用采样，请将以下`ApplicationInsights.json`内容添加到文件：

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>配置 Java SDK

1. 下载并配置最新的 web 应用程序[Application Insights JAVA SDK](../../azure-monitor/app/java-get-started.md)。

2. 通过将以下代码片段添加到文件来`ApplicationInsights.xml` **启用固定速率采样模块**：

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. 使用`Processor`标记的`FixedRateSamplingTelemetryProcessor`中的以下标记，可以在采样中包括或排除特定类型的遥测数据：
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

可在采样中包括或排除的遥测类型为`Dependency`：、 `Event`、 `Exception`、 `PageView`、 `Request`和。 `Trace`

> [!NOTE]
> 对于采样百分比，请选择一个接近于 100/N 的百分比，其中 N 是整数。  当前采样不支持其他值。

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>为 OpenCensus Python 应用程序配置固定速率采样

使用最新的 [OpenCensus Azure Monitor 导出程序](../../azure-monitor/app/opencensus-python.md)检测应用程序。

> [!NOTE]
> 固定速率采样不适用于指标导出程序。 这意味着自定义度量值是唯一不能配置采样的遥测类型。 度量值导出程序将发送它跟踪的所有遥测数据。

#### <a name="fixed-rate-sampling-for-tracing"></a>用于跟踪的固定速率采样 ####
可以在 `Tracer` 配置中指定 `sampler`。 如果未提供显式采样器，则`ProbabilitySampler`默认情况下将使用。 `ProbabilitySampler`默认情况下，将使用速率1/10000，这意味着每个10000请求中的一个将发送到 Application Insights。 如需指定采样率，请参阅下文。

若要指定采样率，请确保`Tracer`指定采样速率介于0.0 和1.0 之间的采样器。 采样率 1.0 代表 100%，这意味着所有请求都会作为遥测数据发送到 Application Insights。

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>日志的固定速率采样 ####
您可以通过修改`AzureLogHandler` `logging_sampling_rate`可选参数来配置的固定速率采样。 如果未提供任何参数，将使用采样速率1.0。 采样率 1.0 代表 100%，这意味着所有请求都会作为遥测数据发送到 Application Insights。

```python
handler = AzureLogHandler(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>为包含 JavaScript 的网页配置固定速率采样

基于 JavaScript 的网页可以配置为使用 Application Insights。 遥测是从用户浏览器中运行的客户端应用程序发送的，并且可以从任何服务器承载页面。

[为 Application Insights 配置基于 JavaScript 的网页](javascript.md)时，请修改从 Application Insights 门户获取的 javascript 代码片段。

> [!TIP]
> 在包含 JavaScript 的 ASP.NET 应用中，代码段通常会`_Layout.cshtml`出现在中。

在检测密钥前插入类似于 `samplingPercentage: 10,` 的行：

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

对于采样百分比，请选择一个接近于 100/N 的百分比，其中 N 是整数。 当前采样不支持其他值。

#### <a name="coordinating-server-side-and-client-side-sampling"></a>协调服务器端和客户端采样

客户端 JavaScript SDK 与服务器端 SDK 一起加入固定速率采样。 检测到的页面将仅从服务器端 SDK 使其在采样中包含的相同用户发送客户端遥测。 此逻辑设计用于维护客户端和服务器端应用程序之间的用户会话的完整性。 因此，在 Application Insights 中的任何特定遥测项都可以找到此用户或会话的所有其他遥测项，并在 "搜索" 中，可以在相关的页面视图和请求之间导航。

如果客户端和服务器端遥测不显示协调的示例：

* 验证是否已在服务器和客户端上启用了采样。
* 检查在客户端和服务器上设置的采样百分比相同。
* 确保 SDK 版本是 2.0 或更高版本。

## <a name="ingestion-sampling"></a>引入采样

引入采样在 web 服务器、浏览器和设备的遥测到达 Application Insights 服务终结点时运行。 尽管它未减少从应用发送的遥测流量，但它确实减少了由 Application Insights 处理和保留（并收费）的遥测量。

如果应用经常超过其每月配额，且没有使用任一种基于 SDK 的采样类型的选项，请使用此种类型的采样。 

在“使用情况和预估成本”页面中设置采样率：

![在应用程序的 "概述" 边栏选项卡中，依次单击 "设置"、"配额"、"示例"，然后选择采样率并单击 "更新"](./media/sampling/data-sampling.png)

与其他类型的采样一样，该算法会保留相关的遥测项。 例如，在“搜索”中检查遥测数据时，可以查找与特定异常相关的请求。 诸如请求速率和异常率等指标计数会正确保留。

被采样丢弃的数据点将无法在任何 Application Insights 功能（如[连续导出](../../azure-monitor/app/export-telemetry.md)）中使用。

当自适应采样或固定速率采样正在运行时，引入采样不会运行。 默认情况下，在使用 ASP.NET SDK 或 ASP.NET Core SDK 时，或在[Azure App Service](azure-web-apps.md)中或使用状态监视器启用 Application Insights 时，将默认启用自适应采样。 当 Application Insights 服务终结点接收到遥测数据时，它会检查遥测数据，如果采样率报告为小于100% （表示正在对遥测进行采样），则将忽略设置的引入采样速率。

> [!WARNING]
> "门户" 磁贴上显示的值指示为引入采样设置的值。 如果任何一种 SDK 采样（自适应采样或固定速率采样）正在进行，则它不表示实际采样率。

## <a name="when-to-use-sampling"></a>何时使用采样

通常，对于大多数小型和中型应用程序，不需要采样。 通过收集所有用户活动的数据，获取最有用的诊断信息和最准确的统计。 

采样的主要优势在于：

* 当应用程序在短时间间隔内发送非常高的遥测数据点时，Application Insights 服务将删除（"限制"）数据点。 采样可降低应用程序发生限制的可能性。
* 保持在定价层的数据点[配额](pricing.md)以内。 
* 减少遥测集合中的网络流量。 

### <a name="which-type-of-sampling-should-i-use"></a>应该使用哪种类型的采样？

**在以下情况下使用引入采样：**

* 通常使用每月的遥测配额。
* 收到来自用户 Web 浏览器的大量遥测数据。
* 使用的 SDK 版本不支持采样，例如版本早于 2 的 ASP.NET 版本。

**在以下情况下使用固定速率采样：**

* 你希望在客户端和服务器之间进行同步采样，以便在调查[搜索](../../azure-monitor/app/diagnostic-search.md)中的事件时，可以在客户端和服务器上的相关事件之间导航，如页面视图和 HTTP 请求。
* 对应用的适当采样百分比有信心。 它应该足够高以获取准确指标，但应低于超出定价配额和限制的速率。

**使用自适应采样：**

如果使用其他采样格式的条件不适用，我们建议使用自适应采样。 默认情况下，此设置在 ASP.NET/ASP.NET Core SDK 中启用。 在达到特定的最小速率之前，不会减少流量，因此，可能不会对使用率较低的站点进行抽样。

## <a name="knowing-whether-sampling-is-in-operation"></a>了解采样是否正在运行

若要查找实际采样率（无论是否已应用），请使用如下所示的[分析查询](../../azure-monitor/app/analytics.md)：

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

如果发现任何类型`RetainedPercentage`小于100，则将对该类型的遥测进行抽样。

> [!IMPORTANT]
> Application Insights 不采样会话、指标（包括自定义指标）或任何采样方法中的性能计数器遥测类型。 这些类型始终不会从采样中排除，因为对这些遥测类型而言，精度的降低可能非常不理想。

## <a name="how-sampling-works"></a>采样的工作方式

采样算法决定要丢弃哪些遥测项以及要保留哪些项。 不管是由 SDK 还是在 Application Insights 服务中进行的，都是如此。 采样决策取决于多个规则，目标是保留所有相互关联的数据点不变，同时在 Application Insights 中保持可操作和可靠而且即使有精简数据集的诊断体验。 例如，如果你的应用程序包含在示例中的失败请求，将保留其他遥测项（如为此请求记录的异常和跟踪）。 采样会将它们一起保留或删除。 因此，在 Application Insights 中查看请求详细信息时，始终可以看到请求以及其关联的遥测项。

采样决定取决于请求的操作 ID，这意味着属于特定操作的所有遥测项不是被保留就是被删除。 对于没有设置操作 ID 的遥测项（例如，不带 HTTP 上下文的异步线程报告的遥测项），采样只捕获每个类型的遥测项的百分比。

当遥测发回给用户时，Application Insights 服务会以收集时所使用的同一采样百分比来调整指标，以补偿缺失的数据点。 因此，当用户在 Application Insights 中查看遥测数据，会看到统计的正确近似值非常接近于实际值。

近似值的准确性很大程度上取决于配置的采样百分比。 此外，对于处理通常来自大量用户的类似请求的较大量的应用程序，准确性也会提升。 相反，对于不处理大量负载的应用程序，不需要采样，因为这些应用程序通常可以发送其所有遥测的同时保持在配额以内，而不会因限制而造成数据丢失。 

## <a name="frequently-asked-questions"></a>常见问题解答

*ASP.NET 和 ASP.NET Core Sdk 中的默认采样行为是什么？*

* 如果使用上述 SDK 的某个最新版本，则默认会启用自适应采样：每秒采样 5 个遥测项。
  默认情况下`AdaptiveSamplingTelemetryProcessor`添加了两个节点，一个节点在`Event`采样中包括该类型，而另一个`Event`节点在采样中排除了该类型。 此配置意味着，SDK 会尝试将遥测项限制为类型的`Event`五个遥测项和所有其他类型的五个遥测项，从而确保与其他`Events`遥测类型分别进行抽样。 事件通常用于业务遥测，在大多数情况下不应受到诊断遥测量的影响。
  
  下面显示了生成的`ApplicationInsights.config`默认文件。 在 ASP.NET Core 中，将在代码中启用相同的默认行为。 使用[此页前面部分中的示例](#configuring-adaptive-sampling-for-aspnet-core-applications)来更改此默认行为。

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

是否可以对遥测数据进行多次采样？**

* 不能。 如果项已采样，则 SamplingTelemetryProcessor 将不考虑项的采样。 同样适用于引入采样，这也不会将采样应用于已在 SDK 自身中进行了采样的那些项。

*为何不采样简单“收集每个遥测类型 %X”？*

* 虽然这种采样方法会提供较高的精度（以跃点数为准），但它会突破将诊断数据与每个用户、会话和请求关联的功能，这对于诊断而言至关重要。 因此，对于 "收集 X% 的应用用户的所有遥测项" 或 "收集所有应用程序请求的所有遥测项" 之类的策略，采样更好。 对于与请求不关联的遥测项（如后台异步处理），回退为“收集每个遥测类型 %X 的所有项”。 

*采样百分比是否随时间变化？*

* 是，自适应采样会根据当前观察到的遥测量，逐渐更改采样百分比。

*如果我使用固定速率采样，如何知道哪一个才样百分比最适合我的应用？*

* 一种方法是从自适应采样开始，了解应用选定的速率（请参阅上述问题），然后使用该速率切换到固定速率采样。 
  
    否则，就必须猜测。 分析 Application Insights 中当前遥测使用量、观察出现的任何限制，并估计所收集的遥测量。 这三个输入以及所选的定价层暗示你可能想要减少收集的遥测的数量。 但是，用户数量的增加或遥测量中的某些其他变化可能导致估计无效。

*如果将采样百分比设置得太低，会发生什么情况？*

* 过短的采样百分比会导致过多的采样，并在 Application Insights 尝试对数据量减少的数据的可视化效果进行补偿时降低近似值的准确性。 此外，您的诊断体验可能会受到负面影响，因为某些不常发生的失败或速度缓慢的请求可能会被采样。

*如果配置的采样百分比太高，会发生什么情况？*

* 配置过高的采样百分比（不太积极）将导致收集的遥测数据量不足。 仍可能会遇到与限制相关的遥测数据丢失，而使用 Application Insights 的成本可能因过高费用而超出预算。

*可以在哪些平台上使用采样？*

* 如果 SDK 未执行采样，则对于过于特定量的任何遥测，自动运行引入采样。 例如，如果你使用的是较旧版本的 ASP.NET SDK 或 Java SDK，则可以使用此配置。
* 如果你使用的是当前的 ASP.NET 或 ASP.NET Core Sdk （托管在 Azure 或自己的服务器上），则默认情况下会获取自适应采样，但你可以按上文所述切换到固定速率。 使用固定速率采样，浏览器 SDK 会自动同步到示例相关的事件。 
* 如果你使用的是当前 Java 代理，则可以配置`ApplicationInsights.json` （适用于 Java SDK， `ApplicationInsights.xml`配置）以启用固定速率采样。 默认情况下，采样处于关闭状态。 通过固定速率采样，浏览器 SDK 和服务器会自动同步到相关的示例事件。

*某些罕见事件始终需要查看。如何使它们超过采样模块？*

* 为此，最佳方法是编写一个自定义的 [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)，以便在想要保留的遥测项中将 `SamplingPercentage` 设置为 100，如下所示。 由于保证初始化程序将在遥测处理器（包括采样）之前运行，因此可以确保所有采样技术都会出于任何采样考虑而忽略此项目。 自定义遥测初始值设定项在 ASP.NET SDK、ASP.NET Core SDK、JavaScript SDK 和 Java SDK 中提供。 例如，可以使用 ASP.NET SDK 配置遥测初始值设定项：

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>旧版 SDK 版本

自适应采样适用于 ASP.NET v 2.0.0-beta3 和更高版本、Applicationinsights.config AspNetCore SDK v 2.2.0-beta1 和更高版本的 Application Insights SDK，默认情况下已启用。

固定速率采样是 SDK 在2.0.0 和 Java SDK 版本2.0.1 和更高版本中 ASP.NET 的一项功能。

在 ASP.NET SDK 的 v 2.5.0-beta2 和 ASP.NET Core SDK 的 v 2.2.0-beta3 之前，采样决策基于定义 "user" 的应用程序（即，最常见的 web 应用程序）的用户 ID 的哈希。 对于未定义用户的应用程序类型（如 Web 服务），采样决定取决于请求的操作 ID。 最新版本的 ASP.NET 和 ASP.NET Core Sdk 使用操作 ID 进行采样决策。

## <a name="next-steps"></a>后续步骤

* [筛选](../../azure-monitor/app/api-filtering-sampling.md)可以对 SDK 发送的内容提供更严格地控制。
* 阅读开发人员网络文章[Application Insights 的 "优化遥测](https://msdn.microsoft.com/magazine/mt808502.aspx)"。
