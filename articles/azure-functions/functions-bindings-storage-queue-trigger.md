---
title: 适用于 Azure Functions 的 Azure 队列存储触发器
description: 了解如何在 Azure 队列存储数据更改时运行 Azure 功能。
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492794"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>适用于 Azure Functions 的 Azure 队列存储触发器

队列存储触发器在向 Azure 队列存储添加消息时运行函数。

## <a name="encoding"></a>编码

函数需要 base64 编码字符串。 对编码类型进行的任何调整（若要将数据作为 base64 编码字符串进行准备）需要在调用服务中实现。

## <a name="example"></a>示例

在队列中收到新项时，可以使用队列触发器启动一个函数。 队列消息作为输入提供给该函数。

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示可在每次处理某个队列项之后轮询 `myqueue-items` 队列并写入日志的 [C# 函数](functions-dotnet-class-library.md)。

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

以下示例演示 function.json 文件中的一个队列触发器绑定以及使用该绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 每次处理某个队列项之后，该函数会轮询 `myqueue-items` 队列并写入日志。

function.json 文件如下所示：

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

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

[用法](#usage)部分解释了 function.json 中的 `name` 属性命名的 `myQueueItem`。  [消息元数据部分](#message-metadata)解释了所有其他所示变量。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个队列触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 每次处理某个队列项之后，该函数会轮询 `myqueue-items` 队列并写入日志。

function.json 文件如下所示：

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

[配置](#configuration)部分解释了这些属性。

> [!NOTE]
> Name 参数在包含队列项有效负载的 JavaScript 代码中反映为 `context.bindings.<name>`。 此有效负载也作为第二个参数传递给函数。

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[用法](#usage)部分解释了 function.json 中的 `name` 属性命名的 `myQueueItem`。  [消息元数据部分](#message-metadata)解释了所有其他所示变量。

# <a name="python"></a>[Python](#tab/python)

下面的示例演示如何通过触发器读取传递给函数的队列消息。

存储队列触发器在函数*类型*设置为 `queueTrigger`的*json*中定义。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

代码 *_\_init_\_py*将参数声明为 `func.ServiceBusMessage`，这允许您在函数中读取队列消息。

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

以下 Java 示例显示了存储队列触发器函数，该函数将触发的消息记录到队列 `myqueuename`中。

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>特性和批注

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)，请使用以下属性来配置队列触发器：

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  该特性的构造函数采用要监视的队列的名称，如以下示例中所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  可以设置 `Connection` 属性以指定包含要使用的存储帐户连接字符串的应用设置，如以下示例中所示：

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  有关完整示例，请参阅[示例](#example)。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

要使用的存储帐户按以下顺序确定：

* `QueueTrigger` 特性的 `Connection` 属性。
* 作为 `QueueTrigger` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* “AzureWebJobsStorage”应用设置。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

C#脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger` 批注使你可以访问触发函数的队列。 下面的示例通过 `message` 参数使队列消息可用于函数。

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| properties    | 说明 |
|-------------|-----------------------------|
|`name`       | 声明函数签名中的参数名称。 触发函数时，此参数的值将具有队列消息的内容。 |
|`queueName`  | 声明存储帐户中的队列名称。 |
|`connection` | 指向存储帐户连接字符串。 |

---

## <a name="configuration"></a>配置

下表解释了在 function.json 文件和 `QueueTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用| 必须设置为 `queueTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction**| 不适用 | 只能在 *function.json* 文件中设置。 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 |包含功能代码中的队列项有效负载的变量的名称。  |
|**queueName** | **QueueName**| 要轮询的队列的名称。 |
|连接 | **Connection** |包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为 "MyStorage"，函数运行时将查找名为 "MyStorage" 的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>用法

# <a name="c"></a>[C#](#tab/csharp)

使用方法参数（如 `string paramName`）访问消息数据。 可以绑定到以下任何类型：

* 对象 - Functions 运行时将 JSON 负载反序列化为代码中定义的任意类的实例。 
* `string`
* `byte[]`
* [CloudQueueMessage]

如果尝试绑定到 `CloudQueueMessage` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="c-script"></a>[C#脚本](#tab/csharp-script)

使用方法参数（如 `string paramName`）访问消息数据。 `paramName` 是在*函数 json*的 `name` 属性中指定的值。 可以绑定到以下任何类型：

* 对象 - Functions 运行时将 JSON 负载反序列化为代码中定义的任意类的实例。 
* `string`
* `byte[]`
* [CloudQueueMessage]

如果尝试绑定到 `CloudQueueMessage` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

队列项有效负载可通过 `context.bindings.<NAME>`，其中 `<NAME>` 与*函数 json*中定义的名称匹配。 如果有效负载为 JSON，则会将值反序列化为对象。

# <a name="python"></a>[Python](#tab/python)

通过类型为[QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python)的参数访问队列消息。

# <a name="java"></a>[Java](#tab/java)

[QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable)批注使你可以访问触发函数的队列消息。

---

## <a name="message-metadata"></a>消息元数据

[队列触发器提供了数个元数据属性。](./functions-bindings-expressions-patterns.md#trigger-metadata) 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 这些属性是[CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)类的成员。

|properties|Type|说明|
|--------|----|-----------|
|`QueueTrigger`|`string`|队列有效负载（如果是有效的字符串）。 如果队列消息有效负载是一个字符串，则 `QueueTrigger` 与*函数 json*中 `name` 属性命名的变量具有相同的值。|
|`DequeueCount`|`int`|此消息取消排队的次数。|
|`ExpirationTime`|`DateTimeOffset`|消息过期的时间。|
|`Id`|`string`|队列消息 ID。|
|`InsertionTime`|`DateTimeOffset`|消息添加到队列的时间。|
|`NextVisibleTime`|`DateTimeOffset`|消息下一次可见的时间。|
|`PopReceipt`|`string`|消息的 pop 接收方。|

## <a name="poison-messages"></a>病毒消息

队列触发函数失败时，Azure Functions 会针对给定的队列消息重试该函数最多 5 次（包括第一次尝试）。 如果 5 次尝试全部失败，函数运行时会将一条消息添加到名为 *&lt;originalqueuename>-poison* 的队列。 可以编写一个函数来处理有害队列中的消息，并记录这些消息，或者发送需要注意的通知。

若要手动处理有害消息，请检查队列消息的 [dequeueCount](#message-metadata)。

## <a name="polling-algorithm"></a>轮询算法

队列触发器实现了随机指数退让算法，以降低空闲队列轮询对存储交易成本造成的影响。

算法使用以下逻辑：

- 当发现消息时，运行时将等待两秒钟，然后检查另一条消息
- 如果未找到任何消息，它将等待大约四秒，然后重试。
- 如果后续尝试获取队列消息失败，则等待时间会继续增加，直到达到最长等待时间（默认为 1 分钟）。
- 可以通过 [host.json 文件](functions-host-json.md#queues)中的 `maxPollingInterval` 属性配置最大等待时间。

对于本地开发，最大轮询间隔默认为两秒。

对于计费，运行时所用的时间是 "免费" 的，不会根据你的帐户进行计数。

## <a name="concurrency"></a>并发

当有多个队列消息在等待时，队列触发器会检索一批消息并以并发方式调用函数实例来处理它们。 默认情况下，批大小为 16。 当处理的数量下降到 8 时，运行时可获取另一个批，并开始处理这些消息。 因此，单个虚拟机 (VM) 上每个函数处理的最大并发消息数是 24。 此限制分别应用于每个 VM 上各个队列触发的函数。 如果你的函数应用横向扩展到多个 VM，则每个 VM 将等待触发器并尝试运行函数。 例如，如果某个函数应用横向扩展到 3 个 VM，则每个队列触发的函数的默认最大并发实例数是 72。

可以在 [host.json 文件](functions-host-json.md#queues)中配置用于获取新批的批大小和阈值。 如果希望在函数应用中最大程度地降低查询触发的函数的并行执行，可以将批大小设置为 1。 只有当函数在单个虚拟机 (VM) 上运行时，此设置才可消除并发。 

队列触发器会自动阻止函数多次处理队列消息；无需将函数编写为幂等函数。

## <a name="hostjson-properties"></a>host json 属性

[host.json](functions-host-json.md#queues) 文件包含控制队列触发器行为的设置。 有关可用设置的详细信息，请参阅[host. json 设置](functions-bindings-storage-queue-output.md#hostjson-settings)部分。

## <a name="next-steps"></a>后续步骤

- [写入队列存储消息（输出绑定）](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
