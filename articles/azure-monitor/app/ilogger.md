---
title: 使用 ILogger 在 Azure Application Insights 中浏览 .NET 跟踪日志
description: 将 Azure 应用程序 Insights ILogger 提供程序与 ASP.NET Core 和控制台应用程序结合使用的示例。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cijothomas
ms.author: cithomas
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0e93e2a98d96ca7f436f20e579ab625341024686
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819476"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider for .NET Core ILogger 日志

ASP.NET Core 支持使用各种内置和第三方日志记录提供程序的日志记录 API。 日志记录是通过在*ILogger*实例上调用**日志（）** 或其变体来完成的。 本文演示如何使用*ApplicationInsightsLoggerProvider*来捕获控制台和 ASP.NET Core 应用程序中的 ILogger 日志。 本文还介绍了 ApplicationInsightsLoggerProvider 如何与其他 Application Insights 遥测数据集成。
有关详细信息，请参阅[登录 ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)。

## <a name="aspnet-core-applications"></a>ASP.NET Core 应用程序

默认情况下，ApplicationInsightsLoggerProvider 在[APPLICATIONINSIGHTS.CONFIG SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 （及更高版本）中通过任一标准方法启用常规 Application Insights 监视时启用：
- 通过对 IWebHostBuilder 调用 **.useapplicationinsights**扩展方法 
- 通过对 IServiceCollection 调用**AddApplicationInsightsTelemetry**扩展方法

ILogger 记录 ApplicationInsightsLoggerProvider 捕获的配置与所收集的任何其他遥测的配置相同。 它们具有相同的一组 TelemetryInitializers 和 TelemetryProcessors，使用同一 TelemetryChannel，并与其他遥测的方式关联和采样。 如果使用 2.7.0-beta3 或更高版本，则无需执行任何操作即可捕获 ILogger 日志。

默认情况下，仅将*警告*或更高版本的 ILogger 日志（来自所有类别）发送到 Application Insights。 但是，您可以[应用筛选器来修改此行为](#control-logging-level)。 从**Program.cs**或**Startup.cs**捕获 ILogger 日志需要执行其他步骤。 （请参阅[在 ASP.NET Core 应用程序中捕获 Startup.cs 和 Program.cs 中的 ILogger 日志](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps)。）

如果使用的是早期版本的 Applicationinsights.config SDK，或者想要只使用 ApplicationInsightsLoggerProvider 而不进行任何其他 Application Insights 监视，请使用以下过程：

1. 安装 NuGet 包：

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. 修改**Program.cs** ，如下所示：

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

步骤2中的代码将配置 `ApplicationInsightsLoggerProvider`。 下面的代码演示一个示例控制器类，该类使用 `ILogger` 发送日志。 Application Insights 捕获日志。

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>在 ASP.NET Core 应用中捕获 Startup.cs 和 Program.cs 中的 ILogger 日志

> [!NOTE]
> 在 ASP.NET Core 3.0 及更高版本中，将无法在 Startup.cs 和 Program.cs 中注入 `ILogger`。 有关详细信息，请参阅 https://github.com/aspnet/Announcements/issues/353 。

新的 ApplicationInsightsLoggerProvider 可以在应用程序启动管道的早期捕获日志。 尽管 ApplicationInsightsLoggerProvider 自动在 Application Insights 中启用（从版本 2.7.0-beta3 开始），但它在管道中的后面没有设置检测密钥。 因此，只会捕获**控制器**其他类中的日志。 若要捕获每个以**Program.cs**和**Startup.cs**本身开头的日志，必须显式启用 ApplicationInsightsLoggerProvider 的检测密钥。 此外，当你从**Program.cs**或**Startup.cs**本身记录时， *TelemetryConfiguration*未完全设置。 因此，这些日志将具有使用 InMemoryChannel 的最小配置，不使用采样，并且没有标准遥测初始值设定项或处理器。

下面的示例演示了**Program.cs**和**Startup.cs**的这一功能。

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

2\.7.0-beta2 之前的 Applicationinsights.config SDK 版本支持现已过时的日志记录提供程序。 此提供程序通过 ILoggerFactory 的**AddApplicationInsights （）** 扩展方法启用。 建议迁移到新提供程序，这涉及两个步骤：

1. 从 ILoggerFactory **（）** 方法中删除*AddApplicationInsights （）* 调用，以避免双重日志记录。
2. 重新应用代码中的任何筛选规则，因为新提供程序不会遵守这些规则。 *ILoggerFactory. AddApplicationInsights （）* 的重载采用最少的 LogLevel 或 filter 函数。 对于新的提供程序，筛选是日志记录框架自身的一部分。 Application Insights 提供程序不会执行此操作。 因此，应删除通过*ILoggerFactory （）* 重载提供的所有筛选器。 并且应按照[控件日志记录级别](#control-logging-level)说明提供筛选规则。 如果使用*appsettings*筛选日志记录，它将继续使用新的提供程序，因为这两种方法都使用相同的提供程序别名*applicationinsights.config*。

你仍可以使用旧的提供程序。 （仅在主版本更改为3时删除。*xx*.）但建议迁移到新的提供程序，原因如下：

- 以前的提供程序缺少对[日志范围](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)的支持。 在新提供程序中，作用域中的属性会自动作为自定义属性添加到收集的遥测数据。
- 现在，可以在应用程序启动管道中更早捕获日志。 现在可以捕获**程序**和**启动**类中的日志。
- 对于新的提供程序，筛选是在框架级别本身进行的。 可以按照与其他提供程序（包括控制台、调试等）中的内置提供程序相同的方式，将日志筛选到 Application Insights 提供程序。 你还可以将相同的筛选器应用于多个提供程序。
- 在 ASP.NET Core （2.0 及更高版本）中，[启用日志记录提供程序](https://github.com/aspnet/Announcements/issues/255)的建议方法是在**Program.cs**本身中使用 ILoggingBuilder 上的扩展方法。

> [!Note]
> 新的提供程序适用于面向 NETSTANDARD 2.0 或更高版本的应用程序。 如果你的应用程序以较旧的 .NET Core 版本（如 .NET Core 1.1）为目标，或者它以 .NET Framework 为目标，请继续使用旧的提供程序。

## <a name="console-application"></a>控制台应用程序

> [!NOTE]
> 有一个名为 Applicationinsights.config 的新 beta Application Insights SDK [WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) ，可用于为任何控制台应用程序启用 Application Insights （ILogger 和其他 Application Insights 遥测）。 建议在[此处](../../azure-monitor/app/worker-service.md)使用此包和相关说明。
释放此新包的稳定版本后，将弃用下面的示例。

下面的代码演示一个示例控制台应用程序，该应用程序配置为将 ILogger 跟踪发送到 Application Insights。

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

此示例使用 `Microsoft.Extensions.Logging.ApplicationInsights`的独立包。 默认情况下，此配置使用 "最小值" TelemetryConfiguration 将数据发送到 Application Insights。 最小值表示 InMemoryChannel 是使用的通道。 没有采样，也没有标准的 TelemetryInitializers。 对于控制台应用程序，可以重写此行为，如以下示例所示。

安装此附加包：

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

以下部分说明如何使用服务重写默认 TelemetryConfiguration **。配置\<TelemetryConfiguration > （）** 方法。 此示例将设置 `ServerTelemetryChannel` 和采样。 它将自定义 ITelemetryInitializer 添加到 TelemetryConfiguration 中。

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

## <a name="control-logging-level"></a>控件日志记录级别

ASP.NET Core *ILogger*具有用于应用[日志筛选](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering)的内置机制。 这样，你就可以控制发送到每个注册的提供程序（包括 Application Insights 提供程序）的日志。 可以在配置（通常通过使用*appsettings*文件）或代码中完成筛选。 此功能由框架本身提供。 它并不特定于 Application Insights 提供程序。

下面的示例将筛选规则应用于 ApplicationInsightsLoggerProvider。

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>在配置中创建具有 appsettings 的筛选规则

对于 ApplicationInsightsLoggerProvider，提供程序别名 `ApplicationInsights`。 下面的*appsettings*部分将从以 "Microsoft" 开头的类别*中的所有*类别和*错误*以及更高的类别配置日志，以发送到 `ApplicationInsightsLoggerProvider`。

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

下面的代码段从所有类别、从以 "Microsoft"*开头的类别*（将发送到 `ApplicationInsightsLoggerProvider`）配置日志中的*警告*和更高版本。 此配置与*appsettings*的上一节中的配置相同。

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>ApplicationInsightsLoggerProvider 的旧版本和新版本有哪些？

[APPLICATIONINSIGHTS.CONFIG SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)包含内置 ApplicationInsightsLoggerProvider （applicationinsights.config），该内置通过 ApplicationInsightsLoggerProvider 启用了这一功能。扩展方法。 此提供程序被标记为 "已过时，版本 2.7.0-beta2"。 在下一次主版本更改时将完全删除它。 [Applicationinsights.config. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)包本身未过时。 需要启用对请求、依赖项等的监视。

建议的替代项为新的独立包[applicationinsights.config](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)，其中包含改进的 ApplicationInsightsLoggerProvider （Applicationinsights.config. ApplicationInsightsLoggerProvider）和 ILoggerBuilder 上的扩展方法，用于启用该功能。

[APPLICATIONINSIGHTS.CONFIG SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3 依赖于新包，并自动启用 ILogger 捕获。

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>为什么某些 ILogger 日志在 Application Insights 中显示了两次？

如果已通过调用 `ILoggerFactory`上的 `AddApplicationInsights` 启用了旧版本的 ApplicationInsightsLoggerProvider，则可能会发生重复。 检查**配置**方法是否包含以下内容，并将其删除：

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

如果从 Visual Studio 中进行调试时遇到双重日志记录，请在启用 Application Insights 的代码中将 `EnableDebugLogger` 设置为*false* ，如下所示。 此重复和修复仅在调试应用程序时相关。

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>我已更新到[APPLICATIONINSIGHTS.CONFIG SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)版本 2.7.0-beta3，并自动捕获来自 ILogger 的日志。 如何实现完全关闭此功能？

请参阅[控件日志记录级别](../../azure-monitor/app/ilogger.md#control-logging-level)部分，了解如何筛选日志。 若要关闭 ApplicationInsightsLoggerProvider，请使用 `LogLevel.None`：

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

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>为什么某些 ILogger 日志与其他日志没有相同的属性？

Application Insights 通过使用用于每个其他遥测的相同 TelemetryConfiguration 来捕获和发送 ILogger 日志。 但有一个例外。 默认情况下，当你从**Program.cs**或**Startup.cs**记录时，TelemetryConfiguration 未完全设置。 来自这些位置的日志没有默认配置，因此它们不会运行所有 TelemetryInitializers 和 TelemetryProcessors。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>我使用的是独立包 Applicationinsights.config，并希望手动记录某些其他自定义遥测。 我该怎么办？

当你使用独立包时，`TelemetryClient` 不会注入到 DI 容器，因此你需要创建 `TelemetryClient` 的新实例，并使用记录器提供程序使用的相同配置，如下面的代码所示。 这可确保对所有自定义遥测以及来自 ILogger 的遥测使用相同的配置。

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
> 如果使用 Applicationinsights.config AspNetCore 包来启用 Application Insights，请修改此代码以直接在构造函数中 `TelemetryClient`。 有关示例，请参阅[此常见问题](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions)。


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>ILogger 日志产生哪种 Application Insights 遥测类型？ 在哪里可以查看 Application Insights 中的 ILogger 日志？

ApplicationInsightsLoggerProvider 捕获 ILogger 日志并从中创建 TraceTelemetry。 如果在 ILogger 上将异常对象传递到**Log （）** 方法，则将创建*ExceptionTelemetry*而不是 TraceTelemetry。 可以在与任何其他 TraceTelemetry 或 Application Insights ExceptionTelemetry （包括门户、analytics 或 Visual Studio 本地调试器）相同的位置找到这些遥测项。

如果希望始终发送 TraceTelemetry，请使用此代码片段： ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>我未安装 SDK，并使用 Azure Web 应用扩展为 ASP.NET Core 应用程序启用 Application Insights。 如何实现使用新的提供程序吗？ 

Azure Web Apps 中的 Application Insights 扩展使用旧的提供程序。 可以在应用程序的*appsettings*文件中修改筛选规则。 若要利用新的提供程序，请通过对 SDK 进行 NuGet 依赖关系来使用生成时检测。 当扩展切换为使用新的提供程序时，将更新本文。

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>我使用的是独立包 Applicationinsights.config，并通过调用生成器启用 Application Insights 提供程序 **。AddApplicationInsights （"ikey"）** 。 是否有从配置中获取检测密钥的选项？


修改 Program.cs 和 appsettings，如下所示：

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

   `appsettings.json`相关部分：

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

仅当使用独立日志记录提供程序时，才需要此代码。 对于常规 Application Insights 监视，会自动从配置路径*applicationinsights.config： Instrumentationkey*加载检测密钥。 Appsettings 应如下所示：

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

* [登录 ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights 中的 .NET 跟踪日志](../../azure-monitor/app/asp-net-trace-logs.md)
