---
title: ASP.NET Core 应用程序的 Azure 应用程序见解 |Microsoft Docs
description: 监视 ASP.NET Core Web 应用程序的可用性、性能和使用情况。
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 1a9bc3e46e108c50b36e0318e0f9a51a94e83573
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475509"
---
# <a name="application-insights-for-aspnet-core-applications"></a>适用于 ASP.NET Core 应用程序的 Application Insights

本文介绍如何为 [ASP.NET Core](/aspnet/core) 应用程序启用 Application Insights。 完成本文中所述的步骤后，Application Insights 将从 ASP.NET Core 应用程序收集请求、依赖项、异常、性能计数器、检测信号和日志。

本文所用的示例是一个面向 `netcoreapp3.0` 的 [MVC 应用程序](/aspnet/core/tutorials/first-mvc-app)。 这些说明适用于所有 ASP.NET Core 应用程序。 如果使用的是[辅助角色服务](/aspnet/core/fundamentals/host/hosted-services#worker-service-template)，请使用[此处](./worker-service.md)的说明。

## <a name="supported-scenarios"></a>支持的方案

无论在何处以何种方式运行你的应用程序，[适用于 ASP.NET Core 的 Application Insights SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 都可对其进行监视。 如果应用程序正在运行并与 Azure 建立了网络连接，则可以收集遥测数据。 只要支持 .NET Core，就能支持 Application Insights 监视。 支持范围包括：
* **操作系统**：Windows、Linux 或 Mac。
* **托管方法**：进程内或进程外。
* 部署方法：框架依赖或自包含。
* Web 服务器：IIS（Internet 信息服务器）或 Kestrel。
* **托管平台**：Azure 应用服务的 Web 应用功能、Azure VM、Docker、Azure Kubernetes 服务 (AKS) 等。
* **.NET Core 运行时版本**：1.XX、2.XX 或 3.XX
* **IDE**：Visual Studio、VS Code 或命令行。

> [!NOTE]
> ASP.NET Core 3.X 需要 [Application Insights 2.8.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/2.8.0) 或更高版本。

## <a name="prerequisites"></a>先决条件

- 一个正常运行的 ASP.NET Core 应用程序。 如果需要创建 ASP.NET Core 应用程序，请遵循此 [ASP.NET Core 教程](/aspnet/core/getting-started/)。
- 有效的 Application Insights 检测密钥。 将任何遥测数据发送到 Application Insights 都需要使用此密钥。 如果需要创建新的 Application Insights 资源来获取检测密钥，请参阅[创建 Application Insights 资源](./create-new-resource.md)。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>启用 Application Insights 服务器端遥测 (Visual Studio)

1. 在 Visual Studio 中打开项目。

    > [!TIP]
    > 如果需要，可为项目设置源代码管理，以便可以跟踪 Application Insights 做出的所有更改。 若要启用源代码管理，请选择“文件” > “添加到源代码管理”。 

2. 选择“项目” > “添加 Application Insights 遥测” 。

3. 选择“入门”。 选项文本根据 Visual Studio 版本的不同而异。 在某些早期版本中，使用的是“免费开始”按钮。

4. 选择订阅。 然后选择“资源” > “注册”。 

5. 将 Application Insights 添加到项目后，确认使用的是最新稳定版本的 SDK。 转到“项目” > “管理 NuGet 包” > “Microsoft.ApplicationInsights.AspNetCore”。   根据需要选择“更新”。

     ![显示在何处选择要更新的 Application Insights 包的屏幕截图](./media/asp-net-core/update-nuget-package.png)

6. 如果你已遵循可选的提示操作并已将项目添加到源代码管理，请转到“视图” > “团队资源管理器” > “更改”。   然后选择每个文件，以查看 Application Insights 遥测功能所做的更改的差异视图。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>启用 Application Insights 服务器端遥测（不使用 Visual Studio）

1. 安装[适用于 ASP.NET Core 的 Application Insights SDK NuGet 包](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我们建议始终使用最新稳定版本。 在[开源 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-dotnet/releases)中可以找到 SDK 的完整发行说明。

    以下代码示例演示了要添加到项目的 `.csproj` 文件中的更改。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.13.1" />
        </ItemGroup>
    ```

2. 将 `services.AddApplicationInsightsTelemetry();` 添加到 `Startup` 类中的 `ConfigureServices()` 方法，如以下示例所示：

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // This code adds other services for your application.
            services.AddMvc();
        }
    ```

3. 设置检测密钥。

    尽管可将检测密钥作为参数提供给 `AddApplicationInsightsTelemetry`，但我们建议在配置中指定检测密钥。 以下代码示例演示如何在 `appsettings.json` 中指定检测密钥。 在发布期间，请确保将 `appsettings.json` 复制到应用程序根文件夹。

    ```json
        {
          "ApplicationInsights": {
            "InstrumentationKey": "putinstrumentationkeyhere"
          },
          "Logging": {
            "LogLevel": {
              "Default": "Warning"
            }
          }
        }
    ```

    或者，在以下任一环境变量中指定检测密钥：

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例如：

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    * `APPINSIGHTS_INSTRUMENTATIONKEY` 通常在 [Azure Web 应用](./azure-web-apps.md?tabs=net)中使用，但也可以在所有支持此 SDK 的平台使用。 （如果要执行无代码 Web 应用监视，则在不使用连接字符串的情况下必须使用此格式。）

    现在，也可以使用[连接字符串](./sdk-connection-string.md?tabs=net)来代替设置检测密钥。

    > [!NOTE]
    > 在代码中指定的检测密钥优先于环境变量 `APPINSIGHTS_INSTRUMENTATIONKEY`，而后者又优先于其他选项。

## <a name="run-your-application"></a>运行应用程序

运行应用程序并向其发出请求。 现在，遥测数据应会流入 Application Insights。 Application Insights SDK 自动将传入的 Web 请求收集到应用程序，并收集以下遥测数据。

### <a name="live-metrics"></a>实时指标

[实时指标](./live-stream.md)可用于快速验证是否正确配置了 Application Insights 监视。 尽管可能需要在几分钟时间后遥测数据才开始显示在门户和分析结果中，但实时指标能够近实时地显示正在运行的进程的 CPU 使用率。 它还可以显示其他遥测数据，例如请求、依赖项、跟踪等。

### <a name="ilogger-logs"></a>ILogger 日志

自动捕获通过 `Warning` 或更高严重性的 `ILogger` 发出的日志。 遵循 [ILogger 文档](ilogger.md#control-logging-level)自定义 Application Insights 捕获的日志级别。

### <a name="dependencies"></a>依赖项

默认情况已启用依赖项收集。 [此文](asp-net-dependencies.md#automatically-tracked-dependencies)介绍了自动收集的依赖项，并提供了执行手动跟踪的步骤。

### <a name="performance-counters"></a>性能计数器

对 ASP.NET Core 中的[性能计数器](./web-monitor-performance.md)的支持限制如下：

* 如果应用程序在 Azure Web 应用 (Windows) 中运行，则 SDK 2.4.1 和更高版本将收集性能计数器。
* 如果应用程序在 Windows 中运行，并且面向 `NETSTANDARD2.0` 或更高版本，则 SDK 2.7.1 和更高版本将收集性能计数器。
* 对于面向 .NET Framework 的应用程序，所有版本的 SDK 都支持性能计数器。
* SDK 2.8.0 及更高版本在 Linux 中支持 cpu/memory 计数器， 但不在 Linux 中支持其他计数器。 在 Linux（和其他非 Windows 环境）中，获取系统计数器的建议方法是使用 [EventCounter](#eventcounter)

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` 默认已启用，它会从 .NET Core 3.X 应用收集默认的计数器集。 [EventCounter](eventcounters.md) 教程列出了收集的默认计数器集。 它还包含有关自定义列表的说明。

## <a name="enable-client-side-telemetry-for-web-applications"></a>为 Web 应用程序启用客户端遥测

完成前面所述的步骤足以开始收集服务器端遥测数据。 如果应用程序包含客户端组件，请遵循后续步骤开始收集[使用情况遥测数据](./usage-overview.md)。

1. 在 `_ViewImports.cshtml` 中添加注入代码：

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在 `_Layout.cshtml` 中，将 `HtmlHelper` 插入到 `<head>` 节的末尾、任何其他脚本的前面。 若要从页面报告任何自定义 JavaScript 遥测数据，请将其注入到此片段的后面：

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```
    
从 SDK v2.14 开始，除了使用 `FullScript` 之外，还可以使用 `ScriptBody`。 如果需要控制 `<script>` 标记以设置内容安全策略，请使用此标记：

```cshtml
 <script> // apply custom changes to this script tag.
     @Html.Raw(JavaScriptSnippet.ScriptBody)
 </script>
```

前面引用的 `.cshtml` 文件名取自默认的 MVC 应用程序模板。 从根本上讲，若要为应用程序正确启用客户端监视，JavaScript 代码片段必须出现在所要监视的应用程序的每个页面的 `<head>` 节中。 将 JavaScript 代码片段添加到 `_Layout.cshtml` 即可实现此应用程序模板的此目标。 

如果项目不包含 `_Layout.cshtml`，你仍然可以添加[客户端监视](./website-monitoring.md)。 为此，可将 JavaScript 代码片段添加到用于控制应用中所有页面的 `<head>` 的等效文件。 或者，可将代码片段添加到多个页面中，但这种解决方法很难掌控，因此我们一般不建议。

## <a name="configure-the-application-insights-sdk"></a>配置 Application Insights SDK

可以自定义适用于 ASP.NET Core 的 Application Insights SDK 以更改默认配置。 Application Insights SDK ASP.NET 的用户可以使用 `ApplicationInsights.config` 或通过修改 `TelemetryConfiguration.Active` 来熟悉配置更改。 对于 ASP.NET Core，需以不同的方式更改配置。 使用 ASP.NET Core 的内置[依赖项注入](/aspnet/core/fundamentals/dependency-injection)将 ASP.NET Core SDK 添加到应用程序并对其进行配置。 除非另有指示，否则几乎所有的配置更改都是在 `Startup.cs` 类的 `ConfigureServices()` 方法中完成的。 以下部分提供了详细信息。

> [!NOTE]
> 在 ASP.NET Core 应用程序中，不支持通过修改 `TelemetryConfiguration.Active` 来更改配置。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

可以通过向 `AddApplicationInsightsTelemetry` 传递 `ApplicationInsightsServiceOptions` 来修改一些通用设置，如以下示例所示：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
}
```

`ApplicationInsightsServiceOptions` 中的完整设置列表

|设置 | 描述 | 默认
|---------------|-------|-------
|EnablePerformanceCounterCollectionModule  | 启用/禁用 `PerformanceCounterCollectionModule` | true
|EnableRequestTrackingTelemetryModule   | 启用/禁用 `RequestTrackingTelemetryModule` | true
|EnableEventCounterCollectionModule   | 启用/禁用 `EventCounterCollectionModule` | true
|EnableDependencyTrackingTelemetryModule   | 启用/禁用 `DependencyTrackingTelemetryModule` | true
|EnableAppServicesHeartbeatTelemetryModule  |  启用/禁用 `AppServicesHeartbeatTelemetryModule` | true
|EnableAzureInstanceMetadataTelemetryModule   |  启用/禁用 `AzureInstanceMetadataTelemetryModule` | true
|EnableQuickPulseMetricStream | Enable/Disable LiveMetrics feature | true
|EnableAdaptiveSampling | 启用/禁用自适应采样 | true
|EnableHeartbeat | 启用/禁用检测信号功能，该功能定期（默认间隔为 15 分钟）发送名为“HeartbeatState”的自定义指标，其中包含有关运行时等的信息，例如 .NET 版本、Azure 环境信息（如果适用）等。 | true
|AddAutoCollectedMetricExtractor | 启用/禁用 AutoCollectedMetrics 提取程序 - 一个 TelemetryProcessor，在采样发生之前发送有关请求/依赖项的聚合前指标。 | 是
|RequestCollectionOptions.TrackExceptions | 启用/禁用请求收集模块的未经处理的异常跟踪报告。 | 在 NETSTANDARD2.0 中为 false（因为异常是通过 ApplicationInsightsLoggerProvider 跟踪的），否则为 true。

有关最新列表，请参阅 [`ApplicationInsightsServiceOptions` 中的可配置设置](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>采样

适用于 ASP.NET Core 的 Application Insights SDK 支持固定频率和自适应采样。 自适应采样默认已启用。 

有关详细信息，请参阅 [配置 ASP.NET Core 应用程序的自适应采样](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)。

### <a name="adding-telemetryinitializers"></a>添加 TelemetryInitializer

如果要使用其他信息充实遥测数据，请使用[遥测初始值设定项](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)。

将任何新的 `TelemetryInitializer` 添加到 `DependencyInjection` 容器，如以下代码所示。 SDK 会自动拾取添加到 `DependencyInjection` 容器的任何 `TelemetryInitializer`。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
}
```

### <a name="removing-telemetryinitializers"></a>删除 TelemetryInitializer

默认已提供遥测初始化表达式。 若要删除所有或特定的遥测初始化表达式，请在调用 `AddApplicationInsightsTelemetry()` 之后使用以下示例代码。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

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

可以使用 `IServiceCollection` 中的扩展方法 `AddApplicationInsightsTelemetryProcessor` 将自定义遥测处理程序添加到 `TelemetryConfiguration`。 在[高级筛选方案](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer)中使用遥测处理器。 使用以下示例。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddApplicationInsightsTelemetry();
    services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

    // If you have more processors:
    services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
}
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>配置或删除默认的 TelemetryModule

Application Insights 使用遥测模块自动收集有关特定工作负荷的有用遥测数据，无需用户手动跟踪。

默认已启用以下自动收集模块。 这些模块负责自动收集遥测数据。 可以禁用或配置这些模块，以改变其默认行为。

* `RequestTrackingTelemetryModule` - 从传入的 Web 请求收集 RequestTelemetry。
* `DependencyTrackingTelemetryModule`-从传出 http 调用和 sql 调用收集[dependencytelemetry 描述](./asp-net-dependencies.md)。
* `PerformanceCollectorModule` - 收集 Windows PerformanceCounters。
* `QuickPulseTelemetryModule` - 收集遥测数据以便在实时指标门户中显示。
* `AppServicesHeartbeatTelemetryModule` - 收集有关托管应用程序的 Azure 应用服务环境的检测信号（以自定义指标的形式发送）。
* `AzureInstanceMetadataTelemetryModule` - 收集有关托管应用程序的 Azure VM 环境的检测信号（以自定义指标的形式发送）。
* `EventCounterCollectionModule` - 收集 [EventCounters.](eventcounters.md)。 此模块是一项新功能，可在 SDK 版本 2.8.0 及更高版本中使用。

若要配置任何默认的 `TelemetryModule`，请按以下示例中所示使用 `IServiceCollection` 中的扩展方法 `ConfigureTelemetryModule<T>`。

```csharp
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry();

    // The following configures DependencyTrackingTelemetryModule.
    // Similarly, any other default modules can be configured.
    services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
            {
                module.EnableW3CHeadersInjection = true;
            });

    // The following removes all default counters from EventCounterCollectionModule, and adds a single one.
    services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                module.Counters.Clear();
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

    // The following removes PerformanceCollectorModule to disable perf-counter collection.
    // Similarly, any other default modules can be removed.
    var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
    if (performanceCounterService != null)
    {
        services.Remove(performanceCounterService);
    }
}
```

从 2.12.2 版本开始，[`ApplicationInsightsServiceOptions`](#using-applicationinsightsserviceoptions) 包含用于禁用默认模块的简单选项。

### <a name="configuring-a-telemetry-channel"></a>配置遥测通道

默认[遥测通道](./telemetry-channels.md)是 `ServerTelemetryChannel` 。 可按以下示例所示替代该通道。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="disable-telemetry-dynamically"></a>动态禁用遥测

如果要有条件和动态地禁用遥测，可以在代码中的任何位置使用 ASP.NET Core 依赖项注入容器解析 `TelemetryConfiguration` 实例，并在其上设置 `DisableTelemetry` 标志。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

上述操作不会阻止任何自动收集模块收集遥测数据。 使用上述方法只会禁止向 Application Insights 发送遥测数据。 如果不需要某个特定的自动收集模块，最好是[删除遥测模块](#configuring-or-removing-default-telemetrymodules)

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-application-insights-support-aspnet-core-3x"></a>Application Insights 是否支持 ASP.NET Core 3.X？

是的。 请更新到 [Application Insights SDK for ASP.NET Core](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.8.0 或更高版本。 更低的 SDK 版本不支持 ASP.NET Core 3.X。

此外，如果使用[此处](#enable-application-insights-server-side-telemetry-visual-studio)基于 Visual Studio 的说明，请更新到最新版本的 Visual Studio 2019 (16.3.0) 以完成加入。 旧版 Visual Studio 不支持 ASP.NET Core 3.X 应用的自动加入。

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何跟踪不会自动收集的遥测数据？

使用构造函数注入获取 `TelemetryClient` 的实例，然后对其调用所需的 `TrackXXX()` 方法。 我们不建议在 ASP.NET Core 应用程序中创建新的 `TelemetryClient` 实例。 `DependencyInjection` 容器中已注册了 `TelemetryClient` 的单一实例，该实例与剩余的遥测功能共享 `TelemetryConfiguration`。 仅当需要与剩余的遥测功能使用不同的配置时，才建议创建新的 `TelemetryClient` 实例。

以下示例演示如何从控制器跟踪其他遥测数据。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // Use constructor injection to get a TelemetryClient instance.
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // Call the required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

有关 Application Insights 中自定义数据报告的详细信息，请参阅 [Application Insights 自定义指标 API 参考](./api-custom-events-metrics.md)。 类似的方法可用于将自定义指标发送到使用[GETMETRIC API](./get-metric.md)Application Insights。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 模板使用 IWebHostBuilder 中的 UseApplicationInsights() 扩展方法来启用 Application Insights。 这种用法是否仍然有效？

尽管 `UseApplicationInsights()` 扩展方法仍受支持，但它在 Application Insights SDK 版本 2.8.0 中已标记为弃用。 在下一个 SDK 主要版本中，将会删除此方法。 启用 Application Insights 遥测的建议方式是使用 `AddApplicationInsightsTelemetry()`，因为它提供用于控制某个配置的重载。 此外，在 ASP.NET Core 3.X 应用中，`services.AddApplicationInsightsTelemetry()` 是启用 Application Insights 的唯一方式。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我正在将 ASP.NET Core 应用程序部署到 Web 应用。 是否仍要从 Web 应用启用 Application Insights 扩展？

如果在生成时已按本文所示安装了 SDK，则无需从应用服务门户启用 [Application Insights 扩展](./azure-web-apps.md)。 即使安装了扩展，在检测到已将 SDK 添加到应用程序时，该扩展也仍会回退。 如果从扩展启用 Application Insights，则无需安装和更新 SDK。 但是，遵照本文中的说明启用 Application Insights 会更灵活，原因如下：

   * Application Insights 遥测功能将在以下位置或模式下继续运行：
       * 所有操作系统，包括 Windows、Linux 和 Mac。
       * 所有发布模式，包括“独立”或“框架相关”。
       * 所有目标框架，包括完整的 .NET Framework。
       * 所有托管选项，包括 Web 应用、VM、Linux、容器、Azure Kubernetes 服务和非 Azure 托管。
       * 所有 .NET Core 版本，包括预览版。
   * 从 Visual Studio 调试时，可在本地查看遥测数据。
   * 可以使用 `TrackXXX()` API 跟踪其他自定义遥测数据。
   * 可以完全控制配置。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>是否可以使用状态监视器之类的工具来启用 Application Insights 监视？

否。 [状态监视器](./monitor-performance-live-website-now.md)和[状态监视器 v2](./status-monitor-v2-overview.md) 目前仅支持 ASP.NET 4.x。

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>系统是否会自动为 ASP.NET Core 2.0 应用程序启用 Application Insights？

`Microsoft.AspNetCore.All` 2.0 元包包含 Application Insights SDK（版本 2.1.0）。 如果在 Visual Studio 调试器中运行该应用程序，则 Visual Studio 会启用 Application Insights，并在 IDE 本身中显示遥测数据。 除非指定检测密钥，否则遥测数据不会发送到 Application Insights 服务。 我们建议遵照本文中的说明启用 Application Insights，即使是对于 2.0 应用。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果在 Linux 中运行应用程序，是否支持所有功能？

是的。 SDK 的功能支持在所有平台中是相同的，不过存在以下例外情况：

* 该 SDK 在 Linux 上收集[事件计数器](./eventcounters.md)，因为[性能计数器](./performance-counters.md)仅在 Windows 中受支持。 大多数指标是相同的。
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
        services.AddApplicationInsightsTelemetry();
    }
```

### <a name="is-this-sdk-supported-for-the-new-net-core-3x-worker-service-template-applications"></a>新的 .NET Core 3.X 辅助角色服务模板应用程序是否支持此 SDK？

此 SDK 需要 `HttpContext`，因此在任何非 HTTP 应用程序（包括 .NET Core 3.X 辅助角色服务应用程序）中无法正常工作。 有关如何使用新发布的 Microsoft.ApplicationInsights.WorkerService SDK 在此类应用程序中启用 Application Insights，请参阅[此文档](worker-service.md)。

## <a name="open-source-sdk"></a>开源 SDK

* [阅读代码或为其做出贡献](https://github.com/microsoft/ApplicationInsights-dotnet#recent-updates)

有关最新的更新和 bug 修复，[请参阅发行说明](./release-notes.md)。

## <a name="next-steps"></a>后续步骤

* [浏览用户流](./usage-flows.md)，了解用户如何在应用中导航。
* [配置快照收集](./snapshot-debugger.md)，以便在引发异常时查看源代码和变量的状态。
* [使用 API](./api-custom-events-metrics.md) 发送自己的事件和指标，以获取应用性能和使用情况的详细视图。
* 使用[可用性测试](./monitor-web-app-availability.md)从世界各地不断检查应用。
* [ASP.NET Core 中的依赖项注入](/aspnet/core/fundamentals/dependency-injection)

