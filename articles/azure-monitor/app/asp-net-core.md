---
title: 适用于 ASP.NET Core 的 Azure Application Insights | Microsoft Docs
description: 监视 ASP.NET Core Web 应用程序的可用性、性能和使用情况。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mbullwin
ms.openlocfilehash: cb7ace20fd0a59dafff3d7f8240f54c3c8e12492
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66226399"
---
# <a name="application-insights-for-aspnet-core-applications"></a>适用于 ASP.NET Core 应用程序的 Application Insights

本文介绍如何启用适用于 Application Insights [ASP.NET Core](https://docs.microsoft.com/aspnet/core)应用程序。 完成这篇文章中的说明进行操作后，Application Insights 将开始收集从 ASP.NET Core 应用程序的请求、 依赖项、 异常、 性能计数器，检测信号和日志。 示例应用程序是[MVC 应用程序](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)面向`netcoreapp2.2`，但这些说明也适用于所有 ASP.NET Core 应用程序。

## <a name="supported-scenarios"></a>支持的方案

[适用于 ASP.NET Core 的 Application Insights SDK （软件开发工具包）](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以监视你的应用程序而不考虑位置和方式如何运行该应用程序。 如果你的应用程序正在运行，并且具有网络连接到 Azure，可以收集遥测数据。 这意味着，在 Application Insights 监视时均支持无处不在支持.NET Core。 此支持包括任何操作系统 (Windows、 Linux、 Mac)、 （进程内 vs 的进程外） 的托管方法、 部署方法 (依赖于框架的 vs 自包含)，Web 服务器 (IIS，Kestrel)，托管平台 （Azure Web 应用、 Azure 虚拟机、 Docker，Azure Kubernetes 服务 (AKS) 中，依次类推。）或 IDE （Visual Studio 中，VS Code 中，命令行）。

## <a name="prerequisites"></a>必备组件

- 一个正常运行的 ASP.NET Core 应用程序。 请按照[获取 ASP.NET Core 入门指南](https://docs.microsoft.com/aspnet/core/getting-started/)创建 ASP.NET Core 应用程序，如果需要。
- 将任何遥测数据发送到 Application Insights 服务时所需的有效 Application Insights 检测密钥。 请按照[创建资源的说明](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)根据需要创建新的 Application Insights 资源，并获取检测密钥。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>启用 Application Insights 服务器端遥测数据 (Visual Studio)

1. 在 Visual Studio 中打开项目。

    > [!TIP]
    > 而不是一个必需的步骤，非常有用来设置你的项目的源代码管理，以便可以跟踪所做的 Application Insights 的所有更改。 若要启用源控制选**文件** > **添加到源代码管理**。

2. 选择“项目” > “添加 Application Insights 遥测”   。

3. 选择“入门”。  （根据 Visual Studio 版本的不同，文本可能会稍有不同。 某些较旧的版本具有“免费开始”按钮。） 

4. 选择订阅，然后选择“资源” > “注册”   。

5. 将 Application Insights 添加到你的项目之后, 检查以确认你正在使用的 sdk 的最新稳定版本。 转到**项目** > **管理 NuGet 包** > **Microsoft.ApplicationInsights.AspNetCore** > 如有需要选择**更新**。

     ![屏幕截图使用 Application Insights 包更新为选择管理 NuGet 包屏幕](./media/asp-net-core/update-nuget-package.png)

6. 如果遵循可选提示和你的项目添加到源代码管理则可以转到**视图** > **团队资源管理器** > **更改**和选择差异视图中添加 Application Insights 遥测数据所做的更改的每个单个文件。

## <a name="enable-application-insights-server-side-telemetry-without-visual-studio"></a>启用 Application Insights 服务器端遥测数据 （不使用 Visual Studio)

1. 安装[适用于 ASP.NET Core 的 Application Insights SDK NuGet 包](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我们建议始终使用最新稳定版本。 在找不到 SDK 的完整的发行说明[打开源 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)。

    以下代码片段显示了所做的更改要添加到项目的`.csproj`文件。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
        </ItemGroup>
    ```

2. 添加`services.AddApplicationInsightsTelemetry();`到`ConfigureServices()`中的方法在`Startup`类。 下面是完整示例。

    ```csharp
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            // The following line enables Application Insights telemetry collection.
            services.AddApplicationInsightsTelemetry();
    
            // code adding other services for your application
            services.AddMvc();
        }
    ```

3. 设置检测密钥。

    虽然可以作为参数提供的检测密钥`AddApplicationInsightsTelemetry`，我们建议在配置中指定的检测密钥。 以下示例演示如何在 `appsettings.json` 中指定检测密钥。 发布时，请确保将 `appsettings.json` 复制到应用程序根文件夹。

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

    或者，也可在以下环境变量的任何一个指定的检测密钥。

    APPINSIGHTS_INSTRUMENTATIONKEY

    ApplicationInsights:InstrumentationKey

    示例：

    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    `APPINSIGHTS_INSTRUMENTATIONKEY` 通常用于指定要部署到 Azure Web 应用的应用程序的检测密钥。

    > [!NOTE]
    > 在代码中指定的检测密钥优先于环境变量 `APPINSIGHTS_INSTRUMENTATIONKEY`，而后者又优先于其他选项。

## <a name="run-your-application"></a>运行应用程序

 运行应用程序并向其发出请求。 现在，遥测数据应开始流入 Application Insights。 Application Insights SDK 自动收集以下遥测数据。

|请求/依赖项 |详细信息|
|---------------|-------|
|Requests | 向应用程序的传入 web 请求。 |
|Http/Https | 使用进行的调用`HttpClient`。 |
|SQL | 使用进行的调用`SqlClient`。 |
|[Azure 存储](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 存储客户端进行的调用。 |
|[事件中心客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 版及更高版本。 |
|[ServiceBus 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 版及更高版本。 |
|Azure Cosmos DB | 如果使用 HTTP/HTTPS，仅跟踪自动。 Application Insights 不会捕获 TCP 模式。 |

### <a name="performance-counters"></a>性能计数器

为支持[性能计数器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)在 ASP.NET Core 仅限于以下

   * 如果应用程序在 Azure Web 应用 (Windows) 中运行，则 SDK 2.4.1 和更高版本将收集性能计数器。
   * 如果应用程序在 Windows 中运行，并且面向 `NETSTANDARD2.0` 或更高版本，则 SDK 2.7.0-beta3 和更高版本将收集性能计数器。
   * 对于面向.NET Framework 的应用程序，在所有版本的 SDK 支持性能计数器。
   * 添加性能计数器支持在 Linux 中的时，将更新这篇文章。

### <a name="ilogger-logs"></a>ILogger 日志

[ILogger 日志](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)严重性的`Warning`或更高版本会自动从 SDK 版本 2.7.0-beta3 捕获或更高版本。

### <a name="live-metrics"></a>实时指标

可能需要几分钟时间才能开始在门户中显示的遥测数据。 若要快速检查是否一切正常，最好是在向运行中的应用程序发出请求时使用[实时指标](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)。

## <a name="enable-client-side-telemetry-for-web-applications"></a>为 Web 应用程序启用客户端遥测

上面的步骤都不足以开始收集服务器端遥测数据。 如果你的应用程序具有客户端组件，然后按照以下步骤来开始收集[使用情况遥测数据](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)从那里。

1. 在`_ViewImports.cshtml`，添加注入：

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在`_Layout.cshtml`，到末尾插入 HtmlHelper`<head>`部分之前的任何其他脚本。 要从页面报告的任何自定义 JavaScript 遥测数据应注入到此片段的后面：

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml`上面引用的文件名称是从默认的 MVC 应用程序模板。 从根本上讲，若要正确启用客户端监视应用程序需要的 JavaScript 代码片段位于`<head>`部分您想要监视的应用程序的每一页。 此应用程序模板添加到 Javascript 代码片段`_Layout.cshtml`有效地实现此目标。 如果你的项目不具有此特定文件仍可以添加[客户端监视](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)。 您将只是需要为添加 JavaScript 同等的文件，用于控制`<head>`的所有页面中你的应用，或者无法添加到多个单独的代码段页尽管这会很难维护和通常是不建议使用。

## <a name="configuring-application-insights-sdk"></a>配置 Application Insights SDK

可以自定义适用于 ASP.NET Core 的 Application Insights SDK 以更改默认配置。 Application Insights SDK ASP.NET 的用户可以使用 `ApplicationInsights.config` 或通过修改 `TelemetryConfiguration.Active` 来熟悉配置。 对于 ASP.NET Core，需以不同的方式完成配置。 添加到应用程序和配置使用 ASP.NET Core 的内置的 ASP.NET Core SDK[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 除非另有说明，否则几乎所有的配置更改都是在 `Startup.cs` 类的 `ConfigureServices()` 方法中完成的。 请阅读以下部分了解详细信息。

> [!NOTE]
>  通过修改来更改配置`TelemetryConfiguration.Active`不建议在 ASP.NET Core 应用程序中。

### <a name="configuring-using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions 进行配置

可以通过向 `AddApplicationInsightsTelemetry` 传递 `ApplicationInsightsServiceOptions` 来修改某些通用设置。 下面显示了一个示例。

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

在[此处](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)可以找到 `ApplicationInsightsServiceOptions` 中可配置设置的确切列表。

### <a name="sampling"></a>采样

适用于 ASP.NET Core 的 Application Insights SDK 支持 FixedRate 和自适应采样。 自适应采样默认已启用。 请按照我们[自适应采样的指南](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)，若要了解如何配置 ASP.NET Core 应用程序的采样。

### <a name="adding-telemetryinitializers"></a>添加 TelemetryInitializer

[遥测初始值设定项](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)你想要定义与所有遥测数据一起发送的全局属性时使用。

若要添加新的 `TelemetryInitializer`，请将其添加到 DependencyInjection 容器，如下所示。 SDK 会自动拾取添加到 DependencyInjection 容器的 `TelemetryInitializer`。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>删除 TelemetryInitializer

若要删除默认存在的所有或特定 TelemetryInitializer，请在调用 `AddApplicationInsightsTelemetry()` **之后**使用以下示例代码。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>添加 TelemetryProcessor

可以使用 `IServiceCollection` 中的扩展方法 `AddApplicationInsightsTelemetryProcessor` 将自定义遥测处理程序添加到 `TelemetryConfiguration`。 在使用遥测处理器[高级筛选方案](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)以允许对什么是包含或排除的遥测数据发送到 Application Insights 服务的更直接控制。 使用以下示例。

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

Application Insights 的方式使用遥测模块[自动收集有用的信息](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies)有关特定工作负荷，而无需其他配置。

以下自动收集模块默认已启用，它们负责自动收集遥测数据。 可禁用和配置这些模块，以改变默认行为。

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

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

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>配置遥测通道

使用的默认通道是 `ServerTelemetryChannel`。 可以遵循以下示例将其重写。

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="i-want-to-track-additional-telemetry-other-than-the-auto-collected-telemetry-how-do-i-do-it"></a>我想要跟踪的自动收集遥测数据以外的其他遥测数据。 我该怎么办？

使用构造函数注入获取 `TelemetryClient` 的实例，然后对其调用所需的 `TrackXXX()` 方法。 不建议在 ASP.NET Core 应用程序中创建新的 `TelemetryClient` 实例，因为 DI 容器中已注册了 `TelemetryClient` 的单一实例，该实例与剩余的遥测功能共享 `TelemetryConfiguration`。 仅当需要与剩余的遥测功能使用不同的配置时，才建议创建新的 `TelemetryClient` 实例。 以下示例演示如何从控制器跟踪其他遥测数据。

```csharp
using Microsoft.ApplicationInsights;

public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 请参阅[Application Insights 自定义指标 API 参考](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)有关 Application Insights 中的报表的自定义数据的说明。

### <a name="some-visual-studio-templates-used-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 模板使用 IWebHostBuilder 中的 UseApplicationInsights() 扩展方法来启用 Application Insights。 这种用法是否仍然有效？

使用此方法启用 Application Insights 是有效的，Visual Studio 载入以及 Azure Web 应用扩展都使用此方法。 但是，我们建议使用 `services.AddApplicationInsightsTelemetry()`，因为它提供重载来控制某些配置。 两者方法在内部执行相同的操作，因此，如果要应用任何自定义配置，则调用或者是没问题。

### <a name="i-am-deploying-my-aspnet-core-application-to-azure-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我正在将 ASP.NET Core 应用程序部署到 Azure Web 应用。 仍应该启用从 Web 应用的 Application Insights 扩展

如果这篇文章中所示，在生成时安装了 SDK，，则无需启用应用服务门户中的 Application Insights 扩展。 即使安装了扩展，它会将关闭时检测到 SDK 已添加到应用程序。 启用 Application Insights 从扩展使您安装和更新 SDK。 但是，根据本文所述启用 Application Insights 会更灵活，原因如下。
   * Application Insights 遥测数据将继续工作：
       * 所有操作系统 - Windows、Linux、Mac。
       * 所有发布模式下的独立或依赖于框架的。
       * 所有目标框架，包括完整的 .NET Framework。
       * 所有托管选项 - Azure Web 应用、VM、Linux、容器、AKS、非 Azure。
   * 从 Visual Studio 调试时，可在本地查看遥测数据。
   * 允许使用 `TrackXXX()` API 跟踪其他自定义遥测数据。
   * 您可以完全控制配置。

### <a name="can-i-enable-application-insights-monitoring-using-tools-like-status-monitor"></a>可以启用 Application Insights 监视使用状态监视器之类的工具？

不。 [状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)及其即将推出的替代[状态监视器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前支持 ASP.NET 4.x 仅。

### <a name="i-have-an-aspnet-core-20-application-isnt-application-insights-automatically-enabled-without-me-doing-anything"></a>我有一个 ASP.NET Core 2.0 应用程序。 Application Insights 不自动启用而无需执行任何操作？

`Microsoft.AspNetCore.All` 2.0 版元包包含 Application Insights SDK （版本 2.1.0），并运行 Visual Studio 调试器的应用程序，如果 Visual Studio 实现 Application Insights，本地本身在 IDE 中显示遥测数据。 除非显式指定检测密钥，否则遥测数据不会发送到 Application Insights 服务。 我们建议遵循本文中的说明启用 Application Insights 中，即使对于 2.0 应用程序。

### <a name="i-run-my-application-in-linux-are-all-features-supported-in-linux-as-well"></a>我在 Linux 中运行应用程序。 所有功能在 Linux 中也都支持？

* 是的。 SDK 的功能支持在所有平台中是相同的，不过存在以下例外情况：

    * 非 Windows 操作系统目前不支持性能计数器。
    * 即使`ServerTelemetryChannel`启用默认情况下，如果应用程序运行在 Linux 或 MacOS，通道不会自动创建本地存储文件夹以暂时保留遥测数据，如果存在网络问题。 如果出现暂时性的网络或服务器问题，此限制会导致遥测数据丢失。 此问题的解决方法是让用户配置通道的本地文件夹，如下所示。

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="open-source-sdk"></a>开源 SDK
[阅读代码或为其做出贡献](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>视频

- 有关从头开始[使用 NET Core 和 Visual Studio 配置 Application Insights](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) 的外部分步说明视频。
- 有关从头开始[使用 .NET Core 和 Visual Studio Code 配置 Application Insights](https://youtu.be/ygGt84GDync) 的外部分步说明视频。

## <a name="next-steps"></a>后续步骤
* [浏览用户流](../../azure-monitor/app/usage-flows.md)，了解用户如何在应用中导航。
* [配置快照收集](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)，以便在引发异常时查看源代码和变量的状态。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)，发送自己的事件和指标以获取应用的性能和使用情况的更详细视图。
* 使用[可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)从世界各地不断检查应用。
