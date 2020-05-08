---
title: 如何使用 WebJobs SDK
description: 详细了解如何为 WebJobs SDK 编写代码。 创建事件驱动的后台处理作业，用于访问 Azure 和第三方服务中的数据。
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: a046791b8c50577c1921764b06bac5d88780194d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734988"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 Azure WebJobs SDK 进行事件驱动的后台处理

本文提供有关如何使用 Azure WebJobs SDK 的指导。 若要立即开始使用 WebJobs，请参阅[用于事件驱动式后台处理的 Azure WebJobs SDK 入门](webjobs-sdk-get-started.md)。 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 版本

下面是 WebJobs SDK 版本 3.*x* 与版本 2.*x* 之间的重要差别：

* 版本 3.*x* 中添加了对 .NET Core 的支持。
* 在版本 3.*x* 中，需要显式安装 WebJobs SDK 所需的存储绑定扩展。 在版本 2.*x* 中，存储绑定包含在 SDK 中。
* 用于 .NET Core 的 Visual Studio 工具 (3.*x*) 项目不同于 .NET Framework 工具 (2.*x*) 项目。 有关详细信息，请参阅[使用 Visual Studio 开发和部署 WebJob - Azure 应用服务](webjobs-dotnet-deploy-vs.md)。

本文会尽量提供同时适用于版本 3.*x* 和版本 2.*x* 的示例。

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) 是基于 WebJobs SDK 构建的，本文提供了适用于某些主题的 Azure Functions 文档的链接。 注意 Functions 与 WebJobs SDK 之间的以下差异：
> * Azure Functions 版本 2.*x* 对应于 WebJobs SDK 版本 3.*x*，Azure Functions 1.*x* 对应于 WebJobs SDK 2.*x*。 源代码存储库使用 WebJobs SDK 编号。
> * Azure Functions C# 类库的示例代码类似于 WebJobs SDK 代码，不过，在 WebJobs SDK 项目中，无需指定 `FunctionName` 特性。
> * 某些绑定类型，例如 HTTP (Webhook) 以及基于 HTTP 的事件网格，只在 Functions 中受支持。
>
> 有关详细信息，请参阅 [WebJobs SDK 和 Azure Functions 的比较](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。

## <a name="webjobs-host"></a>WebJobs 主机

主机是函数的运行时容器。  它会侦听触发器并调用函数。 在版本 3.*x* 中，主机是 `IHost` 的实现。 在版本 2.*x* 中，使用的是 `JobHost` 对象。 在代码中创建主机实例，并编写代码来自定义其行为。

这是直接使用 WebJobs SDK 与通过 Azure Functions 间接使用它的主要差别。 在 Azure Functions 中，由于由服务控制主机，因此无法通过编写代码来定义主机。 Azure Functions 允许通过 host.json 文件中的设置自定义主机行为。 这些设置是字符串而不是代码，限制可执行的自定义类型。

### <a name="host-connection-strings"></a>主机连接字符串

在本地运行时，WebJobs SDK 在 local.settings.json 文件中查找 Azure 存储和 Azure 服务总线连接字符串；在 Azure 中运行时，它会在 WebJob 的环境中查找这些字符串。 默认情况下，需要名为 `AzureWebJobsStorage` 的存储连接字符串设置。  

版本2。*x*的 SDK 允许将自己的名称用于这些连接字符串或将其存储在其他位置。 您可以使用在代码中设置名称[`JobHostConfiguration`]，如下所示：

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

由于版本 3.*x* 使用默认的 .NET Core 配置 API，因此没有 API 可用于更改连接字符串名称。

### <a name="host-development-settings"></a>主机开发设置

可在开发模式下运行主机，提高本地开发效率。 下面介绍部分设置，这些设置在开发模式下运行时会发生更改：

| 属性 | 开发设置 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`：最大化日志输出。 |
| `Queues.MaxPollingInterval`  | 使用较小的值可确保立即触发队列方法。  |
| `Singleton.ListenerLockPeriod` | 使用 15 秒值有助于实现快速迭代开发。 |

启用开发模式的过程取决于 SDK 版本。 

#### <a name="version-3x"></a>版本3。*x*

版本3。*x*使用标准 ASP.NET Core api。 对[`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)实例[`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment)调用方法。 传递名为 `development` 的字符串，如以下示例中所示：

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

`JobHostConfiguration` 类具有 `UseDevelopmentSettings` 方法，该方法支持开发模式。  以下示例演示如何使用开发设置。 若要使 `config.IsDevelopment` 在本地运行时返回 `true`，请设置名为 `AzureWebJobsEnv`、值为 `Development` 的本地环境变量。

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>管理并发连接数（版本 2.*x*）

版本3中的。*x*，连接限制默认为无限连接。 如果由于某种原因需要更改此限制，则可以使用[`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler)类的属性。

在版本 2.*x* 中，使用 [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API 控制主机的并发连接数。 在2中。*x*在启动 web 作业主机之前，应将此值从默认值2增加。

使用 `HttpClient` 从某个函数发出的所有传出 HTTP 请求都会流经 `ServicePointManager`。 达到 `DefaultConnectionLimit` 中设置的值后，`ServicePointManager` 会开始将请求排队，然后再发送请求。 假设 `DefaultConnectionLimit` 设置为 2，并且代码发出了 1,000 个 HTTP 请求。 最初，只允许 2 个请求传入 OS。 其他998将排队等候，直到它们的空间不足。 这意味着 `HttpClient` 可能会超时，因为它似乎已发出请求，但是，OS 从未将此请求发送到目标服务器。 因此，可能会出现看似不合理的行为：本地 `HttpClient` 花费了 10 秒来完成请求，但服务在 200 毫秒内就返回了每个请求。 

ASP.NET 应用程序的默认值是 `Int32.MaxValue`，这可能非常适合在“基本”或更高级别应用服务计划中运行的 WebJob。 WebJob 通常需要 Always On 设置，该设置仅受“基本”和更高级别应用服务计划的支持。

如果 WebJob 在“免费”或“共享”应用服务计划中运行，则应用程序会受到应用服务沙盒的限制：当前的[连接限制为 300 个](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 如果在 `ServicePointManager` 中指定无限制的连接数，则很有可能会达到沙盒连接阈值，并且站点将会关闭。 在这种情况下，将 `DefaultConnectionLimit` 设置为更小的值（例如 50 或 100）可以防止此问题发生，同时仍可保持足够的吞吐量。

必须在发出任何 HTTP 请求之前配置该设置。 出于此原因，WebJobs 主机不应自动尝试调整该设置。 在主机启动之前可能已发生 HTTP 请求，因而可能导致意外的行为。 最佳的做法是先在 `Main` 方法中设置值，紧接着初始化 `JobHost`，如下所示：

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

函数必须是公共方法，并且必须具有一个触发器特性或[`NoAutomaticTrigger`](#manual-triggers)属性。

### <a name="automatic-triggers"></a>自动触发器

自动触发器调用函数来响应事件。 以下示例函数由添加到 Azure 队列存储的消息触发。 该函数的响应方式是从 Azure Blob 存储中读取 Blob：

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` 特性告知运行时，每当某个队列消息显示在 `myqueue-items` 队列中，就要调用该函数。 `Blob` 特性告知运行时要使用队列消息读取 *sample-workitems* 容器中的 Blob。 `samples-workitems`容器中的 blob 项的名称直接从队列触发器获取为绑定表达式（`{queueTrigger}`）。

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>手动触发器

若要手动触发函数，请使用 `NoAutomaticTrigger` 特性，如下所示：

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

通过将属性应用于方法返回值，可以对输出绑定使用方法返回值。 请参阅[使用 Azure 函数返回值](../azure-functions/functions-bindings-return-value.md)中的示例。

## <a name="binding-types"></a>绑定类型

安装和管理绑定类型的过程取决于使用的是 SDK 版本 3.*x* 还是版本 2.*x*。 可以在特定绑定类型的 Azure Functions [参考文章](#binding-reference-information)的“包”部分找到要为该绑定类型安装的包。 异常是 Files 触发器和绑定（适用于本地文件系统），不受 Azure Functions 的支持。

#### <a name="version-3x"></a>版本3。*x*

版本3中的。*x*，存储绑定包含在`Microsoft.Azure.WebJobs.Extensions.Storage`包中。 在 `ConfigureWebJobs` 方法中调用 `AddAzureStorage` 扩展方法，如下所示：

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

若要使用其他触发器和绑定类型，请安装包含这些类型的 NuGet 包，并调用在扩展中实现的 `Add<binding>` 扩展方法。 例如，若要使用 Azure Cosmos DB 绑定，请安装 `Microsoft.Azure.WebJobs.Extensions.CosmosDB` 并调用 `AddCosmosDB`，如下所示：

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

以下触发器和绑定类型包含在版本 2.*x* 的 `Microsoft.Azure.WebJobs` 包中：

* Blob 存储
* 队列存储
* 表存储

若要使用其他触发器和绑定类型，请安装包含这些类型的 NuGet 包，并对 `JobHostConfiguration` 对象调用 `Use<binding>` 方法。 例如，若要使用 Timer 触发器，请安装 `Microsoft.Azure.WebJobs.Extensions` 并在 `Main` 方法中调用 `UseTimers`，如下所示：

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

使用[`ExecutionContext`]web 作业可以绑定到。 使用此绑定，可以在函数签名[`ExecutionContext`]中访问作为参数。 例如，以下代码使用上下文对象访问调用 ID，使用该 ID 可以关联给定函数调用生成的所有日志。  

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

绑定到的[`ExecutionContext`]过程取决于 SDK 版本。

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

前面所述的 `Microsoft.Azure.WebJobs.Extensions` 包还提供了一个可以通过调用 `UseCore` 方法注册的特殊绑定类型。 此绑定使你可以在[`ExecutionContext`]函数签名中定义参数，如下所示：

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

可以配置某些触发器和绑定的行为。 配置过程取决于 SDK 版本。

* **版本3。*x*：** 在中`Add<Binding>` `ConfigureWebJobs`调用方法时设置配置。
* **版本2。*x*：** 通过在传入的配置对象中设置属性来设置配置。 `JobHost`

这些特定于绑定的设置相当于 Azure Functions 的 [host.json 项目文件](../azure-functions/functions-host-json.md)中的设置。

可配置以下绑定：

* [Azure CosmosDB 触发器](#azure-cosmosdb-trigger-configuration-version-3x)
* [事件中心触发器](#event-hubs-trigger-configuration-version-3x)
* [队列存储触发器](#queue-storage-trigger-configuration)
* [SendGrid 绑定](#sendgrid-binding-configuration-version-3x)
* [服务总线触发器](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Azure CosmosDB 触发器配置（版本 3.*x*）

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

有关更多详细信息，请参阅 [Azure CosmosDB 绑定](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)一文。

### <a name="event-hubs-trigger-configuration-version-3x"></a>事件中心触发器配置（版本 3.*x*）

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

有关更多详细信息，请参阅[事件中心绑定](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json)一文。

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

有关更多详细信息，请参阅[队列存储绑定](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties)一文。

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

有关更多详细信息，请参阅 [host.json v1.x 参考](../azure-functions/functions-host-json-v1.md#queues)。

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid 绑定配置（版本 3.*x*）

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

有关更多详细信息，请参阅 [SendGrid 绑定](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings)一文。

### <a name="service-bus-trigger-configuration-version-3x"></a>服务总线触发器配置（版本 3.*x*）

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

有关更多详细信息，请参阅[服务总线绑定](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings)一文。

### <a name="configuration-for-other-bindings"></a>其他绑定的配置

某些触发器和绑定类型定义其自身的自定义配置类型。 例如，File 触发器允许指定要监视的根路径，如以下示例中所示：

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

有时，你想要在代码中指定队列名称、Blob 名称、容器或表名称，而不是进行硬编码。 例如，可能要在配置文件或环境变量中指定 `QueueTrigger` 特性的队列名称。

为此，可以向 `JobHostConfiguration` 对象传入 `NameResolver` 对象。 在触发器或绑定特性构造函数参数中包含占位符，`NameResolver` 代码将提供用于取代这些占位符的实际值。 占位符的标识方式是以百分号 (%) 将其括住，如下所示：

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此代码允许在测试环境中使用名为 `logqueuetest` 的队列，并在生产环境中使用名为 `logqueueprod` 的队列。 在 `appSettings` 集合中指定条目名称，而不是硬编码的队列名称。

如果未提供自定义值，则默认值 `NameResolver` 将会生效。 默认设置从应用设置或环境变量中获取值。

`NameResolver` 类从 `appSettings` 获取队列名称，如下所示：

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

使用依赖关系注入配置解析程序。 这些示例需要下列 `using` 语句：

```cs
using Microsoft.Extensions.DependencyInjection;
```

可以通过调用上[`ConfigureServices`] [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)的扩展方法来添加冲突解决程序，如以下示例中所示：

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

将 `NameResolver` 类传入 `JobHost` 对象，如下所示：

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

如果需要在使用 `Queue`、`Blob` 或 `Table` 等绑定特性之前在函数中执行某项操作，可以使用 `IBinder` 接口。

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

Azure Functions 文档中提供了有关每个绑定类型的参考信息。 每篇绑定参考文章中介绍了以下信息。 （此示例基于存储队列。）

* [包](../azure-functions/functions-bindings-storage-queue.md)。 需要安装哪个包才能在 WebJobs SDK 项目中支持绑定。
* [示例](../azure-functions/functions-bindings-storage-queue-trigger.md)。 代码示例。 C# 类库示例适用于 WebJobs SDK。 只需省略 `FunctionName` 特性。
* [特性](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations)。 用于绑定类型的特性。
* [配置](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration)。 特性属性和构造函数参数的解释。
* [使用情况](../azure-functions/functions-bindings-storage-queue-trigger.md#usage)。 可绑定到哪些类型，以及有关绑定工作原理的信息。 例如：轮询算法、有害队列处理。
  
有关绑定引用项目的列表，请参阅 Azure Functions 的[触发器和绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)一文中的 "支持的绑定"。 在该列表中，HTTP、Webhook 和事件网格绑定仅受 Azure Functions 的支持，而不受 WebJobs SDK 的支持。

## <a name="disable-attribute"></a>Disable 特性 

[`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs)属性允许您控制是否可以触发函数。 

在以下示例中，如果应用设置 `Disable_TestJob` 使用值 `1` 或 `True`（不区分大小写），则函数不会运行。 在这种情况下，运行时将创建日志消息“函数 'Functions.TestJob' 已禁用”。**

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

在 Azure 门户中更改应用设置值时，WebJob 会重启并选取新的设置。

可以在参数、方法或类级别声明该特性。 设置名称还可以包含绑定表达式。

## <a name="timeout-attribute"></a>Timeout 特性

如果[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)某个函数在指定的时间内未完成，则该特性将导致该函数被取消。 以下示例中的函数不带 Timeout 特性，将会运行一天。 如果指定了 Timeout，该函数将在 15 秒后被取消。

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

可以在类或方法级别应用 Timeout 特征，并可以使用 `JobHostConfiguration.FunctionTimeout` 指定全局超时。 类级别或方法级别的超时替代全局超时。

## <a name="singleton-attribute"></a>Singleton 特性

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)特性可确保仅运行一个函数实例，即使存在主机 web 应用的多个实例时也是如此。 使用[分布式锁定](#viewing-lease-blobs)可实现此目的。

在此示例中，在任意给定时间只会运行 `ProcessImage` 函数的单个实例：

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

可以使用这些设置来确保函数在单个实例上作为单一实例运行。 若要确保在 Web 应用横向扩展到多个实例时只运行函数的单个实例，请对该函数应用侦听器级别的单一实例锁 (`[Singleton(Mode = SingletonMode.Listener)]`)。 启动 JobHost 时获取侦听器锁。 如果三个横向扩展的实例全部同时启动，只有其中的一个实例获取该锁，并且只有一个侦听器启动。

### <a name="scope-values"></a>范围值

可以在单一实例中指定一个范围表达式/值。** 表达式/值可确保特定范围内的所有函数执行都将序列化。 以这种方式实现更细化的锁定可以为函数提供一定程度的并行度，同时根据你的需求串行化其他调用。 例如，在以下代码中，范围表达式将绑定到传入消息的 `Region` 值。 如果队列分别在区域 East、East 和 West 中包含 3 条消息，则区域为“East”的消息将串行运行，而区域为 West 的消息将与 East 中的这些消息并行运行。

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

锁的默认范围为 `SingletonScope.Function`，这意味着，锁范围（Blob 租约路径）已绑定到完全限定的函数名称。 若要跨函数锁定，请指定 `SingletonScope.Host`，并使用在不想要同时运行的所有函数中相同的范围 ID 名称。 在以下示例中，每次只会运行 `AddItem` 或 `RemoveItem` 的一个实例：

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

WebJobs SDK 在幕后使用 [Azure Blob 租约](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)来实现分布式锁定。 可以在 `AzureWebJobsStorage` 存储帐户的 `azure-webjobs-host` 容器中的路径“locks”下面找到单一实例使用的租约 Blob。 例如，前面演示的第一个 `ProcessImage` 示例的租约 Blob 路径可能是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路径包含 JobHost ID，在本例中为 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>异步函数

有关如何编写异步函数代码的信息，请参阅 [Azure Functions 文档](../azure-functions/functions-dotnet-class-library.md#async)。

## <a name="cancellation-tokens"></a>取消令牌

有关如何处理取消令牌的信息，请参阅有关[取消令牌和正常关闭](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)的 Azure Functions 文档。

## <a name="multiple-instances"></a>多个实例

如果 Web 应用在多个实例上运行，则会有一个连续的 WebJob 在每个实例上运行，并侦听触发器和调用函数。 各种触发器绑定旨在以协作方式有效分担各个实例上的工作，以便横向扩展到多个实例后可以处理更多的负载。

尽管某些触发器可能会导致重复处理，但队列和 Blob 存储触发器可以自动阻止函数多次处理队列消息或 Blob。 有关详细信息，请参阅 Azure Functions 文档中的[针对完全相同的输入进行设计](../azure-functions/functions-idempotent.md)。

计时器触发器会自动确保只会运行计时器的一个实例，因此，在给定的计划时间，不会运行多个函数实例。

如果要确保即使有多个主机 web 应用的实例，也只有一个函数实例运行，可以使用[`Singleton`](#singleton-attribute)特性。

## <a name="filters"></a>筛选器

通过函数筛选器（预览版）可以使用自己的逻辑自定义 WebJobs 执行管道。 筛选器类似于[ASP.NET Core 筛选](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)器。 可将其实现为应用到函数或类的声明性特性。 有关详细信息，请参阅[函数筛选器](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)。

## <a name="logging-and-monitoring"></a>日志记录和监视

我们建议使用针对 ASP.NET 开发的日志记录框架。 [入门](webjobs-sdk-get-started.md)文章中介绍了其用法。 

### <a name="log-filtering"></a>日志筛选

`ILogger` 实例创建的每个日志都包含关联的 `Category` 和 `Level`。 [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)为枚举，整数代码指示相对重要性：

|LogLevel    |代码|
|------------|---|
|跟踪       | 0 |
|调试       | 1 |
|信息 | 2 |
|警告     | 3 |
|错误       | 4 |
|关键    | 5 |
|None        | 6 |

您可以单独筛选每个类别的特定[`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)类别。 例如，你可能想要查看有关 Blob 触发器处理的所有日志，但对于其他任何操作，只想查看 `Error` 和更高级别的日志。

#### <a name="version-3x"></a>版本3。*x*

版本3。*x* SDK 依赖于 .net Core 中内置的筛选。 使用 `LogCategories` 类，可以为特定函数、触发器或用户定义类别。 它还能为特定主机状态（例如，`Startup` 和 `Results`）定义筛选器。 这样就可以微调日志记录输出。 如果在定义类别中未找到任何匹配项，筛选器在决定是否筛选消息时会回退到 `Default` 值。

`LogCategories` 需要以下 using 语句：

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

以下示例构造的筛选器默认会筛选 `Warning` 级别的所有日志。 `Function` 和 `results`类别（等效于版本 2.*x* 中的 `Host.Results`）在 `Error` 级别进行筛选。 筛选器将当前类别与 `LogCategories` 实例中所有已注册的级别进行比较，并选择最长匹配项。 这意味着，为 `Host.Triggers` 注册的 `Debug` 级别将匹配 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 这样，便可以控制更广泛的类别，而无需添加每个类别。

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

在版本 2.*x* 的 SDK 中，`LogCategoryFilter` 类用于控制筛选。 `LogCategoryFilter` 包含初始值为 `Information` 的 `Default` 属性，这意味着，将会记录级别为 `Information`、`Warning`、`Error` 或 `Critical` 的所有消息，但会筛选掉级别为 `Debug` 或 `Trace` 的所有消息。

与版本 3.*x* 中的 `LogCategories` 一样，使用 `CategoryLevels` 属性可以指定特定类别的日志级别，以便能够微调日志记录输出。 如果在 `CategoryLevels` 字典中未找到任何匹配项，筛选器在决定是否筛选消息时会回退到 `Default` 值。

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

为 [Application Insights](../azure-monitor/app/app-insights-overview.md) 实现自定义遥测的过程取决于 SDK 版本。 要了解如何配置 Application Insights，请参阅[添加 Application Insights 日志记录](webjobs-sdk-get-started.md#add-application-insights-logging)。

#### <a name="version-3x"></a>版本3。*x*

由于 WebJobs SDK 的版本 3.x** 依赖于 .NET Core 通用主机，因此不再提供自定义遥测工厂。 但可以使用依赖关系注入将自定义遥测添加到管道。 本部分中的示例要求使用下列 `using` 语句：

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

的以下自定义实现[`ITelemetryInitializer`]允许你将自己[`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry)添加到默认。 [`TelemetryConfiguration`]

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

在[`ConfigureServices`]生成器中调用，将您的[`ITelemetryInitializer`]自定义添加到管道。

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

[`TelemetryConfiguration`]构造后，将包括的[`ITelemetryInitializer`]所有注册类型。 若要了解详细信息，请参阅[用于处理自定义事件和指标的 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md)。

版本3中的。*x*，你不再需要在主机停止[`TelemetryClient`]时刷新。 .NET Core 依赖项注入系统自动释放已注册`ApplicationInsightsLoggerProvider`的，这会刷新[`TelemetryClient`]。

#### <a name="version-2x"></a>版本2。*x*

在版本 2.x** 中，Application Insights 提供程序为 WebJobs SDK 在内部创建的 [`TelemetryClient`] 使用 [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll)。 当 Application Insights 终结点时不可用或限制传入请求时，此通道会[在 Web 应用的文件系统中保存请求，并稍后提交这些请求](https://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

[`TelemetryClient`]是由实现`ITelemetryClientFactory`的类创建的。 默认情况下，此为[`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)。

如果要修改 Application Insights 管道的任何部分，则可以提供自己`ITelemetryClientFactory`的，主机将使用类来构造。 [`TelemetryClient`] 例如，此代码会重写 `DefaultTelemetryClientFactory` 以修改 `ServerTelemetryChannel` 的属性：

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

`SamplingPercentageEstimatorSettings` 对象配置[自适应采样](https://docs.microsoft.com/azure/application-insights/app-insights-sampling)。 这意味着，在某些大容量方案中，Applications Insights 会向服务器发送选定的遥测数据子集。

创建遥测工厂后，可将其传入 Application Insights 日志记录提供程序：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> 后续步骤

本文提供的代码片段演示了如何处理 WebJobs SDK 的常用方案。 有关完整示例，请参阅 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost)。

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[ITelemetryInitializer]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
