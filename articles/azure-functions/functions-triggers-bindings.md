---
title: "Azure Functions 中的触发器和绑定"
description: "了解如何使用 Azure Functions 中的触发器和绑定将代码执行连接到联机事件和基于云的服务。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: a122271b5fdffd9db33a7dca5908e15f002041d7
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 触发器和绑定概念

本文提供有关 Azure Functions 中触发器和绑定的概念性概述。 此处介绍所有绑定和支持的语言通用的功能。

## <a name="overview"></a>概述

“触发器”定义函数的调用方式。 一个函数必须只有一个触发器。 触发器具有关联数据，该数据通常是触发函数的有效负载。

输入和输出“绑定”提供从代码内连接到数据的声明性方式。 绑定是可选项，一个函数可以有多个输入和输出绑定。 

借助触发器和绑定，可避免对正在使用的服务的详细信息进行硬编码。 函数接收函数参数中的数据（例如，队列消息内容）。 使用函数的返回值发送数据（例如，用于创建队列消息），返回值可能是 `out` 参数或[收集器对象](functions-reference-csharp.md#writing-multiple-output-values)。

使用 Azure 门户开发函数时，会在 function.json 文件中配置触发器和绑定。 门户提供此配置的 UI，但可通过更换为“高级编辑器”，直接编辑文件。

当使用 Visual Studio 开发一个用于创建类库的函数时，通过使用属性修饰方法和参数来配置触发器和绑定。

## <a name="supported-bindings"></a>支持的绑定

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

有关哪些绑定处于预览状态或已批准在生产环境中使用的信息，请参阅[支持的语言](supported-languages.md)。

## <a name="example-queue-trigger-and-table-output-binding"></a>示例：队列触发器和表输出绑定

假设希望在 Azure 队列存储中显示一条新消息时就将一个新行写入 Azure 表存储。 使用 Azure 队列存储触发器和 Azure 表存储输出绑定即可实现此方案。 

下面是用于这种方案的 function.json 文件。 

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 数组中的第一个元素是队列存储触发器。 `type` 和 `direction` 属性标识该触发器。 `name` 属性标识将接收队列消息内容的函数参数。 要监视的队列的名称位于 `queueName` 中，连接字符串位于由 `connection` 标识的应用设置中。

`bindings` 数组中的第二个元素是 Azure 表存储输出绑定。 `type` 和 `direction` 属性标识该绑定。 `name` 属性指定函数提供新表行的方式，在此例中是使用函数返回值来提供。 表格的名称位于 `tableName` 中，连接字符串位于由 `connection` 标识的应用设置中。

若要在 Azure 门户中查看和编辑 *function.json* 的内容，请单击函数“集成”选项卡上的“高级编辑器”选项。

> [!NOTE]
> `connection` 的值是包含连接字符串的应用设置的名称，而不是连接字符串本身的名称。 绑定使用应用设置中存储的连接字符串，以强制执行 function.json 不包含服务密钥这一最佳做法。

以下是适用于此触发器和绑定的 C# 脚本代码。 请注意，提供队列消息内容的参数的名称是 `order`；需使用此名称是因为 function.json 中的 `name` 属性值是 `order` 

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

可将同一 function.json 文件用于 JavaScript 函数：

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

在类库中，由特性提供这些触发器和绑定信息 &mdash; 队列和表名称、存储帐户、输入和输出 &mdash; 的函数参数：

```csharp
 public static class QueueTriggerTableOutput
 {
     [FunctionName("QueueTriggerTableOutput")]
     [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
     public static Person Run(
         [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order, 
         TraceWriter log)
     {
         return new Person() {
                 PartitionKey = "Orders",
                 RowKey = Guid.NewGuid().ToString(),
                 Name = order["Name"].ToString(),
                 MobileNumber = order["MobileNumber"].ToString() };
     }
 }

 public class Person
 {
     public string PartitionKey { get; set; }
     public string RowKey { get; set; }
     public string Name { get; set; }
     public string MobileNumber { get; set; }
 }
```

## <a name="binding-direction"></a>绑定方向

所有触发器和绑定在 *function.json* 文件中都有一个 `direction` 属性：

- 对于触发器，方向始终为 `in`
- 输入和输出绑定使用 `in` 和 `out`
- 某些绑定支持特殊方向 `inout`。 如果使用 `inout`，则“集成”选项卡中仅“高级编辑器”可用。

使用[类库中的特性](functions-dotnet-class-library.md)来配置触发器和绑定时，方向在特性构造函数中提供或推断自参数类型。

## <a name="using-the-function-return-type-to-return-a-single-output"></a>使用函数返回类型以返回单个输出

上述示例演示如何使用函数返回值向绑定提供输出，这通过为 `name` 属性使用特殊值 `$return` 在 function.json 中指定。 （仅受提供返回值的语言支持，如 C# 脚本、JavaScript 和 F#。）如果函数有多个输出绑定，`$return` 只可用于其中一个输出绑定。 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

以下示例演示如何在 C# 脚本、JavaScript 和 F# 中将返回类型与输出绑定结合使用。

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>绑定 dataType 属性

在 .NET中，使用参数类型来定义输入数据的数据类型。 例如，使用 `string` 绑定到队列触发器的文本、一个要读取为二进制内容的字节数组，以及一个要反序列化为 POCO 对象的自定义类型。

对于动态键入的语言（如 JavaScript），请在 function.json 文件中使用 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，将 `dataType` 设置为 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 的其他选项是 `stream` 和 `string`。

## <a name="resolving-app-settings"></a>解析应用设置

作为最佳做法，应使用应用设置（而不是配置文件）来管理密钥和连接字符串。 这会限制对这些密钥的访问，并可在公共源代码控制存储库中安全地存储 *function.json*。

应用设置在想要根据环境更改配置时也很有用。 例如，在测试环境中，可能想要监视不同的队列或 blob 存储容器。

只要一个值用百分号括起来（如 `%MyAppSetting%`），就会解析应用设置。 请注意，触发器和绑定的 `connection` 属性是一种特殊情况，该属性会自动将值解析为应用设置。 

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
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-metadata-properties"></a>触发器元数据属性

除了触发器提供的数据负载（如触发函数的队列消息），许多触发器还会提供其他元数据值。 这些值可用作 C# 和 F# 中的输入参数，或用作 JavaScript 中 `context.bindings` 对象的属性。 

例如，Azure 队列存储触发器支持以下属性：

* QueueTrigger - 如果字符串有效，将触发消息内容
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

这些元数据值可在 function.json 文件属性中访问。 例如，假设使用队列触发器，且队列消息中包含要读取的 blob 的名称。 在 function.json 文件中，可在 blob `path` 属性中使用 `queueTrigger` 元数据属性，如下面的示例中所示：

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

相应参考文章中会详细介绍每种触发器的元数据属性。 有关示例，请参阅[队列触发器元数据](functions-bindings-storage-queue.md#trigger---message-metadata)。 在门户“集成”选项卡的绑定配置区域下方的“文档”部分中，还提供了文档。  

## <a name="binding-expressions-and-patterns"></a>绑定表达式和模式

触发器和绑定的最强大功能之一是*绑定表达式*。 在绑定的配置中，可定义稍后可以在其他绑定或代码中使用的模式表达式。 触发器元数据还可以用于绑定表达式，如上述部分中所示。

例如，假设想要重设特定 blob 存储容器中的图像大小，类似于 Azure 门户“新建函数”页中的“图像调整器”模板（参见“示例”方案）。 

以下是 *function.json* 的定义：

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

请注意，`filename` 参数既可以在 blob 触发器定义中使用，也可以在 blob 输出绑定中使用。 此参数还可以用于函数代码。

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

类库中的特性同样能够使用绑定表达式和模式。 例如，下面是类库中一个调整映像大小的函数：

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
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

### <a name="create-guids"></a>创建 GUID

`{rand-guid}` 绑定表达式用于创建 GUID。 以下示例使用 GUID 创建唯一的 blob 名称： 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>当前时间

绑定表达式 `DateTime` 解析为 `DateTime.UtcNow`。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties"></a>绑定到自定义输入属性

绑定表达式还可以引用在触发器负载本身中定义的属性。 例如，可能想要通过 webhook 中提供的文件名动态绑定到 blob 存储文件。

例如，以下 *function.json* 使用触发器负载中名为 `BlobName` 的属性：

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

要在 C# 和 F# 中实现此目的，必须定义 POCO，它用于定义会在触发器负载中执行反序列化的字段。

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

在 JavaScript 中，会自动执行 JSON 反序列化，然后可以直接使用属性。

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

## <a name="configuring-binding-data-at-runtime"></a>在运行时配置绑定数据

在 C# 和其他 .NET 语言中，可以使用命令性绑定模式，而不是 function.json 和特性中的声明式绑定。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 若要了解详细信息，请参阅 C# 开发人员参考中的[通过命令性绑定在运行时进行绑定](functions-reference-csharp.md#imperative-bindings)。

## <a name="functionjson-file-schema"></a>function.json 文件架构

Function.json 文件架构可在 [http://json.schemastore.org/function](http://json.schemastore.org/function) 处获取。

## <a name="next-steps"></a>后续步骤

有关特定绑定的详细信息，请参阅以下文章：

- [HTTP 和 webhook](functions-bindings-http-webhook.md)
- [计时器](functions-bindings-timer.md)
- [队列存储](functions-bindings-storage-queue.md)
- [Blob 存储](functions-bindings-storage-blob.md)
- [表存储](functions-bindings-storage-table.md)
- [事件中心](functions-bindings-event-hubs.md)
- [服务总线](functions-bindings-service-bus.md)
- [Azure Cosmos DB](functions-bindings-cosmosdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [通知中心](functions-bindings-notification-hubs.md)
- [移动应用](functions-bindings-mobile-apps.md)
- [外部文件](functions-bindings-external-file.md)
