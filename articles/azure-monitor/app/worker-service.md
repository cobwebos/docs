---
title: 适用于辅助角色服务应用（非 HTTP 应用）的 Application Insights
description: 使用 Azure Monitor Application Insights 监视 .NET Core/.NET Framework 非 HTTP 应用。
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1f9b35022e63c4c3fe671237149602f8db465466
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117871"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>适用于辅助角色服务应用程序（非 HTTP 应用）的 Application Insights

Application Insights 正在发布名为 `Microsoft.ApplicationInsights.WorkerService` 的新 SDK，该 SDK 最适合用于消息传递、后台任务、控制台应用程序等非 HTTP 工作负荷。此类应用程序不像传统 ASP.NET/ASP.NET Core Web 应用程序那样具有传入 HTTP 请求的概念，因此，不支持对 [ASP.NET](asp-net.md) 或 [ASP.NET Core](asp-net-core.md) 应用程序使用 Application Insights 包。

新 SDK 本身不执行任何遥测收集， 而是引入了其他众所周知的 Application Insights 自动收集器，例如 [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/)、[PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/)、[ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 等。此 SDK 公开 `IServiceCollection` 中的扩展方法用于启用和配置遥测收集。

## <a name="supported-scenarios"></a>支持的方案

[适用于辅助角色服务的 Application Insights SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 最适合用于非 HTTP 应用程序，无论这些应用程序在何处或者如何运行。 如果应用程序正在运行并与 Azure 建立了网络连接，则可以收集遥测数据。 只要支持 .NET Core，就能支持 Application Insights 监视。 此包可在新引入的 [.NET Core 3.0 辅助角色服务](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances)、[Asp.Net Core 2.1/2.2 中的后台任务](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)、控制台应用 (.NET Core/ .NET Framework) 等工作负荷中使用。

## <a name="prerequisites"></a>先决条件

有效的 Application Insights 检测密钥。 将任何遥测数据发送到 Application Insights 都需要使用此密钥。 如果需要创建新的 Application Insights 资源来获取检测密钥，请参阅[创建 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="using-application-insights-sdk-for-worker-services"></a>使用适用于辅助角色服务的 Application Insights SDK

1. 将 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 包安装到应用程序。
   以下代码片段演示了需要添加到项目的 `.csproj` 文件中的更改。

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. 调用 `IServiceCollection` 中的 `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` 扩展方法并提供检测密钥。 应在应用程序的开头调用此方法。 具体位置取决于应用程序的类型。

1. 通过调用 `serviceProvider.GetRequiredService<TelemetryClient>();` 或使用构造函数注入，从依赖项注入 (DI) 容器中检索 `ILogger` 实例或 `TelemetryClient` 实例。 此步骤将触发 `TelemetryConfiguration` 和自动收集模块的设置。

以下部分提供了适用于每种应用程序的具体说明。

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 辅助角色服务应用程序

[此处](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)分享了完整示例

1. 下载并安装 [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. 使用 Visual Studio 的新建项目模板或命令行 `dotnet new worker` 创建新的辅助角色服务项目
3. 将 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 包安装到应用程序。

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

5. 按以下示例修改 `Worker.cs`。

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
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
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

或者，在以下任一环境变量中指定检测密钥。
`APPINSIGHTS_INSTRUMENTATIONKEY` 或 `ApplicationInsights:InstrumentationKey`

例如： `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
或者 `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

通常，`APPINSIGHTS_INSTRUMENTATIONKEY` 指定要作为 Web 作业部署到 Web 应用的应用程序的检测密钥。

> [!NOTE]
> 在代码中指定的检测密钥优先于环境变量 `APPINSIGHTS_INSTRUMENTATIONKEY`，而后者又优先于其他选项。

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>使用托管服务的 ASP.NET Core 后台任务

[此文档](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2&tabs=visual-studio)介绍了如何在 ASP.NET Core 2.1/2.2 应用程序中创建后台任务。

[此处](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService)分享了完整示例

1. 将 Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 包安装到应用程序。
2. 将 `services.AddApplicationInsightsTelemetryWorkerService();` 添加到 `ConfigureServices()` 方法，如以下示例所示：

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

下面是后台任务逻辑所在的 `TimedHostedService` 的代码。

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
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 设置检测密钥。
   使用上述 .NET Core 3.0 辅助角色服务示例中的相同 `appsettings.json`。

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework 控制台应用程序

如本文开头所述，甚至可以使用新包从普通的控制台应用程序启用 Application Insights 遥测。 此包针对 [`NetStandard2.0`](https://docs.microsoft.com/dotnet/standard/net-standard)，因此可用于 .NET Core 2.0 或更高版本，以及 .NET Framework 4.7.2 或更高版本中的控制台应用。

[此处](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)分享了完整示例

1. 将 Microsoft.ApplicationInsights.WorkerService(https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 包安装到应用程序。

2. 按以下示例所示修改 Program.cs。

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
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
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

此控制台应用程序也使用相同的默认 `TelemetryConfiguration`，可按前面部分中的示例所示的相同方式对其进行自定义。

## <a name="run-your-application"></a>运行应用程序

运行应用程序。 上述所有示例中的示例辅助角色每秒对 bing.com 发出 http 调用，并使用 ILogger 发出几个日志。 这些行包装在用于创建操作的 `TelemetryClient` 的 `StartOperation` 调用内部（在此示例中，`RequestTelemetry` 名为“operation”）。 Application Insights 将收集这些 ILogger 日志（默认为警告或更高级别）和依赖项，这些日志将通过父子关系关联到 `RequestTelemetry`。 这种关联也会跨进程/网络边界发生。 例如，如果对另一个受监视组件发出调用，则该组件也会关联到此父级。

可将 `RequestTelemetry` 的此自定义操作视为等效于典型 Web 应用程序中的传入 Web 请求。 尽管不一定要使用操作，但操作最适合 [Application Insights 关联数据模型](https://docs.microsoft.com/azure/azure-monitor/app/correlation) - `RequestTelemetry` 充当父操作，在辅助角色迭代中生成的每个遥测数据被视为在逻辑上属于同一操作。 此方法还确保生成的所有遥测数据（自动和手动）具有相同的 `operation_id`。 由于采样基于 `operation_id`，因此采样算法会在单个迭代中保留或删除所有遥测数据。

下面列出了 Application Insights 自动收集的整个遥测数据。

### <a name="live-metrics"></a>实时指标

[实时指标](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)可用于快速验证是否正确配置了 Application Insights 监视。 尽管可能需要在几分钟时间后遥测数据才开始显示在门户和分析结果中，但实时指标能够近实时地显示正在运行的进程的 CPU 使用率。 它还可以显示其他遥测数据，例如请求、依赖项、跟踪等。

### <a name="ilogger-logs"></a>ILogger 日志

自动捕获通过 `Warning` 或更高严重性的 `ILogger` 发出的日志。 遵循 [ILogger 文档](ilogger.md#control-logging-level)自定义 Application Insights 捕获的日志级别。

### <a name="dependencies"></a>依赖项

默认情况已启用依赖项收集。 [此文](asp-net-dependencies.md#automatically-tracked-dependencies)介绍了自动收集的依赖项，并提供了执行手动跟踪的步骤。

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` 默认已启用，它会从 .NET Core 3.0 应用收集默认的计数器集。 [EventCounter](eventcounters.md)教程列出了收集的默认计数器集。 它还包含有关自定义列表的说明。

### <a name="manually-tracking-additional-telemetry"></a>手动跟踪附加遥测数据

尽管 SDK 会按如上所述自动收集遥测数据，但在大多数情况下，用户需要将附加遥测数据发送到 Application Insights 服务。 跟踪附加遥测数据的建议方法是从依赖项注入获取 `TelemetryClient` 的实例，然后对其调用某个受支持的 `TrackXXX()` [API](api-custom-events-metrics.md) 方法。 另一种典型用例是[操作的自定义跟踪](custom-operations-tracking.md)。 以上辅助角色示例演示了此方法。

## <a name="configure-the-application-insights-sdk"></a>配置 Application Insights SDK

辅助角色服务 SDK 使用的默认 `TelemetryConfiguration` 类似于 ASP.NET 或 ASP.NET Core 应用程序中使用的自动配置，但不包括用于从 `HttpContext` 中扩充遥测的 TelemetryInitializers。

可以自定义适用于辅助角色服务的 Application Insights SDK 来更改默认配置。 Application Insights ASP.NET Core SDK 的用户可以使用 ASP.NET Core 的内置[依赖项注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)来熟悉配置更改。 WorkerService SDK 也基于类似的原则。 几乎所有的配置更改都是通过调用 `IServiceCollection` 中的相应方法在 `ConfigureServices()` 节中进行的，下面对此做了详述。

> [!NOTE]
> 使用此 SDK 时，不支持通过修改 `TelemetryConfiguration.Active` 来更改配置，并且不会反映更改。

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

请注意，此 SDK 中的 `ApplicationInsightsServiceOptions` 位于命名空间 `Microsoft.ApplicationInsights.WorkerService` 中，而不是像 ASP.NET Core SDK 那样位于 `Microsoft.ApplicationInsights.AspNetCore.Extensions` 中。

`ApplicationInsightsServiceOptions` 中的常用设置

|设置 | 说明 | 默认
|---------------|-------|-------
|EnableQuickPulseMetricStream | Enable/Disable LiveMetrics feature | 是
|EnableAdaptiveSampling | 启用/禁用自适应采样 | 是
|EnableHeartbeat | 启用/禁用检测信号功能，该功能定期（默认间隔为 15 分钟）发送名为“HeartBeatState”的自定义指标，其中包含有关运行时等的信息，例如 .NET 版本、Azure 环境信息（如果适用）等。 | 是
|AddAutoCollectedMetricExtractor | 启用/禁用 AutoCollectedMetrics 提取程序 - 一个 TelemetryProcessor，在采样发生之前发送有关请求/依赖项的聚合前指标。 | 是

有关最新列表，请参阅 [`ApplicationInsightsServiceOptions` 中的可配置设置](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>采样

适用于辅助角色服务的 Application Insights SDK 支持固定速率采样和自适应采样。 自适应采样默认已启用。 为辅助角色服务配置采样的方式与对 [ASP.NET Core 应用程序](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-adaptive-sampling-for-aspnet-core-applications)使用的方式相同。

### <a name="adding-telemetryinitializers"></a>添加 TelemetryInitializer

若要定义连同所有遥测数据一起发送的属性，请使用[遥测初始化表达式](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer)。

将任何新的 `TelemetryInitializer` 添加到 `DependencyInjection` 容器，SDK 会自动将其添加到 `TelemetryConfiguration`。

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>删除 TelemetryInitializer

默认已提供遥测初始化表达式。 若要删除所有或特定的遥测初始化表达式，请在调用 `AddApplicationInsightsTelemetryWorkerService()` 之后使用以下示例代码。**

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

可以使用 `IServiceCollection` 中的扩展方法 `AddApplicationInsightsTelemetryProcessor` 将自定义遥测处理程序添加到 `TelemetryConfiguration`。 使用[高级筛选方案](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#itelemetryprocessor-and-itelemetryinitializer)中的遥测处理程序可以更直接地控制要在发送到 Application Insights 服务的遥测数据中包含或排除哪些内容。 使用以下示例。

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

Application Insights 使用遥测模块自动收集有关特定工作负荷的遥测数据，无需手动跟踪。

默认已启用以下自动收集模块。 这些模块负责自动收集遥测数据。 可以禁用或配置这些模块，以改变其默认行为。

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`-（目前涉及此遥测模块的问题。 有关临时解决方法，请参阅[GitHub 问题 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
)。）
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

使用构造函数注入获取 `TelemetryClient` 的实例，然后对其调用所需的 `TrackXXX()` 方法。 不建议创建新的 `TelemetryClient` 实例。 `DependencyInjection` 容器中已注册了 `TelemetryClient` 的单一实例，该实例与剩余的遥测功能共享 `TelemetryConfiguration`。 仅当需要与剩余的遥测功能使用不同的配置时，才建议创建新的 `TelemetryClient` 实例。

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>是否可以使用 Visual Studio IDE 将 Application Insights 加入辅助角色服务项目？

目前只有 ASP.NET/ASP.NET Core 应用程序支持 Visual Studio IDE 加入。 当 Visual Studio 支持加入辅助角色服务应用程序时，本文档将会更新。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>是否可以使用状态监视器之类的工具来启用 Application Insights 监视？

不是。 [状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)和[状态监视器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview) 目前仅支持 ASP.NET 4.x。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果在 Linux 中运行应用程序，是否支持所有功能？

是的。 此 SDK 的功能支持在所有平台中是相同的，不过存在以下例外情况：

* 性能计数器仅在 Windows 中受支持，但“实时指标”中所示的进程 CPU/内存除外。
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

[.NET Core 控制台应用程序](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) 如果使用的控制台应用程序是以 .NET Core（2.0 或更高版本）或 .NET Framework（4.7.2 或更高版本）编写的，请使用此示例

[使用 HostedServices 的 ASP .NET Core 后台任务](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) 如果在 Asp.Net Core 2.1/2.2 中操作，并根据[此处](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2)的官方指南创建后台任务，请使用此示例

[.NET Core 3.0 辅助角色服务](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) 如果根据[此处](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-3.0&tabs=visual-studio#worker-service-template)的官方指南创建了 .NET Core 3.0 辅助角色服务应用程序，请使用此示例

## <a name="open-source-sdk"></a>开源 SDK

[阅读并参与编写代码](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)。

## <a name="next-steps"></a>后续步骤

* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md) 发送自己的事件和指标，以获取应用性能和使用情况的详细视图。
* [跟踪系统不会自动跟踪的附加依赖项](../../azure-monitor/app/auto-collect-dependencies.md)。
* [扩充或筛选自动收集的遥测数据](../../azure-monitor/app/api-filtering-sampling.md)。
* [ASP.NET Core 中的依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。
