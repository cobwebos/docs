---
title: 使用 ILogger 在 Azure Application Insights 中浏览 .NET 跟踪日志
description: 有关将 Azure Application Insights ILogger 提供程序与 ASP.NET Core 及控制台应用程序配合使用的示例。
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: acc7a218d40ec7b752d9495bd48e5f37436d736d
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169458"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider

ASP.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 对 *ILogger* 实例调用 **log()** 或其变体可以实现日志记录。 本文演示如何使用 *ApplicationInsightsLoggerProvider* 在控制台和 ASP.NET Core 应用程序中捕获 ILogger 日志。 此外，本文还会介绍 ApplicationInsightsLoggerProvider 如何与其他 Application Insights 遥测功能集成。
有关详细信息，请参阅[登录 ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

在 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.7.0-beta3（和更高版本）中，通过以下任一标准方法启用常规的 Application Insights 监视功能时，默认会启用 ApplicationInsightsLoggerProvider：
- 通过对 IWebHostBuilder 调用 **UseApplicationInsights** 扩展方法 
- 通过对 IServiceCollection 调用 **AddApplicationInsightsTelemetry** 扩展方法

ILogger ApplicationInsightsLoggerProvider 捕获的日志与收集的任何其他遥测数据采用相同的配置。 它们与其他遥测数据具有相同的 TelemetryInitializer 和 TelemetryProcessor 集，使用相同的 TelemetryChannel，并且关联方式和采样方式也相同。 如果使用版本 2.7.0-beta3 或更高版本，则无需执行任何操作就能捕获 ILogger 日志。

默认情况下，只会将“警告”或更高级别的 ILogger 日志（所有类别中）发送到 Application Insights。 但是，可以[应用筛选器来修改此行为](#control-logging-level)。 从 **Program.cs** 或 **Startup.cs** 捕获 ILogger 日志需要执行额外的步骤。 （请参阅[在 ASP.NET Core 应用程序中从 Startup.cs 和 Program.cs 捕获 ILogger 日志](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)。）

如果使用早期版本的 Microsoft.ApplicationInsights.AspNet SDK，或者只想使用 ApplicationInsightsLoggerProvider 而不启用任何其他 Application Insights 监视，请使用以下过程：

1. 安装 NuGet 包：

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 按如下所示修改 **Program.cs**：

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

步骤 2 中的代码将配置 `ApplicationInsightsLoggerProvider`。 以下代码演示了一个使用 `ILogger` 发送日志的示例 Controller 类。 Application Insights 将捕获日志。

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>在 ASP.NET Core 应用中从 Startup.cs 和 Program.cs 捕获 ILogger 日志

> [!NOTE]
> 在 ASP.NET Core 3.0 及更高版本中，无法再在 Startup.cs 和 Program.cs 中注入 `ILogger`。 有关详细信息，请参阅 https://github.com/aspnet/Announcements/issues/353 。

新的 ApplicationInsightsLoggerProvider 可以提前在应用程序启动管道中捕获日志。 尽管 Application Insights（从版本 2.7.0-beta3 开始）中会自动启用 ApplicationInsightsLoggerProvider，但只有在稍后进入管道后，才会设置它的检测密钥。 因此，只会从 **Controller**/其他类捕获日志。 若要捕获从 **Program.cs** 和 **Startup.cs** 本身开始的每个日志，必须显式为 ApplicationInsightsLoggerProvider 启用检测密钥。 此外，在从 **Program.cs** 或 **Startup.cs** 本身记录日志时，不会完全设置 *TelemetryConfiguration*。 因此，这些日志将采用最低的配置，该配置使用 InMemoryChannel，不使用采样，且不使用标准的初始化程序或处理程序。

以下示例使用 **Program.cs** 和 **Startup.cs** 演示此功能。

#### <a name="example-programcs"></a>示例 Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>示例 Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>从旧的 ApplicationInsightsLoggerProvider 迁移

2\.7.0-beta2 之前的 Microsoft.ApplicationInsights.AspNet SDK 版本支持的某个日志记录提供程序现已过时。 此提供程序是通过 ILoggerFactory 的 **AddApplicationInsights()** 扩展方法启用的。 我们建议迁移到新的提供程序，这涉及到两个步骤：

1. 从 **Startup.Configure()** 方法中删除 *ILoggerFactory.AddApplicationInsights()* 调用，以避免重复日志记录。
2. 在代码中重新应用任何筛选规则，因为新提供程序不遵循旧规则。 *ILoggerFactory.AddApplicationInsights()* 的重载采用最低的 LogLevel 或筛选函数。 在新提供程序中，筛选是日志记录框架本身的一部分。 Application Insights 提供程序不会执行筛选。 因此，应删除通过 *ILoggerFactory.AddApplicationInsights()* 重载提供的所有筛选器。 应该遵照[控制日志记录级别](#control-logging-level)中的说明提供筛选规则。 如果使用 *appsettings.json* 筛选日志记录，此筛选可继续在新提供程序中进行，因为两者使用相同的提供程序别名 *ApplicationInsights*。

仍可以使用旧的提供程序。 （旧提供程序只会在 3.*xx* 主要版本中删除。）但是，我们建议迁移到新提供程序，原因如下：

- 以前的提供程序缺少对[日志范围](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)的支持。 在新提供程序中，范围中的属性将作为自定义属性自动添加到收集的遥测数据。
- 现在，可以在应用程序启动管道中更早地捕获日志。 现在可以从 **Program** 和 **Startup** 类捕获日志。
- 在新提供程序中，筛选是在框架级别本身上执行的。 在 Application Insights 提供程序中，可以像在其他提供程序（包括 Console、Debug 等内置提供程序）中一样筛选日志。 还可以将相同的筛选器应用到多个提供程序。
- 在 ASP.NET Core（2.0 和更高版本）中，[启用日志记录提供程序](https://github.com/aspnet/Announcements/issues/255)的建议方式是在 **Program.cs** 本身中的 ILoggingBuilder 上使用扩展方法。

> [!Note]
> 新提供程序适用于面向 NETSTANDARD2.0 或更高版本的应用程序。 如果应用程序面向较低的 .NET Core 版本（例如 .NET Core 1.1）或者面向 .NET Framework，请继续使用旧提供程序。

## <a name="console-application"></a>控制台应用程序

> [!NOTE]
> 有一个名为 Applicationinsights.config 的新 beta Application Insights SDK [WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ，可用于为任何控制台应用程序启用 Application Insights （ILogger 和其他 Application Insights 遥测）。 建议在[此处](../../azure-monitor/app/worker-service.md)使用此包和相关说明。
释放此新包的稳定版本后，将弃用下面的示例。

以下代码演示了一个配置为向 Application Insights 发送 ILogger 跟踪的示例控制台应用程序。

已安装的包：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

此示例使用独立包 `Microsoft.Extensions.Logging.ApplicationInsights`。 默认情况下，此配置使用最低的 TelemetryConfiguration 将数据发送到 Application Insights。 “最低”表示 InMemoryChannel 是使用的通道。 它不使用采样，也不使用标准的 TelemetryInitializer。 可为控制台应用程序重写此行为，如以下示例所示。

安装此附加包：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

以下部分说明如何使用服务重写默认 TelemetryConfiguration **。配置\<TelemetryConfiguration > ()** 方法。 此示例设置 `ServerTelemetryChannel` 和采样。 它将自定义的 ITelemetryInitializer 添加到 TelemetryConfiguration。

```csharp
    // Create the DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制日志记录级别

ASP.NET Core *ILogger* 基础结构提供一个内置机制用于应用[日志筛选](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)。 这样，就可以控制发送到每个已注册的提供程序（包括 Application Insights 提供程序）的日志。 可以在配置中（通常是使用 *appsettings.json* 文件）或者在代码中执行筛选。 此功能由框架本身提供。 它并不是 Application Insights 提供程序专有的功能。

以下示例将筛选规则应用到 ApplicationInsightsLoggerProvider。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>使用 appsettings.json 在配置中创建筛选规则

对于 ApplicationInsightsLoggerProvider，提供程序别名为 `ApplicationInsights`。 以下 *appsettings.json* 节配置所有类别中“警告”和更高级别的日志，以及以“Microsoft”开头的类别中要发送到 `ApplicationInsightsLoggerProvider` 的“错误”和更高级别的日志。

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>在代码中创建筛选规则

以下代码片段配置所有类别中“警告”和更高级别的日志，以及以“Microsoft”开头的类别中要发送到 `ApplicationInsightsLoggerProvider` 的“错误”和更高级别的日志。 此配置是与前面的 *appsettings.json* 节中的配置相同。

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider 的旧版本和新版本是什么？

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包含已通过 **ILoggerFactory** 扩展方法启用的内置 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)。 在版本 2.7.0-beta2 中，此提供程序已标记为过时。 在下一次主要版本更改中，会彻底删除此提供程序。 [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包本身尚未过时。 对请求、依赖项等启用监视需要用到它。

建议的替代方法是安装新的独立包 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含改进的 ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider)，以及 ILoggerBuilder 中用于启用该提供程序的扩展方法。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.7.0-beta3 依赖于新包，可自动启用 ILogger 捕获。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>为何某些 ILogger 日志在 Application Insights 中会显示两次？

如果通过对 `ILoggerFactory` 调用 `AddApplicationInsights` 来启用旧版（现在过时）ApplicationInsightsLoggerProvider，则可能会出现重复项。 检查 **Configure** 方法是否包含以下内容，如有则将其删除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果在 Visual Studio 中调试时看到重复的日志，请在启用 Application Insights 的代码中将 `EnableDebugLogger` 设置为 *false*，如下所示。 这种重复现象和修复方法仅与调试应用程序时的情况相关。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新到 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.7.0-beta3，现在可以自动捕获 ILogger 中的日志。 如何完全禁用此功能？

请参阅[控制日志记录级别](../../azure-monitor/app/ilogger.md#control-logging-level)部分有关如何筛选日志的一般信息。 若要禁用 ApplicationInsightsLoggerProvider，请使用 `LogLevel.None`：

**在代码中：**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**在配置中：**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>为何某些 ILogger 日志包含的属性与其他日志不同？

Application Insights 使用对其他每项遥测功能所用的相同 TelemetryConfiguration 来捕获和发送 ILogger 日志。 但存在一种例外情况。 默认情况下，在从 **Program.cs** 或 **Startup.cs** 记录日志时，不会完全设置 TelemetryConfiguration。 来自这些位置的日志不采用默认配置，因此它们不会运行所有的 TelemetryInitializer 和 TelemetryProcessor。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我正在使用独立包 Microsoft.Extensions.Logging.ApplicationInsights，并想要手动记录一些附加的自定义遥测数据。 如何做到这一点？

使用独立包时，`TelemetryClient` 不会注入到 DI 容器，因此你需要创建 `TelemetryClient` 的新实例，并使用记录器提供程序所用的相同配置，如以下代码所示。 这可以确保对所有自定义遥测数据以及来自 ILogger 的遥测数据使用相同的配置。

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> 如果使用 Microsoft.ApplicationInsights.AspNetCore 包启用 Application Insights，请修改此代码，以便在构造函数中直接获取 `TelemetryClient`。 有关示例，请参阅[此常见问题解答](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>将从 ILogger 日志生成哪种类型的 Application Insights 遥测数据？ 或者，可以在何处查看 Application Insights 中的 ILogger 日志？

ApplicationInsightsLoggerProvider 捕获 ILogger 日志，并基于这些日志创建 TraceTelemetry。 如果将某个 Exception 对象传递给 ILogger 中的 **Log()** 方法，则会创建 *ExceptionTelemetry* 而不是 TraceTelemetry。 在显示 Application Insights 的任何其他 TraceTelemetry 或 ExceptionTelemetry 的位置（包括门户、Analytics 或 Visual Studio 本地调试器），都可以找到这些遥测项。

若要始终发送 TraceTelemetry，请使用此代码片段：```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我没有安装 SDK，而是使用 Azure Web 应用扩展来为 ASP.NET Core 应用程序启用 Application Insights。 如何使用新的提供程序？ 

Azure Web 应用中的 Application Insights 扩展使用旧提供程序。 可以在应用程序的 *appsettings.json* 文件中修改筛选规则。 若要利用新提供程序，请通过 SDK 中的某个 NuGet 依赖项使用生成时检测。 当扩展改用新的提供程序时，本文将会更新。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>我正在使用独立包 Microsoft.Extensions.Logging.ApplicationInsights 并通过调用 **builder.AddApplicationInsights("ikey")** 来启用 Application Insights 提供程序。 是否有某个选项可从配置中获取检测密钥？


请按如下所示修改 Program.cs 和 appsettings.json：

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   `appsettings.json` 中的相关节：

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

仅当使用独立的日志记录提供程序时，才需要提供此代码。 对于常规的 Application Insights 监视，系统会自动从配置路径 *ApplicationInsights:Instrumentationkey* 中加载检测密钥。 Appsettings.json 应如下所示：

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>后续步骤

了解有关以下方面的详细信息：

* [ASP.NET Core 中的日志记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights 中的 .NET 跟踪日志](../../azure-monitor/app/asp-net-trace-logs.md)
