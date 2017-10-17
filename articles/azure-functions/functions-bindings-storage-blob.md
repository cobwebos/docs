---
title: "Azure Functions Blob 存储绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 存储触发器和绑定。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob 存储绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和使用 Azure Blob 存储绑定。 Azure Functions 支持 Azure Blob 存储使用触发器、输入以及输出绑定。 有关所有绑定中提供的功能，请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> 不支持[仅 blob 存储帐户](../storage/common/storage-create-storage-account.md#blob-storage-accounts)。 Blob 存储触发器和绑定需要使用常规用途存储帐户。 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Blob 存储触发器和绑定

如果使用 Azure Blob 存储触发器，在检测到新的或更新的 blob 时，将调用函数代码。 Blob 内容会作为输入提供给函数。

使用 Functions 门户中的“集成”选项卡定义 Blob 存储触发器。 门户在 function.json 中的“绑定”部分创建以下定义：

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

定义 Blob 输入和输出绑定时，将 `blob` 作为绑定类型：

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* `path` 属性支持绑定表达式和筛选器参数。 请参阅[名称模式](#pattern)。
* `connection` 属性必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，选择存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。

> [!NOTE]
> 在消耗计划中使用 Blob 触发器时，函数应用处于空闲状态后，处理新 Blob 的过程中可能会出现长达 10 分钟的延迟。 函数应用运行后，就会立即处理 Blob。 若要避免此初始延迟，请考虑以下选项之一：
> - 使用已启用“始终可用”的应用服务计划。
> - 使用另一种机制来触发 Blob 处理，例如包含 Blob 名称的队列消息。 有关示例，请参阅[具有 Blob 输入绑定的队列触发器](#input-sample)。

<a name="pattern"></a>

### <a name="name-patterns"></a>名称模式
可以在 `path` 属性中指定 Blob 名称模式，它可以是一个筛选器或绑定表达式。 请参阅[绑定表达式和模式](functions-triggers-bindings.md#binding-expressions-and-patterns)。

例如，若要筛选以字符串 "original" 开头的 blob，请使用以下定义。 此路径会在输入容器中找到名为 original-Blob1.txt 的 blob，并且函数代码中 `name` 变量的值为 `Blob1`。

```json
"path": "input/original-{name}",
```

若要单独绑定到 blob 文件名和扩展名，请使用两种模式。 此路径还会找到名为 original-Blob1.txt 的 blob，并且函数代码中 `blobname` 和 `blobextension` 变量的值为 original-Blob1 和 txt 。

```json
"path": "input/{blobname}.{blobextension}",
```

可通过使用文件扩展名的固定值限制 blob 的文件类型。 例如，若仅需触发 .png 文件，请使用以下模式：

```json
"path": "samples/{name}.png",
```

大括号在名称模式中为特殊字符。 若要指定名称中包含大括号的 blob 名称，可使用两个大括号对大括号进行转义。 以下示例会在映像容器中找到名为 {20140101}-soundfile.mp3 的 blob，函数代码中的 `name` 变量值将为 soundfile .mp3。 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>触发器元数据

Blob 触发器提供了几个元数据属性。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 这些值和 [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) 具有相同的语义。

- BlobTrigger。 键入 `string`。 触发 blob 路径
- Uri。 键入 `System.Uri`。 主位置的 blob 的 URI。
- Properties。 键入 `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`。 Blob 的系统属性。
- Metadata。 键入 `IDictionary<string,string>`。 Blob 的用户定义元数据。

<a name="receipts"></a>

### <a name="blob-receipts"></a>Blob 回执
Azure Functions 运行时确保没有为相同的新 blob 或更新 blob 多次调用 blob 触发器函数。 为了确定是否已处理给定的 blob 版本，它会维护 blob 回执。

Azure Functions 将 Blob 回执存储在函数应用的 Azure 存储帐户中名为 azure-webjobs-hosts 的容器中（由 `AzureWebJobsStorage` 应用设置定义）。 Blob 回执包含以下信息：

* 触发的函数（"&lt;function app name>.Functions.&lt;function name>"，例如："MyFunctionApp.Functions.CopyBlob"）
* 容器名称
* Blob 类型（"BlockBlob" 或 "PageBlob"）
* Blob 名称
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

若要强制重新处理某个 blob，可从 azure-webjobs-hosts 容器中手动删除该 blob 的 blob 回执。

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>处理有害 blob
当给定 blob 的 blob 触发函数失败时，Azure Functions 将默认重试该函数共计 5 次。 

如果 5 次尝试全部失败，Azure Functions 会将消息添加到名为 webjobs-blobtrigger-poison 的存储队列。 有害 Blob 的队列消息是包含以下属性的 JSON 对象：

* FunctionId（格式为 &lt;function app name>.Functions.&lt;function name>）
* BlobType（"BlockBlob" 或 "PageBlob"）
* ContainerName
* BlobName
* ETag（blob 版本标识符，例如："0x8D1DC6E70A277EF"）

### <a name="blob-polling-for-large-containers"></a>大容器的 blob 轮询
如果受监视的 blob 容器包含 10,000 多个 blob，则 Functions 运行时将扫描日志文件，监视新的或更改的 blob。 此过程不是实时过程。 创建 blob 之后数分钟或更长时间内可能仍不会触发函数。 此外，[将“尽力”创建存储日志](/rest/api/storageservices/About-Storage-Analytics-Logging)。 并不能保证捕获了所有事件。 在某些情况下可能会遗漏某些日志。 如果需要更快或更可靠的 blob 处理，在创建 blob 时，请考虑创建[队列消息](../storage/queues/storage-dotnet-how-to-use-queues.md)。 然后，使用[队列触发器](functions-bindings-storage-queue.md)而不是 blob 触发器来处理 blob。

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>使用 blob 触发器和输入绑定
在 .NET 函数中，使用 `Stream paramName` 等方法参数访问 blob 数据。 其中，`paramName` 是在[触发器配置](#trigger)中指定的值。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入 blob 数据。

在.NET 中，可以绑定到以下列表中的任何类型。 如果用作输入绑定，其中某些类型需要 function.json 中的 `inout` 绑定方向。 此方向不受标准编辑器支持，因此必须使用高级编辑器。

* `TextReader`
* `Stream`
* `ICloudBlob`（需要 "inout" 绑定方向）
* `CloudBlockBlob`（需要 "inout" 绑定方向）
* `CloudPageBlob`（需要 "inout" 绑定方向）
* `CloudAppendBlob`（需要 "inout" 绑定方向）

如果需要文本 blob，还可以绑定到 .NET `string` 类型。 由于整个 blob 内容都会加载到内存，因此仅建议 blob 比较小时才这么做。 平时，最好使用 `Stream` 或 `CloudBlockBlob` 类型。

## <a name="trigger-sample"></a>触发器示例
假设有以下定义 blob 存储触发器的 function.json：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

请参阅特定于语言的示例，该示例记录添加到受监视容器中每个 blob 的内容。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>C# 中的 blob 触发器示例 #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Node.js 中的触发器示例

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>使用 Blob 输出绑定

在 .NET 函数中，应使用函数签名中的 `out string` 参数，或者使用以下列表中的一种类型。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输出 blob。

在 .NET 函数中，可输出到以下任意类型：

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>带 blob 输入和输出的队列触发器示例
假设有以下 function.json，其定义了[队列存储触发器](functions-bindings-storage-queue.md)、blob 存储输入和 blob 存储输出。 请注意 `queueTrigger` 元数据属性的使用。 在 blob 输入和输出 `path` 属性中：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

请参阅将输入 blob 复制到输出 blob 的语言特定示例。

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>C# 中的 blob 绑定示例 #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Node.js 中的 blob 绑定示例

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


