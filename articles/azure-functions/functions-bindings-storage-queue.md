---
title: "Azure Functions 存储队列绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Azure 存储触发器和绑定。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/18/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: 770cac8809ab9f3d6261140333ec789ee1390daf
ms.openlocfilehash: bf9bd2a1b5acdf5a4a4f862bef693f8c60c63a33


---
# <a name="azure-functions-storage-queue-bindings"></a>Azure Functions 存储队列绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和编码 Azure 存储队列绑定。 Azure Functions 支持 Azure 存储队列的触发器和 输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>存储队列触发器
通过 Azure 存储队列触发器可监视存储队列的新消息并对其做出反应。 

函数的存储队列触发器在函数 .json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中创建存储帐户或选择现有存储帐户时，可在“集成”选项卡中配置此应用设置。 若要手动创建此应用设置，请参阅[管理应用服务设置](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)。

可在 host.json 文件中包含[其他设置](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)以进一步微调存储队列触发器。  

### <a name="handling-poison-queue-messages"></a>处理有害队列消息
队列触发函数失败时，Azure Functions 对于给定队列消息重试该函数最多&5; 次（包括第一次尝试）。 如果&5; 次尝试全部失败，函数会将消息添加到名为 &lt;originalqueuename>-poison 的存储队列。 你可以编写一个函数来处理有害队列中的消息，并记录这些消息，或者发送需要注意的通知。 

若要手动处理有害消息，可以通过检查 `dequeueCount` 获取信息被处理的次数（请参阅[队列触发器元数据](#meta)）。

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>触发器使用情况
在 C# 函数中，通过在函数签名中使用命名参数来绑定到输入消息，如 `<T> <name>`。
其中 `T` 是数据要进行反序列化的目标数据类型，`paramName` 是在[触发器绑定](#trigger)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输入 blob 数据。

队列消息可以反序列化为以下任何类型：

* [Object](https://msdn.microsoft.com/library/system.object.aspx) - 适用于 JSON 序列化的消息。 在声明自定义输入类型时，运行时会尝试反序列化 JSON 对象。 
* String
* Byte Array
* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx)（仅限 C#）

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>队列触发器元数据
可以使用以下变量名称在函数中获取队列元数据：

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger（另一种以字符串形式检索队列消息文本的方法）

请参阅如何使用[触发器示例](#triggersample)中的队列元数据

<a name="triggersample"></a>

## <a name="trigger-sample"></a>触发器示例
假设采用以下 function.json，其定义存储队列触发器：

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

请参阅可检索和记录队列元数据的特定于语言的示例。

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C 中的触发器示例# #
```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js 中的触发器示例

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>存储队列输出绑定
借助 Azure 存储队列输出绑定，用户可将消息写入到函数中的存储队列。 

函数的存储队列输出在函数 .json 的 `bindings` 数组中使用以下 JSON 对象：

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection` 必须包含具有存储连接字符串的应用设置的名称。 在 Azure 门户中，创建存储帐户或选择现有存储帐户时，“集成”选项卡中的标准编辑器会为你配置此应用设置。 若要手动创建此应用设置，请参阅[管理应用服务设置](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)。

<a name="outputusage"></a>

## <a name="output-usage"></a>输出使用情况
在 C# 函数中，通过在函数签名中使用命名 `out` 参数写入队列消息，如 `out <T> <name>`。 在本例中，`T` 是消息要进行序列化的目标数据类型，`paramName` 是在[输出绑定](#output)中指定的名称。 在 Node.js 函数中，使用 `context.bindings.<name>` 访问输出。

可以使用代码中的任何数据类型输出队列消息：

* 任何 [Object](https://msdn.microsoft.com/library/system.object.aspx)：`out MyCustomType paramName`  
用于 JSON 序列化。  在声明自定义输出类型时，运行时会尝试将对象序列化至 JSON。 函数退出时，如果输出参数为 null，则运行时将创建队列消息作为 null 对象。
* String：`out string paramName`  
用于测试消息。 只有当函数退出时字符串参数为非 null，运行时才创建消息。
* Byte array：`out byte[]` 

这些附加输出类型支持 C# 函数：

* [CloudQueueMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.aspx)：`out CloudQueueMessage` 
* `ICollector<T>` 或 `IAsyncCollector<T>`，其中 `T` 是所支持的类型之一。

<a name="outputsample"></a>

## <a name="output-sample"></a>输出示例
假设有以下定义[存储队列触发器](functions-bindings-storage-queue.md)、存储 blob 输入和存储 blob 输出的 function.json：

使用手动触发器，并将输入写入队列消息的存储队列输出绑定的示例 function.json：

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "queue",
      "name": "myQueueItem",
      "queueName": "myqueue",
      "connection": "my_storage_connection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

请参阅为每个输入队列消息写入输出队列消息的特定于语言的示例。

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C 中的输出示例# #

```cs
public static void Run(string input, out string myQueueItem, TraceWriter log)
{
    myQueueItem = "New message: " + input;
}
```

或者，发送多条消息，

```cs
public static void Run(string input, ICollector<string> myQueueItem, TraceWriter log)
{
    myQueueItem.Add("Message 1: " + input);
    myQueueItem.Add("Message 2: " + "Some other message.");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js 中的输出示例

```javascript
module.exports = function(context) {
    // Define a new message for the myQueueItem output binding.
    context.bindings.myQueueItem = "new message";
    context.done();
};
```

或者，发送多条消息，

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>后续步骤

有关使用存储队列触发器和绑定的函数示例，请参阅[创建与 Azure 服务连接的 Azure Function](functions-create-an-azure-connected-function.md)。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Jan17_HO3-->


