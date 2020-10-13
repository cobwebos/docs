---
title: 使用 ILogger 浏览 .NET 跟踪日志 - Azure Application Insights
description: 有关将 Azure Application Insights ILogger 提供程序与 ASP.NET Core 及控制台应用程序配合使用的示例。
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 987d5b78c5fe680f43ff6a001e7a31a8ae9f6124
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931455"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider 的日志记录

本文演示如何使用 `ApplicationInsightsLoggerProvider` 捕获 `ILogger` 控制台和 ASP.NET Core 应用程序中的日志。
若要了解详细日志记录，请参阅 [ASP.NET Core 中的日志记录](/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

`ApplicationInsightsLoggerProvider` 当使用 [代码](./asp-net-core.md) 或 [代码较少](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) 的方法配置 applicationinsights.config 时，默认情况下为 ASP.NET Core 应用程序启用。

默认情况下，从所有类别)  (的*警告*和更高版本的 `ILogger` 日志都将发送给 Application Insights。 [categories](/aspnet/core/fundamentals/logging/#log-category) 但你可以 [自定义此行为](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection)。 从 **Program.cs** 或 **Startup.cs** 捕获 ILogger 日志需要执行额外的步骤。 （请参阅[在 ASP.NET Core 应用程序中从 Startup.cs 和 Program.cs 捕获 ILogger 日志](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)。）

如果只想使用 `ApplicationInsightsLoggerProvider` 而不使用任何其他 Application Insights 监视，请使用以下步骤。

1. 安装 NuGet 包：

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. 修改 `Program.cs` ，如下所示：

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
                   builder.AddApplicationInsights("put-actual-ikey-here");

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
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>在 ASP.NET Core 应用中从 Startup.cs 和 Program.cs 捕获 ILogger 日志

> [!NOTE]
> 在 ASP.NET Core 3.0 及更高版本中，无法再在 Startup.cs 和 Program.cs 中注入 `ILogger`。 有关详细信息，请参阅 https://github.com/aspnet/Announcements/issues/353 。

`ApplicationInsightsLoggerProvider` 可以在应用程序启动之前提前捕获日志。 虽然 ApplicationInsightsLoggerProvider 在 Application Insights（从 2.7.1 版开始）中已自动启用，但它直到稍后进入管道才设置检测密钥。 因此，只会从 **Controller**/其他类捕获日志。 若要捕获从 **Program.cs** 和 **Startup.cs** 本身开始的每个日志，必须显式为 ApplicationInsightsLoggerProvider 启用检测密钥。 此外，在从 **Program.cs** 或 **Startup.cs** 本身记录日志时，不会完全设置 *TelemetryConfiguration*。 因此，这些日志将具有使用 [InMemoryChannel](./telemetry-channels.md)的最小配置，不使用 [采样](./sampling.md)，并且没有标准 [遥测初始值设定项或处理器](./api-filtering-sampling.md)。

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
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>从旧的 ApplicationInsightsLoggerProvider 迁移

2\.7.1 之前的 Microsoft.ApplicationInsights.AspNet SDK 版本支持的某个日志记录提供程序现已过时。 此提供程序是通过 ILoggerFactory 的 **AddApplicationInsights()** 扩展方法启用的。 我们建议迁移到新的提供程序，这涉及到两个步骤：

1. 从 **Startup.Configure()** 方法中删除 *ILoggerFactory.AddApplicationInsights()* 调用，以避免重复日志记录。
2. 在代码中重新应用任何筛选规则，因为新提供程序不遵循旧规则。 *ILoggerFactory.AddApplicationInsights()* 的重载采用最低的 LogLevel 或筛选函数。 在新提供程序中，筛选是日志记录框架本身的一部分。 Application Insights 提供程序不会执行筛选。 因此，应删除通过 *ILoggerFactory.AddApplicationInsights()* 重载提供的所有筛选器。 应该遵照[控制日志记录级别](#control-logging-level)中的说明提供筛选规则。 如果使用 *appsettings.json* 筛选日志记录，此筛选可继续在新提供程序中进行，因为两者使用相同的提供程序别名 *ApplicationInsights*。

仍可以使用旧的提供程序。 （旧提供程序只会在 3.*xx* 主要版本中删除。）但是，我们建议迁移到新提供程序，原因如下：

- 以前的提供程序缺少对[日志范围](/aspnet/core/fundamentals/logging#log-scopes)的支持。 在新提供程序中，范围中的属性将作为自定义属性自动添加到收集的遥测数据。
- 现在，可以在应用程序启动管道中更早地捕获日志。 现在可以从 **Program** 和 **Startup** 类捕获日志。
- 在新提供程序中，筛选是在框架级别本身上执行的。 在 Application Insights 提供程序中，可以像在其他提供程序（包括 Console、Debug 等内置提供程序）中一样筛选日志。 还可以将相同的筛选器应用到多个提供程序。
- 在 ASP.NET Core（2.0 和更高版本）中，[启用日志记录提供程序](https://github.com/aspnet/Announcements/issues/255)的建议方式是在 **Program.cs** 本身中的 ILoggingBuilder 上使用扩展方法。

> [!Note]
> 新提供程序适用于面向 NETSTANDARD2.0 或更高版本的应用程序。 从 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.14.0 起，新的提供程序也可用于面向 .NET Framework NET461 或更高版本的应用程序。 如果应用程序面向较低的 .NET Core 版本（例如 .NET Core 1.1）或者面向低于 NET46 的 .NET Framework，请继续使用旧提供程序。

## <a name="console-application"></a>控制台应用程序

> [!NOTE]
> 有一个名为 [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 的新 Application Insights SDK，可用于为任何控制台应用程序启用 Application Insights（ILogger 和其他 Application Insights 遥测）。 建议在[此处](./worker-service.md)使用此包和相关说明。

如果只想在没有任何其他 Application Insights 监视的情况下使用 ApplicationInsightsLoggerProvider，请使用以下步骤。

已安装的包：

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
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

        logger.LogInformation("Logger is working");

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

以下部分介绍如何使用 **services.Configure\<TelemetryConfiguration>()** 方法重写默认的 TelemetryConfiguration。 此示例设置 `ServerTelemetryChannel` 和采样。 它将自定义的 ITelemetryInitializer 添加到 TelemetryConfiguration。

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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制日志记录级别

`ILogger` 具有用于应用 [日志筛选](/aspnet/core/fundamentals/logging#log-filtering)的内置机制。 这样，就可以控制发送到每个已注册的提供程序（包括 Application Insights 提供程序）的日志。 可以在配置中（通常是使用 *appsettings.json* 文件）或者在代码中执行筛选。

下面的示例演示如何将筛选规则应用到 `ApplicationInsightsLoggerProvider` 。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>使用 appsettings.json 在配置中创建筛选规则

对于 ApplicationInsightsLoggerProvider，提供程序别名为 `ApplicationInsights`。 appsettings.json 的以下节指示日志记录提供程序在“警告”级别及更高级别进行记录（通常情况下）。 然后，它重写 `ApplicationInsightsLoggerProvider` 以记录在“错误”及更高级别以“Microsoft”开头的类别。

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>在代码中创建筛选规则

以下代码片段配置所有类别中“警告”和更高级别的日志，以及以“Microsoft”开头的类别中要发送到 `ApplicationInsightsLoggerProvider` 的“错误”和更高级别的日志。  此配置是与前面的 *appsettings.json* 节中的配置相同。

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>日志范围

`ApplicationInsightsLoggingProvider` 默认情况下，支持启用 [日志作用域](/aspnet/core/fundamentals/logging#log-scopes) 和作用域。

如果作用域的类型为 `IReadOnlyCollection<KeyValuePair<string,object>>` ，则集合中的每个键值对将作为自定义属性添加到 application insights 遥测。 在下面的示例中，将捕获日志， `TraceTelemetry` 并在 "属性" )  ( "MyKey" 和 "MyValue"。

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

如果使用任何其他类型作为作用域，则它们将存储在 application insights 遥测中的属性 "Scope" 下。 在下面的示例中， `TraceTelemetry` 将具有一个名为 "scope" 的属性，该属性包含作用域。

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>常见问题

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider 的旧版本和新版本是什么？

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包含已通过 **ILoggerFactory** 扩展方法启用的内置 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)。 从版本 2.7.1 开始，此提供程序已标记为过时。 在下一次主要版本更改中，会彻底删除此提供程序。 [Microsoft.ApplicationInsights.AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 包本身尚未过时。 对请求、依赖项等启用监视需要用到它。

建议的替代方法是安装新的独立包 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含改进的 ApplicationInsightsLoggerProvider (Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider)，以及 ILoggerBuilder 中用于启用该提供程序的扩展方法。

[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.7.1 依赖于新包，可自动启用 ILogger 捕获。

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

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新到 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 版本 2.7.1，现在可以自动捕获 ILogger 中的日志。 如何完全禁用此功能？

请参阅[控制日志记录级别](#control-logging-level)部分有关如何筛选日志的一般信息。 若要禁用 ApplicationInsightsLoggerProvider，请使用 `LogLevel.None`：

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
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> 如果使用 Microsoft.ApplicationInsights.AspNetCore 包启用 Application Insights，请修改此代码，以便在构造函数中直接获取 `TelemetryClient`。 有关示例，请参阅[此常见问题解答](./asp-net-core.md#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>将从 ILogger 日志生成哪种类型的 Application Insights 遥测数据？ 或者，可以在何处查看 Application Insights 中的 ILogger 日志？

ApplicationInsightsLoggerProvider 捕获 ILogger 日志，并基于这些日志创建 TraceTelemetry。 如果在上将异常对象传递到 `Log` 方法 `ILogger` ，则将创建 *ExceptionTelemetry* 而不是 TraceTelemetry。 在显示 Application Insights 的任何其他 TraceTelemetry 或 ExceptionTelemetry 的位置（包括门户、Analytics 或 Visual Studio 本地调试器），都可以找到这些遥测项。

若要始终发送 TraceTelemetry，请使用此代码片段：```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我没有安装 SDK，而是使用 Azure Web 应用扩展来为 ASP.NET Core 应用程序启用 Application Insights。 如何使用新的提供程序？ 

Azure Web 应用中的 Application Insights 扩展使用新提供程序。 可以在应用程序的 *appsettings.json* 文件中修改筛选规则。

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

* [ASP.NET Core 中的日志记录](/aspnet/core/fundamentals/logging)
* [Application Insights 中的 .NET 跟踪日志](./asp-net-trace-logs.md)

