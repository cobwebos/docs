---
title: Azure Functions 的 Azure 事件网格输出绑定
description: 了解如何在 Azure Functions 中发送事件网格事件。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 0237bcbf98578d9f83f3c9652661c786df54e73a
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627681"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions 的 Azure 事件网格输出绑定

使用事件网格输出绑定将事件写入到自定义主题。 必须具有有效的[自定义主题访问密钥](../event-grid/security-authentication.md#authenticate-publishing-clients-using-sas-or-key)。

若要了解设置和配置详细信息，请参阅[概述](./functions-bindings-event-grid.md)。

> [!NOTE]
> 事件网格输出绑定不支持共享访问签名（SAS 令牌）。 必须使用主题的访问密钥。

> [!IMPORTANT]
> 事件网格输出绑定仅适用于 Functions 2.x 和更高版本。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示使用方法返回值作为输出将消息写入到事件网格自定义主题的 [C# 函数](functions-dotnet-class-library.md)：

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

以下示例演示如何使用 `IAsyncCollector` 接口发送一批消息。

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

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的事件网格输出绑定数据。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

下面是可创建一个事件的 C# 脚本代码：

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

下面是可创建多个事件的 C# 脚本代码：

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的事件网格输出绑定数据。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

下面是可创建单个事件的 JavaScript 代码：

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

下面是可创建多个事件的 JavaScript 代码：

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

# <a name="python"></a>[Python](#tab/python)

事件网格输出绑定不适用于 Python。

# <a name="java"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

对于[c # 类库](functions-dotnet-class-library.md)，请使用[EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)属性。

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

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

事件网格输出绑定不适用于 Python。

# <a name="java"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

---

## <a name="configuration"></a>配置

下表说明了在*函数 json*文件和`EventGrid`属性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type  | 不适用 | 必须设置为 "eventGrid"。 |
|**方向键** | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|**name** | 不适用 | 函数代码中使用的表示事件的变量名称。 |
|**topicEndpointUri** |**TopicEndpointUri** | 包含自定义主题的 URI 的应用设置的名称，例如`MyTopicEndpointUri`。 |
|**topicKeySetting** |**TopicKeySetting** | 应用设置的名称，该设置包含自定义主题的访问密钥。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> 确保将 " `TopicEndpointUri`配置" 属性的值设置为包含自定义主题的 URI 的应用设置的名称。 不要在此属性中直接指定自定义主题的 URI。

## <a name="usage"></a>用法

# <a name="c"></a>[C#](#tab/csharp)

使用方法参数（如） `out EventGridEvent paramName`发送消息。 若要编写多条消息，可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 代替 `out EventGridEvent`。

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

使用方法参数（如） `out EventGridEvent paramName`发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<EventGridEvent>` 或 `IAsyncCollector<EventGridEvent>` 代替 `out EventGridEvent`。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用`context.bindings.<name>`访问 output 事件，其中`<name>`是在`name` *函数 json*的属性中指定的值。

# <a name="python"></a>[Python](#tab/python)

事件网格输出绑定不适用于 Python。

# <a name="java"></a>[Java](#tab/java)

事件网格输出绑定对于 Java 不可用。

---

## <a name="next-steps"></a>后续步骤

* [调度事件网格事件](./functions-bindings-event-grid-trigger.md)
