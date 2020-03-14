---
title: Azure Functions 绑定表达式和模式
description: 了解如何基于常见模式创建不同的 Azure Functions 绑定表达式。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 2d0cf18de09932c5d66e269a85919f4d85383c5b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277643"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions 绑定表达式模式

[触发器和绑定](./functions-triggers-bindings.md)的最强大功能之一是*绑定表达式*。 在 *function.json* 文件、函数参数和代码中，可以使用表达式解析为各种源的值。

大多数表达式的标识方式是将其包装在大括号中。 例如，在队列触发器函数中，`{queueTrigger}` 解析为队列消息文本。 如果 Blob 输出绑定的 `path` 属性为 `container/{queueTrigger}`，并且函数由队列消息 `HelloWorld` 触发，则创建名为 `HelloWorld` 的 Blob。

绑定表达式的类型

* [应用设置](#binding-expressions---app-settings)
* [触发器文件名](#trigger-file-name)
* [触发器元数据](#trigger-metadata)
* [JSON 有效负载](#json-payloads)
* [新 GUID](#create-guids)
* [当前日期和时间](#current-time)

## <a name="binding-expressions---app-settings"></a>绑定表达式 - 应用设置

作为最佳做法，应使用应用设置（而不是配置文件）来管理密钥和连接字符串。 这会限制对这些密钥的访问，并可在公共源代码管理存储库中安全存储 *function.json* 等文件。

应用设置在想要根据环境更改配置时也很有用。 例如，在测试环境中，可能想要监视不同的队列或 blob 存储容器。

应用设置绑定表达式的标识方式不同于其他绑定表达式：它们包装在百分比号而不是大括号中。 例如，如果 Blob 输出绑定路径为 `%Environment%/newblob.txt`，`Environment` 应用设置值为 `Development`，则会在 `Development` 容器中创建 Blob。

当函数在本地运行时，应用设置值来自 *local.settings.json* 文件。

请注意，触发器和绑定的 `connection` 属性是一种特殊情况，该属性会自动将值解析为应用设置（不带百分比号）。 

以下示例是一个 Azure 队列存储触发器，该触发器使用应用设置 `%input-queue-name%` 定义要触发的队列。

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

可在类库中使用此相同的方法：

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>触发器文件名

Blob 触发器的 `path` 可以是一种模式，用于引用其他绑定和函数代码中的触发 Blob 的名称。 该模式还可包含筛选条件，用于指定哪些 Blob 可以触发函数调用。

例如，在以下 Blob 触发器绑定中，`path` 模式为 `sample-images/{filename}`（创建名为 `filename` 的绑定表达式）：

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

然后，可以在输出绑定中使用表达式 `filename`，用于指定所创建的 Blob 的名称：

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

函数代码可以使用 `filename` 作为参数名称来访问相同的值：

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

类库中的特性同样能够使用绑定表达式和模式。 在以下示例中，特性构造函数参数的值与前面 `path`function.json*示例中的* 值相同： 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

还可以为文件名的各个部分创建表达式。 在下面的示例中，仅对与模式匹配的文件名触发函数： `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

有关如何在 Blob 路径字符串中使用表达式和模式的详细信息，请参阅[存储 Blob 绑定参考](functions-bindings-storage-blob.md)。

## <a name="trigger-metadata"></a>触发器元数据

除了触发器提供的数据有效负载（如触发函数的队列消息内容），许多触发器还会提供其他元数据值。 这些值可用作 C# 和 F# 中的输入参数，或用作 JavaScript 中 `context.bindings` 对象的属性。 

例如，Azure 队列存储触发器支持以下属性：

* QueueTrigger - 如果字符串有效，将触发消息内容
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

这些元数据值可在 function.json 文件属性中访问。 例如，假设使用队列触发器，且队列消息中包含要读取的 blob 的名称。 在 function.json 文件中，可在 blob `queueTrigger` 属性中使用 `path` 元数据属性，如下面的示例中所示：

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

相应参考文章中会详细介绍每种触发器的元数据属性。 有关示例，请参阅[队列触发器元数据](functions-bindings-storage-queue-trigger.md#message-metadata)。 在门户“集成”选项卡的绑定配置区域下方的“文档”部分中，还提供了文档。  

## <a name="json-payloads"></a>JSON 有效负载

当触发器有效负载为 JSON 时，可以在相同的函数和函数代码中，引用其他绑定的配置中的相应属性。

以下示例演示一个接收 JSON 格式 Blob 名称的 Webhook 函数的 *function.json* 文件：`{"BlobName":"HelloWorld.txt"}`。 Blob 输入绑定读取 Blob，HTTP 输出绑定在 HTTP 响应中返回 Blob 内容。 可以看到，Blob 输入绑定通过直接引用 `BlobName` 属性来获取 Blob 名称 (`"path": "strings/{BlobName}"`)

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

要在 C# 和 F# 中运行此代码，需要使用一个类来定义要反序列化的字段，如以下示例中所示：

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

在 JavaScript 中，会自动执行 JSON 反序列化。

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>点表示法

如果 JSON 有效负载中的某些属性是包含属性的对象，可以使用点表示法直接引用这些对象。 例如，假设 JSON 如下所示：

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

可以直接以 `FileName` 的形式引用 `BlobName.FileName`。 使用此 JSON 格式时，上述示例中的 `path` 属性如下所示：

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

在 C# 中，需要两个类：

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>创建 GUID

`{rand-guid}` 绑定表达式用于创建 GUID。 `function.json` 文件中的以下 Blob 路径创建名称类似于 *50710cb5-84b9-4d87-9d83-a03d6976a682.txt* 的 Blob。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>当前时间

绑定表达式 `DateTime` 解析为 `DateTime.UtcNow`。 `function.json` 文件中的以下 Blob 路径创建名称类似于 *2018-02-16T17-59-55Z.txt* 的 Blob。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>在运行时绑定

在 C# 和其他 .NET 语言中，可以使用命令性绑定模式，而不是 function.json和特性中的声明式绑定。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 若要了解详细信息，请参阅 [C# 开发人员参考](functions-dotnet-class-library.md#binding-at-runtime)或 [C# 脚本开发人员参考](functions-reference-csharp.md#binding-at-runtime)。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用 Azure 函数返回值](./functions-bindings-return-value.md)
