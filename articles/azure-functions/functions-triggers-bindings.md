---
title: "使用 Azure Functions 中的触发器和绑定 | Microsoft 文档"
description: "了解如何使用 Azure Functions 中的触发器和绑定将代码执行连接到联机事件和基于云的服务。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: a56d71d437814ed08b2e0a05d9acc8448f6b9ae5
ms.lasthandoff: 03/17/2017


---

# <a name="learn-how-to-work-with-triggers-and-bindings-in-azure-functions"></a>了解如何使用 Azure Functions 中的触发器和绑定 
本主题介绍如何使用 Azure Functions 中的触发器和绑定将代码连接到各种触发器和 Azure 服务以及其他基于云的服务。 其中专门介绍了一些受所有绑定类型支持的高级绑定功能和语法。  

有关使用特定类型的触发器或绑定的详细信息，请参阅以下参考主题之一：

| | | | |  
| --- | --- | --- | --- |  
| [HTTP/Webhook](functions-bindings-http-webhook.md) | [计时器](functions-bindings-timer.md) | [移动应用](functions-bindings-mobile-apps.md) | [服务总线](functions-bindings-service-bus.md)  |  
| [DocumentDB](functions-bindings-documentdb.md) |  [存储 Blob](functions-bindings-storage-blob.md) | [存储队列](functions-bindings-storage-queue.md) |  [存储表](functions-bindings-storage-table.md) |  
| [事件中心](functions-bindings-event-hubs.md) | [通知中心](functions-bindings-notification-hubs.md) | [SendGrid](functions-bindings-sendgrid.md) | [Twilio](functions-bindings-twilio.md) |   
| | | | |  

这些文章假定用户已阅读了 [Azure Functions 开发人员参考](functions-reference.md)、[C#](functions-reference-csharp.md)、[F#](functions-reference-fsharp.md) 或 [Node.js](functions-reference-node.md) 开发人员参考文章。

## <a name="overview"></a>概述
触发器是用于触发自定义代码的事件响应。 触发器使用户能够在 Azure 平台或本地之间响应事件。 绑定表示将代码连接到所需触发器或相关的联输入/输出数据的必要元数据。 每个函数的 *function.json* 文件包含所有相关绑定。 一个函数可以拥有的输入和输出绑定数量没有限制。 但是，每个函数仅支持单个触发器绑定。  

若要深入了解其他可与 Azure Function 应用集成的绑定，请参考下表。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

为了总体上更好地了解触发器和绑定，假定要执行某些代码来处理放入到 Azure 存储队列中的新项。 Azure Functions 提供了 Azure 队列触发器以支持此操作。 监视队列需要以下信息：

* 队列所在的存储帐户。
* 队列名称。
* 代码引用放入到队列中的新项所使用的的变量名称。  

队列触发器绑定包含一个 Azure 函数的此信息。 下面是包含一个队列触发器的 function.json 示例。 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

代码可能会发送不同类型的输出，发送的类型具体取决于处理新队列项的方式。 例如，用户可能想要将新的记录写入 Azure 存储表。  若要执行此操作，请创建到 Azure 存储表的输出绑定。 下面是一个示例 function.json，其中包含可用于队列触发器的存储表输出绑定。 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

下面的 C# 函数会响应放入队列的新项，并将新用户条目写入 Azure 存储表。

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

有关更多代码示例和支持的 Azure 存储类型的更多具体信息，请参阅[适用于 Azure 存储的 Azure Functions 触发器和绑定](functions-bindings-storage.md)。

若要在 Azure 门户中使用更高级的绑定功能，请单击函数“集成”选项卡上的“高级编辑器”选项。 通过高级编辑器，可直接在门户中编辑 function.json。

## <a name="random-guids"></a>随机 GUID
Azure Functions 向绑定提供语法来生成随机 GUID。 下面的绑定语法将输出写入到存储容器中具有唯一名称的新 BLOB： 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>返回单个输出
在函数代码返回单个输出的情况下，可使用名为 `$return` 的输出绑定在代码中保留更自然的函数签名。 这仅可用于支持返回值的语言（C#、Node.js、F#）。 绑定与以下用于队列触发器的 blob 输出绑定相类似。

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

下面的 C# 代码会更自然地返回输出，无需在函数签名中使用 `out` 参数。

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

异步示例：

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


以下演示了 Node.js 的同一方法：

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

下面是 F# 的示例：

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

通过使用 `$return` 指定单个输出，还可将其用于多个输出参数。

## <a name="resolving-app-settings"></a>解析应用设置
最佳做法是使用应用设置将敏感信息存储为运行时环境的一部分。 通过将敏感信息与应用的配置文件隔离，在使用公共存储库存储应用文件时可限制信息暴露。  

应用设置名称包含在百分号中（即 `%your app setting%`）时，Azure Functions 运行时会将应用设置解析为值。 下面的 [Twilio 绑定](functions-bindings-twilio.md)在绑定的 `from` 字段中使用名为 `TWILIO_ACCT_PHONE` 的应用设置。 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>参数绑定
可将设置配置为动态绑定到触发器的输入绑定一部分的数据，而不是对输出绑定属性使用静态配置设置。 请考虑使用 Azure 存储队列处理新订单的情况。 每个新的队列项是至少包含以下属性的 JSON 字符串：

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address",
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

你可能会使用 Twilio 帐户向客户发送短信，告知客户已收到订单。  可将 Twilio 输出绑定的 `body` 和 `to` 字段配置为动态绑定到作为此输出一部分的 `name` 和 `mobileNumber`，如下所示。

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

现在，函数代码仅需初始化输出参数，如下所示。 执行期间，输出属性会绑定到所需的输入数据。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js：

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>运行时高级绑定（命令性绑定）

使用 function.json 的标准输入和输出绑定模式称为[*声明性*](https://en.wikipedia.org/wiki/Declarative_programming)绑定，其中绑定由 JSON 声明定义。 但是，可使用[命令性](https://en.wikipedia.org/wiki/Imperative_programming)绑定。 通过此模式，可动态绑定到函数代码中受支持的任意数量的输入和输出绑定。
在绑定路径计算或其他输入在函数中需要在运行时（而不是在设计时）发生的情况下，可能需要命令式绑定。 

如下所示定义命令性绑定：

- 对于所需的命令性绑定，**不要**包含 function.json 中的条目。
- 传递输入参数 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 或 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)。 
- 使用下面的 C# 模式执行数据绑定。

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

其中，`BindingTypeAttribute` 是定义绑定的 .NET 属性，`T` 是该绑定类型所支持的输入或输出类型。 `T` 也不能是 `out` 参数类型 （例如 `out JObject`）。 例如，移动应用表输出绑定支持 [6 种输出类型](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)，但对于 `T`，可仅使用 [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 或 [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)。
    
下面的示例代码使用在运行时定义的 blob 路径创建[存储 blob 输出绑定](functions-bindings-storage-blob.md#storage-blob-output-binding)，然后将字符串写入此 blob。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) 定义[存储 blob](functions-bindings-storage-blob.md) 输入或输出绑定，[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) 是支持的输出绑定类型。
以其原本方式，此代码会获取存储帐户连接字符串（即 `AzureWebJobsStorage`）的默认应用设置。 通过添加 [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) 和将属性数组传递到 `BindAsync<T>()`，可指定要使用的自定义应用设置。 例如，

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

下表显示了对于每个绑定类型要使用的相应 .NET 属性和要引用的包。

> [!div class="mx-codeBreakAll"]
| 绑定 | 属性 | 添加引用 |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| 事件中心 | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| 通知中心 | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| 服务总线 | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| 存储队列 | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 存储 blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| 存储表 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [测试函数](functions-test-a-function.md)
* [扩展函数](functions-scale.md)


