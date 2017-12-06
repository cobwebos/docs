---
title: "Azure Functions 的 Azure 服务总线绑定"
description: "了解如何在 Azure Functions 中使用 Azure 服务总线触发器和绑定。"
services: functions
documentationcenter: na
author: tdykstra
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
ms.author: tdykstra
ms.openlocfilehash: 6d59b26fa4ab17c17827a8e3450e808e40e5c2dd
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服务总线绑定

本文介绍如何在 Azure Functions 中使用 Azure 服务总线绑定。 Azure Functions 支持对服务总线队列和主题的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="trigger"></a>触发器

使用服务总线触发器响应来自服务总线队列或主题的消息。 

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [预编译 C#](#trigger---c-example)
* [C# 脚本](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示[预编译的 C# 函数](functions-dotnet-class-library.md)，用于记录服务总线队列消息。

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem, 
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数记录服务总线队列消息。

下面是 *function.json* 文件中的绑定数据：

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

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

### <a name="trigger---f-example"></a>触发器 - F# 示例

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数记录服务总线队列消息。 

下面是 *function.json* 文件中的绑定数据：

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

F# 脚本代码如下所示：

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数记录服务总线队列消息。 

下面是 *function.json* 文件中的绑定数据：

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

JavaScript 脚本代码如下所示：

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

## <a name="trigger---attributes"></a>触发器 - 特性

对于[预编译的 C#](functions-dotnet-class-library.md) 函数，请使用以下特性来配置服务总线触发器：

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusTriggerAttribute.cs)，在 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中定义

  该特性的构造函数采用队列或者主题和订阅的名称。 也可指定连接的访问权限。 如果未指定访问权限，则默认为 `Manage`。 [触发器 - 配置](#trigger---configuration)部分介绍了如何选择访问权限设置。 下面是一个示例，演示了用于字符串参数的属性：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  可以设置 `Connection` 属性来指定要使用的服务总线帐户，如以下示例中所示：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

  有关完整示例，请参阅[触发器 - 预编译 C# 示例](#trigger---c-example)。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs)，在 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中定义

  提供另一种方式来指定要使用的服务总线帐户。 构造函数采用包含服务总线连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, TraceWriter log)
  {
      ...
  }
  ```

要使用的服务总线帐户按以下顺序确定：

* `ServiceBusTrigger` 特性的 `Connection` 属性。
* 作为 `ServiceBusTrigger` 特性应用到同一参数的 `ServiceBusAccount` 特性。
* 应用到函数的 `ServiceBusAccount` 特性。
* 应用到类的 `ServiceBusAccount` 特性。
* “AzureWebJobsServiceBus”应用设置。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件和 `ServiceBusTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为“serviceBusTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 变量的名称，表示函数代码中的队列或主题消息。 设置为“$return”可引用函数返回值。 | 
|**queueName**|**QueueName**|要监视的队列的名称。  仅在监视队列的情况下设置，不为主题设置。
|**topicName**|**TopicName**|要监视的主题的名称。 仅在监视主题的情况下设置，不为队列设置。|
|**subscriptionName**|**SubscriptionName**|要监视的订阅的名称。 仅在监视主题的情况下设置，不为队列设置。|
|**连接**|**Connection**|应用设置的名称，包含要用于此绑定的服务总线连接字符串。 如果应用设置名称以“AzureWebJobs”开头，则只能指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyServiceBus”，函数运行时将会查找名为“AzureWebJobsMyServiceBus”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为“AzureWebJobsServiceBus”的应用设置中的默认服务总线连接字符串。<br><br>若要获取连接字符串，请执行[获取管理凭据](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)中显示的步骤。 必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。 |
|**accessRights**|**Access**|连接字符串的访问权限。 可用值为 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为“listen”。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>触发器 - 用法

在 C# 和 C# 脚本中，可以使用 `string paramName` 之类的方法参数访问队列或主题消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 可以使用下面的任意类型而不使用 `string`：

* `byte[]` - 适用于二进制数据。
* 自定义类型 - 如果消息包含 JSON，Azure Functions 会尝试反序列化 JSON 数据。
* `BrokeredMessage` - 提供带 [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) 方法的反序列化消息。

在 JavaScript 中通过 `context.bindings.<name>` 访问队列或主题消息。 `<name>` 是在 *function.json* 的 `name` 属性中指定的值。 服务总线消息作为字符串或 JSON 对象传递到函数中。

## <a name="trigger---poison-messages"></a>触发器 - 有害消息

无法在 Azure Functions 中控制或配置有害消息处理。 服务总线处理有害消息本身。

## <a name="trigger---peeklock-behavior"></a>触发器 - PeekLock 行为

Functions 运行时以 [PeekLock 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)接收消息。 如果函数成功完成，则对此消息调用 `Complete`；如果函数失败，则调用 `Abandon`。 如果函数的运行时间长于 `PeekLock` 超时时间，则会自动续订锁定。

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](functions-host-json.md#servicebus) 文件包含控制服务总线触发器行为的设置。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>输出

使用 Azure 服务总线输出绑定发送队列或主题消息。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [预编译 C#](#output---c-example)
* [C# 脚本](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示[预编译的 C# 函数](functions-dotnet-class-library.md)，用于发送服务总线队列消息：

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 *function.json* 文件中的绑定数据：

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

下面是可创建一条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [F# 脚本函数](functions-reference-fsharp.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 *function.json* 文件中的绑定数据：

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

下面是可创建一条消息的 F# 脚本代码：

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 *function.json* 文件中的绑定数据：

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

下面是可创建一条消息的 JavaScript 脚本代码：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

下面是可创建多条消息的 JavaScript 脚本代码：

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

## <a name="output---attributes"></a>输出 - 特性

对于[预编译的 C#](functions-dotnet-class-library.md) 函数，请使用 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) 中定义的 [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs) 特性。

该特性的构造函数采用队列或者主题和订阅的名称。 也可指定连接的访问权限。 [输出 - 配置](#output---configuration)部分介绍了如何选择访问权限设置。 下面是一个示例，说明了应用于该函数的返回值的属性：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的服务总线帐户，如以下示例中所示：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, TraceWriter log)
{
    ...
}
```

有关完整示例，请参阅[输出 - 预编译 C# 示例](#output---c-example)。

可以使用 `ServiceBusAccount` 特性在类、方法或参数级别指定要使用的服务总线帐户。  有关详细信息，请参阅[触发器 - 特性](#trigger---attributes-for-precompiled-c)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件和 `ServiceBus` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为“serviceBus”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“out”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 变量的名称，表示函数代码中的队列或主题。 设置为“$return”可引用函数返回值。 | 
|**queueName**|**QueueName**|队列名称。  仅在发送队列消息的情况下设置，不为主题设置。
|**topicName**|**TopicName**|要监视的主题的名称。 仅在发送主题消息的情况下设置，不为队列设置。|
|**subscriptionName**|**SubscriptionName**|要监视的订阅的名称。 仅在发送主题消息的情况下设置，不为队列设置。|
|**连接**|**Connection**|应用设置的名称，包含要用于此绑定的服务总线连接字符串。 如果应用设置名称以“AzureWebJobs”开头，则只能指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyServiceBus”，函数运行时将会查找名为“AzureWebJobsMyServiceBus”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为“AzureWebJobsServiceBus”的应用设置中的默认服务总线连接字符串。<br><br>若要获取连接字符串，请执行[获取管理凭据](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)中显示的步骤。 必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。|
|**accessRights**|**Access** |连接字符串的访问权限。 可用值为“manage”和“listen”。 默认值是“manage”，指示连接具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为“listen”。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

在 C# 和 C# 脚本中，可以使用 `out string paramName` 之类的方法参数访问队列或主题。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 可以使用下面的任意参数类型：

* `out T paramName` - `T` 可以是任何可 JSON 序列化的类型。 如果函数退出时参数值为 null，Functions 将创建具有 null 对象的消息。
* `out string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out BrokeredMessage` - 如果函数退出时参数值为 null，Functions 不创建消息。

若要在 C# 或 C# 脚本函数中创建多条消息，可以使用 `ICollector<T>` 或 `IAsyncCollector<T>`。 调用 `Add` 方法时创建了一条消息。

在 JavaScript 中通过 `context.bindings.<name>` 访问队列或主题。 `<name>` 是在 *function.json* 的 `name` 属性中指定的值。 可以向 `context.binding.<name>` 分配一个字符串、字节数组或 Javascript 对象（反序列化为 JSON）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
