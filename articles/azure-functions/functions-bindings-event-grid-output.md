---
title: Azure Functions 的 Azure 事件网格输出绑定
description: 了解如何在 Azure Functions 中发送事件网格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368943"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions 的 Azure 事件网格输出绑定

使用事件网格输出绑定将事件写入自定义主题。 您必须具有[该自定义主题的有效访问密钥](../event-grid/security-authentication.md#custom-topic-publishing)。

有关设置和配置的详细信息，请参阅[概述](./functions-bindings-event-grid.md)。

> [!NOTE]
> 事件网格输出绑定不支持共享访问签名（SAS 令牌）。 必须使用该主题的访问密钥。

> [!IMPORTANT]
> 事件网格输出绑定仅适用于函数1.x 和更高版本。

## <a name="example"></a>示例

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

下面的示例演示一个[ C#函数](functions-dotnet-class-library.md)，该函数使用方法返回值作为输出将消息写入事件网格自定义主题：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

下面的示例演示如何使用 `IAsyncCollector` 接口发送一批消息。

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

下面的示例演示了*函数 json*文件中的事件网格输出绑定数据。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

下面是C#创建一个事件的脚本代码：

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

下面是C#用于创建多个事件的脚本代码：

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

下面的示例演示了*函数 json*文件中的事件网格输出绑定数据。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

下面是创建单个事件的 JavaScript 代码：

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

下面是创建多个事件的 JavaScript 代码：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

事件网格输出绑定不适用于 Python。

# <a name="javatabjava"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

---

## <a name="attributes-and-annotations"></a>特性和批注

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

对于[ C#类库](functions-dotnet-class-library.md)，请使用[EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)属性。

该特性的构造函数采用包含自定义主题名称的应用设置的名称，以及包含主题键的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#configuration)。 下面是 `EventGrid` 特性的示例：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[示例](#example)。

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="pythontabpython"></a>[Python](#tab/python)

事件网格输出绑定不适用于 Python。

# <a name="javatabjava"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `EventGrid` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type | 不适用 | 必须设置为 "eventGrid"。 |
|direction | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|name | 不适用 | 函数代码中使用的表示事件的变量名称。 |
|**topicEndpointUri** |**TopicEndpointUri** | 包含自定义主题的 URI 的应用设置的名称，例如 `MyTopicEndpointUri`。 |
|**topicKeySetting** |**TopicKeySetting** | 应用设置的名称，该设置包含自定义主题的访问密钥。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 确保将 "`TopicEndpointUri` 配置" 属性的值设置为包含自定义主题的 URI 的应用设置的名称。 不要在此属性中直接指定自定义主题的 URI。

## <a name="usage"></a>使用情况

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

使用方法参数（例如 `out EventGridEvent paramName`）发送消息。 若要编写多条消息，可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 代替 `out EventGridEvent`。

# <a name="c-scripttabcsharp-script"></a>[C#脚本](#tab/csharp-script)

使用方法参数（例如 `out EventGridEvent paramName`）发送消息。 在 C# 脚本中，`paramName` 是在 `name`function.json*的* 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 代替 `out EventGridEvent`。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>`，其中 `<name>` 是*函数 json*的 `name` 属性中指定的值来访问输出事件。

# <a name="pythontabpython"></a>[Python](#tab/python)

事件网格输出绑定不适用于 Python。

# <a name="javatabjava"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

---

## <a name="next-steps"></a>后续步骤

* [调度事件网格事件](./functions-bindings-event-grid-trigger.md)
