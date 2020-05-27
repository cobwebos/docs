---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e055f2d7b98df9357ecdee5e044305e35935682e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791683"
---
使用函数触发器来响应发送到事件中心事件流的事件。 若要设置触发器，必须具有基础事件中心的读取访问权限。 触发函数时，传递给函数的消息充当字符串类型。

## <a name="scaling"></a>扩展

事件触发的函数的每个实例由单个 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例提供支持。 触发器（由事件中心提供支持）确保只有一个 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例能够在给定分区上获得租约。

例如，考虑如下所述的一个事件中心：

* 10 个分区
* 在所有分区之间平均分配 1000 个事件，每个分区中有 100 条消息

首次启用函数时，只有一个函数实例。 我们将第一个函数实例命名为 `Function_0`。 `Function_0` 函数具有单个 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例，此实例在所有十个分区上都有租约。 此实例从分区 0-9 读取事件。 从此时开始，将发生下列情况之一：

* **不需要新的函数实例**： `Function_0`在函数缩放逻辑生效之前，能够处理所有1000事件。 在这种情况下，所有 1,000 条消息都由 `Function_0` 处理。

* **添加了额外的函数实例**：如果函数缩放逻辑确定包含的`Function_0`消息数超过了可处理的消息数，则将创建一个`Function_1`新的 function app 实例（）。 此新函数也有一个关联的 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例。 当基础事件中心检测到新的主机实例正在尝试读取消息时，它会在主机实例之间负载平衡分区。 例如，可以将分区 0-4 分配给 `Function_0`，将分区 5-9 分配给 `Function_1`。

* **添加了更多函数实例**：如果函数缩放逻辑确定`Function_0`和`Function_1`的消息数多于它们可以处理的消息数，则`Functions_N`将创建新的 function app 实例。  会一直创建应用，直到 `N` 大于事件中心分区数为止。 在我们的示例中，事件中心再次对分区进行负载均衡，在本例中是在实例 `Function_0`...`Functions_9` 之间进行的。

进行缩放时， `N`实例是大于事件中心分区数的数字。 此模式用于确保在分区从其他实例中变得可用时，可以使用[EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)实例获取这些分区中的锁。 你只需为执行函数实例时使用的资源付费。 换句话说，不需要为过度预配的资源付费。

当所有函数执行都完成时（不管是否有错误），则会将检查点添加到关联的存储帐户。 检查点设置成功后，永远不会再次检索所有 1,000 条消息。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示用于记录事件中心触发器消息正文的 [C# 函数](../articles/azure-functions/functions-dotnet-class-library.md)。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

若要在函数代码中访问[事件元数据](#event-metadata)，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.Azure.EventHubs` 使用 using 语句）。 此外，还可以通过在方法签名中使用绑定表达式来访问相同的属性。  以下示例演示了获取相同数据的两种方法：

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

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](../articles/azure-functions/functions-reference-csharp.md)。 该函数记录事件中心触发器的消息正文。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

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

若要在函数代码中访问[事件元数据](#event-metadata)，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.Azure.EventHubs` 使用 using 语句）。 此外，还可以通过在方法签名中使用绑定表达式来访问相同的属性。  以下示例演示了获取相同数据的两种方法：

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](../articles/azure-functions/functions-reference-node.md)。 此函数将读取[事件元数据](#event-metadata)并记录消息。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

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

若要批量接收事件，请将 function.json 文件中的 `cardinality` 设为 `many`**，如以下示例所示。

### <a name="version-2x-and-higher"></a>版本 2.x 及更高版本

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

# <a name="python"></a>[Python](#tab/python)

以下示例演示 function.json 文件中的事件中心触发器绑定以及使用该绑定的 [Python 函数](../articles/azure-functions/functions-reference-python.md)**。 此函数将读取[事件元数据](#event-metadata)并记录消息。

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

# <a name="java"></a>[Java](#tab/java)

下面的示例演示了记录事件中心触发器消息正文的事件中心触发器绑定。

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

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](../articles/azure-functions/functions-dotnet-class-library.md)中，使用 [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称、使用者组的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[触发器配置部分](#configuration)。 下面是 `EventHubTriggerAttribute` 特性的示例：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[触发器 - C# 示例](#example)。

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

在 Java[函数运行时库](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)中，对其值来自事件中心的参数使用[EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger)批注。 带有这些注释的参数会导致函数在事件到达时运行。 可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

---

## <a name="configuration"></a>配置

下表说明了在*函数 json*文件和`EventHubTrigger`属性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type  | n/a | 必须设置为 `eventHubTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**方向键** | n/a | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | n/a | 在函数代码中表示事件项的变量的名称。 |
|**path** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**eventHubName** |**EventHubName** | Functions 2.x 及更高版本。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 可以通过应用设置 %eventHubName% 引用 |
|**consumerGroup** |**ConsumerGroup** | 一个可选属性，用于设置[使用者组](../articles/event-hubs/event-hubs-features.md#event-consumers)，该组用于订阅事件中心中的事件。 如果将其省略，则会使用 `$Default` 使用者组。 |
|**基数** | n/a | 对于 JavaScript。 设为 `many` 以启用批处理。  如果省略或设为 `one`，将向函数传递一条消息。 |
|**connection** |**连接** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 [命名空间](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) （而不是事件中心本身）**** 的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须至少具有读取权限才可激活触发器。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>事件元数据

事件中心触发器提供了几个[元数据属性](../articles/azure-functions/./functions-bindings-expressions-patterns.md)。 元数据属性可用作其他绑定中的绑定表达式的一部分，或者用作代码中的参数。 属性来自[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)类。

|properties|类型|说明|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` 实例。|
|`EnqueuedTimeUtc`|`DateTime`|排队时间 (UTC)。|
|`Offset`|`string`|数据相对于事件中心分区流的偏移量。 偏移量是事件中心流中的事件的标记或标识符。 该标识符在事件中心流的分区中是惟一的。|
|`PartitionKey`|`string`|事件数据应该发送到的分区。|
|`Properties`|`IDictionary<String,Object>`|事件数据的用户属性。|
|`SequenceNumber`|`Int64`|事件的逻辑序列号。|
|`SystemProperties`|`IDictionary<String,Object>`|系统属性，包括事件数据。|

请参阅在本文的前面部分使用这些属性的[代码示例](#example)。

## <a name="hostjson-properties"></a>host json 属性
<a name="host-json"></a>

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。 根据 Azure Functions 版本，配置有所不同。

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]