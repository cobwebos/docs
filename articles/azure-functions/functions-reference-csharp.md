---
title: "Azure Functions 开发人员参考 | Microsoft Docs"
description: "了解如何开发使用 C # 的 Azure 功能。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: f28cda01-15f3-4047-83f3-e89d5728301c
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/04/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 76b43c78341abb638e2ede97f68c05ee6df0700f
ms.lasthandoff: 04/18/2017


---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# developer reference（Azure Functions C# 开发人员参考）
> [!div class="op_single_selector"]
> * [C# 脚本](functions-reference-csharp.md)
> * [F# 脚本](functions-reference-fsharp.md)
> * [Node.js](functions-reference-node.md)
> 
> 

Azure Functions 的 C# 体验基于 Azure WebJobs SDK。 数据通过方法参数流入 C # 函数。 在 `function.json`指定参数名称，没有访问函数记录器和取消令牌等的预定义的名称。

本文假定已阅读 [Azure Functions 开发人员参考](functions-reference.md)。

## <a name="how-csx-works"></a>.csx 的工作原理
`.csx` 格式允许编写更少的“样本”，而是集中于 C# 函数的编写。 对于 Azure Functions，只需包括需要的任何程序集引用和命名空间；与往常一样，不需将所有内容都封装在命名空间和类中，只需定义 `Run` 方法。 如果需要包括任何类，例如定义普通旧 CLR 对象 (POCO) 对象，可以在同一文件内含入一个类。   

## <a name="binding-to-arguments"></a>绑定到参数
通过 *function.json* 配置中的 `name` 属性，各种绑定绑定到 C# 函数。 每个绑定都有自己支持的类型并对其进行记录；例如，blob 触发器支持字符串、POCO 或几种其他类型。 可以使用最符合需要的类型。 POCO 对象必须为每个属性定义 getter 和 setter。 

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

> [!TIP]
>
> 如果计划使用 HTTP 或 WebHook 绑定，建议阅读这个有关 [HTTPClient](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md) 的最佳做法文档。
>

## <a name="logging"></a>日志记录
若要登录到 C# 中的流式处理日志输出，可以包括 `TraceWriter` 类型化参数。 建议将其命名为 `log`。 们建议在 Azure Functions 中避免 `Console.Write`。

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

## <a name="async"></a>异步
若要使函数异步，使用 `async` 关键字并返回 `Task` 对象。

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## <a name="cancellation-token"></a>取消令牌
在某些情况下，可能会遇到对关闭敏感的操作。 编写能处理故障的代码，正确处理关闭请求，定义 [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) 类型化参数。  如果触发主机关闭，将提供`CancellationToken`。 

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
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
* `Microsoft.Azure.WebJobs.Host`。

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

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`。

此外，以下程序集比较特殊，可能由 simplename 引用 (例如 `#r "AssemblyName"`)：

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

如果需要引用私有程序集，可以将程序集文件上传到  `bin` 与功能相关的文件，并通过使用文件名（例如`#r "MyAssembly.dll"`）来引用它. 有关如何将文件上载到函数文件夹的信息，请参阅下一部分中有关程序包管理的信息。

## <a name="package-management"></a>包管理
若要在 C# 函数中使用 NuGet 包，可将 *project.json* 文件上传到函数应用程序的文件系统中的函数的文件夹。 下面是示例 *project.json* 文件，其添加对 Microsoft.ProjectOxford.Face 1.1.0 版的引用：

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

只支持.NET Framework 4.6，因此请确保 *project.json* 文件指定 `net46`如下所示。

上载 *project.json* 文件时，运行时获取包，并自动将引用添加到包程序集。 无需添加 `#r "AssemblyName"` 指令。 只需添加所需的 `using` 语句到 *run.csx* 文件，以便使用 NuGet 包中定义的类型。

在 Functions 运行时，通过比较 `project.json` 和 `project.lock.json` 运行 NuGet 还原。 如果各文件的日期和时间戳不匹配，则会运行 NuGet 还原且 NuGet 会下载更新包。 但是，如果各文件的日期和时间戳匹配，则 NuGet 不执行还原。 因此不得部署 `project.lock.json`，因为这会导致 NuGet 跳过还原且该函数将不具备所需包。 若要避免部署锁定文件，请将 `project.lock.json` 添加到 `.gitignore` 文件。

### <a name="how-to-upload-a-projectjson-file"></a>如何上传 project.json 文件
1. 首先，确保函数应用程序正在运行，可以通过在 Azure 门户中打开函数来执行此操作。 
   
    通过此操作，还可以访问显示程序包安装输出位置的流式日志日志。 
2. 若要上传项目.json 文件，请使用 [Azure Functions 开发人员参考主题](functions-reference.md#fileupdate)中**如何更新函数应用文件**部分描述的其中一种方法。 
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

## <a name="reusing-csx-code"></a>重用.csx 代码
可以使用类和方法定义其他 *.csx* 文件中您 *run.csx* 文件。 为此，需使用 run.csx 文件中的 `#load` 指令。 在下面的实例中，在 myLogger.csx 中共享了名为 `MyLogger` 的日志记录例程，并使用 `#load` 指令将其加载到 run.csx： 

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

如果要使用 POCO 对象在函数间强类型化参数时，常见模式是使用共享的 .csx。 在下面的简化示例中，一个 HTTP 触发器和队列触发器共享名为 `Order` 的 POCO 对象以强类型化顺序数据：

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

`#load` 指令仅适用于 *.csx*C# 脚本）文件，不适用于 *.cs* 文件。 

## <a name="versioning"></a>版本控制

Functions 运行时作为 Function App 的站点扩展运行。 站点扩展是可扩展点，能够将功能添加到 Azure 应用服务、网站或 Function App。 `Kudu`.和 `Monaco` 是站点扩展的两个示例，也可以创建和使用自定义扩展。 可以使用 `FUNCTIONS_EXTENSION_VERSION` 应用设置配置扩展的版本。

`FUNCTIONS_EXTENSION_VERSION` 仅设置运行时的主版本。 例如：值“~1”表示 Function App 将使用 1 作为其主版本。 Function Apps 在发布后，将升级到各自新的次要版本。 这允许管理升级到各版本的时机，以避免重大更改。

此外，可能想要在运行时成为门户中默认版本之前，升级该运行时。 不过不用担心，可以随时通过将 `FUNCTIONS_EXTENSION_VERSION` 设置为其旧值来执行回滚。

*若要确定 Azure Function App 的运行时版本：*

请在 Kudu 的 `D:\local\Config` 文件夹中找到 `applicationhost.config` 文件。 `virtualDirectory` 条目显示确切的 Functions 运行时版本： 

```xml
<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Functions\0.8.10564" />
```
可以使用此值来设置 Function App 的特定主要和次要运行时版本。 每当更改 Function App 版本后，必须重新启动它。

## <a name="advanced-binding-at-runtime-imperative-binding"></a>运行时高级绑定（命令性绑定）

在 C# 和其他 .NET 语言中，可以使用[命令性](https://en.wikipedia.org/wiki/Imperative_programming)绑定模式，而不是 *function.json* 中的[*声明式*](https://en.wikipedia.org/wiki/Declarative_programming)绑定。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 通过此模式，可动态绑定到函数代码中受支持的任意数量的输入和输出绑定。

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

其中，`BindingTypeAttribute` 是定义绑定的 .NET 属性，`T` 是该绑定类型所支持的输入或输出类型。 `T` 也不能是 `out` 参数类型 （例如 `out JObject`）。 例如，移动应用表输出绑定支持 [6 种输出类型](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)，但对于 `T`，可仅使用 [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)。
    
下面的示例代码使用在运行时定义的 blob 路径创建[存储 blob 输出绑定](functions-bindings-storage-blob.md#storage-blob-output-binding)，然后将字符串写入此 blob。

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
以其原本方式，此代码会获取存储帐户连接字符串（即 `AzureWebJobsStorage`）的默认应用设置。 通过添加 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) 和将属性数组传递到 `BindAsync<T>()`，可指定要使用的自定义应用设置。 例如，

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
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| 事件中心 | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| 通知中心 | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| 服务总线 | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| 存储队列 | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 存储 blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 存储表 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions F# 开发人员参考](functions-reference-fsharp.md)
* [Azure Functions NodeJS 开发人员参考](functions-reference-node.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)


