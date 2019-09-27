---
title: 辅助角色服务应用的 Application Insights （非 HTTP 应用） |Microsoft Docs
description: 通过 Application Insights 监视 .NET Core/.NET Framework 非 HTTP 应用。
services: application-insights
documentationcenter: .net
author: cithomas
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: cithomas
ms.openlocfilehash: 2b92b58b75389f84f1ea18c6d48538c343a13212
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337961"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>辅助角色服务应用程序的 Application Insights （非 HTTP 应用程序）

Application Insights 正在发布名`Microsoft.ApplicationInsights.WorkerService`为的新 SDK，它最适用于非 HTTP 工作负荷，例如消息传递、后台任务、控制台应用程序等。这些类型的应用程序不具有传入 HTTP 请求（如传统 ASP.NET/ASP.NET Core Web 应用程序）的概念，因此不支持对[ASP.NET](asp-net.md)或[ASP.NET Core](asp-net-core.md)应用程序使用 Application Insights 包。

新 SDK 本身不会进行任何遥测收集。 相反，它会引入其他众所周知的 Application Insights 自动收集器，如[microsoft.applicationinsights.dependencycollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)、 [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)、 [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)等。此 SDK 公开了的`IServiceCollection`扩展方法，用于启用和配置遥测收集。

## <a name="supported-scenarios"></a>支持的方案

[辅助角色服务的 APPLICATION INSIGHTS SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)最适用于非 HTTP 应用程序，无论它们在何处运行，都是如此。 如果应用程序正在运行并与 Azure 建立了网络连接，则可以收集遥测数据。 只要支持 .NET Core，就能支持 Application Insights 监视。 此包可用于新引入的[.Net Core 3.0 辅助服务](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)、 [Asp.Net Core 2.1/2.2](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)、控制台应用（.net Core/.NET Framework）等中的后台任务。

## <a name="prerequisites"></a>先决条件

有效的 Application Insights 检测密钥。 将任何遥测数据发送到 Application Insights 都需要使用此密钥。 如果需要创建新的 Application Insights 资源来获取检测密钥，请参阅[创建 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="using-application-insights-sdk-for-worker-services"></a>使用辅助角色服务 Application Insights SDK

1. 将[applicationinsights.config. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)包安装到应用程序。
   以下代码片段显示了需要添加到项目`.csproj`文件的更改。

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.8.0" />
    </ItemGroup>
```

1. `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` 对`IServiceCollection`提供检测密钥的调用扩展方法。 应在应用程序的开头调用此方法。 具体位置取决于应用程序的类型。

1. 通过调用`TelemetryClient` `ILogger` 或使用构造函数注入，从依赖关系注入（DI`serviceProvider.GetRequiredService<TelemetryClient>();` ）容器中检索实例或实例。 此步骤将触发设置`TelemetryConfiguration`和自动收集模块。

以下各节介绍了每种类型的应用程序的特定说明。

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 辅助角色服务应用程序

[此处](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)共享了完整示例

1. 下载并安装[.Net Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. 使用 Visual Studio "新建项目模板" 或命令行创建新的辅助角色服务项目`dotnet new worker`
3. 将[applicationinsights.config. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)包安装到应用程序。

4. 将 `services.AddApplicationInsightsTelemetryWorkerService();` 添加到 `Program.cs` 类中的 `CreateHostBuilder()` 方法，如以下示例所示：

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. `Worker.cs`根据下面的示例修改。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. 设置检测密钥。

    尽管可将检测密钥作为参数提供给 `AddApplicationInsightsTelemetryWorkerService`，但我们建议在配置中指定检测密钥。 以下代码示例演示如何在 `appsettings.json` 中指定检测密钥。 在发布期间，请确保将 `appsettings.json` 复制到应用程序根文件夹。

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

也可以在以下环境变量中指定检测密钥。
`APPINSIGHTS_INSTRUMENTATIONKEY` 或 `ApplicationInsights:InstrumentationKey`

例如： `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
或`SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

通常， `APPINSIGHTS_INSTRUMENTATIONKEY`将作为 web 作业部署到 web 应用的应用程序的检测密钥指定。

> [!NOTE]
> 在代码中指定的检测密钥优先于环境变量 `APPINSIGHTS_INSTRUMENTATIONKEY`，而后者又优先于其他选项。

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>ASP.NET Core 托管服务的后台任务
[本](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio)文档介绍如何在 ASP.NET Core 2.1/2.2 应用程序中创建背景任务。

[此处](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)共享了完整示例

1. 将 applicationinsights.config. WorkerService （ https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 打包到应用程序。
2. `services.AddApplicationInsightsTelemetryWorkerService();` 添加`ConfigureServices()`到方法，如以下示例中所示：

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

下面是后台任务逻辑`TimedHostedService`所在位置的代码。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = await httpClient.GetAsync("https://bing.com");
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 设置检测密钥。
   `appsettings.json`使用上面的 .net Core 3.0 辅助角色服务示例。

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework 控制台应用程序

如本文开头所述，可以使用新包甚至从常规的控制台应用程序启用 Application Insights 遥测。 此包针对[`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard).net Core 2.0 或更高版本中的控制台应用程序，并 .NET Framework 4.7.2 或更高版本。

[此处](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)共享了完整示例

1. 将 applicationinsights.config. WorkerService （ https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 打包到应用程序。

2. 修改 Program.cs，如下例所示。

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key must be specified here.
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

此控制台应用程序还使用相同的`TelemetryConfiguration`默认值，并且可以采用与之前部分中的示例相同的方式对其进行自定义。

## <a name="run-your-application"></a>运行应用程序

运行应用程序。 上述所有上述示例中的示例工作人员均可每秒从 http 调用到 bing.com，并使用 ILogger 发出几个日志。 这些行将被包装在`StartOperation`对的`TelemetryClient`调用中，用于创建操作（在此示例`RequestTelemetry`中名为 "操作"）。 Application Insights 将收集这些 ILogger 日志（默认警告或更高版本）和依赖项，并将它们与具有`RequestTelemetry`父子关系的关联。 相关也能跨进程/网络边界。 例如，如果调用了另一个监视的组件，则它也将与此父组件相关联。

在典型的 web `RequestTelemetry`应用程序中，可以将此自定义操作视为等效于传入的 web 请求。 虽然不需要使用操作，但它最适合与[Application Insights 相关数据模型](https://docs.microsoft.com/azure/azure-monitor/app/correlation)-使用`RequestTelemetry`作为父操作，并且在工作线程迭代内生成的每个遥测将以逻辑方式被视为属于相同操作。 此方法还可确保生成的所有遥测（自动和手动）都具有相同`operation_id`的。 当采样基于`operation_id`时，采样算法会保留或删除单个迭代中的所有遥测数据。

下面列出了 Application Insights 自动收集的全部遥测数据。

### <a name="live-metrics"></a>实时指标

[实时指标](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)可用于快速验证是否已正确配置 Application Insights 监视。 尽管可能需要几分钟时间才能使遥测开始出现在门户和分析中，但实时指标会以近乎实时的方式显示正在运行的进程的 CPU 使用率。 它还可以显示其他遥测数据，如请求、依赖项、跟踪等。

### <a name="ilogger-logs"></a>ILogger 日志

自动捕获通过`ILogger`严重性`Warning`或更高版本发出的日志。 按照[ILogger 文档](ilogger.md#control-logging-level)来自定义 Application Insights 捕获的日志级别。

### <a name="dependencies"></a>依赖项

默认情况下启用依赖项集合。 [本文介绍](asp-net-dependencies.md#automatically-tracked-dependencies)自动收集的依赖项，还包含执行手动跟踪的步骤。

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule`默认情况下启用，并且它将从 .NET Core 3.0 应用程序收集默认的计数器集。 [EventCounter](eventcounters.md)教程列出了收集的默认计数器集。 它还包含有关自定义列表的说明。

### <a name="manually-tracking-additional-telemetry"></a>手动跟踪其他遥测数据

尽管 SDK 会按上述说明自动收集遥测数据，但在大多数情况下，用户需要将附加遥测发送到 Application Insights 服务。 跟踪附加遥测的建议方法是`TelemetryClient`从依赖关系注入获取的实例，然后对其调用受支持`TrackXXX()`的[API](api-custom-events-metrics.md)方法之一。 另一种典型用例是[操作的自定义跟踪](custom-operations-tracking.md)。 以上辅助角色示例演示了这种方法。

## <a name="configure-the-application-insights-sdk"></a>配置 Application Insights SDK

辅助角色`TelemetryConfiguration`服务 SDK 使用的默认值类似于 ASP.NET 或 ASP.NET Core 应用程序中使用的自动配置，减去用于从`HttpContext`其扩充遥测数据的 TelemetryInitializers。

你可以为辅助角色服务自定义 Application Insights SDK，以更改默认配置。 Application Insights ASP.NET Core SDK 的用户可能熟悉如何 ASP.NET Core 使用内置[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)来更改配置。 WorkerService SDK 也基于类似的原则。 通过在上`IServiceCollection`调用相应的方法`ConfigureServices()` ，在部分中进行几乎所有的配置更改，如下所述。

> [!NOTE]
> 使用此 SDK 时，不支持通过修改`TelemetryConfiguration.Active`来更改配置，并且将不会反映更改。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

可以通过向 `AddApplicationInsightsTelemetryWorkerService` 传递 `ApplicationInsightsServiceOptions` 来修改一些通用设置，如以下示例所示：

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

请注意`ApplicationInsightsServiceOptions` ，在此 sdk 中，在`Microsoft.ApplicationInsights.WorkerService`命名空间中`Microsoft.ApplicationInsights.AspNetCore.Extensions` ，而不是 ASP.NET Core SDK 中的。

中常用的设置`ApplicationInsightsServiceOptions`

|设置 | 描述 | 默认
|---------------|-------|-------
|EnableQuickPulseMetricStream | 启用/禁用 LiveMetrics 功能 | 真
|EnableAdaptiveSampling | 启用/禁用自适应采样 | 真
|EnableHeartbeat | 启用/禁用检测信号功能，该功能定期（15分钟默认值）发送名为 "HeartBeatState" 的自定义指标，其中包含有关运行时（如 .NET 版本、Azure 环境信息，如果适用）等的信息。 | 真
|AddAutoCollectedMetricExtractor | 启用/禁用 AutoCollectedMetrics 提取程序，它是一种 TelemetryProcessor，它在采样发生之前发送有关请求/依赖项的预聚合度量值。 | 真

有关最新列表，请参阅[中`ApplicationInsightsServiceOptions`的可配置设置](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>采样

辅助角色服务的 Application Insights SDK 支持固定速率和自适应采样。 自适应采样默认已启用。 为辅助角色服务配置采样的方式与[ASP.NET Core 应用程序](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)一样。

### <a name="adding-telemetryinitializers"></a>添加 TelemetryInitializer

如果要定义与所有遥测一起发送的属性，请使用[遥测初始值设定项](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)。

向`DependencyInjection`容器添加`TelemetryInitializer`任何新的，SDK 会`TelemetryConfiguration`自动将其添加到中。

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>删除 TelemetryInitializer

默认已提供遥测初始化表达式。 若要删除所有或特定的遥测初始化表达式，请在调用 `AddApplicationInsightsTelemetryWorkerService()` 之后使用以下示例代码。

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>添加遥测处理程序

可以使用 `IServiceCollection` 中的扩展方法 `AddApplicationInsightsTelemetryProcessor` 将自定义遥测处理程序添加到 `TelemetryConfiguration`。 使用[高级筛选方案](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)中的遥测处理程序可以更直接地控制要在发送到 Application Insights 服务的遥测数据中包含或排除哪些内容。 使用以下示例。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>配置或删除默认的 TelemetryModule

Application Insights 使用遥测模块自动收集有关特定工作负载的遥测，无需手动跟踪。

默认已启用以下自动收集模块。 这些模块负责自动收集遥测数据。 可以禁用或配置这些模块，以改变其默认行为。

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

若要配置任何默认的 `TelemetryModule`，请按以下示例中所示使用 `IServiceCollection` 中的扩展方法 `ConfigureTelemetryModule<T>`。

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>配置遥测通道

默认通道为 `ServerTelemetryChannel`。 可按以下示例所示替代该通道。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>动态禁用遥测

如果要有条件和动态地禁用遥测，可以在代码中的任何位置使用 ASP.NET Core 依赖项注入容器解析 `TelemetryConfiguration` 实例，并在其上设置 `DisableTelemetry` 标志。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何跟踪不会自动收集的遥测数据？

使用构造函数注入获取 `TelemetryClient` 的实例，然后对其调用所需的 `TrackXXX()` 方法。 不建议创建新`TelemetryClient`实例。 `DependencyInjection` 容器中已注册了 `TelemetryClient` 的单一实例，该实例与剩余的遥测功能共享 `TelemetryConfiguration`。 仅当需要与剩余的遥测功能使用不同的配置时，才建议创建新的 `TelemetryClient` 实例。

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>是否可以使用 Visual Studio IDE 将 Application Insights 加入辅助角色服务项目？

目前仅支持 ASP.NET/ASP.NET 核心应用程序的 Visual Studio IDE 载入。 当 Visual Studio 为载入辅助角色服务应用程序提供支持时，将更新此文档。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>是否可以使用状态监视器之类的工具来启用 Application Insights 监视？

否。 [状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[状态监视器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 目前仅支持 ASP.NET 4.x。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果在 Linux 中运行应用程序，是否支持所有功能？

是。 此 SDK 的功能支持在所有平台中都是相同的，但有以下例外：

* 性能计数器仅在 Windows 中受支持，但在实时指标中显示的进程 CPU/内存除外。
* 尽管默认已启用 `ServerTelemetryChannel`，但如果应用程序在 Linux 或 MacOS 中运行，出现网络问题时，通道不会自动创建本地存储文件夹来暂时保留遥测数据。 由于这种限制，在出现暂时性的网络或服务器时，遥测数据将会丢失。 若要解决此问题，请为通道配置一个本地文件夹：

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>示例应用程序

[.Net Core 控制台应用程序](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)如果使用的是以 .NET Core （2.0 或更高版本）或 .NET Framework （4.7.2 或更高版本）编写的控制台应用程序，请使用此示例

[ASP .Net Core 后台任务与 HostedServices](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)如果你使用的是 Asp.Net Core 2.1/2.2，并按照[此处](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)的官方指导创建后台任务，请使用此示例

[.Net Core 3.0 工作线程服务](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)如果你有一个 .NET Core 3.0 辅助角色服务应用程序，则请使用此示例，如[此处](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)所述

## <a name="open-source-sdk"></a>开源 SDK

[阅读代码或为其做出贡献](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="next-steps"></a>后续步骤

* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)发送自己的事件和指标, 以详细了解应用的性能和使用情况。
* [跟踪不自动跟踪的其他依赖项](../../azure-monitor/app/auto-collect-dependencies.md)。
* [丰富或筛选自动收集的遥测](../../azure-monitor/app/api-filtering-sampling.md)数据。
* [ASP.NET Core 中的依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。
