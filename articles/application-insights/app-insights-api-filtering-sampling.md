---
title: "Azure Application Insights SDK 中的筛选和预处理 | Microsoft Docs"
description: "为 SDK 编写遥测处理器和遥测初始值设定项，以在将遥测发送到 Application Insights 门户之前筛选属性或将其添加到数据。"
services: application-insights
documentationcenter: 
author: beckylino
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: bwren
ms.openlocfilehash: c04cdabf53e4018877691f7272f6cc022b42df39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK 中的筛选和预处理遥测 | Microsoft Azure


可以为 Application Insights SDK 编写和配置插件，以在将遥测发送到 Application Insights 服务之前自定义遥测的捕获和处理方式。

* [采样](app-insights-sampling.md)可在不影响统计信息的情况下减少遥测量。 它还保留相关数据点，以便可以在诊断问题时导航这些点。 在门户中，总计相乘以补偿采样。
* 通过[适用于 ASP.NET](#filtering) 或 [Java](app-insights-java-filter-telemetry.md) 的遥测处理器筛选可先在 SDK 中选择或修改遥测，然后再将其发送到服务器。 例如，可以通过排除机器人请求减少遥测量。 但是，与采样相比，筛选是更基本的减少流量方法。 它允许更好地控制传输的内容，但你必须知道它会影响统计信息（例如，如果筛选出所有成功请求）。
* [遥测初始值设定项将属性添加](#add-properties)到从应用发送的任何属性，包括来自标准模块的遥测。 例如，可以添加计算得出的值；或在门户中筛选数据所依据的版本号。
* [SDK API](app-insights-api-custom-events-metrics.md) 用于发送自定义事件和指标。

开始之前：

* 在应用中安装[适用于 ASP.NET](app-insights-asp-net.md) 或 [Java](app-insights-java-get-started.md) 的 Application Insights SDK。

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>筛选：ITelemetryProcessor
使用此技术可以更直接地控制要在遥测流中包含或排除的内容。 可以将其与采样结合使用，也可以单独使用。

若要筛选遥测，编写遥测处理器并为 SDK 注册它。 所有遥测都通过处理器，可以选择从流中删除它或添加属性。 这包括来自标准模块的遥测（例如 HTTP 请求收集器和依赖项收集器）以及自行编写的遥测。 例如，可以筛选出有关机器人请求或成功依赖项调用的遥测。

> [!WARNING]
> 使用处理器筛选从 SDK 发送的遥测会使门户中看到的统计信息出现偏差，并使它难以跟进相关项目。
>
> 此时，考虑使用[采样](app-insights-sampling.md)。
>
>

### <a name="create-a-telemetry-processor-c"></a>创建遥测处理器 (C#)
1. 验证项目中的 Application Insights SDK 是版本 2.0.0 或更高版本。 在“Visual Studio 解决方案资源管理器”中右键单击项目并选择“管理 NuGet 包”。 在“NuGet 包管理器”中，选中 Microsoft.ApplicationInsights.Web。
2. 若要创建筛选器，请实现 ITelemetryProcessor。 这是另一个扩展点，如遥测模块、遥测初始值设定项和遥测通道。

    请注意，遥测处理器构建一个处理链。 实例化遥测处理器时，会传递指向该链中下一个处理器的链接。 将遥测数据点传递到处理方法时，它实现该方法，然后调用该链中的下一个遥测处理器。

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. 在 ApplicationInsights.config 中插入此项：

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

（本部分与初始化采样筛选器部分相同。）

可通过在类中提供公共命名属性，传递 .config 文件中的字符串值。

> [!WARNING]
> 注意将 .config 文件中的类型名称和任何属性名称匹配到代码中的类和属性名称。 如果 .config 文件引用不存在的类型或属性，该 SDK 在发送任何遥测时可能静默失败。
>
>

**或者，**可以在代码中初始化筛选器。 在合适的初始化类（例如，Global.asax.cs 中的 AppStart）中，将处理器插入链：

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

在此点后创建的 TelemetryClients 将使用处理器。

### <a name="example-filters"></a>示例筛选器
#### <a name="synthetic-requests"></a>综合请求
筛选出机器人和 Web 测试。 尽管指标资源管理器提供筛选出综合源的选项，但此选项可通过在 SDK 上筛选它们减少流量。

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>身份验证失败
筛选出带有“401”响应的请求。

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>筛选出快速远程依赖项调用
如果只想诊断速度较慢的调用，则筛选出快速调用。

> [!NOTE]
> 这会使门户上看到的统计信息出现偏差。 依赖项图看起来就像所有依赖项调用均失败一样。
>
>

``` C#

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

## <a name="add-properties-itelemetryinitializer"></a>添加属性：ITelemetryInitializer
使用遥测初始值设定项定义通过所有遥测发送的全局属性；并覆盖标准遥测模块的所选行为。

例如，Web 程序包的 Application Insights 将收集有关 HTTP 请求的遥测。 默认情况下，它标记为所有请求失败，并且响应代码 >= 400。 但是，如果希望将 400 视为成功，可以提供一个设置成功属性的遥测初始值设定项。

如果提供了遥测初始值设定项，只要调用任何 Track*() 方法，就会调用它。 这包括由标准遥测模块调用的方法。 按照约定，这些模块不会设置已由初始值设定项设置的任何属性。

**定义初始值设定项**

*C#*

```C#

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
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**加载初始值设定项**

在 ApplicationInsights.config 中：

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*或者，*可以在代码中实例化初始值设定项，例如在 Global.aspx.cs 中：

```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[查看此示例的详细信息。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

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

                // To check the telemetry item’s type - for example PageView:
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

有关 telemetryItem 上可用的非自定义属性摘要，请参阅[Application Insights 导出数据模型](app-insights-export-data-model.md)。

可添加任意数量的初始值设定项。

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 和 ITelemetryInitializer
遥测处理器和遥测初始值设定项之间的区别是什么？

* 它们的用途有一些重叠之处：二者均可用于向遥测添加属性。
* TelemetryInitializers 始终在 TelemetryProcessors 之前运行。
* TelemetryProcessors 允许完全替换或丢弃遥测项。
* TelemetryProcessors 不处理性能计数器遥测。


## <a name="reference-docs"></a>参考文档
* [API 概述](app-insights-api-custom-events-metrics.md)
* [ASP.NET 参考](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK 代码
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>后续步骤
* [搜索事件和日志](app-insights-diagnostic-search.md)
* [采样](app-insights-sampling.md)
* [故障排除](app-insights-troubleshoot-faq.md)
