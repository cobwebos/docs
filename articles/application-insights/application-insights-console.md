---
title: 适用于控制台应用程序的 Azure Application Insights | Microsoft Docs
description: 监视 Web 应用程序的可用性、性能和使用情况。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2018
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 613a0329c7ca449096386f3efb4d3f1f75d9349b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631983"
---
# <a name="application-insights-for-net-console-applications"></a>适用于 .NET 控制台应用程序的 Application Insights
使用 [Application Insights](app-insights-overview.md) 可以监视 Web 应用程序的可用性、性能和使用情况。

需要 [Microsoft Azure](http://azure.com) 订阅。 使用 Microsoft 帐户登录，该帐户可能适用于 Windows、XBox Live 或其他 Microsoft 云服务。 团队可能拥有 Azure 组织订阅：要求所有者使用 Microsoft 帐户你将加入其中。

## <a name="getting-started"></a>入门

* 在 [Azure 门户](https://portal.azure.com)中，[创建 Application Insights 资源](app-insights-create-new-resource.md)。 对于应用程序类型，选择“常规”。
* 获取检测密钥的副本。 在创建的新资源的“概要”下拉列表中找到该密钥。 
* 安装最新的 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) 包。
* 在跟踪任何遥测之前，请先在代码中设置检测密钥（或设置 APPINSIGHTS_INSTRUMENTATIONKEY 环境变量）。 设置后，应能手动跟踪遥测并在 Azure 门户中查看

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

* 安装最新版本的 [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) 包 - 它可自动跟踪 HTTP、SQL 或一些其他外部依赖项调用。

可通过代码或 `ApplicationInsights.config` 文件初始化和配置 Application Insights。 请确保尽早进行初始化。 

> [!NOTE]
> 提到了 **ApplicationInsights.config** 的说明仅适用于以 .NET Standard 为目标的应用，不适用于 .NET Core 应用程序。 

### <a name="using-config-file"></a>使用配置文件

默认情况下，创建 `TelemetryConfiguration` 时，Application Insights SDK 在工作目录中查找 `ApplicationInsights.config` 文件

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

还可以指定该配置文件的路径。

```csharp
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration("ApplicationInsights.config");
```

有关详细信息，请参阅[配置文件参考](app-insights-configuration-with-applicationinsights-config.md)。

通过安装最新版本的 [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) 包，可以获取配置文件的完整示例。 此处是依赖项集合的“最小”配置，等效于代码示例。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>通过代码配置遥测集合

* 在应用程序启动期间创建并配置 `DependencyTrackingTelemetryModule` 实例 - 该实例必须是单一实例，并在应用程序生存期中保留。

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* 添加常用遥测初始值设定项

```csharp
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* 对于 .NET Framework Windows 应用，还可安装并初始化性能计数器收集器模块，如[此处](http://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)所述

#### <a name="full-example"></a>完整示例

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.Active;

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/NuGet/ApplicationInsights.config.install.xdt#L20
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>后续步骤
* [监视依赖项](app-insights-asp-net-dependencies.md)，查看 REST、SQL 或其他外部资源是否会降低性能。
* [使用 API](app-insights-api-custom-events-metrics.md)，发送自己的事件和指标以获取应用的性能和使用情况的更详细视图。
