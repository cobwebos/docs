---
title: 适用于 Azure Functions 的 Azure 服务总线输出绑定
description: 了解如何从 Azure Functions 发送 Azure 服务总线消息。
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 3fc8f205bff52fad6e55b7aa6692ec80ae5e954a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212147"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>适用于 Azure Functions 的 Azure 服务总线输出绑定

使用 Azure 服务总线输出绑定发送队列或主题消息。

有关设置和配置详细信息，请参阅[概述](functions-bindings-service-bus-output.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示发送服务总线队列消息的 [C# 函数](functions-dotnet-class-library.md)：

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 function.json 文件中的绑定数据：

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
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 function.json 文件中的绑定数据：

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
    context.bindings.outputSbQueue = message;
    context.done();
};
```

下面是可创建多条消息的 JavaScript 脚本代码：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

以下示例演示如何写出到 Python 中的服务总线队列。

在将*类型*设置为的*function.js*中定义了服务总线绑定定义 `serviceBus` 。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

在* _ \_ _ \_ py*中，通过将值传递给方法，可以将消息写入队列 `set` 。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

以下示例演示了一个 Java 函数，该函数在由 HTTP 请求触发时将消息发送到服务总线队列 `myqueue`。

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将写入服务总线队列的函数参数使用 `@QueueOutput` 注释。  参数类型应为 `OutputBinding<T>`，其中 T 是 POJO 的任何本机 Java 类型。

Java 函数也可将内容写入服务总线主题。 以下示例使用 `@ServiceBusTopicOutput` 注释来说明输出绑定的配置。 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)。

该特性的构造函数采用队列或者主题和订阅的名称。 也可指定连接的访问权限。 [输出 - 配置](#configuration)部分介绍了如何选择访问权限设置。 下面是一个示例，说明了应用于该函数的返回值的属性：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

可以设置 `Connection` 属性来指定应用设置（其中包含要使用的服务总线连接字符串）的名称，如以下示例中所示：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - 示例](#example)。

可以使用 `ServiceBusAccount` 特性在类、方法或参数级别指定要使用的服务总线帐户。  有关详细信息，请参阅[触发器 - 特性](functions-bindings-service-bus-trigger.md#attributes-and-annotations)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

使用 `ServiceBusQueueOutput` 和 `ServiceBusTopicOutput` 注释可以函数输出的形式写入消息。 使用这些注释修饰的参数必须声明为 `OutputBinding<T>`，其中 `T` 是与消息类型对应的类型。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `ServiceBus` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为“serviceBus”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“out”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 变量的名称，表示函数代码中的队列或主题消息。 设置为“$return”可引用函数返回值。 |
|**queueName**|**QueueName**|队列名称。  仅在发送队列消息的情况下设置，不为主题设置。
|**topicName**|**TopicName**|主题名称。 仅在发送主题消息的情况下设置，不为队列设置。|
|连接|**Connection**|应用设置的名称，包含要用于此绑定的服务总线连接字符串。 如果应用设置名称以“AzureWebJobs”开头，则只能指定该名称的余下部分。 例如，如果将 `connection` 设为“MyServiceBus”，Functions 运行时会查找名为“AzureWebJobsMyServiceBus”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为“AzureWebJobsServiceBus”的应用设置中的默认服务总线连接字符串。<br><br>若要获取连接字符串，请执行[获取管理凭据](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)中显示的步骤。 必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。|
|**accessRights**（仅限 v1）|**Access**|连接字符串的访问权限。 可用值为 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为“listen”。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。 在 Azure Functions 版本 2.x 及更高版本中，此属性不可用，因为最新版本的服务总线 SDK 不支持管理操作。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

在 Azure Functions 1.x 中，如果队列尚不存在并且 `accessRights` 设置为 `manage`，则运行时会创建队列。 在 Functions 版本 2.x 及更高版本中，队列或主题必须已存在；如果指定了不存在的队列或主题，则函数将失败。 

# <a name="c"></a>[C#](#tab/csharp)

对输出绑定使用以下参数类型：

* `out T paramName` - `T` 可以是任何可 JSON 序列化的类型。 如果函数退出时参数值为 null，Functions 将创建具有 null 对象的消息。
* `out string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out BrokeredMessage` - 如果函数退出时参数值为 null，Functions 不创建消息（适用于 Functions 1.x）
* `out Message` - 如果函数退出时参数值为 null，Functions 不创建消息（适用于 Functions 2.x 及更高版本）
* `ICollector<T>` 或 `IAsyncCollector<T>`（适用于异步方法）- 用于创建多条消息。 调用 `Add` 方法时创建了一条消息。

使用 C# 函数时：

* 异步函数需要返回值或 `IAsyncCollector` 而不是 `out` 参数。

* 若要访问会话 ID，请绑定到 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 类型并使用 `sessionId` 属性。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

对输出绑定使用以下参数类型：

* `out T paramName` - `T` 可以是任何可 JSON 序列化的类型。 如果函数退出时参数值为 null，Functions 将创建具有 null 对象的消息。
* `out string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out BrokeredMessage` - 如果函数退出时参数值为 null，Functions 不创建消息（适用于 Functions 1.x）
* `out Message` - 如果函数退出时参数值为 null，Functions 不创建消息（适用于 Functions 2.x 及更高版本）
* `ICollector<T>` 或 `IAsyncCollector<T>` - 用于创建多条消息。 调用 `Add` 方法时创建了一条消息。

使用 C# 函数时：

* 异步函数需要返回值或 `IAsyncCollector` 而不是 `out` 参数。

* 若要访问会话 ID，请绑定到 [`Message`](/dotnet/api/microsoft.azure.servicebus.message) 类型并使用 `sessionId` 属性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name from function.json>` 访问队列或主题。 可以向 `context.binding.<name>` 分配一个字符串、字节数组或 JavaScript 对象（反序列化为 JSON）。

# <a name="python"></a>[Python](#tab/python)

使用 [Azure 服务总线 SDK](../service-bus-messaging/index.yml) 而不是内置的输出绑定。

# <a name="java"></a>[Java](#tab/java)

使用 [Azure 服务总线 SDK](../service-bus-messaging/index.yml) 而不是内置的输出绑定。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 服务总线 | [服务总线错误代码](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| 服务总线 | [服务总线限制](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 及更高版本中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的设置。 有关全局配置设置的详细信息，请参阅 [Azure Functions 版本的 host.json 参考](functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

如果将 `isSessionsEnabled` 设置为 `true`，则将采用 `sessionHandlerOptions`。  如果将 `isSessionsEnabled` 设置为 `false`，则将采用 `messageHandlerOptions`。

|属性  |默认 | 说明 |
|---------|---------|---------|
|prefetchCount|0|获取或设置消息接收方可以同时请求的消息数。|
|maxAutoRenewDuration|00:05:00|自动续订消息锁的最长持续时间。|
|autoComplete|是|是触发器在处理后自动调用 complete，还是函数代码手动调用 complete。<br><br>仅在 C# 中支持将其设置为 `false`。<br><br>如果设置为 `true`，则触发器会在函数执行成功完成时自动完成该消息，否则会放弃该消息。<br><br>设置为 `false` 时，你负责调用 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet) 方法来完成、放弃消息或将消息放入死信队列。 如果引发了异常（并且未调用任何 `MessageReceiver` 方法），则锁仍然存在。 锁到期后，消息会重新排队，同时 `DeliveryCount` 会递增，并且锁会自动续订。<br><br>在非 C# 函数中，函数中的异常会导致运行时在后台调用 `abandonAsync`。 如果未发生异常，则在后台调用 `completeAsync`。 |
|maxConcurrentCalls|16|对于每个缩放实例，消息泵应对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。|
|maxConcurrentSessions|2000|每个缩放实例可以并发处理的最大会话数。|

## <a name="next-steps"></a>后续步骤

- [创建服务总线队列或主题消息时运行函数（触发器）](./functions-bindings-service-bus-trigger.md)
