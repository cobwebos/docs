---
title: "搭配使用 Azure Functions 和 .Net 类库 | Microsoft Docs"
description: "了解如何创作可用于 Azure Functions 的 .NET 类库"
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "Azure Functions，函数，事件处理，动态计算，无服务体系结构"
ms.assetid: 9f5db0c2-a88e-4fa8-9b59-37a7096fc828
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/09/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dbcec586fe5ee06da38c37cf1ead2469386cc5c3
ms.contentlocale: zh-cn
ms.lasthandoff: 06/10/2017


---
# <a name="using-net-class-libraries-with-azure-functions"></a>搭配使用 Azure Functions 和 .Net 类库

除脚本文件外，Azure Functions 还支持发布类库作为一个或多个函数的实现。 建议使用 [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)。

## <a name="prerequisites"></a>先决条件 

本文要求满足以下先决条件：

- [Visual Studio 2017 15.3 预览版](https://www.visualstudio.com/vs/preview/)。 安装工作负荷“ASP.NET 和 Web 开发”以及“Azure 开发”。
- [Azure Function Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=AndrewBHall-MSFT.AzureFunctionToolsforVisualStudio2017)

## <a name="functions-class-library-project"></a>Functions 类库项目

在 Visual Studio 中创建新的 Azure Functions 项目。 新的项目模板会创建 *host.json* 和 *local.settings.json* 文件。 可[在 host.json 中自定义 Azure Functions 运行时设置](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)。 

*local.settings.json* 文件将存储应用设置、连接字符串和 Azure Functions Core Tools 的设置。 若要了解该结构的详细信息，请参阅[在本地对 Azure Functions 编写代码和进行测试](functions-run-local.md#local-settings)。

### <a name="functionname-attribute"></a>FunctionName 属性

属性 [ `FunctionNameAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/FunctionNameAttribute.cs) 将标记一个方法作为函数入口点。 它必须仅与一个触发器并与零个或更多输入及输出绑定同时使用。

### <a name="conversion-to-functionjson"></a>转换为 function.json

生成 Azure Functions 项目后，它将在目录中创建一个 `[FunctionName]` 定义的函数名所匹配的文件 `function.json`。 它可指定触发器和绑定，并指向项目程序集文件。

转换操作由 NuGet 程序包 [Microsoft\.NET\.Sdk\.Functions](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions) 执行。 源可在 GitHub 存储库 [azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk) 中使用。

## <a name="triggers-and-bindings"></a>触发器和绑定

下表列出了可在 Azure Functions 类库项目中使用的触发器和绑定。 所有属性均位于命名空间 `Microsoft.Azure.WebJobs` 中。

| 绑定 | 属性 | NuGet 包 |
|------   | ------    | ------        |
| [Blob 存储触发器、输入、输出](#blob-storage) | [BlobAttribute]、[StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | [Blob 存储] |
| [Cosmos DB 输入和输出绑定](#cosmos-db) | [DocumentDBAttribute] | [Microsoft.Azure.WebJobs.Extensions.DocumentDB] | 
| [事件中心触发器和输出](#event-hub) | [EventHubTriggerAttribute]、[EventHubAttribute] | [Microsoft.Azure.WebJobs.ServiceBus] |
| [外部文件输入和输出](#api-hub) | [ApiHubFileAttribute] | [Microsoft.Azure.WebJobs.Extensions.ApiHub] |
| [HTTP 和 Webhook 触发器](#http) | [HttpTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions.Http] |
| [移动应用输入和输出](#mobile-apps) | [MobileTableAttribute] | [Microsoft.Azure.WebJobs.Extensions.MobileApps] | 
| [通知中心输出](#nh) | [NotificationHubAttribute] | [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] | 
| [队列存储触发器和输出](#queue) | [QueueAttribute]、[StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [SendGrid 输出](#sendgrid) | [SendGridAttribute] | [Microsoft.Azure.WebJobs.Extensions.SendGrid] | 
| [服务总线触发器和输出](#service-bus) | [ServiceBusAttribute]、[ServiceBusAccountAttribute] | [Microsoft.Azure.WebJobs.ServiceBus]
| [表存储输入和输出](#table) | [TableAttribute]、[StorageAccountAttribute] | [Microsoft.Azure.WebJobs] | 
| [计时器触发器](#timer) | [TimerTriggerAttribute] | [Microsoft.Azure.WebJobs.Extensions] | 
| [Twilio 输出](#twilio) | [TwilioSmsAttribute] | [Microsoft.Azure.WebJobs.Extensions.Twilio] | 

<a name="blob-storage"></a>

### <a name="blob-storage-trigger-input-and-output-bindings"></a>Blob 存储触发器、输入和输出绑定

Azure Functions 支持 Azure Blob 存储使用触发器、输入以及输出绑定。 有关绑定表达式和元数据的详细信息，请参阅 [Azure Functions Blob 存储绑定](functions-bindings-storage-blob.md)。

Blob 触发器由 `[BlobTrigger]` 属性定义。 可使用 `[StorageAccount]` 属性定义由整个函数或类使用的存储帐户。

```csharp
[StorageAccount("AzureWebJobsStorage")]
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Blob 输入和输出通过 `[Blob]` 属性以及一个表示读取或写入的 `FileAccess` 参数进行定义。 以下示例使用 Blob 触发器和 Blob 输出绑定。

```csharp
[FunctionName("ResizeImage")]
[StorageAccount("AzureWebJobsStorage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

<a name="cosmos-db"></a>

### <a name="cosmos-db-input-and-output-bindings"></a>Cosmos DB 输入和输出绑定

Azure Functions 支持 Cosmos DB 的输入和输出绑定。 有关 Cosmos DB 绑定功能的详细信息，请参阅 [Azure Functions Cosmos DB 绑定](functions-bindings-documentdb.md)。

若要绑定到 Cosmos DB 文档，请使用 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.DocumentDB] 中的属性 `[DocumentDB]`。 以下示例具有一个队列触发器和一个 DocumentDB API 输出绑定：

```csharp
[FunctionName("QueueToDocDB")]        
public static void Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, 
    [DocumentDB("ToDoList", "Items", ConnectionStringSetting = "DocDBConnection")] out dynamic document)
{
    document = new { Text = myQueueItem, id = Guid.NewGuid() };
}

```

<a name="event-hub"></a>

### <a name="event-hubs-trigger-and-output"></a>事件中心触发器和输出

Azure Functions 支持事件中心的触发器和输出绑定。 有关详细信息，请参阅 [Azure Functions 事件中心绑定](functions-bindings-event-hubs.md)。

`[Microsoft.Azure.WebJobs.ServiceBus.EventHubTriggerAttribute]` 和 `[Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute]` 类型在 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus] 中进行定义。 

以下示例使用事件中心触发器：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnection")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

以下示例有一个事件中心输出，使用方法返回值作为输出：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnection")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

<a name="api-hub"></a>

### <a name="external-file-input-and-output"></a>外部文件输入和输出

Azure Functions 支持 Google Drive、Dropbox 和 OneDrive 等外部文件使用触发器、输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 外部文件绑定](functions-bindings-external-file.md)。 `[ExternalFileTrigger]` 和 `[ExternalFile]` 属性在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.ApiHub] 中进行定义。

下面的 C# 示例演示外部文件输入和输出绑定。 代码将输入文件复制到输出文件。

```csharp
[StorageAccount("MyStorageConnection")]
[FunctionName("ExternalFile")]
[return: ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}-Copy", FileAccess.Write)]
public static string Run([QueueTrigger("myqueue-items")] string myQueueItem, 
    [ApiHubFile("MyFileConnection", "samples-workitems/{queueTrigger}", FileAccess.Read)] string myInputFile, 
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    return myInputFile;
}
```

<a name="http"></a>

### <a name="http-and-webhooks"></a>HTTP 和 webhook

`HttpTrigger` 属性用于定义 HTTP 触发器或 webhook。 此属性在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.Http] 中进行定义。 可自定义授权级别、webhook 类型、路由和方法。 以下示例使用匿名身份验证和 _genericJson_ webhook 类型定义 HTTP 触发器。

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a name="mobile-apps"></a>

### <a name="mobile-apps-input-and-output"></a>移动应用输入和输出

Azure Functions 支持移动应用的输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 移动应用绑定](functions-bindings-mobile-apps.md)。 `[MobileTable]` 属性在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.MobileApps] 中进行定义。

以下示例演示移动应用输出绑定：

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem, TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

<a name="nh"></a>

### <a name="notification-hubs-output"></a>通知中心输出

Azure Functions 支持通知中心使用输出绑定。 有关详细信息，请参阅 [Azure Functions 通知中心输出绑定](functions-bindings-notification-hubs.md)。 `[NotificationHub]` 属性在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.NotificationHubs] 中进行定义。

<a name="queue"></a>

### <a name="queue-storage-trigger-and-output"></a>队列存储触发器和输出

Azure Functions 支持 Azure 队列使用触发器和输出绑定。 有关详细信息，请参阅 [Azure Functions 队列存储绑定](functions-bindings-storage-queue.md)。

以下示例使用 `[Queue]` 属性演示如何通过队列输出绑定使用函数返回类型。 若要定义队列触发器，请使用 `[QueueTrigger]` 属性。

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    // HTTP trigger with queue output binding
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }

    // Queue trigger
    [FunctionName("QueueTrigger")]
    [StorageAccount("AzureWebJobsStorage")]
    public static void QueueTrigger([QueueTrigger("myqueue-items")] string myQueueItem, TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}

```

<a name="sendgrid"></a>

### <a name="sendgrid-output"></a>SendGrid 输出

Azure Functions 支持 SendGrid输出绑定以按编程方式发送电子邮件。 有关详细信息，请参阅 [Azure Functions SendGrid 绑定](functions-bindings-sendgrid.md)。

`[SendGrid]` 属性在 NuGet 包 [Microsoft.Azure.WebJobs.Extensions.SendGrid] 中进行定义。

<a name="service-bus"></a>

### <a name="service-bus-trigger-and-output"></a>服务总线触发器和输出

Azure Functions 支持对服务总线队列和主题的触发器和输出绑定。 有关配置绑定的详细信息，请参阅 [Azure Functions 服务总线绑定](functions-bindings-service-bus.md)。

`[ServiceBusTrigger]` 和 `[ServiceBus]` 属性在 NuGet 包 [Microsoft.Azure.WebJobs.ServiceBus] 中进行定义。 

服务总线队列触发器的示例如下：

```csharp
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run([ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

以下示例介绍服务总线输出绑定，使用方法返回类型作为输出：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, TraceWriter log)
{
    log.Info($"C# function processed: {input.Text}");
    return input.Text;
}
```        

<a name="table"></a>

### <a name="table-storage-input-and-output"></a>表存储输入和输出

Azure Functions 支持 Azure 表存储使用输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 表存储绑定](functions-bindings-storage-table.md)。

以下示例一个带两个函数的类，演示表存储输出和输入绑定。 

```csharp
[StorageAccount("AzureWebJobsStorage")]
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, TraceWriter log)
    {
        log.Info($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }

    // use the metadata parameter "queueTrigger" to bind the queue payload
    [FunctionName("TableInput")]
    public static void TableInput([QueueTrigger("table-items")] string input, [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, TraceWriter log)
    {
        log.Info($"C# function processed: {poco.Text}");
    }
}

```

<a name="timer"></a>

### <a name="timer-trigger"></a>计时器触发器

Azure Functions 有一个计时器触发器绑定，允许用户根据定义的计划运行函数代码。 有关绑定功能的详细信息，请参阅[使用 Azure Functions 计划代码执行](functions-bindings-timer.md)。

在“消耗”计划中，可通过 [CRON 表达式](http://en.wikipedia.org/wiki/Cron#CRON_expression)定义计划。 若使用应用服务计划，则还可使用 TimeSpan 字符串。 

以下示例定义每 5 分钟运行一次的计时器触发器：

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

<a name="twilio"></a>

### <a name="twilio-output"></a>Twilio 输出

Azure Functions 支持 Twilio 输出绑定以使函数能够发送短信。 有关详细信息，请参阅[使用 Twilio 输出绑定从 Azure Functions 发送短信](functions-bindings-twilio.md)。 

`[TwilioSms]` 属性在 [Microsoft.Azure.WebJobs.Extensions.Twilio] 包中进行定义。

下面的 C# 示例使用一个队列触发器和一个 Twilio 输出绑定：

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run([QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

## <a name="next-steps"></a>后续步骤

若要深入了解如何在 C# 脚本中使用 Azure Functions，请参阅 [Azure Functions C\# 脚本开发人员参考](functions-reference-csharp.md)。

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


<!-- NuGet packages --> 
[Microsoft.Azure.WebJobs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.DocumentDB]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.MobileApps]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.NotificationHubs/1.1.0-beta1
[Microsoft.Azure.WebJobs.ServiceBus]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.SendGrid]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid/2.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions.Http]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http/1.0.0-beta1
[Microsoft.Azure.WebJobs.Extensions.BotFramework]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.BotFramework/1.0.15-beta
[Microsoft.Azure.WebJobs.Extensions.ApiHub]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ApiHub/1.0.0-beta4
[Microsoft.Azure.WebJobs.Extensions.Twilio]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio/1.1.0-beta1
[Microsoft.Azure.WebJobs.Extensions]: http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/2.1.0-beta1


<!-- Links to source --> 
[DocumentDBAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs
[EventHubAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs
[EventHubTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs
[MobileTableAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs
[NotificationHubAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs 
[ServiceBusAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs
[ServiceBusAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs
[QueueAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs
[StorageAccountAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs
[BlobAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs
[TableAttribute]: https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs
[TwilioSmsAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs
[SendGridAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs
[HttpTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs
[ApiHubFileAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.ApiHub/ApiHubFileAttribute.cs
[TimerTriggerAttribute]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs
