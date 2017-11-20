---
title: "Azure Functions 队列存储绑定"
description: "了解如何在 Azure Functions 中使用 Azure 队列存储触发器和输出绑定。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 9cf506d571c8d67a1e48ce34860db3dbc3445509
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions 队列存储绑定

本文介绍如何在 Azure Functions 中使用 Azure 队列存储绑定。 Azure Functions 支持对队列使用触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="queue-storage-trigger"></a>队列存储触发器

在队列中收到新项时，可以使用队列触发器启动一个函数。 队列消息作为输入提供给该函数。

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [预编译 C#](#trigger---c-example)
* [C# 脚本](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示可在每次处理某个队列项之后轮询 `myqueue-items` 队列并写入日志的[预编译 C#](functions-dotnet-class-library.md) 代码。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 每次处理某个队列项之后，该函数会轮询 `myqueue-items` 队列并写入日志。

*function.json* 文件如下所示：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[配置](#trigger---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

[用法](#trigger---usage)部分解释了 function.json 中的 `name` 属性命名的 `myQueueItem`。  [消息元数据部分](#trigger---message-metadata)解释了所有其他所示变量。

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 每次处理某个队列项之后，该函数会轮询 `myqueue-items` 队列并写入日志。

*function.json* 文件如下所示：

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[配置](#trigger---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[用法](#trigger---usage)部分解释了 function.json 中的 `name` 属性命名的 `myQueueItem`。  [消息元数据部分](#trigger---message-metadata)解释了所有其他所示变量。

## <a name="trigger---attributes-for-precompiled-c"></a>触发器 - 预编译 C# 的特性
 
对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用以下特性来配置队列触发器：

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)，在 NuGet 包 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中定义

  该特性的构造函数采用要监视的队列的名称，如以下示例中所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  ```
 
* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)，在 NuGet 包 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中定义

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

要使用的存储帐户按以下顺序确定：

* `QueueTrigger` 特性的 `Connection` 属性。
* 作为 `QueueTrigger` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* “AzureWebJobsStorage”应用设置。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 *function.json* 文件和 `QueueTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用| 必须设置为 `queueTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction**| 不适用 | 只能在 *function.json* 文件中设置。 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 |表示函数代码中的队列的变量的名称。  | 
|**queueName** | **QueueName**| 要轮询的队列的名称。 | 
|**连接** | **Connection** |包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br/>在本地进行开发时，应用设置将取 [local.settings.json 文件](functions-run-local.md#local-settings-file)的值。|

## <a name="trigger---usage"></a>触发器 - 用法
 
在 C# 和 C# 脚本中，可以使用 `Stream paramName` 等方法参数访问 Blob 数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 可以绑定到以下任何类型：

* POCO 对象 - 函数运行时将 JSON 有效负载反序列化为 POCO 对象。 
* `string`
* `byte[]`
* [CloudQueueMessage]

在 JavaScript 中，可以使用 `context.bindings.<name>` 访问队列项有效负载。 如果有效负载为 JSON，则会将它反序列化为对象。

## <a name="trigger---message-metadata"></a>触发器 - 消息元数据

队列触发器提供了几个元数据属性。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 这些值的语义与 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) 相同。

|属性|类型|说明|
|--------|----|-----------|
|`QueueTrigger`|`string`|队列有效负载（如果是有效的字符串）。 如果队列消息有效负载是字符串，则 `QueueTrigger` 包含的值与 *function.json* 中 `name` 属性命名的变量的值相同。|
|`DequeueCount`|`int`|此消息取消排队的次数。|
|`ExpirationTime`|`DateTimeOffset?`|消息过期的时间。|
|`Id`|`string`|队列消息 ID。|
|`InsertionTime`|`DateTimeOffset?`|消息添加到队列的时间。|
|`NextVisibleTime`|`DateTimeOffset?`|消息下一次可见的时间。|
|`PopReceipt`|`string`|消息的 pop 接收方。|

## <a name="trigger---poison-messages"></a>触发器 - 有害消息

队列触发函数失败时，Azure Functions 会针对给定的队列消息重试该函数最多 5 次（包括第一次尝试）。 如果 5 次尝试全部失败，函数运行时会将一条消息添加到名为 *&lt;originalqueuename>-poison* 的队列。 可以编写一个函数来处理有害队列中的消息，并记录这些消息，或者发送需要注意的通知。

若要手动处理有害消息，请检查队列消息的 [dequeueCount](#trigger---message-metadata)。

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](functions-host-json.md#queues) 文件包含控制队列触发器行为的设置。

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="queue-storage-output-binding"></a>队列存储输出绑定

使用 Azure 队列存储输出绑定可将消息写入队列。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [预编译 C#](#output---c-example)
* [C# 脚本](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示针对收到的每个 HTTP 请求创建一条队列消息的[预编译 C#](functions-dotnet-class-library.md) 代码。

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数针对收到的每个 HTTP 请求创建一个包含 POCO 有效负载的队列项。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[配置](#output---configuration)部分解释了这些属性。

下面是可创建一条队列消息的 C# 脚本代码：

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

可以使用 `ICollector` 或 `IAsyncCollector` 参数一次性发送多条消息。 以下 C# 脚本代码发送多条消息，其中一条消息包含 HTTP 请求数据，另一条消息包含硬编码值：

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个 Blob 触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数针对收到的每个 HTTP 请求创建一个队列项。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[配置](#output---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

可以通过定义 `myQueueItem` 输出绑定的消息数组，一次性发送多条消息。 以下 JavaScript 代码针对收到的每个 HTTP 请求发送两条包含硬编码值的队列消息。

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes-for-precompiled-c"></a>输出 - 预编译 C# 的特性
 
对于[预编译 C#](functions-dotnet-class-library.md) 函数，请使用 NuGet 包 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 中定义的 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)。

该特性将应用到 `out` 参数，或应用到函数的返回值。 该特性的构造函数采用队列的名称，如以下示例中所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
```

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[触发器 - 预编译 C# 的特性](#trigger---attributes-for-precompiled-c)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 *function.json* 文件和 `Queue` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**类型** | 不适用 | 必须设置为 `queue`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `out`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 表示函数代码中的队列的变量的名称。 设置为 `$return` 可引用函数返回值。| 
|**queueName** |**QueueName** | 队列的名称。 | 
|**连接** | **Connection** |包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br>在本地进行开发时，应用设置将取 [local.settings.json 文件](functions-run-local.md#local-settings-file)的值。|

## <a name="output---usage"></a>输出 - 用法
 
在 C# 和 C# 脚本中，可以使用 `out T paramName` 等方法参数编写一条队列消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 可以使用方法返回类型而不使用 `out` 参数，`T` 可为以下任何类型：

* 可序列化为 JSON 的 POCO
* `string`
* `byte[]`
* [CloudQueueMessage] 

在 C# 和 C# 脚本中，可使用以下类型之一编写多条队列消息： 

* `ICollector<T>` 或 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

在 JavaScript 函数中，可以使用 `context.bindings.<name>` 访问输出队列消息。 可对队列项有效负载使用字符串或 JSON 可序列化对象。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [转到有关使用队列存储触发器的快速入门](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [转到有关使用队列存储输出绑定的教程](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage