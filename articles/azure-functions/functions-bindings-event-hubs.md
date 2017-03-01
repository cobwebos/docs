---
title: "Azure Functions 事件中心绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 事件中心绑定。"
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/02/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: c8e9f9709d13295c9414e525f1f60abf0d0accb7
ms.openlocfilehash: 0bfbfd3828aacdee0b6630ced034f2c1e0451abd


---
# <a name="azure-functions-event-hub-bindings"></a>Azure Functions 事件中心绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何对 Azure Functions 的 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)绑定进行配置和编写代码。
Azure Functions 支持事件中心的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

如果不熟悉 Azure 事件中心，请参阅 [Azure 事件中心概述](../event-hubs/event-hubs-what-is-event-hubs.md)。

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>事件中心触发器
使用事件中心触发器来响应发送到事件中心事件流的事件。 若要设置触发器，必须具有事件中心的读取访问权限。

函数的事件中心触发器使用 function.json 的 `bindings` 数组中的以下 JSON 对象：

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the Event Hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup` 是一个可选属性，用于设置[使用者组](../event-hubs/event-hubs-what-is-event-hubs.md#event-consumers)，该组用于订阅事件中心中的事件。 如果将其省略，则会使用 `$Default` 使用者组。  
`connection` 必须是应用设置的名称，该名称中包含事件中心命名空间的连接字符串。
单击“命名空间”（而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须至少具有读取权限才可激活触发器。

可在 host.json 文件中提供[其他设置](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)来进一步优化事件中心触发器。  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>触发器使用情况
触发事件中心触发器函数时，会将触发此函数的消息作为字符串传递到该函数中。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>触发器示例
假定 function.json 的 `bindings` 数组中具有下面的事件中心触发器：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

请参阅记录事件中心触发器消息正文的特定于语言的示例。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C 中的触发器示例# #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F 中的触发器示例# #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的触发器示例

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hub-output-binding"></a>事件中心输出绑定
使用事件中心输出绑定将事件写入到事件中心事件流。 必须具有事件中心的发送权限才可将事件写入到其中。

输出绑定在 function.json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection` 必须是应用设置的名称，该名称中包含事件中心命名空间的连接字符串。
单击“命名空间”（而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须具有发送权限才可将消息发送到事件流。

## <a name="output-usage"></a>输出使用情况
本部分演示如何在函数代码中使用事件中心输出绑定。

可以使用以下参数类型将消息输出到配置的事件中心：

* `out string`
* `ICollector<string>`（若要输出多条消息）
* `IAsyncCollector<string>`（异步版`ICollector<T>`）

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
假设在 function.json 的 `bindings` 数组中有以下事件中心输出绑定：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

请参阅将事件写入事件流的特定于语言的示例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C 中的输出示例# #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

或创建多个消息：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F 中的输出示例# #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Node.js 中的输出示例

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

或者，发送多条消息，

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]



<!--HONumber=Feb17_HO3-->


