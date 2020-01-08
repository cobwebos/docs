---
title: 如何使用 Web 作业 SDK
description: 详细了解如何为 WebJobs SDK 编写代码。 创建用于访问 Azure 和第三方服务中的数据的事件驱动后台处理作业。
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 2d9de5e7294fdca7514989ba009e9dee8985a084
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421968"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 进行事件驱动的后台处理

本文提供了有关如何使用 Azure WebJobs SDK 的指导。 若要立即开始处理 Web 作业，请参阅[AZURE WEBJOBS SDK 入门，获取事件驱动的后台处理](webjobs-sdk-get-started.md)。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

这是版本3之间的主要区别。*x*和版本2。*x* ：

* 版本3。*x*增加了对 .net Core 的支持。
* 版本3中的。*x*，需要显式安装 WEB 作业 SDK 所需的存储绑定扩展。 版本2。*x*，存储绑定包含在 SDK 中。
* 适用于 .NET Core 的 Visual Studio 工具（3.*x*）项目与 .NET Framework 的工具不同（2）。*x*）项目。 若要了解详细信息，请参阅[使用 Visual Studio 开发和部署 Web 作业-Azure App Service](webjobs-dotnet-deploy-vs.md)。

如果可能，将提供两个版本3的示例。*x*和版本2。*x*。

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md)是在 WEB 作业 SDK 上构建的，本文提供了一些主题的链接，这些链接指向 Azure Functions 文档。 请注意函数和 Web 作业 SDK 之间的这些差异：
> * 版本 2 Azure Functions。*x*对应于 WEB 作业 SDK 版本3。*x*和 Azure Functions 1。*x*对应于 WEB 作业 SDK 2。*x*。 源代码存储库使用 Web 作业 SDK 编号。
> * Azure Functions C#类库的示例代码类似于 WEB 作业 sdk 代码，但您不需要使用 WEB 作业 sdk 项目中的 `FunctionName` 属性。
> * 某些绑定类型仅在函数（如 HTTP （Webhook）和事件网格（基于 HTTP））中受支持。
>
> 有关详细信息，请参阅 [WebJobs SDK 和 Azure Functions 的比较](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

## <a name="webjobs-host"></a>WebJobs 主机

主机是函数的运行时容器。  它会侦听触发器并调用函数。 版本3中的。*x*，主机是 `IHost`的实现。 版本2。*x*使用 `JobHost` 对象。 在代码中创建主机实例，并编写代码来自定义其行为。

这是直接使用 Web 作业 SDK 并通过 Azure Functions 间接使用它的主要区别。 在 Azure Functions 中，服务控制主机，你不能通过编写代码来自定义该主机。 Azure Functions 允许你通过主机 json 文件中的设置自定义主机行为。 这些设置是字符串，而不是代码，这会限制可以执行的自定义类型。

### <a name="host-connection-strings"></a>主机连接字符串

当你在 Azure 中运行时，Web 作业 SDK 在本地或 Web 作业的环境中查找 Azure 存储和 Azure 服务总线连接字符串。 默认情况下，需要一个名为 `AzureWebJobsStorage` 的存储连接字符串设置。  

版本2。*x*的 SDK 允许将自己的名称用于这些连接字符串或将其存储在其他位置。 您可以使用[`JobHostConfiguration`]在代码中设置名称，如下所示：

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

因为版本3。*x*使用默认的 .net Core 配置 api，没有用于更改连接字符串名称的 API。

### <a name="host-development-settings"></a>主机开发设置

可在开发模式下运行主机，提高本地开发效率。 下面是在开发模式下运行时更改的一些设置：

| 属性 | 开发设置 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`：最大化日志输出。 |
| `Queues.MaxPollingInterval`  | 使用较小的值可确保立即触发队列方法。  |
| `Singleton.ListenerLockPeriod` | 使用 15 秒值有助于实现快速迭代开发。 |

启用开发模式的过程取决于 SDK 版本。 

#### <a name="version-3x"></a>版本3。*x*

版本3。*x*使用标准 ASP.NET Core api。 对[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)实例调用[`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment)方法。 传递名为 `development`的字符串，如以下示例中所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本2。*x*

`JobHostConfiguration` 类具有 `UseDevelopmentSettings` 方法，该方法支持开发模式。  以下示例演示如何使用开发设置。 若要使 `config.IsDevelopment` 在本地运行时 `true` 返回，请使用 `Development`值设置名为 `AzureWebJobsEnv` 的本地环境变量。

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>管理并发连接（版本2。*x*）

版本3中的。*x*，连接限制默认为无限连接。 如果出于某种原因您需要更改此限制，则可以使用[`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler)类的[`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver)属性。

版本2。*x*使用[ServicePointManager. servicepointmanager.defaultconnectionlimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API 控制主机的并发连接数。 在2中。*x*在启动 web 作业主机之前，应将此值从默认值2增加。

通过使用 `HttpClient` 流过 `ServicePointManager`从函数发出的所有传出 HTTP 请求。 达到 `DefaultConnectionLimit`中设置的值后，`ServicePointManager` 会在发送请求之前开始排队请求。 假设 `DefaultConnectionLimit` 设置为 2，并且代码发出了 1,000 个 HTTP 请求。 最初，只允许 2 个请求传入 OS。 其他 998 个请求将会排队，直到有可用的空间。 这意味着 `HttpClient` 可能会超时，因为它似乎已发出请求，但该请求从来不会由 OS 发送到目标服务器。 因此，可能会出现看似不合理的行为：本地 `HttpClient` 花费了 10 秒来完成请求，但服务在 200 毫秒内就返回了每个请求。 

ASP.NET 应用程序的默认值为 `Int32.MaxValue`，这对于在基本或更高的应用服务计划中运行的 Webjob 非常有效。 Web 作业通常需要 Always On 设置，且仅在基本和更高的应用服务计划中受支持。

如果 WebJob 在“免费”或“共享”应用服务计划中运行，则应用程序会受到应用服务沙盒的限制：当前的[连接限制为 300 个](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 `ServicePointManager`中存在未绑定的连接限制，则更有可能会达到沙盒连接阈值并关闭站点。 在这种情况下，将 `DefaultConnectionLimit` 设置为更小的值（例如 50 或 100）可以防止此问题发生，同时仍可保持足够的吞吐量。

必须在发出任何 HTTP 请求之前配置该设置。 出于此原因，Web 作业主机不应自动调整设置。 可能会在主机启动之前出现 HTTP 请求，这可能会导致意外的行为。 最好的方法是在初始化 `JobHost`之前立即在 `Main` 方法中设置值，如下所示：

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>触发器

函数必须是公共方法，并且必须具有一个触发器特性或[`NoAutomaticTrigger`](#manual-triggers)特性。

### <a name="automatic-triggers"></a>自动触发器

自动触发器调用函数来响应事件。 请考虑由添加到 Azure 队列存储的消息触发的函数示例。 它通过从 Azure Blob 存储读取 blob 来做出响应：

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` 特性告诉运行时在 `myqueue-items` 队列中出现队列消息时调用函数。 `Blob` 特性告知运行时使用队列消息读取*示例工作项*容器中的 blob。 队列消息的内容（传入到 `myQueueItem` 参数中的函数）是 blob 的名称。

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>手动触发器

若要手动触发某个函数，请使用 `NoAutomaticTrigger` 属性，如下所示：

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

手动触发函数的过程取决于 SDK 版本。

#### <a name="version-3x"></a>版本3。*x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>版本2。*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>输入和输出绑定

通过输入绑定能够以声明方式将 Azure 或第三方服务中的数据提供给代码使用。 输出绑定提供更新数据的方式。 [入门](webjobs-sdk-get-started.md)文章显示了每个示例。

您可以通过将特性应用于方法返回值，对输出绑定使用方法返回值。 请参阅[使用 Azure 函数返回值](../azure-functions/functions-bindings-return-value.md)中的示例。

## <a name="binding-types"></a>绑定类型

安装和管理绑定类型的过程取决于您使用的是版本3。*x*或版本2。*x*的 SDK。 您可以在该绑定类型的 Azure Functions[参考文章](#binding-reference-information)的 "包" 部分中找到特定绑定类型的包。 异常是文件触发器和绑定（对于本地文件系统），Azure Functions 不支持此方法。

#### <a name="version-3x"></a>版本3。*x*

版本3中的。*x*，存储绑定包含在 `Microsoft.Azure.WebJobs.Extensions.Storage` 包中。 在 `ConfigureWebJobs` 方法中调用 `AddAzureStorage` 扩展方法，如下所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

若要使用其他触发器和绑定类型，请安装包含这些类型的 NuGet 包，并调用在扩展中实现的 `Add<binding>` 扩展方法。 例如，如果想要使用 Azure Cosmos DB 绑定，请安装 `Microsoft.Azure.WebJobs.Extensions.CosmosDB` 并调用 `AddCosmosDB`，如下所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

若要使用属于核心服务的 Timer 触发器或 Files 绑定，请分别调用 `AddTimers` 或 `AddFiles` 扩展方法。

#### <a name="version-2x"></a>版本2。*x*

版本2中包含这些触发器和绑定类型。`Microsoft.Azure.WebJobs` 包的*x* ：

* Blob 存储
* 队列存储
* 表存储

若要使用其他触发器和绑定类型，请安装包含这些类型的 NuGet 包，并对 `JobHostConfiguration` 对象调用 `Use<binding>` 方法。 例如，如果要使用计时器触发器，请在 `Main` 方法中安装 `Microsoft.Azure.WebJobs.Extensions` 并调用 `UseTimers`，如下所示：

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

要使用 Files 绑定，请安装 `Microsoft.Azure.WebJobs.Extensions` 并调用 `UseFiles`。

### <a name="executioncontext"></a>ExecutionContext

使用 WebJobs，可绑定到 [`ExecutionContext`]。 使用此绑定，可以访问在函数签名中作为参数的 [`ExecutionContext`]。 例如，以下代码使用上下文对象访问调用 ID，使用该 ID 可以关联给定函数调用生成的所有日志。  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

绑定到[`ExecutionContext`]的过程取决于 SDK 版本。

#### <a name="version-3x"></a>版本3。*x*

在 `ConfigureWebJobs` 方法中调用 `AddExecutionContextBinding` 扩展方法，如下所示：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本2。*x*

前面所述的 `Microsoft.Azure.WebJobs.Extensions` 包还提供了一个可以通过调用 `UseCore` 方法注册的特殊绑定类型。 此绑定使你可以在函数签名中定义一个[`ExecutionContext`]参数，如下所示：

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>绑定配置

您可以配置某些触发器和绑定的行为。 配置这些配置的过程取决于 SDK 版本。

* **版本3。*x*：** 在 `ConfigureWebJobs`中调用 `Add<Binding>` 方法时设置配置。
* **版本2。*x*：** 通过在传入到 `JobHost`的配置对象中设置属性来设置配置。

这些特定于绑定的设置等效于 Azure Functions 中的[host json 项目文件](../azure-functions/functions-host-json.md)中的设置。

可以配置以下绑定：

* [Azure CosmosDB 触发器](#azure-cosmosdb-trigger-configuration-version-3x)
* [事件中心触发器](#event-hubs-trigger-configuration-version-3x)
* [队列存储触发器](#queue-storage-trigger-configuration)
* [SendGrid 绑定](#sendgrid-binding-configuration-version-3x)
* [服务总线触发器](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 触发器配置（版本3）。*x*）

此示例演示如何配置 Azure Cosmos DB 触发器：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

有关更多详细信息，请参阅[Azure CosmosDB 绑定](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)一文。

### <a name="event-hubs-trigger-configuration-version-3x"></a>事件中心触发配置（版本3）。*x*）

此示例演示如何配置事件中心触发器：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

有关更多详细信息，请参阅[事件中心绑定](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings)一文。

### <a name="queue-storage-trigger-configuration"></a>队列存储触发器配置

这些示例演示如何配置队列存储触发器：

#### <a name="version-3x"></a>版本3。*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

有关更多详细信息，请参阅[队列存储绑定](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings)一文。

#### <a name="version-2x"></a>版本2。*x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

有关更多详细信息，请参阅[host v1. x 引用](../azure-functions/functions-host-json-v1.md#queues)。

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 绑定配置（版本3）。*x*）

此示例演示如何配置 SendGrid 输出绑定：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

有关更多详细信息，请参阅[SendGrid 绑定](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)一文。

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus 触发器配置（版本3）。*x*）

此示例演示如何配置服务总线触发器：

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

有关更多详细信息，请参阅[服务总线绑定](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)一文。

### <a name="configuration-for-other-bindings"></a>其他绑定的配置

某些触发器和绑定类型定义它们自己的自定义配置类型。 例如，文件触发器允许指定要监视的根路径，如以下示例中所示：

#### <a name="version-3x"></a>版本3。*x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本2。*x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>绑定表达式

在特性构造函数参数中，可以使用解析为来自各种源的值的表达式。 例如，在以下代码中，`BlobTrigger` 特性的路径创建名为 `filename` 表达式。 用于输出绑定时，`filename` 解析为触发 Blob 的名称。

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

有关绑定表达式的详细信息，请参阅 Azure Functions 文档中的[绑定表达式和模式](../azure-functions/functions-bindings-expressions-patterns.md)。

### <a name="custom-binding-expressions"></a>自定义绑定表达式

有时，您需要在代码中指定队列名称、blob 名称、容器或表名称，而不是对其进行硬编码。 例如，可能要在配置文件或环境变量中指定 `QueueTrigger` 特性的队列名称。

可以通过将中的 `NameResolver` 对象传递到 `JobHostConfiguration` 对象来实现此操作。 在触发器或绑定特性构造函数参数中包含占位符，`NameResolver` 代码将提供用于取代这些占位符的实际值。 可以通过将占位符用百分比（%）来确定占位符符号，如下所示：

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此代码允许在测试环境中使用名为 `logqueuetest` 的队列，并在生产环境中使用名为 `logqueueprod` 的队列。 在 `appSettings` 集合中指定条目名称，而不是硬编码的队列名称。

如果未提供自定义的默认 `NameResolver`，则会生效。 默认设置从应用设置或环境变量中获取值。

`NameResolver` 类从 `appSettings`中获取队列名称，如下所示：

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>版本3。*x*

使用依赖关系注入来配置解析程序。 这些示例需要下列 `using` 语句：

```cs
using Microsoft.Extensions.DependencyInjection;
```

可以通过对[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)调用[`ConfigureServices`]扩展方法来添加冲突解决程序，如以下示例中所示：

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本2。*x*

将 `NameResolver` 类传递到 `JobHost` 对象，如下所示：

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions 实现 `INameResolver` 以从应用设置中获取值，如以下示例中所示。 直接使用 WebJobs SDK 时，可以编写一个自定义实现，用于从偏好的任何源获取占位符替代值。

## <a name="binding-at-runtime"></a>在运行时绑定

如果需要在使用绑定属性（例如 `Queue`、`Blob`或 `Table`）之前在函数中执行某些操作，则可以使用 `IBinder` 接口。

下述示例使用输入队列消息，并在输出队列中创建具有相同内容的新消息。 输出队列名称由函数正文中的代码设置。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

有关详细信息，请参阅 Azure Functions 文档中的[运行时绑定](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime)。

## <a name="binding-reference-information"></a>绑定参考信息

Azure Functions 文档提供了有关每个绑定类型的参考信息。 你将在每个绑定参考文章中找到以下信息。 （此示例基于存储队列。）

* [包](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x)。 需要安装以包含对 Web 作业 SDK 项目中的绑定的支持的包。
* [示例](../azure-functions/functions-bindings-storage-queue.md#trigger---example)。 代码示例。 C#类库示例适用于 WEB 作业 SDK。 只需省略 `FunctionName` 特性。
* [特性](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes)。 要用于绑定类型的特性。
* [配置](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration)。 特性属性和构造函数参数的说明。
* [用法](../azure-functions/functions-bindings-storage-queue.md#trigger---usage)。 可以绑定到的类型，以及绑定的工作方式的相关信息。 例如：轮询算法、有害队列处理。
  
有关绑定引用项目的列表，请参阅 Azure Functions 的[触发器和绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)一文中的 "支持的绑定"。 在该列表中，仅 Azure Functions （而不是 Web 作业 SDK）支持 HTTP、Webhook 和事件网格绑定。

## <a name="disable-attribute"></a>Disable 特性 

利用[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)特性，可以控制是否可以触发函数。 

在以下示例中，如果应用设置 `Disable_TestJob` 的值为 `1` 或 `True` （不区分大小写），则该函数将不会运行。 在这种情况下，运行时将创建日志消息“函数 'Functions.TestJob' 已禁用”。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

在 Azure 门户中更改应用程序设置值时，Web 作业将重新启动以选取新的设置。

可以在参数、方法或类级别声明该特性。 设置名称还可以包含绑定表达式。

## <a name="timeout-attribute"></a>Timeout 特性

如果某个函数在指定的时间内未完成，则该[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)特性将导致该函数被取消。 在下面的示例中，函数将在一天内运行，而不会有 Timeout 属性。 Timeout 导致函数在15秒后被取消。

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

可以在类或方法级别应用 Timeout 属性，还可以使用 `JobHostConfiguration.FunctionTimeout`指定全局超时值。 类级别或方法级别的超时将覆盖全局超时。

## <a name="singleton-attribute"></a>Singleton 特性

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)属性可确保仅运行一个函数实例，即使存在主机 web 应用的多个实例时也是如此。 它通过使用[分布式锁定](#viewing-lease-blobs)来实现此功能。

在此示例中，只有 `ProcessImage` 函数的单个实例在给定时间运行：

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

某些触发器为并发管理提供内置支持：

* **QueueTrigger**。 将 `JobHostConfiguration.Queues.BatchSize` 设置为 `1`。
* **ServiceBusTrigger**。 将 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 设置为 `1`。
* **FileTrigger**。 将 `FileProcessor.MaxDegreeOfParallelism` 设置为 `1`。

可以使用这些设置来确保函数在单个实例上作为单一实例运行。 若要确保当 web 应用横向扩展到多个实例时仅运行函数的单个实例，请在函数（`[Singleton(Mode = SingletonMode.Listener)]`）上应用侦听器级单独锁。 当 JobHost 启动时，将获取侦听器锁。 如果三个横向扩展的实例全部同时启动，只有其中的一个实例获取该锁，并且只有一个侦听器启动。

### <a name="scope-values"></a>范围值

可以在单一实例上指定*作用域表达式/值*。 表达式/值可确保序列化指定范围内的函数的所有执行。 以这种方式实现更细粒度的锁定可能会导致函数实现某些级别的并行，同时根据你的要求对其他调用进行序列化。 例如，在下面的代码中，范围表达式绑定到传入消息的 `Region` 值。 如果队列中包含三条分别为 "东部"、"东部"、"美国西部" 的消息，则具有区域 "东部" 的邮件会以串行方式运行，而与 "西部" 区域中的邮件将与 "东部" 中的

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

锁定的默认作用域为 `SingletonScope.Function`，这意味着锁作用域（blob 租约路径）绑定到完全限定的函数名。 若要在函数间锁定，请指定 `SingletonScope.Host`，并在所有不希望同时运行的函数中使用相同的作用域 ID 名称。 在以下示例中，每次只会运行 `AddItem` 或 `RemoveItem` 的一个实例：

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>查看租约 Blob

WebJobs SDK 在幕后使用 [Azure Blob 租约](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)来实现分布式锁定。 可以在 "锁定" 路径下的 `AzureWebJobsStorage` 存储帐户的 `azure-webjobs-host` 容器中找到单独使用的租约 blob。 例如，前面演示的第一个 `ProcessImage` 示例的租约 Blob 路径可能是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路径包含 JobHost ID，在本例中为 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>异步函数

有关如何编写 async 函数的信息，请参阅[Azure Functions 文档](../azure-functions/functions-dotnet-class-library.md#async)。

## <a name="cancellation-tokens"></a>取消令牌

有关如何处理取消令牌的信息，请参阅有关[取消令牌和正常关闭](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)的 Azure Functions 文档。

## <a name="multiple-instances"></a>多个实例

如果 Web 应用在多个实例上运行，则会有一个连续的 WebJob 在每个实例上运行，并侦听触发器和调用函数。 各种触发器绑定旨在以协作方式有效分担各个实例上的工作，以便横向扩展到多个实例后可以处理更多的负载。

虽然某些触发器可能会导致双重处理，但队列和 blob 存储触发器会自动阻止函数多次处理队列消息或 blob。 有关详细信息，请参阅 Azure Functions 文档中[的 "设计完全相同的输入](../azure-functions/functions-idempotent.md)"。

计时器触发器会自动确保只会运行计时器的一个实例，因此，在给定的计划时间，不会运行多个函数实例。

如果要确保即使有多个主机 web 应用的实例，也只有一个函数实例运行，可以使用[`Singleton`](#singleton-attribute)特性。

## <a name="filters"></a>筛选器

通过函数筛选器（预览版）可以使用自己的逻辑自定义 WebJobs 执行管道。 筛选器类似于[ASP.NET Core 筛选](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)器。 可以将它们实现为应用于函数或类的声明性特性。 有关详细信息，请参阅[函数筛选器](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)。

## <a name="logging-and-monitoring"></a>日志记录和监视

建议为 ASP.NET 开发的日志记录框架。 [入门](webjobs-sdk-get-started.md)文章介绍了如何使用它。 

### <a name="log-filtering"></a>日志筛选

`ILogger` 实例创建的每个日志都包含关联的 `Category` 和 `Level`。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)是枚举，并且整数代码指示相对重要性：

|LogLevel    |代码|
|------------|---|
|跟踪       | 0 |
|调试       | 第 |
|信息 | 2 |
|警告     | 3 |
|错误       | 4 |
|严重    | 5 |
|无        | 6 |

您可以单独筛选每个类别的特定[`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)。 例如，你可能想要查看有关 Blob 触发器处理的所有日志，但对于其他任何操作，只想查看 `Error` 和更高级别的日志。

#### <a name="version-3x"></a>版本3。*x*

版本3。*x* SDK 依赖于 .net Core 中内置的筛选。 使用 `LogCategories` 类，可以为特定函数、触发器或用户定义类别。 它还为特定主机状态（如 `Startup` 和 `Results`）定义筛选器。 这使您可以微调日志记录输出。 如果在定义类别中未找到任何匹配项，筛选器在决定是否筛选消息时会回退到 `Default` 值。

`LogCategories` 需要以下 using 语句：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

下面的示例构造一个筛选器，默认情况下，筛选 `Warning` 级别的所有日志。 `Function` 和 `results` 类别（等效于版本2中的 `Host.Results`。*x*）在 `Error` 级别进行筛选。 筛选器将当前类别与 `LogCategories` 实例中所有已注册的级别进行比较，并选择最长匹配项。 这意味着为 `Host.Triggers` 注册的 `Debug` 级别与 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`匹配。 这样，便可以控制更广泛的类别，而无需添加每个类别。

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>版本2。*x*

版本2。*x* ，使用 `LogCategoryFilter` 类来控制筛选。 `LogCategoryFilter` 的 `Default` 属性的初始值为 `Information`，这意味着，将记录 `Information`、`Warning`、`Error`或 `Critical` 级别的所有消息，但 `Debug` 或 `Trace` 级别的所有消息都将被筛选掉。

与版本3中 `LogCategories` 相同。*x*，`CategoryLevels` 属性允许你指定特定类别的日志级别，以便可以微调日志记录输出。 如果在 `CategoryLevels` 字典中未找到任何匹配项，筛选器在决定是否筛选消息时会回退到 `Default` 值。

以下示例构造的筛选器默认会筛选 `Warning` 级别的所有日志。 `Function` 和 `Host.Results` 类别在 `Error` 级别进行筛选。 `LogCategoryFilter` 将当前类别与所有已注册的 `CategoryLevels` 进行比较，并选择最长匹配项。 因此，为 `Host.Triggers` 注册的 `Debug` 级别将匹配 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 这样，便可以控制更广泛的类别，而无需添加每个类别。

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Application Insights 的自定义遥测

为[Application Insights](../azure-monitor/app/app-insights-overview.md)实现自定义遥测的过程取决于 SDK 版本。 要了解如何配置 Application Insights，请参阅[添加 Application Insights 日志记录](webjobs-sdk-get-started.md#add-application-insights-logging)。

#### <a name="version-3x"></a>版本3。*x*

因为版本3。*x* WEB 作业 SDK 依赖于 .net Core 泛型主机，不再提供自定义遥测工厂。 但你可以通过使用依赖关系注入将自定义遥测添加到管道。 本部分中的示例要求使用下列 `using` 语句：

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

使用以下 [`ITelemetryInitializer`] 的自定义实现，可向默认的 [`TelemetryConfiguration`] 添加自己的 [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry)。

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

在生成器中调用 [`ConfigureServices`]，以将自定义 [`ITelemetryInitializer`] 添加到管道。

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

构造 [`TelemetryConfiguration`] 时，将添加所有已注册类型的 [`ITelemetryInitializer`]。 若要了解详细信息，请参阅[自定义事件和指标的 APPLICATION INSIGHTS API](../azure-monitor/app/api-custom-events-metrics.md)。

版本3中的。*x*，你不再需要在主机停止时刷新[`TelemetryClient`] 。 .NET Core 依赖关系注入系统将自动释放已注册 `ApplicationInsightsLoggerProvider`，可刷新 [`TelemetryClient`]。

#### <a name="version-2x"></a>版本2。*x*

版本2。*x*，WEB 作业 SDK 的 Application Insights 提供程序内部创建的[`TelemetryClient`]使用[`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)。 当 Application Insights 终结点时不可用或限制传入请求时，此通道会[在 Web 应用的文件系统中保存请求，并稍后提交这些请求](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

[`TelemetryClient`] 是实现 `ITelemetryClientFactory` 的类创建的。 默认为 [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)。

若要修改 Application Insights 管道的任何组成部分，可以提供自己的 `ITelemetryClientFactory`，而主机会使用你的类来构造 [`TelemetryClient`]。 例如，以下代码将重写 `DefaultTelemetryClientFactory` 修改 `ServerTelemetryChannel`的属性：

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings` 对象配置[自适应采样](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)。 这意味着，在某些大容量的情况下，Application Insights 会将选定的遥测数据子集发送到服务器。

创建遥测工厂后，将其传递到 Application Insights 日志记录提供程序：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> 后续步骤

本文提供的代码片段演示如何处理用于处理 Web 作业 SDK 的常见方案。 有关完整示例，请参阅 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)。

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
