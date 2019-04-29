---
title: 适用于不使用 Visual Studio 的 ASP.NET Core 的 azure Application Insights |Microsoft Docs
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
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691336"
---
# <a name="application-insights-for-aspnet-core-applications"></a>用于 ASP.NET Core 应用程序的 application Insights

本文介绍了启用适用于 Application Insights 的步骤[ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)应用程序而无需使用 Visual Studio IDE。 如果已安装，则 Visual Studio IDE[这](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)文档包含 Visual Studio 的具体说明。 通过完成本文中的说明进行操作，Application Insights 将开始收集从 ASP.NET Core 应用程序的请求、 依赖项、 异常、 性能计数器，检测信号和日志。 使用示例应用程序是[MVC 应用程序](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2)面向`netcoreapp2.2`，但此处的说明也适用于所有 ASP.NET Core 应用程序。 如果适用，将标出任何例外情况。

## <a name="supported-scenarios"></a>支持的方案

适用于 ASP.NET Core 的 application Insights SDK （软件开发工具包） 可以监视你的应用程序而不考虑位置和方式如何运行该应用程序。 如果你的应用程序正在运行，并且具有网络连接到 Application Insights 服务，应遥测数据收集。 此支持包括但不局限于任何操作系统 (Windows、 Linux、 Mac)、 （进程内 vs 的进程外） 的托管方法、 部署方法 (依赖于框架的 vs 自包含)，Web 服务器 (IIS，Kestrel)，平台 （Azure Web 应用、 Azure VMDocker、 AKS 等。）或 IDE （Visual Studio 中，VS Code 中，命令行）。

## <a name="prerequisites"></a>必备组件

- 正常运行的 ASP.NET Core 应用程序。 请按照[这](https://docs.microsoft.com/aspnet/core/getting-started/)指南，以根据需要创建 ASP.NET Core 应用程序。
- 有效 Application Insights 检测密钥，需将任何遥测数据发送到 Application Insights 服务的。 请按照[这些](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)说明根据需要创建新的 Application Insights 资源，并获取检测密钥。

## <a name="enabling-application-insights-server-side-telemetry"></a>启用 Application Insights 服务器端遥测

1. 适用于 ASP.NET Core，这是常规的 NuGet 包安装 Application Insights SDK。 建议始终使用最新稳定版本。 某些本文中所述的功能可能不可用较旧版本中。 找不到 SDK 的完整的发行说明[此处](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)。 

下面显示了要添加到你的项目的.csproj 文件的更改。

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. 添加`services.AddApplicationInsightsTelemetry();`到`ConfigureServices()`中的方法在`Startup`类。 下面的完整示例。

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

3. 安装程序检测密钥。

   虽然可以作为参数提供检测密钥`services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`，建议在配置中指定的检测密钥。 以下示例演示如何指定检测密钥在`appsettings.json`。 请确保`appsettings.json`复制到发布时的应用程序根文件夹。

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

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` 通常用于指定应用程序部署到 Azure Web 应用的检测密钥。

> [!NOTE]
> 通过环境变量指定在代码 wins 中的检测密钥`APPINSIGHTS_INSTRUMENTATIONKEY`，相对于其他选择的 wins。

4. 运行应用程序，并对其发出请求。 遥测数据在应该开始流动到 Application Insights。 Application Insights SDK 自动收集以下遥测数据。

    1. 请求的传入 web 请求到你的应用程序。
    1. 依赖项
        1. 使用 http/Https 调用 `HttpClient`
        1. 使用发出 SQL 调用 `SqlClient`
        1. 使用 azure 存储调用[Azure 存储客户端](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [事件中心客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) 1.1.0 版及更高版本
        1. [服务总线客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) 3.0.0 版及更高版本

             * 仅当使用 HTTP/HTTPS 时，将自动跟踪 azure Cosmos DB。 Application Insights 不会捕获 TCP 模式。


    1. [性能计数器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. 对 ASP.NET Core 中的性能计数器的支持仅限于以下
            1. SDK 版本 2.4.1 和上面收集性能计数器 （如果在 Azure Web 应用 (Windows) 中运行应用程序
            1. SDK 版本 2.7.0-beta3 和上面收集性能计数器 （如果应用程序是 Windows，在运行目标`NETSTANDARD2.0`或更高版本。
            1. 对于面向完整.NET Framework 的应用程序，在所有版本的 SDK 支持性能计数器。

            添加性能计数器支持在 Linux 中的时，将更新本文档。
    1. [实时指标](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` 日志严重性`Warning`或更高版本会自动从 SDK 版本 2.7.0-beta3 捕获或更高版本。 在[此处](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)了解详细信息。

可能需要几分钟时间才能开始在门户中显示的遥测数据。 若要快速检查一切是否正常，是否它是使用[实时指标](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)，而使请求到运行的应用程序。

## <a name="send-ilogger-logs-to-application-insights"></a>将 ILogger 日志发送到 Application Insights

Application Insights 支持通过 ILogger 发送的捕获日志。 阅读完整文档[此处](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)。

## <a name="enable-client-side-telemetry-for-web-applications"></a>为 Web 应用程序启用客户端遥测

上面的步骤都不足以开始收集服务器端遥测数据。 如果你的应用程序具有客户端组件，然后按照以下步骤来开始收集[使用情况遥测数据](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)从那里。

1. 在 _ViewImports.cshtml，添加注入:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. 在 _Layout.cshtml，到末尾插入 HtmlHelper`<head>`部分之前的任何其他脚本。 应在此代码段后注入希望报表页上从任何自定义 JavaScript 遥测数据：

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

修改根据实际的应用程序的文件名称。 上面使用的名称是从默认 MVC 应用程序模板。

## <a name="configuring-application-insights-sdk"></a>配置 Application Insights SDK

可以自定义适用于 ASP.NET Core 的 application Insights SDK 以更改默认配置。 ASP.NET 的 Application Insights SDK 的用户可能是了解如何配置使用`ApplicationInsights.config`，或通过修改`TelemetryConfiguration.Active`。 适用于 ASP.NET Core，以不同的方式进行配置。 ASP.NET Core SDK 添加到应用程序使用 ASP.NET Core 的内置[DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)机制，和配置相同将还使用 DependencyInjection。 完成几乎所有配置更改后`ConfigureServices()`方法在`Startup.cs`类，除非另有说明。 请按照以下部分，以了解详细信息。

> [!NOTE]
> 务必要注意该修改配置，通过修改`TelemetryConfiguration.Active`不建议在 ASP.NET Core 应用程序中。

### <a name="configuring-using-applicationinsightsserviceoptions"></a>配置使用 ApplicationInsightsServiceOptions

可以通过传递来修改一些通用设置`ApplicationInsightsServiceOptions`到`services.AddApplicationInsightsTelemetry();`。 下面显示了一个示例。

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

可配置设置中的确切列表`ApplicationInsightsServiceOptions`可以找到[此处](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>采样

适用于 ASP.NET Core 的 application Insights SDK 支持 FixedRate 和自适应采样。 默认情况下启用自适应采样。 请按照[这](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)文档，了解如何配置 ASP.NET Core 应用程序的采样。

### <a name="adding-telemetryinitializers"></a>添加 TelemetryInitializers

若要添加新`TelemetryInitializer`，将其添加到 DependencyInjection 容器，如下所示。 `TelemetryInitializer`添加到 DependencyInjection 容器将由 SDK 自动选取。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>删除 TelemetryInitializers

若要删除所有或特定 TelemetryInitializers，都存在默认情况下，使用下面的示例代码**后**调用`AddApplicationInsightsTelemetry()`。

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

### <a name="adding-telemetryprocessors"></a>添加 TelemetryProcessors

自定义遥测处理器可以添加到`TelemetryConfiguration`通过使用扩展方法`AddApplicationInsightsTelemetryProcessor`上`IServiceCollection`。 使用下面的示例。

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

### <a name="configuring-or-removing-default-telemetrymodules"></a>配置或删除默认 TelemetryModules

以下自动收集模块默认情况下，启用并负责自动收集遥测数据。 它们可以禁用和配置来改变默认行为。

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

若要配置任何默认`TelemetryModule`，使用扩展方法`ConfigureTelemetryModule<T>`上`IServiceCollection`如下面的示例中所示。

```csharp
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

使用的默认通道是`ServerTelemetryChannel`。 它可以通过下面的以下示例重写。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>常见问题

*1.我想要跟踪除自动收集遥测数据的一些其他遥测数据。我该怎么办？*

* 获取的实例`TelemetryClient`通过使用构造函数注入和调用所需`TrackXXX()`方法。 不建议创建新`TelemetryClient`在 ASP.NET Core 应用程序，作为单一实例的实例`TelemetryClient`已在共享的 DI 容器中注册`TelemetryConfiguration`与遥测数据的其余部分。 创建一个新`TelemetryClient`实例建议仅当它必须有从遥测数据的其余部分不同的配置。 下面的示例演示如何从控制器跟踪的其他遥测数据。

```csharp
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

*2.某些 Visual Studio 模板上 IWebHostBuilder 使用 UseApplicationInsights() 扩展方法以启用 Application Insights。这种用法是否仍然有效？*

* 使用此方法启用 Application Insights 是有效的并在 Visual Studio 入职和和中的 Azure Web 应用扩展插件使用。 但是，建议使用`services.AddApplicationInsightsTelemery()`因为它提供了重载，用于控制一些配置。 两者方法在内部执行相同操作，因此，如果要应用任何自定义配置，则调用或者是没问题。

*3.我将部署到 Azure Web 应用我的 ASP.NET Core 应用程序。仍应该启用从 Web 应用的 Application Insights 扩展*

* 如果这篇文章中所示，在生成时安装 SDK，，则无需启用 Web 应用门户中的 Application Insights 扩展。 即使已安装扩展，它将回退，当它检测到 SDK 已添加到应用程序。 启用 Application Insights 从扩展使您安装并更新到你的应用程序的 SDK。 但是，根据这篇文章中启用 Application Insights 会出于以下原因更加灵活。
    1. Application Insights 遥测数据将继续工作
        1. 所有操作系统的 Windows，Linux，mac。
        1. 所有发布模式下的独立或依赖于框架的。
        1. 所有目标框架，包括完整的.NET Framework。
        1. 所有宿主选项-Azure Web 应用、 Vm、 Linux、 容器、 AKS，非 Azure。
    1. 从 Visual Studio 调试时，可以本地，查看遥测数据。
    1. 允许跟踪自定义的其他遥测数据使用`TrackXXX()`API。
    1. 可以完全控制配置。

*4.可以启用 Application Insights 监视使用状态监视器之类的工具？*

* 不。 [状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)及其即将推出的替代[IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21)目前仅支持 ASP.NET。 将更新文档时对 ASP.NET Core 应用程序可用的支持。

*5.我有一个 ASP.NET Core 2.0 应用程序？不是 Application Insights 会自动为其启用而无需执行任何操作？*

* `Microsoft.AspNetCore.All` 2.0 元包包含 Application Insights SDK （版本 2.1.0），并运行 Visual Studio 调试器的应用程序，如果 Visual Studio 将允许 application insights 和本地本身在 IDE 中显示遥测数据。 遥测数据未发送到 Application Insights 服务，除非显式指定检测密钥。 我们建议按照这篇文章中的说明进行操作，以启用 Application Insights 中，即使对于 2.0 应用程序。

*6.在 Linux 中运行我的应用程序。所有功能在 Linux 中也都支持？*

* 可以。 对 SDK 的功能支持是相同的所有平台，但存在以下例外：
    1. 在非 Windows 中尚不支持性能计数器。 添加 Linux 支持时，将更新本文档。
    1. 即使`ServerTelemetryChannel`启用默认情况下，如果应用程序运行在非 windows 通道不会自动创建本地存储文件夹以暂时保留遥测数据，如果存在网络问题。 此限制会导致遥测数据会丢失，如果存在临时网络或服务器问题。 此问题的解决方法是为用户配置的通道的本地文件夹，如下所示。

```csharp
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
