---
title: Azure Application Insights SDK 中的筛选和预处理 | Microsoft Docs
description: 为 SDK 编写遥测处理器和遥测初始值设定项，以在将遥测发送到 Application Insights 门户之前筛选属性或将其添加到数据。
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 9f4df83ed60ba94913702b9a32a298f0ac62f9f4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361815"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK 中的筛选和预处理遥测 | Microsoft Azure

你可以编写和配置 Application Insights SDK 的插件，以自定义在将遥测发送到 Application Insights 服务之前如何对其进行充实和处理。

* [采样](sampling.md)可在不影响统计信息的情况下减少遥测量。 它还保留相关数据点，以便可以在诊断问题时导航这些点。 在门户中，总计相乘以补偿采样。
* 通过遥测处理器进行筛选，可以在将遥测发送到服务器之前，筛选出 SDK 中的遥测数据。 例如，可以通过排除机器人请求减少遥测量。 筛选是减少流量比采样更基本的方法。 它允许更好地控制传输的内容，但你必须知道它会影响统计信息（例如，如果筛选出所有成功请求）。
* [遥测初始值设定项添加或修改](#add-properties)从应用发送的任何遥测的属性，包括来自标准模块的遥测。 例如，可以添加计算得出的值；或在门户中筛选数据所依据的版本号。
* [SDK API](../../azure-monitor/app/api-custom-events-metrics.md) 用于发送自定义事件和指标。

开始之前：

* 为你的应用程序安装适当的 SDK： [ASP.NET](asp-net.md)、 [ASP.NET CORE](asp-net-core.md)、[非 HTTP/辅助角色（适用于 .Net/.Net Core](worker-service.md)、 [Java](../../azure-monitor/app/java-get-started.md)或[JavaScript](javascript.md) ）

<a name="filtering"></a>

## <a name="filtering"></a>Filtering

此方法可让你直接控制遥测流中包含或排除的内容。 筛选可用于删除从发送到 Application Insights 的遥测项。 可以将其与采样结合使用，也可以单独使用。

若要筛选遥测数据，请编写遥测处理器，并将其注册到 `TelemetryConfiguration`。 所有遥测数据都通过你的处理器，你可以选择将其从流中删除，或将其发送到链中的下一个处理器。 这包括来自标准模块的遥测数据，例如 HTTP 请求收集器、依赖关系收集器以及您自己跟踪的遥测。 例如，可以筛选出有关机器人请求或成功依赖项调用的遥测。

> [!WARNING]
> 使用处理器筛选从 SDK 发送的遥测会使门户中看到的统计信息出现偏差，并使它难以跟进相关项目。
>
> 此时，考虑使用[采样](../../azure-monitor/app/sampling.md)。
>
>

### <a name="create-a-telemetry-processor-c"></a>创建遥测处理器 (C#)

1. 若要创建筛选器，请实现 `ITelemetryProcessor`。

    请注意，遥测处理器构建一个处理链。 实例化遥测处理器时，将为您提供对链中下一个处理器的引用。 将遥测数据点传递到处理方法时，它将执行其工作，然后调用（或不调用）链中的下一个遥测处理器。

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. 添加处理器。

**ASP.NET 应用**在 Applicationinsights.config 中插入此代码片段：

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

可通过在类中提供公共命名属性，传递 .config 文件中的字符串值。

> [!WARNING]
> 注意将 .config 文件中的类型名称和任何属性名称匹配到代码中的类和属性名称。 如果 .config 文件引用不存在的类型或属性，该 SDK 在发送任何遥测时可能静默失败。
>

**或者，** 可以在代码中初始化筛选器。 在合适的初始化类中（例如 AppStart），在 `Global.asax.cs` 中插入处理器：

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

在此点后创建的 TelemetryClients 将使用处理器。

**ASP.NET Core/辅助角色服务应用**

> [!NOTE]
> 使用 `ApplicationInsights.config` 或 `TelemetryConfiguration.Active` 添加处理器对 ASP.NET Core 应用程序无效，或者如果使用 WorkerService SDK，则无效。

对于使用[ASP.NET Core](asp-net-core.md#adding-telemetry-processors)或[WorkerService](worker-service.md#adding-telemetry-processors)编写的应用，通过在 `IServiceCollection`上使用 `AddApplicationInsightsTelemetryProcessor` 扩展方法来添加新 `TelemetryProcessor`，如下所示。 此方法在 `Startup.cs` 类 `ConfigureServices` 方法中调用。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>示例筛选器

#### <a name="synthetic-requests"></a>综合请求

筛选出机器人和 Web 测试。 尽管指标资源管理器提供筛选合成源的选项，但此选项可通过在 SDK 本身对流量进行筛选来减少流量和引入大小。

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>身份验证失败

筛选出带有“401”响应的请求。

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>筛选出快速远程依赖项调用

如果只想诊断速度较慢的调用，则筛选出快速调用。

> [!NOTE]
> 这会使门户上看到的统计信息出现偏差。
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>诊断依赖项问题

[本博客](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/)介绍了通过自动将常规 Ping 发送到依赖项诊断依赖项问题的项目。

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web 应用程序

**使用 ITelemetryInitializer 进行筛选**

1. 创建遥测初始值设定项回调函数。 回调函数采用作为参数的 `ITelemetryItem`，这是正在处理的事件。 从此回调返回 `false` 将导致筛选掉遥测项。  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. 添加遥测初始化表达式回叫：

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>添加/修改属性： ITelemetryInitializer


使用遥测初始值设定项，通过其他信息和/或重写标准遥测模块设置的遥测属性，来充实遥测数据。

例如，Web 包的 Application Insights 收集有关 HTTP 请求的遥测数据。 默认情况下，它标记为所有请求失败，并且响应代码 >= 400。 但是，如果希望将 400 视为成功，可以提供一个设置成功属性的遥测初始值设定项。

如果提供了遥测初始值设定项，只要调用任何 Track*() 方法，就会调用它。 这包括标准遥测模块调用 `Track()` 方法。 按照约定，这些模块不会设置已由初始值设定项设置的任何属性。 在调用遥测处理器之前调用遥测初始值设定项。 因此，通过初始值设定项完成的任何根据对处理器都可见。

**定义初始值设定项**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET apps：加载初始值设定项**

在 ApplicationInsights.config 中：

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*或者，* 可以在代码中实例化初始值设定项，例如在 Global.aspx.cs 中：

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[查看此示例的详细信息。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/辅助角色服务应用：加载初始值设定项**

> [!NOTE]
> 使用 `ApplicationInsights.config` 或使用 `TelemetryConfiguration.Active` 添加初始值设定项对于 ASP.NET Core 应用程序无效，或者如果使用 WorkerService SDK，则无效。

对于使用[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers)或[WorkerService](worker-service.md#adding-telemetryinitializers)编写的应用，通过将新 `TelemetryInitializer` 添加到依赖关系注入容器来添加新，如下所示。 这是在 `Startup.ConfigureServices` 方法中完成的。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Java 遥测初始值设定项

[Java SDK 文档](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

然后在 applicationinsights.xml 文件中注册自定义初始值设定项。

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript 遥测初始值设定项
*JavaScript*

在从门户获取的初始化代码后立即插入遥测初始值设定项：

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

有关 telemetryItem 上可用的非自定义属性摘要，请参阅[Application Insights 导出数据模型](../../azure-monitor/app/export-data-model.md)。

您可以根据自己的需要添加任意多个初始值设定项，并按添加顺序调用它们。

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python 遥测处理器

OpenCensus Python 中的遥测处理器是在导出遥测之前调用的回调函数。 回调函数必须接受[信封](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86)数据类型作为其参数。 若要筛选出要导出的遥测，请确保回调函数返回 `False`。 可在[此处](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)查看信封中 Azure Monitor 数据类型的架构。

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
你可以根据需要添加任意多个处理器，并按添加它们的顺序调用它们。 如果一个处理器应引发异常，则它不会影响以下处理器。

### <a name="example-telemetryinitializers"></a>示例 TelemetryInitializers

#### <a name="add-custom-property"></a>添加自定义属性

下面的示例初始值设定项将自定义属性添加到每个跟踪的遥测。

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>添加云角色名称

下面的示例初始值设定项将云角色名称设置为每个跟踪的遥测。

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 和 ITelemetryInitializer

遥测处理器和遥测初始值设定项之间的区别是什么？

* 您可以对其执行的操作有一些重叠之处：这两种方法可用于添加或修改遥测的属性，但建议为该目的使用初始值设定项。
* TelemetryInitializers 始终在 TelemetryProcessors 之前运行。
* 可以多次调用 TelemetryInitializers。 按照约定，它们不会设置任何已设置的属性。
* TelemetryProcessors 允许完全替换或丢弃遥测项。
* 确保为每个遥测项调用所有已注册的 TelemetryInitializers。 对于遥测处理器，SDK 保证调用第一个遥测处理器。 不管是否调用了处理器的其余部分，都由前面的遥测处理器确定。
* 使用 TelemetryInitializers 通过其他属性或覆盖现有的属性来充实遥测数据。 使用 TelemetryProcessor 筛选出遥测数据。

## <a name="troubleshooting-applicationinsightsconfig"></a>ApplicationInsights.config 故障排除

* 确认完全限定的类型名称和程序集名称是正确的。
* 确认 applicationinsights.config 文件在输出目录中并且包含所有最新更改。

## <a name="reference-docs"></a>参考文档

* [API 概述](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET 参考](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK 代码

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>后续步骤
* [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)
* [采样](../../azure-monitor/app/sampling.md)
* [故障排除](../../azure-monitor/app/troubleshoot-faq.md)
