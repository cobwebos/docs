---
title: 如何使用 WebJobs SDK 进行事件驱动的后台处理 - Azure
description: 详细了解如何为 WebJobs SDK 编写代码。 创建事件驱动的后台处理作业，用于访问 Azure 服务和第三方服务中的数据。
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 3adf725f76f744fd1d321668fe892b9703de25de
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-use-the-webjobs-sdk-for-event-driven-background-processing"></a>如何使用 WebJobs SDK 进行事件驱动的后台处理

本文提供有关如何为 [WebJobs SDK](webjobs-sdk-get-started.md) 编写代码的指导。 除非另有说明，否则本文档适用于版本 2.x 和 3.x。 3.x 中引入的主要更改是使用 .NET Core 而不是 .NET Framework。

>[!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) 基于 WebJobs SDK，本文链接到某些主题的 Azure Functions 文档。 注意 Functions 与 WebJobs SDK 之间的以下差异：
> * Azure Functions 版本 1.x 对应于 WebJobs SDK 版本 2.x，Azure Functions 2.x 对应于 WebJobs SDK 3.x。 源代码存储库遵循 WebJobs SDK 编号方案，许多存储库具有 v2.x 分支，其中的主分支当前包含 3.x 代码。
> * Azure Functions C# 类库的示例代码类似于 WebJobs SDK 代码，不过，在 WebJobs SDK 项目中，无需指定 `FunctionName` 特性。
> * 某些绑定类型（例如 HTTP、Webhook 以及基于 HTTP 的事件网格）只在 Functions 中受支持。 
> 
> 有关详细信息，请参阅 [WebJobs SDK 和 Azure Functions 的比较](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)。 

## <a name="prerequisites"></a>先决条件

本文假设你已阅读 [WebJobs SDK 入门](webjobs-sdk-get-started.md)。

## <a name="jobhost"></a>JobHost

`JobHost` 对象是函数的运行时容器：它侦听触发器并调用函数。 在代码中创建 `JobHost`，并编写代码来自定义其行为。

这是直接使用 WebJobs SDK 与通过 Azure Functions 间接使用它的主要差别。 在 Azure Functions 中，服务控制 `JobHost`，你无法通过编写代码来定义它。 Azure Functions 允许通过 *host.json* 文件中的设置自定义主机行为。 这些设置是字符串而不是代码，限制可执行的自定义类型。

### <a name="jobhost-connection-strings"></a>JobHost 连接字符串

在本地运行时，WebJobs SDK 在 *local.settings.json* 中查找存储和服务总线连接字符串；在 Azure 中运行时，它会在 WebJob 的环境中查找这些字符串。 如果想要对这些连接字符串使用自己的名称，或将其存储在其他位置，可以在代码中进行设置，如下所示：

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>JobHost 开发设置

`JobHostConfiguration` 类包含 `UseDevelopmentSettings` 方法，调用该方法可以提高本地开发的效率。 下面是此方法更改的某些设置：

| 属性 | 开发设置 |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose`：最大化日志输出。 |
| `Queues.MaxPollingInterval`  | 使用较小的值可确保立即触发队列方法。  |
| `Singleton.ListenerLockPeriod` | 使用 15 秒值有助于实现快速迭代开发。 |

以下示例演示如何使用开发设置。 若要使 `config.IsDevelopment` 在本地运行时返回 `true`，请设置名为 `AzureWebJobsEnv`、值为 `Development` 的本地环境变量。

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

### <a name="jobhost-servicepointmanager-settings"></a>JobHost ServicePointManager 设置

.NET Framework 包含名为 [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) 的 API，该 API 控制主机的并发连接数。 我们建议在启动 WebJobs 主机之前，在默认值 2 的基础上增大此值。

使用 `HttpClient` 从某个函数发出的所有传出 HTTP 请求都会流经 `ServicePointManager`。 达到 `DefaultConnectionLimit` 后，`ServicePointManager` 会开始将请求排队，然后再发送请求。 假设 `DefaultConnectionLimit` 设置为 2，并且代码发出了 1,000 个 HTTP 请求。 最初，实际只允许 2 个请求传入 OS。 其他 998 个请求将会排队，直到有可用的空间。 这意味着 `HttpClient` 可能会超时，因为它认为已发出请求，但是，OS 从未将此请求发送到目标服务器。 因此，可能会出现看似不合理的行为：本地 `HttpClient` 花费了 10 秒来完成请求，但服务在 200 毫秒内就返回了每个请求。 

ASP.NET 应用程序的默认值是 `Int32.MaxValue`，这可能非常适合在“基本”或更高级别应用服务计划中运行的 WebJobs。 WebJobs 通常需要 Always On 设置，该设置仅受“基本”和更高级别应用服务计划的支持。 

如果 WebJob 在“免费”或“共享”应用服务计划中运行，则应用程序会受到应用服务沙盒的限制：当前的[连接限制为 300 个](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits)。 如果在 `ServicePointManager` 中指定无限制的连接数，则很有可能会达到沙盒连接阈值，并且站点将会关闭。 在这种情况下，将 `DefaultConnectionLimit` 设置为更小的值（例如 50 或 100）可以防止此问题发生，同时仍可保持足够的吞吐量。

必须在发出任何 HTTP 请求之前配置该设置。 为此，WebJobs 主机不应自动尝试调整该设置；在主机启动之前可能已发生 HTTP 请求，因而可能导致意外的行为。 最佳的做法是在初始化 `JobHost` 之前，立即在 `Main` 方法中设置值，如以下示例中所示

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>触发器

函数必须是公共方法，并且必须包含一个触发器特性或 [NoAutomaticTrigger](#manual-trigger) 特性。

### <a name="automatic-trigger"></a>自动触发器

自动触发器调用函数来响应事件。 有关示例，请参阅[入门文章](webjobs-sdk-get-started.md)中的“队列触发器”。

### <a name="manual-trigger"></a>手动触发器

若要手动触发某个函数，请使用 `NoAutomaticTrigger` 特性，如以下示例中所示：

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>输入和输出绑定

通过输入绑定能够以声明方式将 Azure 或第三方服务中的数据提供给代码使用。 输出绑定提供更新数据的方式。 [入门文章](webjobs-sdk-get-started.md)中演示了输入和输出绑定的示例。

通过将属性应用于方法返回值，可以对输出绑定使用方法返回值。 请参阅 Azure Functions [触发器和绑定](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value)一文中的示例。

## <a name="binding-types"></a>绑定类型

以下触发器和绑定类型包含在 `Microsoft.Azure.WebJobs` 包中：

* Blob 存储
* 队列存储
* 表存储

若要使用其他触发器和绑定类型，请安装包含这些类型的 NuGet 包，并对 `JobHostConfiguration` 对象调用 `Use<binding>` 方法。 例如，若要使用 Timer 触发器，请安装 `Microsoft.Azure.WebJobs.Extensions` 并在 `Main` 方法中调用 `UseTimers`，如以下示例所示：

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

可以在特定绑定类型的 Azure Functions [参考文章](#binding-reference-information)的“包”部分中找到要为该绑定类型安装的包。 异常是 Files 触发器和绑定（适用于本地文件系统），不受 Azure Functions 的支持。 若要使用 Files 绑定，请安装 `Microsoft.Azure.WebJobs.Extensions` 并调用 `UseFiles`。

### <a name="usecore"></a>UseCore

前面所述的 `Microsoft.Azure.WebJobs.Extensions` 包还提供了一个可以通过调用 `UseCore` 方法注册的特殊绑定类型。 使用此绑定可以在函数签名中定义 [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) 参数。 通过上下文对象可以访问调用 ID，使用该 ID 可以关联给定函数调用生成的所有日志。 下面是一个示例：

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

## <a name="binding-configuration"></a>绑定配置

许多触发器和绑定类型允许配置其行为，配置方法是在传入到 `JobHost` 的配置对象中设置属性。

### <a name="queue-trigger-configuration"></a>队列触发器配置

Azure Functions [host.json 参考](../azure-functions/functions-host-json.md#queues)中介绍了可为存储队列触发器配置的设置。 以下示例演示了如何在 WebJobs SDK 项目中设置这些属性：

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

### <a name="configuration-for-other-bindings"></a>其他绑定的配置

某些触发器和绑定类型定义其自身的自定义配置类型。 例如，File 触发器允许指定要监视的根路径：

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

有关绑定表达式的详细信息，请参阅 Azure Functions 文档中的[绑定表达式和模式](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)。

### <a name="custom-binding-expressions"></a>自定义绑定表达式

有时，要在代码中指定队列名称、Blob 名称、容器或表名称，而不是进行硬编码。 例如，可能要在配置文件或环境变量中指定 `QueueTrigger` 特性的队列名称。

为此，可以向 `JobHostConfiguration` 对象传入 `NameResolver` 对象。 在触发器或绑定特性构造函数参数中包含占位符，`NameResolver` 代码将提供用于取代这些占位符的实际值。 占位符的标识方式是以百分号 (%) 将其括住，如以下示例中所示：
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

此代码允许在测试环境中使用名为 logqueuetest 的队列，并在生产环境中使用名为 logqueueprod 的队列。 在 `appSettings` 集合中指定条目名称，而不是硬编码的队列名称。 

如果未提供自定义值，则使用默认设置 NameResolver。 默认设置从应用设置或环境变量中获取值。

`NameResolver` 类从 `appSettings` 获取队列名称，如以下示例中所示：

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

将 `NameResolver` 类传入 `JobHost` 对象，如以下示例中所示：

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

如果需要在使用 `Queue`、`Blob` 或 `Table` 等绑定特性之前在函数中执行某项操作，则可以使用 `IBinder` 接口。

下述示例使用输入队列消息，并在输出队列中创建具有相同内容的新消息。 输出队列名称由函数正文中的代码设置。

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

有关详细信息，请参阅 Azure Functions 文档中的[运行时绑定](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime)。

## <a name="binding-reference-information"></a>绑定参考信息

Azure Functions 文档中提供了有关每个绑定类型的参考信息。 每篇绑定参考文章中以存储队列为例介绍了以下信息：

* [包](../azure-functions/functions-bindings-storage-queue.md#packages) - 要安装哪个包才能在 WebJobs SDK 项目中包含绑定支持。
* [示例](../azure-functions/functions-bindings-storage-queue.md#trigger---example) - C# 类库示例适用于 WebJobs SDK；只需忽略 `FunctionName` 特性。
* [特性](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) - 用于绑定类型的特性。
* [配置](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) - 特性属性和构造函数参数的解释。
* [用法](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) - 可绑定到哪些类型，以及有关绑定工作原理的信息。 例如：轮询算法、有害队列处理。
  
有关绑定参考文章的列表，请参阅 Azure Functions [触发器和绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)一文中的“支持的绑定”。 在该列表中，HTTP、Webhook 和事件网格绑定仅受 Azure Functions 的支持，而不受 WebJobs SDK 的支持。

## <a name="disable-attribute"></a>Disable 特性 

[Disable](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) 特性用于控制是否可以触发某个函数。 

在以下示例中，如果应用设置 `Disable_TestJob` 使用值“1”或“True”（区分大小写），则函数不会运行。 在这种情况下，运行时将创建日志消息“函数 'Functions.TestJob' 已禁用”。

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

在 Azure 门户中更改应用设置值时，会导致 WebJob 重启并选取新设置。

可以在参数、方法或类级别声明该特性。 设置名称还可以包含绑定表达式。

## <a name="timeout-attribute"></a>Timeout 特性

如果某个函数在指定的时间段内未完成，则 [Timeout](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) 特性会导致该函数被取消。 以下示例中的函数将运行一天，而不会超时。 指定超时后，该函数将在 15 秒后被取消。

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

可以在类或方法级别应用 Timeout 特征，并可以使用 `JobHostConfiguration.FunctionTimeout` 指定全局超时。 类或方法级别的超时替代全局超时。

## <a name="singleton-attribute"></a>Singleton 特性

使用 [Singleton](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) 特性可确保即使有多个主机 Web 应用的实例，也只有一个函数实例运行。 实现[分布式锁定](#viewing-lease-blobs)即可做到这一点。

在以下示例中，在任意给定时间只会运行 `ProcessImage` 函数的单个实例：

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

某些触发器为并发管理提供内置支持：

* **QueueTrigger** - 将 `JobHostConfiguration.Queues.BatchSize` 设置为 1。
* **ServiceBusTrigger** - 将 `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` 设置为 1。
* **FileTrigger** - 将 `FileProcessor.MaxDegreeOfParallelism` 设置为 1。

可以使用这些设置来确保函数在单个实例上作为单一实例运行。 若要确保在 Web 应用横向扩展到多个实例时只运行函数的单个实例，请对该函数应用侦听器级别的单一实例锁 (`[Singleton(Mode = SingletonMode.Listener)]`)。 启动 JobHost 时获取侦听器锁。 如果三个横向扩展的实例全部同时启动，只有其中的一个实例获取该锁，并且只有一个侦听器启动。

### <a name="singletonscopehost"></a>SingletonScope.Host

锁的默认范围为 `SingletonScope.Function`，这意味着，锁范围（Blob 租约路径）已绑定到完全限定的函数名称。 若要跨函数锁定，请指定 `SingletonScope.Host`，并使用在不想要同时运行的所有函数中相同的范围 ID 名称。 在以下示例中，每次只会运行 `AddItem` 或 `RemoveItem` 的一个实例：

```charp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>查看租约 Blob

WebJobs SDK 在幕后使用 [Azure Blob 租约](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs)来实现分布式锁定。 可以在 `AzureWebJobsStorage` 存储帐户的 `azure-webjobs-host` 容器中的路径“locks”下面找到单一实例使用的租约 Blob。 例如，前面演示的第一个 `ProcessImage` 示例的租约 Blob 路径可能是 `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`。 所有路径包含 JobHost ID，在本例中为 061851c758f04938a4426aa9ab3869c0。

## <a name="async-functions"></a>异步函数

有关如何编写异步函数代码的信息，请参阅有关[异步函数](../azure-functions/functions-dotnet-class-library.md#async)的 Azure Functions 文档。

## <a name="cancellation-tokens"></a>取消令牌

有关如何处理取消令牌的信息，请参阅有关[取消令牌和正常关闭](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens)的 Azure Functions 文档。

## <a name="multiple-instances"></a>多个实例

如果 Web 应用在多个实例上运行，则会有一个连续的 WebJob 在每个实例上运行，并侦听触发器和调用函数。 各种触发器绑定旨在以协作方式有效分担各个实例上的工作，以便横向扩展到多个实例后可以处理更多的负载。

队列和 Blob 触发器自动阻止函数多次处理队列消息或 Blob；函数不需要是幂等的。

计时器触发器会自动确保只会运行计时器的一个实例，因此，在给定的计划时间，不会运行多个函数实例。

如果要确保即使有多个主机 Web 应用的实例，也只有一个函数实例运行，可以使用 [Singleton](#singleton) 特性。
    
## <a name="filters"></a>筛选器 

通过函数筛选器（预览版）可以使用自己的逻辑自定义 WebJobs 执行管道。 筛选器类似于 [ASP.NET Core 筛选器](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters)。 可将其实现为应用到函数或类的声明性特性。 有关详细信息，请参阅[函数筛选器](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters)。

## <a name="logging-and-monitoring"></a>日志记录和监视

我们建议使用针对 ASP.NET 开发的日志记录框架；[入门](webjobs-sdk-get-started.md)文章中介绍了其用法。 

### <a name="log-filtering"></a>日志筛选

`ILogger` 实例创建的每个日志都包含关联的 `Category` 和 `Level`。 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) 是一个枚举，整数代码指示相对重要性：

|LogLevel    |代码|
|------------|---|
|跟踪       | 0 |
|调试       | 1 |
|信息 | 2 |
|警告     | 3 |
|错误       | 4 |
|严重    | 5 |
|无        | 6 |

可以根据特定的 [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel) 独立筛选每个类别。 例如，你可能想要查看有关 Blob 触发器处理的所有日志，但对于其他任何操作，只想查看 `Error` 和更高级别的日志。

为了方便指定筛选规则，WebJobs SDK 提供了 `LogCategoryFilter`，可将其传入许多现有的日志记录提供程序，包括 Application Insights 和控制台。

`LogCategoryFilter` 包含初始值为 `Information` 的 `Default` 属性，这意味着，将会记录级别为 `Information`、`Warning`、`Error` 或 `Critical` 的所有消息，但会筛选掉级别为 `Debug` 或 `Trace` 的所有消息。

使用 `CategoryLevels` 属性可以指定特定类别的日志级别，以便能够微调日志记录输出。 如果在 `CategoryLevels` 字典中未找到任何匹配项，筛选器在决定是否筛选消息时会回退到 `Default` 值。

以下示例构造的筛选器默认会筛选 `Warning` 级别的所有日志。 `Function` 或 `Host.Results` 类别在 `Error` 级别筛选。 `LogCategoryFilter` 将当前类别与所有已注册的 `CategoryLevels` 进行比较，并选择最长匹配项。 这意味着，为 `Host.Triggers` 注册的 `Debug` 级别将匹配 `Host.Triggers.Queue` 或 `Host.Triggers.Blob`。 这样，便可以控制更广泛的类别，而无需添加每个类别。

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

Application Insights 提供程序为 WebJobs SDK 创建的 `TelemetryClient` 在内部使用 [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)。 当 Application Insights 终结点时不可用或限制传入请求时，此通道会[在 Web 应用的文件系统中保存请求，并稍后提交这些请求](http://apmtips.com/blog/2015/09/03/more-telemetry-channels)。

`TelemetryClient` 是实现 `ITelemetryClientFactory` 的类创建的。 默认情况下，此类为 [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs)。

若要修改 Application Insights 管道的任何组成部分，可以提供自己的 `ITelemetryClientFactory`，而主机会使用你的类来构造 `TelemetryClient`。 例如，此代码会替代 `DefaultTelemetryClientFactory` 来修改 `ServerTelemetryChannel` 属性：

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

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

SamplingPercentageEstimatorSettings 对象配置[自适应采样](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server)。 这意味着，在某些大容量方案中，App Insights 会向服务器发送选定的遥测数据子集。

创建遥测数据工厂后，可将其传入 Application Insights 日志记录提供程序：

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a>后续步骤

本指南提供的代码片段演示了如何处理 WebJobs SDK 的常见使用方案。 有关完整示例，请参阅 [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples)。