---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 78836ca4e51875be4237267b3bb9256cc4541fe2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791689"
---
使用事件中心输出绑定将事件写入到事件流。 必须具有事件中心的发送权限才可将事件写入到其中。

在尝试实现输出绑定之前，请确保所需的包引用已准备就绪。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

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

以下示例演示如何使用 `IAsyncCollector` 接口发送一批消息。 当你处理来自一个事件中心的消息并将结果发送到另一个事件中心时，这种情况很常见。

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

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](../articles/azure-functions/functions-reference-csharp.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x 及更高版本，第二个示例适用于 Functions 1.x。 

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](../articles/azure-functions/functions-reference-node.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x 及更高版本，第二个示例适用于 Functions 1.x。 

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

# <a name="python"></a>[Python](#tab/python)

以下示例演示 function.json 文件中的事件中心触发器绑定以及使用该绑定的 [Python 函数](../articles/azure-functions/functions-reference-python.md)**。 该函数将消息写入事件中心。

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

# <a name="java"></a>[Java](#tab/java)

以下示例演示一个将包含当前时间的消息写入到事件中心的 Java 函数。

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

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

对于 [C# 类库](../articles/azure-functions/functions-dotnet-class-library.md)，使用 [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#configuration)。 下面是 `EventHub` 特性的示例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#example)。

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

在[Java 函数运行时库](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)中，对其值将发布到事件中心的参数使用[EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)批注。 参数的类型`OutputBinding<T>`应为，其中`T`是 POJO 或任何本机 Java 类型。

---

## <a name="configuration"></a>配置

下表说明了在*函数 json*文件和`EventHub`属性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type  | n/a | 必须设置为“eventHub”。 |
|**方向键** | n/a | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|**name** | n/a | 函数代码中使用的表示事件的变量名称。 |
|**path** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**eventHubName** |**EventHubName** | Functions 2.x 及更高版本。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**connection** |**连接** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 *命名空间* （而不是事件中心本身）**** 的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须具有发送权限才可将消息发送到事件流。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

使用方法参数（如） `out string paramName`发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

使用方法参数（如） `out string paramName`发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用`context.bindings.<name>`访问 output 事件，其中`<name>`是在`name` *函数 json*的属性中指定的值。

# <a name="python"></a>[Python](#tab/python)

有两个选项可用于从函数输出事件中心消息：

- **返回值**：将`name` *函数 json*中的属性设置为`$return`。 使用此配置时，函数的返回值将作为事件中心消息保留。

- **命令式**：向声明为[Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python)类型的参数的[set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none)方法传递值。 传递给`set`的值将持久保存为事件中心消息。

# <a name="java"></a>[Java](#tab/java)

可以通过以下两个选项使用[EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)批注从函数输出事件中心消息：

- **返回值**：通过将批注应用于函数本身，函数的返回值将作为事件中心消息保持。

- **命令式**：若要显式设置消息值，请将批注应用到类型[`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)的特定参数，其中`T`是 POJO 或任何本机 Java 类型。 使用此配置时，向`setValue`方法传递值会将值作为事件中心消息保留。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 事件中心 | [操作指南](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |
