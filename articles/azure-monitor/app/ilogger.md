---
title: 使用 ILogger 在 Azure Application Insights 中浏览 .NET 跟踪日志
description: ASP.NET Core 和控制台应用程序中使用 Azure Application Insights ILogger 提供程序的示例。
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608279"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>对于.NET Core ILogger 日志 ApplicationInsightsLoggerProvider

ASP.NET Core 支持适用于不同种类的内置和第三方日志记录提供程序的日志记录 API。 日志记录通过对调用 log （） 或它的一个变体`ILogger`实例。 本文介绍如何使用`ApplicationInsightsLoggerProvider`捕获`ILogger`控制台和 ASP.NET Core 应用程序中记录。 本文还介绍了如何`ApplicationInsightsLoggerProvider`与其他 Application Insights 遥测数据集成。
若要了解在 Asp.Net Core 中的详细日志记录，请参阅[这篇文章](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

从开始[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)及更高版本，版本 2.7.0-beta3`ApplicationInsightsLoggerProvider`启用使用标准的方法-由的正则 Application Insights 监视时，默认情况下启用调用`UseApplicationInsights`IWebHostBuilder 扩展方法或`AddApplicationInsightsTelemetry`IServiceCollection 的扩展方法。 `ILogger` 日志捕获`ApplicationInsightsLoggerProvider`受到相同的配置任何其他遥测数据收集。 例如  它们具有相同的一组`TelemetryInitializer`s， `TelemetryProcessor`s，使用相同`TelemetryChannel`，并将关联和采样中每个其他遥测一样。  如果您在此版本的 SDK 或更高版本，则不需要任何操作来捕获`ILogger`日志。

默认情况下，仅`ILogger`记录的`Warning`或上方 （从所有类别） 发送到 Application Insights。 可以通过应用筛选器，如所示更改此行为[此处](#control-logging-level)。 此外附加步骤是必需的如果`ILogger`过程中的日志`Program.cs`或`Startup.cs`是要捕获所示[此处](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications)。

如果使用早期版本的 Microsoft.ApplicationInsights.AspNet SDK，或者你想要只需使用 ApplicationInsightsLoggerProvider，而无需任何其他 Application Insights 监视，按照以下步骤。

1.安装 nuget 包。

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2.修改`Program.cs`如下所示

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
                // Providing an instrumentation key here is required if you are using
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

上面的代码将配置`ApplicationInsightsLoggerProvider`。 下面显示了一个示例控制器类，该类使用`ILogger`发送日志，ApplicationInsights 捕获。

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

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>捕获 Startup.cs，Asp.Net Core 应用程序中的 Program.cs 中的 ILogger 日志

使用新 ApplicationInsightsLoggerProvider，就可能可以捕获日志，从应用程序启动管道在早期阶段。 甚至 ApplicationInsightsLoggerProvider 是自动启用 Application Insights （从 2.7.0-beta3 及更高版本)，它不具有更高版本在管道中，因此只有从控制器日志之前的检测密钥设置/将捕获的其他类。 若要捕获从每个日志`Program.cs`和`Startup.cs`本身，一个需要显式启用 ApplicationInsightsLoggerProvider 使用检测密钥。 还有一点需要注意`TelemetryConfiguration`未完全设置时日志记录从`Program.cs`或`Startup.cs`本身，因此这些日志将在使用裸机的最小配置，使用 InMemoryChannel、 任何采样和没有标准遥测初始值设定项或处理器。

下面的示例`Program.cs`和`Startup.cs`使用此功能。

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
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
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

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>从旧 ApplicationInsightsLoggerProvider 迁移

Microsoft.ApplicationInsights.AspNet SDK 2.7.0-beta2 之前, 的版本中支持的日志记录提供程序现已过时。 此提供程序已启用`AddApplicationInsights()`扩展方法的`ILoggerFactory`。 此提供程序现已过时，并推荐用户迁移到新的提供程序。 迁移涉及两个步骤。

1. 删除从 ILoggerFactory.AddApplicationInsights() 调用`Startup.Configure()`方法可避免双日志记录。
2. 重新应用任何筛选规则在代码中的，因为它们不会考虑新的提供程序。 重载 ILoggerFactory.AddApplicationInsights() 花费了最小日志级别或筛选器函数。 使用新的提供程序，筛选是日志记录框架本身的一部分，不通过在 Application Insights 提供程序。 通过提供，因此任何筛选器`ILoggerFactory.AddApplicationInsights()`应删除重载，并且筛选规则应提供以下[这些](#control-logging-level)说明。 如果您使用`appsettings.json`若要筛选日志记录，它将继续使用新的提供程序，因为两者都使用相同的提供程序别名- **ApplicationInsights**。

尽管可以仍使用旧的提供程序 （它现已过时，将仅在主版本更改为 3.xx 中删除），由于以下原因而强烈建议迁移到较新的提供程序。

1. 前面的提供程序不具备的支持[作用域](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)。 在新的提供程序，从作用域的属性将自动添加为自定义属性到收集的遥测。
2. 现在可以更早地在应用程序启动管道捕获日志。 例如  现在可以捕获程序和启动类中的日志。
3. 使用新的提供程序进行筛选在框架级别本身。 筛选的日志到 Application Insights 提供程序可以在完全相同的方式与其他提供程序，包括控制台中，调试，如内置提供程序中，依次类推。 还有可能要应用于多个提供程序相同的筛选器。
4. [建议](https://github.com/aspnet/Announcements/issues/255)ASP.NET Core (及更高版本 2.0) 中的方法，以启用日志记录提供程序是使用 ILoggingBuilder 中的扩展方法`Program.cs`本身。

> [!Note]
> 新的提供程序是适用于应用程序面向`NETSTANDARD2.0`或更高版本。 如果你的应用程序面向较旧的.NET Core 版本，如.NET Core 1.1 或面向.NET Framework，继续使用旧的提供程序。

## <a name="console-application"></a>控制台应用程序

下面的代码演示一个示例控制台应用程序配置为发送`ILogger`到 Application Insights 的跟踪。

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
        // Create DI container.
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

在上面的示例中，独立包`Microsoft.Extensions.Logging.ApplicationInsights`使用。 默认情况下，此配置使用最低要求的 `TelemetryConfiguration` 将数据发送到 Application Insights。 最低要求指使用的通道将为 `InMemoryChannel`，没有采样，也没有标准 TelemetryInitializer。 可为控制台应用程序替代此行为，如以下示例所示。

安装此附加包：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

以下部分演示如何重写默认值`TelemetryConfiguration`通过使用`services.Configure<TelemetryConfiguration>()`方法。 此示例设置`ServerTelemetryChannel`、 采样，并添加一个自定义`ITelemetryInitializer`到`TelemetryConfiguration`。

```csharp
    // Create DI container.
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

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>控制日志记录级别

Asp.Net Core`ILogger`基础结构具有内置的机制来应用[筛选](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)的日志，使用户能够控制发送到每个已注册的提供程序，包括 Application Insights 提供程序的日志。 此筛选可进行配置中 (通常使用`appsettings.json`文件) 或在代码中。 此工具提供的框架本身，并不是特定于 Application Insights 提供程序。

下面提供了将筛选器规则应用于 ApplicationInsightsLoggerProvider 的示例。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>在配置中使用 appsettings.json 创建筛选器规则

对于 ApplicationInsightsLoggerProvider，提供程序别名是`ApplicationInsights`。 如下所示中的部分`appsettings.json`配置日志`Warning`及更高版本所有类别`Error`及更高版本从开头"Microsoft"发送到的类别`ApplicationInsightsLoggerProvider`。

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

### <a name="create-filter-rules-in-code"></a>在代码中创建筛选器规则

下面的代码段配置日志`Warning`及更高版本所有类别`Error`及更高版本从开头 Microsoft 发送到的类别`ApplicationInsightsLoggerProvider`。 此配置是与上面的配置中完成相同`appsettings.json`。

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

*1.什么是旧的和新 ApplicationInsightsLoggerProvider？*

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)附带内置 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider) 使用 ILoggerFactory 启用扩展方法。 此提供程序标记从 2.7.0-beta2 及更高版本已过时，并且将在下一步的主版本更改中完全删除。 [这](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)包本身未过时，并需要它来启用监视的请求、 依赖项等。

* 建议的替代方法是新的独立包[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含一个改进的 ApplicationInsightsLoggerProvider （Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider)，和扩展方法 ILoggerBuilder 上的启用它。

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 及更高版本将依赖于更高版本的包，并启用`ILogger`自动捕获。

*2.我看到一些`ILogger`日志显示在 Application Insights 中两次？*

* 这种重复是如果您有旧版的 （现在已过时） 可能`ApplicationInsightsLoggerProvider`启用通过调用`AddApplicationInsights`上`ILoggerFactory`。 检查是否在`Configure`方法具有以下内容，并将其删除。

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* 如果从 Visual Studio 调试时遇到双日志记录，然后修改使用 Application Insights，如下所示，通过设置来启用的代码`EnableDebugLogger`为 false。 当调试应用程序时，此重复问题并修复才相关。

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3.我已更新到[Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 和我现在看到，用于从记录`ILogger`自动捕获。如何可以关闭此功能完全？*

* 请参阅[这](../../azure-monitor/app/ilogger.md#control-logging-level)部分以了解如何筛选记录一般情况下。 到加以 ApplicationInsightsLoggerProvider 使用`LogLevel.None`它。

  在代码中

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  在配置中

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4.我看到一些`ILogger`日志不让其他人与相同的属性？*

* 应用程序见解捕获并发送`ILogger`使用相同日志`TelemetryConfiguration`用于每个其他遥测。 没有此规则的例外。 默认值`TelemetryConfiguration`未完全设置时日志记录从`Program.cs`或`Startup.cs`本身，以便从这些位置的日志不会默认配置中，并因此不会运行所有`TelemetryInitializer`s 和`TelemetryProcessor`s。

*5.我使用的独立包 Microsoft.Extensions.Logging.ApplicationInsights，并且我想要手动记录一些附加的自定义遥测。我应该如何做的？*

* 使用独立软件包`TelemetryClient`不注入到 DI 容器，因此用户应创建的新实例`TelemetryClient`所使用的记录器提供程序，使用相同的配置，如下所示。 这可确保相同的配置用于所有自定义遥测，以及从 ILogger 捕获。

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
> 请注意，是否包 Microsoft.ApplicationInsights.AspNetCore 包用于启用 Application Insights，则上面的示例应修改以获取`TelemetryClient`直接在构造函数中。 请参阅[这](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions)有关完整示例。


*6.从生成 Application Insights 遥测数据类型`ILogger`日志？ 或可以在何处查看`ILogger`中 Application Insights 的日志？*

* 捕获 ApplicationInsightsLoggerProvider`ILogger`登录并创建了`TraceTelemetry`从它。 如果将异常对象传递给 log （） 方法上 ILogger，然后而不是`TraceTelemetry`、`ExceptionTelemetry`创建。 可以在与任何其他的相同位置中找到这些遥测项`TraceTelemetry`或`ExceptionTelemetry`应用程序见解，包括门户、 分析或 Visual Studio 本地调试器。
如果想要始终发送`TraceTelemetry`，然后使用代码片段```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```。

*7.我没有安装 SDK，并且使用 Azure Web 应用扩展为我的 Asp.Net Core 应用程序启用 Application Insights。如何使用新的提供程序？*

* Application Insights 扩展的 Azure Web 应用中使用旧的提供程序。 筛选规则可以修改在`appsettings.json`为应用程序。 如果你想要充分利用新的提供程序，通过将 SDK 上的 nuget 依赖项使用生成时检测。 扩展名切换为使用新的提供程序时，将更新本文档。

*8.我正在使用的独立包 Microsoft.Extensions.Logging.ApplicationInsights，并启用由调用生成器的 Application Insights 提供程序。AddApplicationInsights("ikey")。是否有从配置获取检测密钥的选项？*


* 修改`Program.cs`和`appsettings.json`，如下所示。

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

从相关部分 `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

仅当使用独立的日志记录提供程序时，上面的代码是必需。 用于正则 Application Insights 监视、 检测密钥自动从加载的配置路径`ApplicationInsights:Instrumentationkey`和`appsettings.json`应看到如下所示。

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

* [在 Asp.Net Core 中的日志记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [在 Application Insights 中的.NET 跟踪日志](../../azure-monitor/app/asp-net-trace-logs.md)
