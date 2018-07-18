---
title: Azure Functions C# 脚本开发人员参考
description: '了解如何使用 C # 脚本开发 Azure Functions。'
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: 1706eaeaa59f09f343d831f0c09f98210eadb820
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970830"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure Functions C# 脚本 (.csx) 开发人员参考

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

本文介绍了如何使用 C# 脚本 (*.csx*) 开发 Azure Functions。

Azure Functions 支持 C# 和 C# 脚本编程语言。 如需[在 Visual Studio 类库项目中使用 C#](functions-develop-vs.md) 方面的指南，请参阅 [C# 开发人员参考](functions-dotnet-class-library.md)。

本文假定你已阅读 [Azure Functions 开发人员指南](functions-reference.md)。

## <a name="how-csx-works"></a>.csx 的工作原理

Azure Functions 的 C# 脚本体验基于 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction)。 数据通过方法参数流入 C # 函数。 参数名称在 `function.json` 文件中指定，而预定义的名称则用于访问函数记录器和取消令牌等内容。

*.csx* 格式允许编写更少的“样本”，因此你可以集中编写 C# 函数。 只需定义 `Run` 方法即可，无需将所有内容都包装在命名空间和类中。 像往常一样，在文件开头包含任何程序集引用和命名空间。

初始化实例时，会编译函数应用的 *.csx* 文件。 此编译步骤意味着，与 C# 类库相比，冷启动之类的操作对于 C# 脚本函数来说可能需要更长的时间。 此编译步骤也说明了为何 C# 脚本函数在 Azure 门户中可以编辑，而 C# 类库则不可以编辑。

## <a name="binding-to-arguments"></a>绑定到参数

输入或输出数据通过 *function.json* 配置文件中的 `name` 属性绑定到 C# 脚本函数参数。 以下示例显示了一个 *function.json* 文件以及适用于队列触发函数的 *run.csx* 文件。 从队列消息接收数据的参数名为 `myQueueItem`，因为这是 `name` 属性的值。

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

[本文后面](#referencing-external-assemblies)对 `#r` 语句做了解释。

## <a name="supported-types-for-bindings"></a>绑定支持的类型

每个绑定都具有其自己支持的类型；例如，Blob 触发器可以与字符串参数、POCO 参数、`CloudBlockBlob` 参数或任何其他几种受支持类型之一配合使用。 [适用于 Blob 绑定的绑定参考文章](functions-bindings-storage-blob.md#trigger---usage)列出了适用于 Blob 触发器的所有受支持参数类型。 有关详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)与[每个绑定类型的绑定参考文档](functions-triggers-bindings.md#next-steps)。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>引用自定义类

如需使用自定义的普通旧 CLR 对象 (POCO) 类，可以将类定义包括在同一文件中，也可以将其置于单独的文件中。

以下示例显示了一个 *run.csx* 示例，后者包括一个 POCO 类定义。

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

POCO 类必须为每个属性定义 getter 和 setter。

## <a name="reusing-csx-code"></a>重用.csx 代码

可以在 *run.csx* 文件中使用其他 *run.csx* 文件中定义的类和方法。 为此，需使用 run.csx 文件中的 `#load` 指令。 在下面的实例中，在 myLogger.csx 中共享了名为 `MyLogger` 的日志记录例程，并使用 `#load` 指令将其加载到 run.csx：

示例 *run.csx*：

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

示例 *mylogger.csx*：

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext);
}
```

若要使用 POCO 对象强类型化在函数间传递的数据，常见模式是使用共享的 .csx 文件。 在下面的简化示例中，一个 HTTP 触发器和队列触发器共享名为 `Order` 的 POCO 对象以强类型化顺序数据：

HTTP 触发器的示例 run.csx：

```cs
#load "..\shared\order.csx"

using System.Net;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function received an order.");
    log.Info(req.ToString());
    log.Info("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

队列触发器的示例 run.csx：

```cs
#load "..\shared\order.csx"

using System;

public static void Run(Order myQueueItem, out Order outputQueueItem,TraceWriter log)
{
    log.Info($"C# Queue trigger function processed order...");
    log.Info(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

示例 order.csx：

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

可以使用相对路径与 `#load` 指令：

* `#load "mylogger.csx"` 加载函数文件夹中的文件。
* `#load "loadedfiles\mylogger.csx"` 加载文件函数文件夹中的文件夹。
* `#load "..\shared\mylogger.csx"` 在同一级别（即 *wwwroot* 的正下方）加载文件夹中的文件，使其成为函数文件夹。

`#load` 指令仅适用于 *.csx* 文件，不适用于 *.cs* 文件。

## <a name="binding-to-method-return-value"></a>绑定到方法返回值

可通过在 *function.json* 中使用名称 `$return` 将方法返回值用于输出绑定。 有关示例，请参阅[触发器和绑定](functions-triggers-bindings.md#using-the-function-return-value)。

## <a name="writing-multiple-output-values"></a>写入多个输出值

若要向输出绑定写入多个值，请使用 [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 类型。 这些类型是只写集合，当方法完成时写入输出绑定。

此示例使用 `ICollector` 将多个队列消息写入到同一队列：

```csharp
public static void Run(ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Hello");
    myQueueItem.Add("World!");
}
```

## <a name="logging"></a>日志记录

若要使用 C# 将输出记录到流式处理日志中，请包括 `TraceWriter` 类型的参数。 建议将其命名为 `log`。 避免在 Azure Functions 中使用 `Console.Write`。 

[Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs) 中定义了 `TraceWriter`。 `TraceWriter` 的日志级别可在 [host.json](functions-host-json.md) 中配置。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> 有关可以用来代替 `TraceWriter` 的较新的日志记录框架，请参阅**监视 Azure Functions** 一文中的[以 C# 函数写入日志](functions-monitoring.md#write-logs-in-c-functions)。

## <a name="async"></a>异步

要使函数[异步](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)，请使用 `async` 关键字并返回 `Task` 对象。

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

## <a name="cancellation-tokens"></a>取消令牌

函数可以接受 [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 参数，以使操作系统能够在函数即将终止时通知代码。 可以使用此通知来确保该函数不会意外终止，导致数据处于不一致状态。

下面的示例演示了如何检查即将发生的函数终止。

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>导入命名空间

如果需要导入命名空间，则可使用 `using` 子句，按正常情况处理。

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

会自动导入以下命名空间，而且是可选的：

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>引用外部程序集

对于框架程序集，通过使用 `#r "AssemblyName"` 指令添加引用。

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

由 Azure 函数主机环境自动添加以下程序集：

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

可通过简单名称（例如 `#r "AssemblyName"`）引用以下程序集：

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>引用自定义程序集

若要引用自定义程序集，可使用共享程序集或私有程序集：
- 共享程序集在函数应用内的所有函数中共享。 若要引用自定义程序集，请将程序集上传到[函数应用根文件夹`bin` (wwwroot) 中名为 ](functions-reference.md#folder-structure) 的文件夹。 
- 私有程序集是给定函数上下文的一部分，支持不同版本的旁加载。 私有程序集应上传到函数目录中的 `bin` 文件夹。 使用文件名（例如 `#r "MyAssembly.dll"`）引用程序集。 

有关如何将文件上传到函数文件夹的信息，请参阅有关[程序包管理](#using-nuget-packages)的部分。

### <a name="watched-directories"></a>监视的目录

自动监视包含函数脚本文件的目录的程序集更改。 若要监视其他目录中的程序集更改，请将其添加到 [host.json](functions-host-json.md) 中的 `watchDirectories` 列表中。

## <a name="using-nuget-packages"></a>使用 NuGet 包

若要在 C# 函数中使用 NuGet 包，可将 project.json 文件上传到函数应用文件系统中函数的文件夹。 下面是示例 *project.json* 文件，其添加对 Microsoft.ProjectOxford.Face 1.1.0 版的引用：

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

在 Azure Functions 1.x 中，只支持.NET Framework 4.6，因此请确保 *project.json* 文件指定 `net46`，如下所示。

上传 *project.json* 文件时，运行时获取包，并自动将引用添加到包程序集。 无需添加 `#r "AssemblyName"` 指令。 只需添加所需的 `using` 语句到 *run.csx* 文件，即可使用 NuGet 包中定义的类型。 

在 Functions 运行时，通过比较 `project.json` 和 `project.lock.json` 运行 NuGet 还原。 如果各文件的日期和时间戳不匹配，则会运行 NuGet 还原且 NuGet 会下载更新包。 但是，如果各文件的日期和时间戳匹配，则 NuGet 不执行还原。 因此，不应部署 `project.lock.json`，因为它会导致 NuGet 跳过包还原。 要避免部署锁定文件，请将 `project.lock.json` 添加到 `.gitignore` 文件。

若要使用自定义 NuGet 源，请在 Function App 根中指定“Nuget.Config”文件中的源。 有关详细信息，请参阅[配置 NuGet 行为](/nuget/consume-packages/configuring-nuget-behavior)。

### <a name="using-a-projectjson-file"></a>使用 project.json 文件

1. 在 Azure 门户中打开函数。 日志选项卡显示包安装输出。
2. 若要上传 project.json 文件，请使用 Azure Functions 开发人员参考主题中[如何更新函数应用文件](functions-reference.md#fileupdate)部分描述的方法之一。
3. 上传完 *project.json* 文件后，将看到类似于函数流式日志中的实例的输出：

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>环境变量

若要获取环境变量或应用设置值，请使用 `System.Environment.GetEnvironmentVariable`，如以下代码示例所示：

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) 属性是用于获取应用设置值的替代 API，但我们建议你使用 `GetEnvironmentVariable`，如下所示。

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>在运行时绑定

在 C# 和其他 .NET 语言中，可以使用[命令性](https://en.wikipedia.org/wiki/Imperative_programming)绑定模式，而不是 *function.json* 中的[*声明式*](https://en.wikipedia.org/wiki/Declarative_programming)绑定。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 通过此模式，可以在函数代码中动态绑定到受支持的输入和输出绑定。

如下所示定义命令性绑定：

- 对于所需的命令性绑定，**不要**包含 function.json 中的条目。
- 传递输入参数 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 或 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)。
- 使用下面的 C# 模式执行数据绑定。

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` 是定义了绑定的 .NET 属性，`T` 是该绑定类型所支持的输入或输出类型。 `T` 不能是 `out` 参数类型（例如 `out JObject`）。 例如，移动应用表输出绑定支持 [6 种输出类型](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)，但对于 `T`，可仅使用 [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)。

### <a name="single-attribute-example"></a>单属性示例

下面的示例代码使用在运行时定义的 blob 路径创建[存储 blob 输出绑定](functions-bindings-storage-blob.md#output)，然后将字符串写入此 blob。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) 定义[存储 blob](functions-bindings-storage-blob.md) 输入或输出绑定，[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) 是支持的输出绑定类型。

### <a name="multiple-attribute-example"></a>多属性示例

上一个示例获取函数应用的主存储帐户连接字符串（即 `AzureWebJobsStorage`）的应用设置。 通过添加 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) 和将属性数组传入 `BindAsync<T>()`，可指定要用于存储帐户的自定义应用设置。 使用一个 `Binder` 参数而非 `IBinder`。  例如：

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

下表列出了每种绑定类型的 .NET 属性及其定义所在的包。

> [!div class="mx-codeBreakAll"]
| 绑定 | 属性 | 添加引用 |
|------|------|------|
| Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
| 事件中心 | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| 移动应用 | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| 通知中心 | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| 服务总线 | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| 存储队列 | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 存储 blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 存储表 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解触发器和绑定](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [详细了解有关 Azure Functions 的最佳做法](functions-best-practices.md)
