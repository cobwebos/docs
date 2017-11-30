---
title: "Azure Functions 事件中心绑定"
description: "了解如何在 Azure Functions 中使用 Azure 事件中心绑定。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: wesmc
ms.openlocfilehash: c2660a3ca8ee7569d49a6998d0dfd5a98a97d294
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="azure-functions-event-hubs-bindings"></a>Azure Functions 事件中心绑定

本文介绍如何使用 Azure Functions 的 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)绑定。 Azure Functions 支持事件中心的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="event-hubs-trigger"></a>事件中心触发器

使用事件中心触发器来响应发送到事件中心事件流的事件。 若要设置触发器，必须具有事件中心的读取访问权限。

触发事件中心触发器函数时，进行触发的消息将作为字符串传递到该函数中。

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [预编译 C#](#trigger---c-example)
* [C# 脚本](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示用于记录事件中心触发器消息正文的[预编译 C#](functions-dotnet-class-library.md) 代码。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

若要访问事件元数据，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.ServiceBus.Messaging` 使用 `using` 语句）。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```
若要成批接收事件，请将 `string` 或 `EventData` 设为数组：

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数记录事件中心触发器的消息正文。

下面是 *function.json* 文件中的绑定数据：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```
C# 脚本代码如下所示：

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

若要访问事件元数据，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.ServiceBus.Messaging` 使用 using 语句）。

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

若要成批接收事件，请将 `string` 或 `EventData` 设为数组：

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>触发器 - F# 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数记录事件中心触发器的消息正文。

下面是 *function.json* 文件中的绑定数据：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

F# 代码如下所示：

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数记录事件中心触发器的消息正文。

下面是 *function.json* 文件中的绑定数据：

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>触发器 - 预编译 C# 的特性

对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中定义的 [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称、使用者组的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[触发器配置部分](#trigger---configuration)。 下面是 `EventHubTriggerAttribute` 特性的示例：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
```

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件和 `EventHubTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `eventHubTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中表示事件项的变量的名称。 | 
|**路径** |**EventHubName** | 事件中心的名称。 | 
|**consumerGroup** |**ConsumerGroup** | 一个可选属性，用于设置[使用者组](../event-hubs/event-hubs-features.md#event-consumers)，该组用于订阅事件中心中的事件。 如果将其省略，则会使用 `$Default` 使用者组。 | 
|**连接** |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击“命名空间”（而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须至少具有读取权限才可激活触发器。<br/>在本地进行开发时，应用设置将取 [local.settings.json 文件](functions-run-local.md#local-settings-file)的值。|

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="event-hubs-output-binding"></a>事件中心输出绑定

使用事件中心输出绑定将事件写入到事件流。 必须具有事件中心的发送权限才可将事件写入到其中。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [预编译 C#](#output---c-example)
* [C# 脚本](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示使用方法返回值作为输出将消息写入到事件中心的[预编译 C# 函数](functions-dotnet-class-library.md)：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数将消息写入事件中心。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

下面是可创建一条消息的 C# 脚本代码：

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数将消息写入事件中心。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

F# 代码如下所示：

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数将消息写入事件中心。

下面是 *function.json* 文件中的绑定数据：

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

下面是可发送一条消息的 JavaScript 代码：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

下面是可发送多条消息的 JavaScript 代码：

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

## <a name="output---attributes-for-precompiled-c"></a>输出 - 预编译 C# 的特性

对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中定义的 [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#output---configuration)。 下面是 `EventHub` 特性的示例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
```

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件和 `EventHub` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为“eventHub”。 |
|**direction** | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|**name** | 不适用 | 函数代码中使用的表示事件的变量名称。 | 
|**路径** |**EventHubName** | 事件中心的名称。 | 
|**连接** |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击“命名空间”（而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须具有发送权限才可将消息发送到事件流。<br/>在本地进行开发时，应用设置将取 [local.settings.json 文件](functions-run-local.md#local-settings-file)的值。|

## <a name="output---usage"></a>输出 - 用法

在 C# 和 C# 脚本中，可以使用 `out string paramName` 等方法参数发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

在 JavaScript 中，通过使用 `context.bindings.<name>` 访问输出事件。 `<name>` 是在 *function.json* 的 `name` 属性中指定的值。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
