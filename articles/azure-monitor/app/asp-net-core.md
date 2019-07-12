---
title: Azure Application Insights 为 ASP.NET Core 应用程序 |Microsoft Docs
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
ms.openlocfilehash: 5ea7ec41ccc721e8eafda56aa7463505ba089845
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827812"
---
# <a name="application-insights-for-aspnet-core-applications"></a>适用于 ASP.NET Core 应用程序的 Application Insights

本文介绍如何启用适用于 Application Insights [ASP.NET Core](https://docs.microsoft.com/aspnet/core)应用程序。 完成这篇文章中的说明进行操作后，Application Insights 将从 ASP.NET Core 应用程序请求、 依赖项、 异常、 性能计数器，检测信号和日志收集。 

我们将使用下面的示例是[MVC 应用程序](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app)面向`netcoreapp2.2`。 可以将这些说明应用于所有 ASP.NET Core 应用程序。

## <a name="supported-scenarios"></a>支持的方案

[适用于 ASP.NET Core 的 Application Insights SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)可以监视你的应用程序，无论它们运行的位置和方式如何。 如果你的应用程序正在运行，并且网络连接到 Azure，可以收集遥测数据。 Application Insights 监视支持无处不在支持.NET Core。 支持包括：
* **操作系统**：Windows、 Linux 或 mac。
* **托管方法**:在进程或进程外。 
* **部署方法**:框架依赖或自包含。
* **Web 服务器**:IIS （Internet 信息服务器） 或 Kestrel。 
* **托管平台**:Azure 应用服务、 Azure VM、 Docker、 Azure Kubernetes 服务 (AKS) 等的 Web 应用功能。
* **IDE**:Visual Studio、 VS Code 或命令行。

## <a name="prerequisites"></a>先决条件

- 正常运行的 ASP.NET Core 应用程序。 如果需要创建一个 ASP.NET Core 应用程序，请按照这[ASP.NET Core 教程](https://docs.microsoft.com/aspnet/core/getting-started/)。
- 有效的 Application Insights 检测密钥。 此密钥需将任何遥测数据发送到 Application Insights。 如果你需要创建新的 Application Insights 资源，若要获取检测密钥，请参阅[创建 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)。

## <a name="enable-application-insights-server-side-telemetry-visual-studio"></a>启用 Application Insights 服务器端遥测数据 (Visual Studio)

1. 在 Visual Studio 中打开你的项目。

    > [!TIP]
    > 如果愿意，您可以设置源代码管理为你的项目以便可以跟踪所有 Application Insights 进行的更改。 若要启用源代码管理，请选择**文件** > **添加到源代码管理**。

2. 选择“项目” > “添加 Application Insights 遥测”   。

3. 选择“入门”。  此选择文本可能会有所不同，具体取决于你的 Visual Studio 版本。 某些较早版本使用**免费开始**按钮。

4. 选择订阅。 然后选择**资源** > **注册**。

5. 将 Application Insights 添加到你的项目之后, 检查以确认使用 SDK 的最新稳定版本。 转到**项目** > **管理 NuGet 包** > **Microsoft.ApplicationInsights.AspNetCore**。 如果需要选择**更新**。

     ![显示选择更新的 Application Insights 包的位置的屏幕截图](./media/asp-net-core/update-nuget-package.png)

6. 如果遵循可选提示和你的项目添加到源代码管理，请转到**视图** > **团队资源管理器** > **更改**。 然后选择每个文件，以查看 Application Insights 遥测数据所做的更改的差异视图。

## <a name="enable-application-insights-server-side-telemetry-no-visual-studio"></a>启用 Application Insights 服务器端遥测数据 (没有 Visual Studio)

1. 安装[适用于 ASP.NET Core 的 Application Insights SDK NuGet 包](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)。 我们建议始终使用最新稳定版本。 SDK 上找到完整的发行说明[开放源代码 GitHub 存储库](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)。

    下面的代码示例显示了所做的更改要添加到项目的`.csproj`文件。

    ```xml
        <ItemGroup>
          <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.7.0" />
        </ItemGroup>
    ```

2. 添加`services.AddApplicationInsightsTelemetry();`到`ConfigureServices()`中的方法在`Startup`类，如本例所示：

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

    尽管可作为参数提供的检测密钥`AddApplicationInsightsTelemetry`，我们建议在配置中指定的检测密钥。 下面的代码示例演示如何指定检测密钥在`appsettings.json`。 请确保`appsettings.json`发布过程复制到应用程序根文件夹。

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

    或者，在以下环境变量的任何一个指定的检测密钥：

    * `APPINSIGHTS_INSTRUMENTATIONKEY`

    * `ApplicationInsights:InstrumentationKey`

    例如：

    * `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

    * `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

    通常情况下，`APPINSIGHTS_INSTRUMENTATIONKEY`指定应用程序部署到 Web 应用的检测密钥。

    > [!NOTE]
    > 通过环境变量指定在代码 wins 中的检测密钥`APPINSIGHTS_INSTRUMENTATIONKEY`，相对于其他选择的 wins。

## <a name="run-your-application"></a>运行应用程序

运行应用程序并对其发出请求。 遥测现在应流动到 Application Insights。 Application Insights SDK 自动收集以下遥测数据。

|请求/依赖项 |详细信息|
|---------------|-------|
|Requests | 向应用程序的传入 web 请求。 |
|HTTP 或 HTTPS | 使用进行的调用`HttpClient`。 |
|SQL | 使用进行的调用`SqlClient`。 |
|[Azure 存储](https://www.nuget.org/packages/WindowsAzure.Storage/) | 使用 Azure 存储客户端进行的调用。 |
|[EventHubs 客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1\.1.0 版及更高版本。 |
|[服务总线客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)| 3\.0.0 版及更高版本。 |
|Azure Cosmos DB | 仅当使用 HTTP/HTTPS 时也会自动跟踪。 Application Insights 不会捕获 TCP 模式。 |

### <a name="performance-counters"></a>性能计数器

为支持[性能计数器](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)在 ASP.NET Core 不能：

   * SDK 版本 2.4.1 和更高版本收集的性能计数器，如果在 Web 应用 (Windows) 中运行应用程序。
   * 如果应用程序运行在 Windows 和目标的 SDK 版本 2.7.0-beta3 和更高版本的收集性能计数器`NETSTANDARD2.0`或更高版本。
   * 对于面向.NET Framework 的应用程序，所有版本的 SDK 都支持性能计数器。
 
添加性能计数器支持在 Linux 中的时，将更新这篇文章。

### <a name="ilogger-logs"></a>ILogger 日志

[ILogger 日志](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)严重性的`Warning`或更高版本中 SDK 版本 2.7.0-beta3 及更高版本，将自动捕获。

### <a name="live-metrics"></a>实时指标

可能需要几分钟才能在门户中显示遥测数据开始。 若要快速确保一切正常运行，最好使用[实时指标](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)向运行的应用程序发出请求时。

## <a name="enable-client-side-telemetry-for-web-applications"></a>启用 web 应用程序的客户端的遥测数据

在前面的步骤，即可帮助您开始收集服务器端遥测数据。 如果应用程序具有客户端组件，请执行下一步的步骤，若要开始收集[使用情况遥测数据](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview)。

1. 在`_ViewImports.cshtml`，添加注入：

    ```cshtml
        @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
    ```

2. 在`_Layout.cshtml`，插入`HtmlHelper`末尾处`<head>`部分之前的任何其他脚本。 如果你想要报告任何自定义 JavaScript 遥测数据从页中，将其插入此代码段后：

    ```cshtml
        @Html.Raw(JavaScriptSnippet.FullScript)
        </head>
    ```

`.cshtml`前面引用的文件名称是从默认的 MVC 应用程序模板。 从根本上讲，如果你想要正确启用客户端监视应用程序，JavaScript 代码片段必须出现在`<head>`部分您想要监视的应用程序的每一页。 可以通过添加到 JavaScript 代码片段来完成这一目标才能使用此应用程序模板`_Layout.cshtml`。 

如果你的项目不包括`_Layout.cshtml`，则仍然可以添加[客户端监视](https://docs.microsoft.com/azure/azure-monitor/app/website-monitoring)。 您可以执行此操作通过将 JavaScript 代码片段添加到同等的文件，用于控制`<head>`的您的应用程序内所有页面。 或可以将代码段添加到多个页，但这种解决方案很难以维护和通常不建议这样。

## <a name="configure-the-application-insights-sdk"></a>配置 Application Insights SDK

您可以自定义 Application Insights SDK 适用于 ASP.NET Core，若要更改默认配置。 Application Insights ASP.NET SDK 的用户可能会熟悉通过更改配置`ApplicationInsights.config`或通过修改`TelemetryConfiguration.Active`。 更改不同的方式针对 ASP.NET Core 的配置。 将 ASP.NET Core SDK 添加到应用程序并将其配置通过使用 ASP.NET Core 内置[依赖关系注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)。 进行中的几乎所有配置更改`ConfigureServices()`方法在`Startup.cs`类，除非将否则转。 以下各节提供的详细信息。

> [!NOTE]
> 在 ASP.NET Core 应用程序，通过修改来更改配置`TelemetryConfiguration.Active`不受支持。

### <a name="using-applicationinsightsserviceoptions"></a>使用 ApplicationInsightsServiceOptions

可以通过修改一些通用设置`ApplicationInsightsServiceOptions`到`AddApplicationInsightsTelemetry`，如下例所示：

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

有关详细信息，请参阅[中的可配置设置`ApplicationInsightsServiceOptions` ](https://github.com/microsoft/ApplicationInsights-aspnetcore/blob/develop/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)。

### <a name="sampling"></a>采样

Application Insights SDK 适用于 ASP.NET Core 支持固定速率和自适应采样。 自适应采样默认已启用。 

有关详细信息，请参阅[配置为 ASP.NET Core 应用程序的自适应采样](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)。

### <a name="adding-telemetryinitializers"></a>添加 TelemetryInitializer

使用[遥测初始值设定项](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)当你想要定义与所有遥测数据一起发送的全局属性。

添加任何新`TelemetryInitializer`到`DependencyInjection`容器中的以下代码所示。 SDK 自动选取任何`TelemetryInitializer`添加到`DependencyInjection`容器。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>删除 TelemetryInitializer

遥测初始值设定项的默认情况下存在。 若要删除所有或特定遥测初始值设定项，使用下面的示例代码*后*调用`AddApplicationInsightsTelemetry()`。

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

### <a name="adding-telemetry-processors"></a>添加遥测处理器

您可以添加到自定义遥测处理器`TelemetryConfiguration`通过使用扩展方法`AddApplicationInsightsTelemetryProcessor`上`IServiceCollection`。 使用中的遥测处理器[高级筛选方案](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#filtering-itelemetryprocessor)以允许更直接地控制什么具有包含或排除的遥测数据发送到 Application Insights 服务。 使用以下示例。

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

Application Insights 使用到的遥测模块[自动收集有用的信息](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies)有关特定工作负荷，而无需其他配置。

默认情况下启用以下自动集合模块。 这些模块负责自动收集遥测数据。 可以禁用或配置它们以更改其默认行为。

* `RequestTrackingTelemetryModule`
* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule`
* `AzureInstanceMetadataTelemetryModule`

若要配置任何默认`TelemetryModule`，使用扩展方法`ConfigureTelemetryModule<T>`上`IServiceCollection`，如以下示例所示。

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

### <a name="configuring-a-telemetry-channel"></a>配置遥测通道

默认通道是`ServerTelemetryChannel`。 您可以覆盖它，如以下示例所示。

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

如果你想要禁用遥测数据，有条件地和动态，您可能会解决`TelemetryConfiguration`与你的代码中的任意位置的 ASP.NET Core 依赖关系注入容器实例并设置`DisableTelemetry`上它的标志。

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

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>如何跟踪不会自动收集遥测数据？

获取的实例`TelemetryClient`通过使用构造函数注入和调用所需`TrackXXX()`方法。 我们不建议创建新`TelemetryClient`中的 ASP.NET Core 应用程序的实例。 单一实例`TelemetryClient`已在中注册`DependencyInjection`容器，共享`TelemetryConfiguration`与遥测数据的其余部分。 创建一个新`TelemetryClient`实例建议仅在需要独立于其他遥测的配置。 

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

有关 Application Insights 中的报表的自定义数据的详细信息，请参阅[Application Insights 自定义指标 API 参考](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/)。

### <a name="some-visual-studio-templates-used-the-useapplicationinsights-extension-method-on-iwebhostbuilder-to-enable-application-insights-is-this-usage-still-valid"></a>某些 Visual Studio 模板上 IWebHostBuilder 使用 UseApplicationInsights() 扩展方法以启用 Application Insights。 这种用法是否仍然有效？

是的使用此方法启用 Application Insights 是有效的。 在 Visual Studio 载入和 Web 应用扩展中，使用此方法。 但是，我们建议使用`services.AddApplicationInsightsTelemetry()`因为它提供了重载，用于控制一些配置。 这两种方法执行相同的操作在内部，因此如果您不需要应用自定义配置，你可以调用任何一种方法。

### <a name="im-deploying-my-aspnet-core-application-to-web-apps-should-i-still-enable-the-application-insights-extension-from-web-apps"></a>我将我的 ASP.NET Core 应用程序到 Web 应用部署。 仍应该启用从 Web 应用的 Application Insights 扩展

如果这篇文章中所示，SDK 安装在生成时，不需要启用应用服务门户中的 Application Insights 扩展。 即使安装了扩展，它会将关闭时检测到 SDK 已添加到应用程序。 如果从扩展启用 Application Insights，你无需安装和更新 SDK。 但是，如果按照这篇文章中的说明启用 Application Insights，具有更大的灵活性，因为：

   * Application Insights 遥测数据将继续工作：
       * 所有操作系统，包括 Windows、 Linux 和 mac。
       * 所有发布模式，包括独立或依赖的框架。
       * 所有目标框架，包括完整的 .NET Framework。
       * 所有宿主选项，包括 Web 应用、 Vm、 Linux、 容器、 Azure Kubernetes 服务和非 Azure 托管。
   * 从 Visual Studio 调试时，可以本地看到遥测数据。
   * 你可以通过使用跟踪更多的自定义遥测`TrackXXX()`API。
   * 您可以完全控制配置。

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>可以启用 Application Insights 监视使用状态监视器之类的工具？

否。 [状态监视器](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)并[状态监视器 v2](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview)目前支持 ASP.NET 4.x 仅。

### <a name="is-application-insights-automatically-enabled-for-my-aspnet-core-20-application"></a>Application Insights 会自动启用我的 ASP.NET Core 2.0 应用程序？

`Microsoft.AspNetCore.All` 2.0 元包包含 Application Insights SDK （版本 2.1.0）。 如果您运行 Visual Studio 调试器的应用程序，Visual Studio 将允许 Application Insights 和本地本身在 IDE 中显示遥测数据。 遥测数据不是发送到 Application Insights 服务，除非指定检测密钥。 我们建议遵循本文中的说明启用 Application Insights 中，即使对于 2.0 应用程序。

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>如果在 Linux 中运行我的应用程序，则支持所有功能？

是的。 对 SDK 的功能支持是在所有平台，但存在以下例外中相同的：

* 仅在 Windows 中支持性能计数器。
* 即使`ServerTelemetryChannel`启用默认情况下，如果应用程序运行在 Linux 或 MacOS，通道不会自动创建本地存储文件夹以暂时保留遥测数据，如果存在网络问题。 鉴于此限制，遥测会丢失临时网络或服务器问题时。 若要解决此问题，请配置的通道的本地文件夹：

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

## <a name="open-source-sdk"></a>开源 SDK

[阅读代码或为其做出贡献](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>视频

- 签出到此外部分步视频[使用.NET Core 和 Visual Studio 中配置 Application Insights](https://www.youtube.com/watch?v=NoS9UhcR4gA&t)从零开始。
- 签出到此外部分步视频[使用.NET Core 和 Visual Studio Code 中配置 Application Insights](https://youtu.be/ygGt84GDync)从零开始。

## <a name="next-steps"></a>后续步骤

* [浏览用户流](../../azure-monitor/app/usage-flows.md)了解用户如何导航您的应用程序。
* [配置快照集合](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)若要查看在引发异常时源代码和变量的状态。
* [使用 API](../../azure-monitor/app/api-custom-events-metrics.md)发送事件和指标的应用程序的性能和使用情况的详细视图。
* 使用[可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)从世界各地不断检查应用。
* [在 ASP.NET Core 中的依赖关系注入](https://docs.microsoft.com/aspnet/fundamentals/dependency-injection)
