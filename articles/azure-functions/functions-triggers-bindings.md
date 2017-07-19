---
title: "使用 Azure Functions 中的触发器和绑定 | Microsoft 文档"
description: "了解如何使用 Azure Functions 中的触发器和绑定将代码执行连接到联机事件和基于云的服务。"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: cc41debb2523df77be4db05817a4c7ac55604439
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017


---

# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 触发器和绑定概念
借助 Azure Functions，可以编写通过“触发器”和“绑定”响应 Azure 中的事件和其他服务的代码。 本文是所有支持编程语言的触发器和绑定的概念性概述。 此处介绍所有绑定通用的功能。

## <a name="overview"></a>概述

触发器和绑定是一种声明性方式，用于定义函数的调用方式以及函数要使用的数据。 “触发器”定义函数的调用方式。 一个函数必须只有一个触发器。 触发器具有关联数据，该数据通常是触发函数的有效负载。 

输入和输出“绑定”提供从代码内连接到数据的声明性方式。 与触发器类似，可在函数配置中指定连接字符串和其他属性。 绑定是可选项，一个函数可以有多个输入和输出绑定。 

使用触发器和绑定，可以编写更通用的代码，不会对与代码交互的服务细节进行硬编码。 服务提供的数据只是函数代码的输入值。 若要将数据输出到其他服务（例如在 Azure 表存储中创建新行），可使用该方法的返回值。 或者，如果需要输出多个值，可使用帮助器对象。 触发器和绑定有 **name** 属性，该属性是代码中用于访问绑定的标识符。

可以在 Azure Functions 门户的“集成”选项卡中配置触发器和绑定。 实际上，UI 会修改函数目录中名为“function.json”的文件。 可以通过转到“高级编辑器”来编辑此文件。

下表显示受 Azure Functions 支持的触发器和绑定。 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>示例：队列触发器和表输出绑定

假设你希望在 Azure 队列存储中显示一条新消息时就将一个新行写入 Azure 表存储。 使用 Azure 队列触发器和表输出绑定接口即可实现此方案。 

队列触发器需要“集成”选项卡中的以下信息：

* 包含队列存储帐户连接字符串的应用设置的名称
* 队列名称
* 代码中用于读取队列消息内容的标识符，如 `order`。

若要写入 Azure 表存储，请使用含有以下详细信息的输出绑定：

* 包含表存储帐户连接字符串的应用设置的名称
* 表名称
* 代码中用于创建输出项的标识符，或函数的返回值。

绑定使用连接字符串的应用设置，以强制执行 *function.json* 不包含服务密钥的最佳做法。

然后，使用提供的标识符以在代码中与 Azure 存储集成。

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
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

以下是与上述代码对应的 *function.json*。 请注意，可以使用相同的配置，无需考虑函数实现所用的语言。

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
若要在 Azure 门户中查看和编辑 *function.json* 的内容，请单击函数“集成”选项卡上的“高级编辑器”选项。

有关与 Azure 存储集成的更多代码示例和详细信息，请参阅[适用于 Azure 存储的 Azure Functions 触发器和绑定](functions-bindings-storage.md)。

### <a name="binding-direction"></a>绑定方向

所有触发器和绑定都有 `direction` 属性：

- 对于触发器，方向始终为 `in`
- 输入和输出绑定使用 `in` 和 `out`
- 某些绑定支持特殊方向 `inout`。 如果使用 `inout`，则“集成”选项卡中仅“高级编辑器”可用。

## <a name="using-the-function-return-type-to-return-a-single-output"></a>使用函数返回类型以返回单个输出

上述示例演示如何使用函数返回值向绑定提供输出，这通过使用特殊名称参数 `$return` 来实现。 （仅受提供返回值的语言支持，如 C#、JavaScript 和 F#。）如果函数有多个输出绑定，`$return` 只可用于其中一个输出绑定。 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

以下示例演示如何在 C#、JavaScript 和 F# 中将返回类型与输出绑定结合使用。

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
    return json;
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

在 .NET中，使用类型来定义输入数据的数据类型。 例如，使用 `string` 绑定到队列触发器的文本和一个要读取为二进制内容的字节数组。

对于动态键入的语言（如 JavaScript），请在绑定定义中使用 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，请使用类型 `binary`：

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

以下示例是一个队列触发器，该触发器使用应用设置 `%input-queue-name%` 定义要触发的队列。

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

## <a name="trigger-metadata-properties"></a>触发器元数据属性

除了触发器提供的数据负载（如触发函数的队列消息），许多触发器还会提供其他元数据值。 这些值可用作 C# 和 F# 中的输入参数，或用作 JavaScript 中 `context.bindings` 对象的属性。 

例如，队列触发器支持以下属性：

* QueueTrigger - 如果字符串有效，将触发消息内容
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

相应参考主题中会详细介绍每种触发器的元数据属性。 在门户“集成”选项卡的绑定配置区域下方的“文档”部分中，还提供了文档。  

例如，由于 blob 触发器有一些延迟，因此可以使用队列触发器运行函数（请参阅 [Blob 存储触发器](functions-bindings-storage-blob.md#storage-blob-trigger)）。 队列消息将包含要触发的 blob 文件名。 使用 `queueTrigger` 元数据属性，可以全部在配置（而不是代码）中指定此行为。

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

触发器的元数据属性还可以用于其他绑定的*绑定表达式*，如以下部分中所述。

## <a name="binding-expressions-and-patterns"></a>绑定表达式和模式

触发器和绑定的最强大功能之一是*绑定表达式*。 在绑定内，可定义稍后可以在其他绑定或代码中使用的模式表达式。 触发器元数据还可以用于绑定表达式，如上述部分中的示例所示。

例如，假设想要重设特定 blob 存储容器中的图像大小，类似于“新建函数”页中的“图像调整器”模板。 转到“新建函数”-> 语言“C#”-> 方案“示例” -> “ImageResizer-CSharp”。 

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


### <a name="random-guids"></a>随机 GUID
Azure Functions 为在绑定中通过 `{rand-guid}` 绑定表达式生成 GUID 提供了一种便捷语法。 以下示例使用此语法生成唯一的 blob 名称： 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>当前时间

可使用解析为 `DateTime.UtcNow` 的绑定表达式 `DateTime`。

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>绑定到绑定表达式中的自定义输入属性

绑定表达式还可以引用在触发器负载本身中定义的属性。 例如，可能想要通过 webhook 中提供的文件名动态绑定到 blob 存储文件。

例如，以下 *function.json* 使用触发器负载中名为 `BlobName` 的属性：

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
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

若要在 C# 和 F# 中实现此目的，必须定义 POCO，它用于定义将在触发器负载中执行反序列化的字段。

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

在 JavaScript 中，将自动执行 JSON 反序列化，然后可以直接使用属性。

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

在 C# 和其他 .NET 语言中，可使用命令性绑定模式，而不是 function.json 中的声明式绑定。 当绑定参数需要在运行时（而非在设计时）计算时，命令性绑定很有用。 若要了解详细信息，请参阅 C# 开发人员参考中的[通过命令性绑定在运行时进行绑定](functions-reference-csharp.md#imperative-bindings)。

## <a name="next-steps"></a>后续步骤
有关特定绑定的详细信息，请参阅以下文章：

- [HTTP 和 webhook](functions-bindings-http-webhook.md)
- [计时器](functions-bindings-timer.md)
- [队列存储](functions-bindings-storage-queue.md)
- [Blob 存储](functions-bindings-storage-blob.md)
- [表存储](functions-bindings-storage-table.md)
- [事件中心](functions-bindings-event-hubs.md)
- [服务总线](functions-bindings-service-bus.md)
- [Cosmos DB](functions-bindings-documentdb.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [通知中心](functions-bindings-notification-hubs.md)
- [移动应用](functions-bindings-mobile-apps.md)
- [外部文件](functions-bindings-external-file.md)

