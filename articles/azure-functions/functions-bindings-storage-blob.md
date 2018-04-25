---
title: Azure Functions 的 Azure Blob 存储绑定
description: 了解如何在 Azure Functions 中使用 Azure Blob 存储触发器和绑定。
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/12/2018
ms.author: glenga
ms.openlocfilehash: eccaf205ae4705848b591442ca0fdb2aab44b9c6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure Blob 存储绑定

本文介绍如何在 Azure Functions 中使用 Azure Blob 存储绑定。 Azure Functions 支持 Blob 的触发器、输入和输出绑定。 本文各部分分别介绍每种绑定：

* [blob 触发器](#trigger)
* [blob 输入绑定](#input)
* [blob 输出绑定](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> blob 触发器不支持[仅限 Blob 的存储帐户](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。 Blob 存储触发器需要使用常规用途存储帐户。 对于输入和输出绑定，可以使用仅限 blob 的存储帐户。

## <a name="packages"></a>包

[Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包中提供了 Blob 存储绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>触发器

检测到新的或更新的 Blob 时，可以使用 Blob 存储触发器来启动某个函数。 Blob 内容会作为输入提供给函数。

> [!NOTE]
> 在消耗计划中使用 Blob 触发器时，函数应用处于空闲状态后，处理新 Blob 的过程中可能会出现长达 10 分钟的延迟。 函数应用运行后，就会立即处理 Blob。 若要避免此初始延迟，请考虑以下选项之一：
> - 使用已启用“始终可用”的应用服务计划。
> - 使用另一种机制来触发 Blob 处理，例如包含 Blob 名称的队列消息。 有关示例，请参阅[本文后面部分的 blob 输入/输出绑定示例](#input---example)。

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [C#](#trigger---c-example)
* [C# 脚本 (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示在 `samples-workitems` 容器中添加或更新 blob 时写入日志的 [C# 函数](functions-dotnet-class-library.md)。

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](functions-triggers-bindings.md#binding-expressions-and-patterns)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#trigger---blob-name-patterns)。

有关 `BlobTrigger` 特性的详细信息，请参阅[触发器 - 特性](#trigger---attributes)。

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 function.json 文件中的一个 Blob 触发器绑定以及使用该绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 在 `samples-workitems` 容器中添加或更新 Blob 时，该函数会写入日志。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](functions-triggers-bindings.md#binding-expressions-and-patterns)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#trigger---blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的[配置](#trigger---configuration)部分。

下面是绑定到 `Stream` 的 C# 脚本代码：

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

下面是绑定到 `CloudBlockBlob` 的 C# 脚本代码：

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例显示了 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [JavaScript 代码](functions-reference-node.md)。 在 `samples-workitems` 容器中添加或更新 Blob 时，该函数会写入日志。

*function.json* 文件如下所示：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

blob 触发器路径 `samples-workitems/{name}` 中的字符串 `{name}` 会创建一个[绑定表达式](functions-triggers-bindings.md#binding-expressions-and-patterns)，可以在函数代码中使用它来访问触发 blob 的文件名。 有关详细信息，请参阅本文下文中的 [Blob 名称模式](#trigger---blob-name-patterns)。

有关 *function.json* 文件属性的详细信息，请参阅解释了这些属性的[配置](#trigger---configuration)部分。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes"></a>触发器 - 特性

对于 [C# 类库](functions-dotnet-class-library.md)，请使用以下属性来配置 blob 触发器：

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  该特性的构造函数采用一个表示要监视的容器的路径字符串，并选择性地采用某种 [Blob 名称模式](#trigger---blob-name-patterns)。 下面是一个示例：

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  有关完整示例，请参阅[触发器 - C# 示例](#trigger---c-example)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

要使用的存储帐户按以下顺序确定：

* `BlobTrigger` 特性的 `Connection` 属性。
* 作为 `BlobTrigger` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* 函数应用的默认存储帐户（“AzureWebJobsStorage”应用设置）。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件和 `BlobTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `blobTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 [用法](#trigger---usage)部分中已阐述异常。 |
|**name** | 不适用 | 表示函数代码中的 Blob 的变量的名称。 | 
|**路径** | **BlobPath** |要监视的容器。  可以是某种 [Blob 名称模式](#trigger-blob-name-patterns)。 | 
|**连接** | **Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[仅限 Blob 的存储帐户](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>触发器 - 用法

在 C# 和 C# 脚本中，可以为触发 blob 使用以下参数类型：

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* 可序列化为 JSON 的 POCO
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> function.json 中需有 "inout" 绑定 `direction` 或 C# 类库中需有 `FileAccess.ReadWrite`。

由于整个 Blob 内容都会加载到内存中，因此，只有当 Blob 较小时才建议绑定到 `string`、`Byte[]` 或 POCO。 平时，最好使用 `Stream` 或 `CloudBlockBlob` 类型。 有关详细信息，请参阅本文后面的[并发和内存使用情况](#trigger---concurrency-and-memory-usage)。

在 JavaScript 中，可以使用 `context.bindings.<name from function.json>` 访问输入 Blob 数据。

## <a name="trigger---blob-name-patterns"></a>触发器 - Blob 名称模式

可以在 *function.json* 的 `path` 属性中或者在 `BlobTrigger` 特性构造函数中指定 Blob 名称模式。 名称模式可以是[筛选器或绑定表达式](functions-triggers-bindings.md#binding-expressions-and-patterns)。 以下部分提供了有关示例。

### <a name="get-file-name-and-extension"></a>获取文件名和扩展名

以下示例演示如何分别绑定到 Blob 文件名和扩展名：

```json
"path": "input/{blobname}.{blobextension}",
```
如果 Blob 名为 *original-Blob1.txt*，则函数代码中 `blobname` 和 `blobextension` 变量的值为 *original-Blob1* 和 *txt*。

### <a name="filter-on-blob-name"></a>按 Blob 名称筛选

以下示例仅根据 `input` 容器中以字符串“original-”开头的 Blob 触发：

```json
"path": "input/original-{name}",
```
 
如果 Blob 名称为 *original-Blob1.txt*，则函数代码中 `name` 变量的值为 `Blob1`。

### <a name="filter-on-file-type"></a>按文件类型筛选

以下示例仅根据 *.png* 文件触发：

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>按文件名中的大括号筛选

若要查找文件名中的大括号，请使用两个大括号将大括号转义。 以下示例筛选名称中包含大括号的 Blob：

```json
"path": "images/{{20140101}}-{name}",
```

如果 Blob 名为 *{20140101}-soundfile.mp3*，则函数代码中的 `name` 变量值为 *soundfile.mp3*。 

## <a name="trigger---metadata"></a>触发器 - 元数据

Blob 触发器提供了几个元数据属性。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 这些值的语义与 [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) 类型相同。

|属性  |Type  |说明  |
|---------|---------|---------|
|`BlobTrigger`|`string`|触发 Blob 的路径。|
|`Uri`|`System.Uri`|主位置的 blob 的 URI。|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Blob 的系统属性。 |
|`Metadata` |`IDictionary<string,string>`|Blob 的用户定义元数据。|

例如，以下 C# 脚本和 JavaScript 示例会记录触发 blob 的路径，包括容器：

```csharp
public static void Run(string myBlob, string blobTrigger, TraceWriter log)
{
    log.Info($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>触发器 - Blob 回执

Azure Functions 运行时确保没有为相同的新 blob 或更新 blob 多次调用 blob 触发器函数。 为了确定是否已处理给定的 blob 版本，它会维护 blob 回执。

Azure Functions 将 Blob 回执存储在函数应用的 Azure 存储帐户中名为 azure-webjobs-hosts 的容器中（由 `AzureWebJobsStorage` 应用设置定义）。 Blob 回执包含以下信息：

* 触发的函数（"&lt;function app name>.Functions.&lt;function name>"，例如："MyFunctionApp.Functions.CopyBlob"）
* 容器名称
* Blob 类型（"BlockBlob" 或 "PageBlob"）
* Blob 名称
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

若要强制重新处理某个 blob，可从 azure-webjobs-hosts 容器中手动删除该 blob 的 blob 回执。

## <a name="trigger---poison-blobs"></a>触发器 - 有害 Blob

当给定 blob 的 blob 触发函数失败时，Azure Functions 将默认重试该函数共计 5 次。 

如果 5 次尝试全部失败，Azure Functions 会将消息添加到名为 webjobs-blobtrigger-poison 的存储队列。 有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 &lt;function app name>.Functions.&lt;function name>）
* BlobType（"BlockBlob" 或 "PageBlob"）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

## <a name="trigger---concurrency-and-memory-usage"></a>触发器 - 并发和内存使用情况

Blob 触发器可在内部使用队列，因此并发函数调用的最大数量受 [host.json 中的队列配置](functions-host-json.md#queues)控制。 默认设置会将并发限制到 24 个调用。 此限制分别应用于使用 blob 触发器的函数。

[消耗计划](functions-scale.md#how-the-consumption-plan-works)将虚拟机 (VM) 上的函数应用限制为 1.5 GB 内存。 内存由每个并发执行函数实例和函数运行时本身使用。 如果 blob 触发的函数将整个 blob 加载到内存中，该函数使用的仅用于 blob 的最大内存为 24 * 最大 blob 大小。 例如，包含 3 个由 blob 触发的函数的函数应用和默认设置，其每 VM 最大并发为 3*24 = 72 个函数调用。

JavaScript 函数会将整个 blob 加载到内存中，并且如果绑定到 `string`、`Byte[]` 或 POCO，则 C# 函数也会如此。

## <a name="trigger---polling"></a>触发器 - 轮询

如果受监视的 blob 容器包含 10,000 多个 blob，则 Functions 运行时将扫描日志文件，监视新的或更改的 blob。 此过程可能会导致延迟。 创建 blob 之后数分钟或更长时间内可能仍不会触发函数。 此外，[将“尽力”创建存储日志](/rest/api/storageservices/About-Storage-Analytics-Logging)。 不保证捕获所有事件。 在某些情况下可能会遗漏某些日志。 如果需要更快或更可靠的 blob 处理，在创建 blob 时，请考虑创建[队列消息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然后，使用[队列触发器](functions-bindings-storage-queue.md)而不是 Blob 触发器来处理 Blob。 另一个选项是使用事件网格；请参阅教程[使用事件网格自动调整上传图像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)。

## <a name="input"></a>输入

使用 blob 存储输入绑定读取 blob。

## <a name="input---example"></a>输入 - 示例

参阅语言特定的示例：

* [C#](#input---c-example)
* [C# 脚本 (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-example"></a>输入 - C# 示例

以下示例演示使用一个队列触发器和一个 blob 输入绑定的 [C# 函数](functions-dotnet-class-library.md)。 队列消息包含该 blob 的名称，函数记录该 blob 的大小。

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>输入 - C# 脚本示例

<!--Same example for input and output. -->

以下示例演示 function.json 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 此函数创建文本 blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>输入 - JavaScript 示例

<!--Same example for input and output. -->

下面的示例展示了 function.json 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [JavaScript 代码](functions-reference-node.md)。 该函数创建 Blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[配置](#input---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input---attributes"></a>输入 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)。

该特性的构造函数采用 Blob 的路径，以及一个表示读取或写入的 `FileAccess` 参数，如以下示例中所示：

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[触发器 - 特性](#trigger---attributes)。

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 *function.json* 文件和 `Blob` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `blob`。 |
|**direction** | 不适用 | 必须设置为 `in`。 [用法](#input---usage)部分中已阐述异常。 |
|**name** | 不适用 | 表示函数代码中的 Blob 的变量的名称。|
|**路径** |**BlobPath** | Blob 的路径。 | 
|**连接** |**Connection**| 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[仅限 Blob 的存储帐户](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。|
|不适用 | **Access** | 表示是要读取还是写入。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>输入 - 用法

在 C# 和 C# 脚本中，可以为 blob 输入绑定使用以下参数类型：

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> function.json 中需有 "inout" 绑定 `direction` 或 C# 类库中需有 `FileAccess.ReadWrite`。

由于整个 Blob 内容都会加载到内存中，因此，只有当 Blob 较小时才建议绑定到 `string` 或 `Byte[]`。 平时，最好使用 `Stream` 或 `CloudBlockBlob` 类型。 有关详细信息，请参阅本文前文中的[并发和内存使用情况](#trigger---concurrency-and-memory-usage)。

在 JavaScript 中，可以使用 `context.bindings.<name from function.json>` 访问 Blob 数据。

## <a name="output"></a>输出

使用 blob 存储输出绑定写入 blob。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [C#](#output---c-example)
* [C# 脚本 (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示使用一个 blob 触发器和两个 blob 输出绑定的 [C# 函数](functions-dotnet-class-library.md)。 在 *sample-images* 容器中创建映像 Blob 时，会触发该函数。 该函数创建该映像 Blob 的小型和中型副本。 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

<!--Same example for input and output. -->

以下示例演示 function.json 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 此函数创建文本 blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[配置](#output---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

<!--Same example for input and output. -->

下面的示例展示了 function.json 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [JavaScript 代码] (functions-reference-node.md)。 该函数创建 Blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[配置](#output---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="output---attributes"></a>输出 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)。

该特性的构造函数采用 Blob 的路径，以及一个表示读取或写入的 `FileAccess` 参数，如以下示例中所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output---c-example)。

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[触发器 - 特性](#trigger---attributes)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件和 `Blob` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `blob`。 |
|**direction** | 不适用 | 对于输出绑定，必须设置为 `out`。 [用法](#output---usage)部分中已阐述异常。 |
|**name** | 不适用 | 表示函数代码中的 Blob 的变量的名称。  设置为 `$return` 可引用函数返回值。|
|**路径** |**BlobPath** | Blob 的路径。 | 
|**连接** |**Connection**| 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[仅限 Blob 的存储帐户](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。|
|不适用 | **Access** | 表示是要读取还是写入。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

在 C# 和 C# 脚本中，可绑定到以下类型，以编写 blob：

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> function.json 中需有 "in" 绑定 `direction` 或 C# 类库中需有 `FileAccess.Read`。

<sup>2</sup> function.json 中需有 "inout" 绑定 `direction` 或 C# 类库中需有 `FileAccess.ReadWrite`。

在异步函数中，请使用返回值或 `IAsyncCollector` 而非 `out` 参数。

由于整个 Blob 内容都会加载到内存中，因此，只有当 Blob 较小时才建议绑定到 `string` 或 `Byte[]`。 平时，最好使用 `Stream` 或 `CloudBlockBlob` 类型。 有关详细信息，请参阅本文前文中的[并发和内存使用情况](#trigger---concurrency-and-memory-usage)。


在 JavaScript 中，可以使用 `context.bindings.<name from function.json>` 访问 Blob 数据。

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 |  引用 |
|---|---|
| Blob | [Blob 错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob、表、队列 |  [存储错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob、表、队列 |  [故障排除](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用 Blob 存储触发器的快速入门](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
