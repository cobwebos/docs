---
title: "Azure Functions 队列存储绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 存储触发器和绑定。"
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 94d1d4c243bede354ae3deba7fbf5da0652567cb
ms.openlocfilehash: f39f674bf576a2661a0e03710b9005b0515b3aa5
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions 队列存储绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和编码 Azure 队列存储绑定。 Azure Functions 支持 Azure 队列的触发器和输出绑定。 有关所有绑定中提供的功能，请参阅 [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>队列存储触发器
通过 Azure 队列存储触发器可监视队列存储的新消息并对其做出反应。 

使用 Functions 门户中的“集成”选项卡定义队列触发器。 门户在 function.json 中的“绑定”部分创建以下定义：

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* `connection` 属性必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，选择存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。

可在 [host.json 文件](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)中包含其他设置以进一步微调队列存储触发器。 例如，可以更改 host.json 中的队列轮询间隔。

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>使用队列触发器
在 Node.js 函数中，使用 `context.bindings.<name>`访问队列数据。


在 .NET 函数中，使用 `CloudQueueMessage paramName` 等方法参数访问队列有效负载。 其中，`paramName` 是在[触发器配置](#trigger)中指定的值。 队列消息可以反序列化为以下任何类型：

* POCO 对象。 如果队列有效负载是一个 JSON 对象，请使用此选项。 函数运行时将有效负载反序列化到 POCO 对象。 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>队列触发器元数据
队列触发器提供了几个元数据属性。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 这些值和 [`CloudQueueMessage`] 具有相同的语义。

* QueueTrigger - 队列有效负载（如果是有效字符串）
* DequeueCount - `int` 类型。 此消息取消排队的次数。
* ExpirationTime - `DateTimeOffset?` 类型。 消息过期的时间。
* Id - `string` 类型。 队列消息 ID。
* InsertionTime - `DateTimeOffset?` 类型。 消息添加到队列的时间。
* NextVisibleTime - 类型 `DateTimeOffset?`。 消息下一次可见的时间。
* PopReceipt - `string` 类型。 消息的 pop 接收方。

请参阅如何使用[触发器示例](#triggersample)中的队列元数据。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>触发器示例
假设采用以下 function.json，其定义队列触发器：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

请参阅可检索和记录队列元数据的特定于语言的示例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的触发器示例 #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的触发器示例

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>处理有害队列消息
队列触发函数失败时，Azure Functions 对于给定队列消息重试该函数最多 5 次（包括第一次尝试）。 如果 5 次尝试全部失败，函数运行时会将消息添加到名为 &lt;originalqueuename>-poison 的队列存储。 你可以编写一个函数来处理有害队列中的消息，并记录这些消息，或者发送需要注意的通知。 

若要手动处理有害消息，请检查队列消息的 `dequeueCount`（请参阅[队列触发器元数据](#meta)）。

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>队列存储输出绑定
借助 Azure 队列存储输出绑定，用户可将消息写入队列。 

使用 Functions 门户中的“集成”选项卡定义队列输出绑定。 门户在 function.json 中的“绑定”部分创建以下定义：

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* `connection` 属性必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，选择存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>使用队列输出绑定
在 Node.js 函数中，使用 `context.bindings.<name>` 访问输出队列。

在 .NET 函数中，可输出到以下任意类型。 当有一个类型参数 `T` 时，`T` 必须为受支持的输出类型之一，例如 `string` 或者 POCO。

* `out T`（序列化为 JSON）
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

还可以使用方法返回类型作为输出绑定。

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>队列输出示例
以下 function.json 通过队列输出绑定定义了 HTTP 触发器：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

请参阅输出附带传入的 HTTP 有效负载的队列消息的特定语言示例。

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>C# 中的队列输出示例 #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

若要发送多条消息，请使用 `ICollector`：

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Node.js 中的队列输出示例

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

或者，发送多条消息，

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>后续步骤

有关使用队列存储触发器和绑定的函数示例，请参阅[创建与 Azure 服务连接的 Azure Function](functions-create-an-azure-connected-function.md)。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage

