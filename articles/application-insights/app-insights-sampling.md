---
title: Azure Application Insights 中的遥测采样 | Microsoft 文档
description: 如何使受控制的遥测数据的卷。
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: 8f0c6e6567e82f885bb5cd0c6b6af797b393969c
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
ms.locfileid: "32309600"
---
# <a name="sampling-in-application-insights"></a>在 Application Insights 中采样


采样是 [Azure Application Insights](app-insights-overview.md) 中的一项功能。 建议用于降低遥测流量和存储，同时保留采用统计方式的应用程序数据的正确分析。 筛选器会选择相关项，以便可以在执行诊断调查时在不同项之间导航。
当指标计数显示在门户时，它们将重新标准化以考虑采样，从而尽可能降低对统计的任何影响。

采样可以降低流量和数据成本，并可帮助用户避免限制。

## <a name="in-brief"></a>简单地说：
* 采样会保留 *n* 条记录中的 1 条并丢弃其余记录。 例如，它可能会保留 5 件事件中的 1 件，采样率为 20%。 
* 如果应用程序发送了大量遥测，则 ASP.NET Web 服务器应用中自动发生采样。
* 还可以在门户中的“使用情况和预估成本”页面、config 文件的 ASP.NET SDK 或 ApplicationInsights.xml 文件的 Java SDK 中手动设置采样，以便同时减少网络流量。
* 如果记录自定义事件，并且想要确定事件集是一同保留还是丢弃，请确保它们的 OperationId 值相同。
* 采样除数 *n* 会在每个记录的属性 `itemCount` 中报告，在“搜索”中它出现在友好名称“请求计数”或“事件计数”下。 如果采样不在运行，则 `itemCount==1`。
* 如果要编写分析查询，应[考虑采样](app-insights-analytics-tour.md#counting-sampled-data)。 特别是，应使用 `summarize sum(itemCount)`，而不是仅对记录进行计数。

## <a name="types-of-sampling"></a>采样类型
有三种备用采样方法：

* **自适应采样**自动调整从 ASP.NET 应用的 SDK 所发送的遥测量。 从 SDK v 2.0.0-beta3 开始，这是默认的采样方法。 自适应采样目前仅适用于 ASP.NET 服务器端遥测。 对于面向完整 Framework 的 Asp.NET Core 应用程序，自 Microsoft.ApplicationInsights.AspNetCore SDK 的版本 1.0.0 开始均支持自适应采样。 对于面向 NetCore 的 Asp.NET Core 应用程序，自 Microsoft.ApplicationInsights.AspNetCore SDK 的 2.2.0-beta1 开始均支持自适应采样。

* 固定速率采样会减少从 ASP.NET 或 Java 服务器和用户浏览器发送的遥测量。 用户设定速率。 客户端和服务器将同步其采样，以便在“搜索”中可以在多个相关页面视图和请求之间导航。
* **引入采样**在 Azure 门户中正常工作。 它会以设置的采样率丢弃一些来自应用的遥测数据。 它不会减少应用发送的遥测流量，但可帮助保持在每月配额内。 引入采样的大优点是，无需重新部署应用就可设置它，并且它统一适用于所有服务器和客户端。 

如果自适应采样或固定速率采样正在运行，将禁用引入采样。

## <a name="ingestion-sampling"></a>引入采样
当来自 Web 服务器、浏览器和设备的遥测达到 Application Insights 服务终结点时，将运行此采样形式。 尽管它未减少从应用发送的遥测流量，但它确实减少了由 Application Insights 处理和保留（并收费）的遥测量。

如果应用经常超过其每月配额，且没有使用任一种基于 SDK 的采样类型的选项，请使用此种类型的采样。 

在“使用情况和预估成本”页面中设置采样率：

![从应用程序“概述”边栏选项卡中，依次单击“设置”、“配额”、“示例”，选择采样率，并单击“更新”。](./media/app-insights-sampling/04.png)

与其他类型的采样一样，该算法会保留相关的遥测项。 例如，在“搜索”中检查遥测数据时，可以查找与特定异常相关的请求。 诸如请求速率和异常率等指标计数会正确保留。

被采样丢弃的数据点将无法在任何 Application Insights 功能（如[连续导出](app-insights-export-telemetry.md)）中使用。

当运行基于 SDK 的自适应采样或固定速率采样时，不会运行引入采样。 请注意，如果在 Visual Studio 中或通过使用状态监视器启用 ASP.NET SDK 并且禁用引入采样，此时将默认启用自适应采样。 如果 SDK 的采样率小于 100%，将忽略设定的引入采样率。

> [!WARNING]
> 磁贴上显示的值指示为引入采样所设定的值。 如果 SDK 采样不在运行，它并不表示实际采样率。
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Web 服务器上的自适应采样
自适应采样适用于 Application Insights SDK for ASP.NET v 2.0.0-beta3 及更高版本，默认处于启用状态。 

自适应采样会影响从 Web 服务器应用发送至 Application Insights 服务终结点的遥测量。 遥测量会自动调整，以保持在指定的最大流量率。

它无法在低遥测量下运行，因此调试中的应用或具有低使用量的网站不会受到影响。

为了实现目标量，一些生成的遥测会被丢弃。 但与其他类型的采样一样，该算法会保留相关的遥测项。 例如，在“搜索”中检查遥测数据时，可以查找与特定异常相关的请求。 

诸如请求速率和异常率等指标计数将进行调整以补偿采样率，以便它们在指标资源管理器中能够显示大约正确的值。

### <a name="update-nuget-packages"></a>更新 NuGet 包 ###

将项目的 NuGet 包更新为最新的 Application Insights 预发行版本。 在 Visual Studio 中，右键单击解决方案资源管理器中的项目、选择“管理 NuGet 包”、选中“包括预发行版本”，并搜索 Microsoft.ApplicationInsights.Web。 

### <a name="configuring-adaptive-sampling"></a>配置自适应采样 ###

在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中，可以调整 `AdaptiveSamplingTelemetryProcessor` 节点中的多个参数。 显示的数字是默认值：

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    自适应算法的目标速率旨在针对**每个服务器主机**。 如果在多台主机上运行 Web 应用，应减小此值以保留在 Application Insights 门户的目标流量速率以内。
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    会重新评估当前遥测速率的间隔。 评估以移动平均线形式进行执行。 可能想要缩短此间隔（如果遥测很容易就激增）。
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    当采样百分比值更改时，多久后我们可以再次降低采样百分比以便捕获更少数据。
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    当采样百分比值更改时，多久后我们可以再次增加采样百分比以便捕获更多数据。
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    随着采样百分比的变化，我们可以设置的最小值是多少。
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    随着采样百分比的变化，我们可以设置的最大值是多少。
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    在移动平均线的计算中，权重分配给最新的值。 使用等于或小于 1 的值。 较小的值会使算法不易受突然的更改影响。
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    当应用已启动时分配的值。 不要在调试时减少该值。 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    不要采样的分号分隔类型列表。 已识别的类型包括：Dependency、Event、Exception、PageView、Request、Trace。 指定类型的所有实例都会传输；对未指定的类型进行采样。

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    要采样的分号分隔类型列表。 已识别的类型包括：Dependency、Event、Exception、PageView、Request、Trace。 将对指定的类型进行采样；其他类型的所有实例始终都会传输。


**若要关闭**自适应采样，请从 applicationinsights-config 中删除 AdaptiveSamplingTelemetryProcessor 节点。

### <a name="alternative-configure-adaptive-sampling-in-code"></a>备选： 在代码中配置自适应采样
除了在 .config 文件中设置采样参数以外，还可以编程方式设置这些值。 这样一来，可以指定在重新计算采样率时调用的回调函数。 例如，可以使用它来了解正在使用的采样率。

从 .config 文件中删除 `AdaptiveSamplingTelemetryProcessor` 节点。

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

（[了解遥测处理器](app-insights-api-filtering-sampling.md#filtering)。）

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>使用 JavaScript 的网页采样
可以针对任何服务器中的固定速率采样配置网页。 

[为 Application Insights 配置网页](app-insights-javascript.md)时，修改从 Application Insights 门户获取的 JavaScript 代码片段。 （在 ASP.NET 应用中，代码段通常出现在 _Layout.cshtml 中。）在检测密钥前插入类似于 `samplingPercentage: 10,` 的行：

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

对于采样百分比，请选择一个接近于 100/N 的百分比，其中 N 是整数。  当前采样不支持其他值。

如果还在服务器上启用了固定速率采样，客户端和服务器将同步，以便在“搜索”中可以在多个相关页面视图和请求之间导航。

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>ASP.NET 和 Java 网站的固定速率采样
固定速率采样会减少从 Web 服务器和 Web 浏览器发送的流量。 与自适应采样不同，它会按用户确定的固定速率来降低遥测。 它还将同步客户端和服务器采样，以便保留相关项，例如，如果在“搜索”中查看页面视图，可以查找其相关的请求。

采样算法保留相关的项。 对于每个 HTTP 请求事件，请求和相关的事件会一起被丢弃或传输。 

在指标资源管理器中，诸如请求和异常计数等速率将乘以某个系数来补偿采样率，以便它们大致正确。

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>使用 ASP.NET 配置固定速率采样 ###

1. **将项目的 NuGet 包更新为**最新的 Application Insights *预发行*版本。 在 Visual Studio 中，右键单击解决方案资源管理器中的项目、选择“管理 NuGet 包”、选中“包括预发行版本”，并搜索 Microsoft.ApplicationInsights.Web。 
2. **禁用自适应采样**：在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中，删除或注释掉 `AdaptiveSamplingTelemetryProcessor` 节点。
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **启用固定速率采样模块。** 将以下片段添加到 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)：
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>使用 JAVA 配置固定速率采样 ###

1. 使用最新的 [Application Insights Java SDK](app-insights-java-get-started.md)下载并配置 Web 应用程序

2. 通过将下面的代码片段添加到 ApplicationInsights.xml 文件，启用固定速率采样模块。

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuilrInProcessors>
    <TelemetryProcessors/>
```

3. 可以使用处理器标记“FixedRateSamplingTelemetryProcessor”中的以下标记，在采样中包括或排除特定类型的遥测
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
可在采样中包括或排除的遥测类型为：Dependency、Event、Exception、PageView、Request 和 Trace。

> [!NOTE]
> 对于采样百分比，请选择一个接近于 100/N 的百分比，其中 N 是整数。  当前采样不支持其他值。
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>备选：启用服务器代码中的固定速率采样
除了在 .config 文件中设置采样参数以外，还可以编程方式设置这些值。 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

（[了解遥测处理器](app-insights-api-filtering-sampling.md#filtering)。）

## <a name="when-to-use-sampling"></a>何时使用采样？
如果使用 ASP.NET SDK 版本 2.0.0-beta3 或更高版本，会自动启用自适应采样。 无论使用何种 SDK 版本，都可以启用引入采样，以允许 Application Insights 对收集的数据采样。

默认情况下，Java SDK 中不启用任何采样。 目前，它仅支持固定速率采样。 Java SDK 不支持自适应采样。

一般情况下，对于大多数小型和中型应用程序，不需要采样。 通过收集所有用户活动的数据，获取最有用的诊断信息和最准确的统计。 

采样的主要优势在于：

* 当应用以短时间间隔发送速率很高的遥测时，Application Insights 服务会删除（“限制”）数据点。 
* 保持在定价层的数据点[配额](app-insights-pricing.md)以内。 
* 减少遥测集合中的网络流量。 

### <a name="which-type-of-sampling-should-i-use"></a>应该使用哪种类型的采样？
**在以下情况下使用引入采样：**

* 遥测经常超出每月配额。
* 使用的 SDK 版本不支持采样，例如版本早于 2 的 ASP.NET 版本。
* 收到来自用户 Web 浏览器的大量遥测。

**在以下情况下使用固定速率采样：**

* 使用 Application Insights SDK for ASP.NET Web 服务版本 2.0.0 或更高版本或者 Java SDK v2.0.1 或更高版本，并且
* 希望同步客户端和服务器之间的采样，因此，调查 [搜索](app-insights-diagnostic-search.md) 中的事件时，可以在客户端和服务器上的相关事件之间导航，例如页面视图和 http 请求。
* 对应用的适当采样百分比有信心。 它应该足够高以获取准确指标，但应低于超出定价配额和限制的速率。 

**使用自适应采样：**

如果使用其他采样格式的条件不适用，我们建议使用自适应采样。 在 ASP.NET 服务器 SDK 版本 2.0.0-beta3 或更高版本中，默认启用该采样。 它不会将流量减少到特定最低速率，因此不会影响使用率较低的站点。

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>如何知道采样是否在运行？
若要查找实际采样率（无论是否已应用），请使用如下所示的[分析查询](app-insights-analytics.md)：

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

在每个保留的记录中，`itemCount` 指示它表示的原始记录数，其等于 1 +（即以前已放弃的记录数）。 

## <a name="how-does-sampling-work"></a>采样如何运行？
ASP.NET 版本 2.0.0 和 Java SDK 版本 2.0.1 及以上版本中 SDK 的固定速率采样功能。 自适应采样是 ASP.NET 版本（从 2.0.0 开始）中 SDK 的一项功能。 引入采样是 Application Insights 服务的一项功能，并且可以在 SDK 未执行采样时运行。 

采样算法决定要删除哪些遥测项，以及要保留哪些遥测项（无论它是在 SDK 中还是在 Application Insights 服务中）。 采样决策取决于多个规则，目标是保留所有相互关联的数据点不变，同时在 Application Insights 中保持可操作和可靠而且即使有精简数据集的诊断体验。 例如，如果有失败的请求，应用汇发送其他遥测项（例如此请求记录的异常和跟踪），采样将不会拆分此请求和其他遥测数据。 采样会将它们一起保留或删除。 因此，在 Application Insights 中查看请求详细信息时，始终可以看到请求以及其关联的遥测项。 

采样决定取决于请求的操作 ID，这意味着属于特定操作的所有遥测项不是被保留就是被删除。 对于未设定操作 ID 的遥测项（例如，异步线程报告的且没有 http 上下文的遥测项），采样仅捕获每个类型的遥测项百分比。 对于 .NET SDK 的 2.5.0-beta2 之前版本以及 ASP.NET Core SDK 的 2.2.0-beta3 之前版本，采样决定基于定义“用户”的应用程序用户 ID 的哈希代码（即最典型 web 应用程序）。 对于未定义用户的应用程序类型（如 Web 服务），采样决定取决于请求的操作 ID。

当遥测发回给用户时，Application Insights 服务会以收集时所使用的同一采样百分比来调整指标，以补偿缺失的数据点。 因此，当用户在 Application Insights 中查看遥测数据，会看到统计的正确近似值非常接近于实际值。

近似值的准确性很大程度上取决于配置的采样百分比。 此外，对于处理通常来自大量用户的类似请求的较大量的应用程序，准确性也会提升。 相反，对于不处理大量负载的应用程序，不需要采样，因为这些应用程序通常可以发送其所有遥测的同时保持在配额以内，而不会因限制而造成数据丢失。 

> [!WARNING]
> Application Insights 不会对指标和会话遥测类型采样。 对于这些遥测类型，可能非常有必要降低精度。
> 

### <a name="adaptive-sampling"></a>自适应采样
自适应采样添加用于监视当前 SDK 的传输速率的组件，并调整采样百分比，以便尝试保持在目标最大速率以内。 调整会定期重新计算，计算依据是传出的传输速率的移动平均线。

## <a name="sampling-and-the-javascript-sdk"></a>采样和 JavaScript SDK
客户端 (JavaScript) SDK 与服务器端 SDK 一同参与固定速率采样。 检测到的页面将仅从服务器端决定“纳入采样”的相同用户发送客户端遥测。 此逻辑的设计目的是维护跨客户端和服务器端的用户会话的完整性。 因此，通过 Application Insights 中的任何特定遥测项，可查找此用户或会话的所有其他遥测项。 

*我的客户端和服务器端遥测未显示上面描述的协调示例。*

* 确认已在服务器和客户端上启用固定速率采样。
* 确保 SDK 版本是 2.0 或更高版本。
* 检查在客户端和服务器上设置的采样百分比相同。

## <a name="frequently-asked-questions"></a>常见问题
*为何不采样简单“收集每个遥测类型 %X”？*

* 虽然此采样方法提供非常高精度的指标近似值，但它会破坏每个用户、会话和请求关联诊断数据的能力，而这对于诊断而言很关键。 因此，对于“收集 %X 应用用户的所有遥测项”或“收集 %X 应用请求的所有遥测”逻辑，采样的效果更佳。 对于与请求不关联的遥测项（如后台异步处理），回退为“收集每个遥测类型 %X 的所有项”。 

*采样百分比是否随时间变化？*

* 是，自适应采样会根据当前观察到的遥测量，逐渐更改采样百分比。

*如果我使用固定速率采样，如何知道哪一个才样百分比最适合我的应用？*

* 一种方法是从自适应采样开始，了解应用选定的速率（请参阅上述问题），然后使用该速率切换到固定速率采样。 
  
    否则，就必须猜测。 分析 Application Insights 中当前遥测使用量、观察出现的任何限制，并估计所收集的遥测量。 这三个输入以及所选的定价层暗示你可能想要减少收集的遥测的数量。 但是，用户数量的增加或遥测量中的某些其他变化可能导致估计无效。

*如果我配置的采样百分比过低，会发生什么情况？*

* 当 Application Insights 尝试补偿数据量减少的数据可视化效果时，过低的采样百分比（过度采样）会降低近似值的准确性。 此外，诊断体验可能会受到负面影响，由于可能会采样某些很少出现的请求或缓慢请求。

*如果我配置的采样百分比过高，会发生什么情况？*

* 将配置过高的采样百分比（力度不够）会导致收集的遥测量减少不够。 仍可能会遇到与限制相关的遥测数据丢失，而使用 Application Insights 的成本可能因过高费用而超出预算。

*可以在哪些平台上使用采样？*

* 如果 SDK 未执行采样，则对于过于特定量的任何遥测，自动运行引入采样。 例如，如果使用的是较旧版本的 ASP.NET SDK 或以前版本的 Java SDK（1.0.10 或更早版本），这会起作用。
* 如果使用的是 ASP.NET SDK 版本 2.0.0 及更高版本（托管在 Azure 中或者自己的服务器上），默认运行自适应采样，不过可以切换到固定速率（如上所述）。 使用固定速率采样，浏览器 SDK 会自动同步到示例相关的事件。 
* 如果使用的是 Java SDK 版本 2.0.1 或更高版本，则可以配置 ApplicationInsights.xml 以打开固定速率采样。 默认情况下，采样处于关闭状态。 使用固定速率采样，浏览器 SDK 会自动同步到示例相关的事件。

*我总是想要查看某些罕见的事件。如何让它们通过采样模块？*

* 使用新的 TelemetryConfiguration（不是默认活动项）初始化 TelemetryClient 的单独实例。 使用它来发送罕见的事件。

## <a name="next-steps"></a>后续步骤
* [筛选](app-insights-api-filtering-sampling.md)可以对 SDK 发送的内容提供更严格地控制。

