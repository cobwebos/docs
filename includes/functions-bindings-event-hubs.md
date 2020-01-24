---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 6324fd0e2957aea46fb5876aa8c91f0906205ccc
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694259"
---
## <a name="trigger"></a>触发器

使用函数触发器来响应发送到事件中心事件流的事件。 若要设置触发器，你必须对基础事件中心具有读取访问权限。 触发函数时，传递给函数的消息将类型化为字符串。

## <a name="trigger---scaling"></a>触发器 - 缩放

事件触发函数的每个实例都由单个[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)实例支持。 触发器（由事件中心提供支持）可确保只有一个[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)实例可以获得给定分区上的租约。

例如，考虑如下所述的一个事件中心：

* 10个分区
* 1000在所有分区上均匀分布的事件，每个分区中包含100条消息

首次启用函数时，只有一个函数实例。 让我们调用第一个函数实例 `Function_0`。 `Function_0` 函数具有[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)的单个实例，该实例持有所有10个分区的租约。 此实例从分区 0-9 读取事件。 从此时开始，将发生下列情况之一：

* **不需要新的函数实例**：在函数缩放逻辑生效之前，`Function_0` 能够处理所有1000事件。 在这种情况下，将 `Function_0`处理所有1000消息。

* **添加了额外的函数实例**：如果函数缩放逻辑确定 `Function_0` 的消息数超过其处理能力，则会创建一个新的 function app 实例（`Function_1`）。 此新函数也具有[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)的关联实例。 当基础事件中心检测到新的主机实例正在尝试读取消息时，它会在主机实例之间负载平衡分区。 例如，可以将分区 0-4 分配给 `Function_0`，将分区 5-9 分配给 `Function_1`。

* **添加了更多函数实例**：如果函数缩放逻辑确定 `Function_0` 和 `Function_1` 的消息数超过其处理能力，则会创建新的 `Functions_N` function app 实例。  将为 `N` 大于事件中心分区数的点创建应用。 在我们的示例中，事件中心再次对分区进行负载均衡，在本例中是在实例 `Function_0`...`Functions_9` 之间进行的。

进行缩放时，`N` 实例是大于事件中心分区数的数字。 此模式用于确保在分区从其他实例中变得可用时，可以使用[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)实例获取这些分区中的锁。 只需为函数实例执行时所使用的资源付费。 换句话说，这种过度预配无需支付费用。

当所有函数执行都完成时（不管是否有错误），则会将检查点添加到关联的存储帐户。 如果检查指针成功，则永远不会再次检索1000的所有消息。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

以下示例演示用于记录事件中心触发器消息正文的 [C# 函数](../articles/azure-functions/functions-dotnet-class-library.md)。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

若要在函数代码中访问[事件元数据](#trigger---event-metadata)，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.Azure.EventHubs` 使用 using 语句）。 此外，还可以通过在方法签名中使用绑定表达式来访问相同的属性。  以下示例演示了获取相同数据的两种方法：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

若要成批接收事件，请将 `string` 或 `EventData` 设为数组。  

> [!NOTE]
> 成批接收事件时，不能像上述示例那样使用 `DateTime enqueuedTimeUtc` 绑定到方法参数，且必须从每个 `EventData` 对象中接收这些事件  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](../articles/azure-functions/functions-reference-csharp.md)。 该函数记录事件中心触发器的消息正文。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。

#### <a name="version-2x-and-higher"></a>版本2.x 和更高版本

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>版本 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# 脚本代码如下所示：

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

若要在函数代码中访问[事件元数据](#trigger---event-metadata)，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.Azure.EventHubs` 使用 using 语句）。 此外，还可以通过在方法签名中使用绑定表达式来访问相同的属性。  以下示例演示了获取相同数据的两种方法：

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

若要成批接收事件，请将 `string` 或 `EventData` 设为数组：

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](../articles/azure-functions/functions-reference-node.md)。 此函数将读取[事件元数据](#trigger---event-metadata)并记录消息。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。

#### <a name="version-2x-and-higher"></a>版本2.x 和更高版本

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>版本 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

若要批量接收事件，请将 function.json 文件中的 `cardinality` 设为 `many`，如以下示例所示。

#### <a name="version-2x-and-higher"></a>版本2.x 和更高版本

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>版本 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

以下示例演示 function.json 文件中的事件中心触发器绑定以及使用该绑定的 [Python 函数](../articles/azure-functions/functions-reference-python.md)。 此函数将读取[事件元数据](#trigger---event-metadata)并记录消息。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="javatabjava"></a>[Java](#tab/java)

下面的示例演示了*函数 json*文件中的事件中心触发器绑定，以及使用绑定的[Java 函数](../articles/azure-functions/functions-reference-java.md)。 该函数记录事件中心触发器的消息正文。

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值来自事件中心的参数使用 `EventHubTrigger` 注释。 带有这些注释的参数会导致函数在事件到达时运行。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

 ---

## <a name="trigger---attributes-and-annotations"></a>触发器-特性和批注

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

在 [C# 类库](../articles/azure-functions/functions-dotnet-class-library.md)中，使用 [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称、使用者组的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[触发器配置部分](#trigger---configuration)。 下面是 `EventHubTriggerAttribute` 特性的示例：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[触发器 - C# 示例](#trigger)。

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java[函数运行时库](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)中，对其值来自事件中心的参数使用[EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger)批注。 带有这些注释的参数会导致函数在事件到达时运行。 可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

---

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 function.json 文件和 `EventHubTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |Description|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 `eventHubTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|direction | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|name | 不适用 | 在函数代码中表示事件项的变量的名称。 |
|**路径** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**eventHubName** |**EventHubName** | 函数1.x 和更高版本。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 可以通过应用设置% eventHubName% 引用 |
|**consumerGroup** |**ConsumerGroup** | 一个可选属性，用于设置[使用者组](../articles/event-hubs/event-hubs-features.md#event-consumers)，该组用于订阅事件中心中的事件。 如果将其省略，则会使用 `$Default` 使用者组。 |
|**基数** | 不适用 | 适用于 JavaScript。 设为 `many` 以启用批处理。  如果省略或设置为 `one`，则会将单个消息传递给函数。 |
|连接 |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 [命名空间](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) （而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须至少具有读取权限才可激活触发器。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>触发器 - 事件元数据

事件中心触发器提供了几个[元数据属性](../articles/azure-functions/./functions-bindings-expressions-patterns.md)。 元数据属性可用作其他绑定中的绑定表达式的一部分，或者用作代码中的参数。 属性来自[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)类。

|属性|类型|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` 实例。|
|`EnqueuedTimeUtc`|`DateTime`|排队时间 (UTC)。|
|`Offset`|`string`|数据相对于事件中心分区流的偏移量。 偏移量是事件中心流中的事件的标记或标识符。 该标识符在事件中心流的分区中是惟一的。|
|`PartitionKey`|`string`|事件数据应该发送到的分区。|
|`Properties`|`IDictionary<String,Object>`|事件数据的用户属性。|
|`SequenceNumber`|`Int64`|事件的逻辑序列号。|
|`SystemProperties`|`IDictionary<String,Object>`|系统属性，包括事件数据。|

请参阅在本文的前面部分使用这些属性的[代码示例](#trigger)。

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>输出

使用事件中心输出绑定将事件写入到事件流。 必须具有事件中心的发送权限才可将事件写入到其中。

在尝试实现输出绑定之前，请确保所需的包引用已准备就绪。

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

以下示例演示使用方法返回值作为输出将消息写入到事件中心的 [C# 函数](../articles/azure-functions/functions-dotnet-class-library.md)：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

下面的示例演示如何使用 `IAsyncCollector` 接口发送一批消息。 当处理来自一个事件中心的消息并将结果发送到另一个事件中心时，此方案很常见。

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](../articles/azure-functions/functions-reference-csharp.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于函数1.x 和更高版本，第二个示例用于函数1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可创建一条消息的 C# 脚本代码：

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](../articles/azure-functions/functions-reference-node.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于函数1.x 和更高版本，第二个示例用于函数1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可发送一条消息的 JavaScript 代码：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

下面是可发送多条消息的 JavaScript 代码：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

以下示例演示 function.json 文件中的事件中心触发器绑定以及使用该绑定的 [Python 函数](../articles/azure-functions/functions-reference-python.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可发送一条消息的 Python 代码：

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="javatabjava"></a>[Java](#tab/java)

下面的示例演示一个 Java 函数，该函数将包含当前时间的消息写入事件中心。

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将被发布到事件中心的参数使用 `@EventHubOutput` 注释。  此参数应为 `OutputBinding<T>` 类型，其中 T 是 POJO 或任何本机 Java 类型。

---

## <a name="output---attributes-and-annotations"></a>输出-属性和批注

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

对于 [C# 类库](../articles/azure-functions/functions-dotnet-class-library.md)，使用 [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#output---configuration)。 下面是 `EventHub` 特性的示例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output)。

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="javatabjava"></a>[Java](#tab/java)

在[Java 函数运行时库](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)中，对其值将发布到事件中心的参数使用[EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)批注。 参数的类型应为 `OutputBinding<T>`，其中 `T` 是 POJO 或任何本机 Java 类型。

---

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 function.json 文件和 `EventHub` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |Description|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为“eventHub”。 |
|direction | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|name | 不适用 | 函数代码中使用的表示事件的变量名称。 |
|**路径** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**eventHubName** |**EventHubName** | 函数1.x 和更高版本。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|连接 |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 *命名空间* （而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须具有发送权限才可将消息发送到事件流。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

使用方法参数（例如 `out string paramName`）发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

使用方法参数（例如 `out string paramName`）发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>`，其中 `<name>` 是*函数 json*的 `name` 属性中指定的值来访问输出事件。

# <a name="pythontabpython"></a>[Python](#tab/python)

有两个选项可用于从函数输出事件中心消息：

- **返回值**：将*函数*中的 `name` 属性设置为 `$return`。 使用此配置时，函数的返回值将作为事件中心消息保留。

- **命令式**：向声明为[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python)类型的参数的[set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法传递值。 传递给 `set` 的值将作为事件中心消息保留。

# <a name="javatabjava"></a>[Java](#tab/java)

可以通过以下两个选项使用[EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)批注从函数输出事件中心消息：

- **返回值**：通过将批注应用于函数本身，函数的返回值将作为事件中心消息保持。

- **命令式**：若要显式设置消息值，请将批注应用于类型[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)的特定参数，其中 `T` 是 POJO 或任何本机 Java 类型。 使用此配置时，向 `setValue` 方法传递值会将值作为事件中心消息保留。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 事件中心 | [操作指南](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍此绑定在版本2.x 和更高版本中可用的全局配置设置。 下面的示例 host json 文件仅包含此绑定的版本 2.x + 设置。 有关版本2.x 和更高版本中的全局配置设置的详细信息，请参阅[host json reference for Azure Functions](../articles/azure-functions/functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](../articles/azure-functions/functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|属性  |默认 | Description |
|---------|---------|---------|
|`maxBatchSize`|64|每个接收循环收到的最大事件计数。|
|`prefetchCount`|不适用|基础 `EventProcessorHost`使用的默认预提取计数。|
|`batchCheckpointFrequency`|第|创建 EventHub 游标检查点之前要处理的事件批数。|
