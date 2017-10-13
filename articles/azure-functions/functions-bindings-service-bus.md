---
title: "Azure Functions 服务总线触发器和绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 服务总线触发器和绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: glenga
ms.openlocfilehash: 71149aaacc940a62e085cf1ce103a0214d05bd1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-service-bus-bindings"></a>Azure Functions 服务总线绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和使用 Azure 服务总线绑定。 

Azure Functions 支持对服务总线队列和主题的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>服务总线触发器
使用服务总线触发器响应来自服务总线队列或主题的消息。 

服务总线队列和主题触发器通过 function.json 的 `bindings` 数组中的以下 JSON 对象进行定义：

* *队列*触发器：

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* *主题*触发器：

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

注意以下事项：

* 对于 `connection`，[在函数应用中创建一个应用设置](functions-how-to-use-azure-function-app-settings.md)，其中包含指向服务总线命名空间的连接字符串，并在触发器的 `connection` 属性中指定应用设置的名称。 按照[获取管理凭据](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)中显示的步骤，获取连接字符串。
  必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。
  如果将 `connection` 留空，触发器假定已在名为 `AzureWebJobsServiceBus` 的应用设置中指定默认服务总线连接字符串。
* 对于 `accessRights`，可用的值是 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为 `listen`。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。

## <a name="trigger-behavior"></a>触发器行为
* **单线程** - 默认情况下，Functions 运行时同时处理多个消息。 要指示运行时一次只处理单个队列或主题消息，请在 host.json 中将 `serviceBus.maxConcurrentCalls` 设置为 1。 
  有关 host.json 的信息，请参阅[文件夹结构](functions-reference.md#folder-structure)和 [host.json](https://github .com/Azure/azure-webjobs-sdk-script/wiki/host.json)。
* **有害消息处理** - 服务总线执行自己的有害消息处理，此操作无法在 Azure Functions 配置或代码中控制或配置。 
* **PeekLock 行为** - Functions 运行时接收 [`PeekLock` 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode) 的消息，并且在函数成功完成时，对此消息调用 `Complete`；如果函数失败，则调用 `Abandon`。 
  如果函数的运行时间长于 `PeekLock` 超时时间，则会自动续订锁定。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>触发器使用情况
本部分演示如何在函数代码中使用服务总线触发器。 

在 C# 和 F# 中，服务总线触发器消息可以反序列化为以下任何输入类型：

* `string` - 适用于字符串消息
* `byte[]` -适用于二进制数据
* 任何[对象](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的数据。
  如果声明自定义输入类型（如 `CustomType`），Azure Functions 会尝试将 JSON 数据反序列化为指定类型。
* `BrokeredMessage` - 提供带 [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) 方法的反序列化消息。

在 Node.js 中，服务总线触发器消息将作为字符串或 JSON 对象传递到函数。

<a name="triggersample"></a>

## <a name="trigger-sample"></a>触发器示例
假设有以下 function.json：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

请参阅处理服务总线队列消息的特定于语言的示例。

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C# 中的触发器示例 #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F# 中的触发器示例 #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的触发器示例

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>服务总线输出绑定
函数的服务总线队列和主题输出使用 function.json 的 `bindings` 数组中的以下 JSON 对象：

* *队列*输出：

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* *主题*输出：

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

注意以下事项：

* 对于 `connection`，[在函数应用中创建一个应用设置](functions-how-to-use-azure-function-app-settings.md)，其中包含指向服务总线命名空间的连接字符串，并在输出绑定的 `connection` 属性中指定应用设置的名称。 按照[获取管理凭据](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)中显示的步骤，获取连接字符串。
  必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。
  如果将 `connection` 留空，输出绑定假定已在名为 `AzureWebJobsServiceBus` 的应用设置中指定默认服务总线连接字符串。
* 对于 `accessRights`，可用的值是 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为 `listen`。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。

<a name="outputusage"></a>

## <a name="output-usage"></a>输出使用情况
在 C# 和 F# 中，Azure Functions 可以从以下任何类型创建服务总线队列消息：

* 任何 [对象](https://msdn.microsoft.com/library/system.object.aspx) - 参数定义外观类似 `out T paramName` (C#)。
  Functions 将对象反序列化为 JSON 消息。 如果函数结束时输出值为 null，Functions 将创建具有 null 对象的消息。
* `string` - 参数定义外观类似 `out string paraName` (C#)。 如果函数结束时参数值为非 null，Functions 将创建消息。
* `byte[]` - 参数定义外观类似 `out byte[] paraName` (C#)。 如果函数结束时参数值为非 null，Functions 将创建消息。
* `BrokeredMessage` - 参数定义外观类似 `out BrokeredMessage paraName` (C#)。 如果函数结束时参数值为非 null，Functions 将创建消息。

有关如何在 C# 函数中创建多条消息，可以使用 `ICollector<T>` 或 `IAsyncCollector<T>`。 调用 `Add` 方法时创建了一条消息。

在 Node.js 中，可以向 `context.binding.<paramName>` 分配一个字符串、字节数组或 Javascript 对象（反序列化为 JSON）。

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
假设有以下定义服务总线队列输出的 function.json：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

请参阅向服务总线队列发送消息的特定于语言的示例。

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C# 中的输出示例 #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

或创建多个消息：

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F# 中的输出示例 #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的输出示例

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

或创建多个消息：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

