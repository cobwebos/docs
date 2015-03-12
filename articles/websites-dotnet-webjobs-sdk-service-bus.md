<properties pageTitle="如何通过 WebJobs SDK 使用 Azure Service Bus" metaKeywords="WebJobs SDK Azure Service Bus .NET C#" description="了解如何通过 WebJobs SDK 使用 Azure Service Bus 队列和主题。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,service-bus" documentationCenter=".NET" title="How to use Azure Service Bus with the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="12/15/2014" ms.author="tdykstra" />

# 如何通过 WebJobs SDK 使用 Azure Service Bus

本指南提供 C# 代码示例，用于演示如何在创建或更新 Azure Blob 后触发进程。这些代码示例使用 [WebJobs SDK](../websites-dotnet-webjobs-sdk/) 版本 1.x。

本指南假定你知道[如何在 Visual Studio 中创建 Web 作业项目，其中包含指向存储帐户](../websites-dotnet-webjobs-sdk-get-started/)的连接字符串。

代码段只显示了函数，而未显示用于创建 `JobHost` 对象的代码，如以下示例所示：

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 目录

-   [先决条件](#prerequisites)
-   [如何在接收队列消息时触发函数](#trigger)
-   [如何创建队列消息](#create)
-   [如何使用 Service Bus 主题](#topics)
-   [存储队列文章涵盖的相关主题](#queues)
-   [后续步骤](#nextsteps)

## <a id="prerequisites"></a> 先决条件

若要使用 Service Bus，你必须安装 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet 包，以及其他 WebJobs SDK 包。 

你还必须设置 AzureWebJobsServiceBus 连接字符串，以及存储连接字符串。

## <a id="trigger"></a> 如何在接收 Service Bus 队列消息时触发函数

若要编写接收队列消息时 WebJobs SDK 调用的函数，请使用 `ServiceBusTrigger` 属性。该属性构造函数使用一个参数来指定要轮询的队列名称。

### ServicebusTrigger 工作原理

SDK 接收 `PeekLock` 模式的消息，如果函数成功完成，则对该消息调用 `Complete`；如果函数失败，则调用 `Abandon`。如果该函数的运行时间超过 `PeekLock` 超时，则会自动续订锁。

Service Bus 会自行执行有害队列处理，因此不需要由 WebJobs SDK 控制或在其中配置。 

### 字符串队列消息

以下代码示例读取包含字符串的队列消息，并将字符串写入 WebJobs SDK 仪表板。

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**注意：**如果你在未使用 WebJobs SDK 的应用程序中创建队列消息，请确保将 [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) 设置为"text/plain"。

### POCO 队列消息

SDK 将自动反序列化包含 POCO[（无格式传统 CLR 对象](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)）类型 JSON 的队列消息。以下代码示例将读取包含 `BlobInformation`对象（该对象包含 `BlobName` 属性）的队列消息：

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

有关演示如何使用 POCO 属性来处理同一函数中 Blob 和表的代码示例，请参阅[本文的存储队列版本](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs)。

### ServiceBusTrigger 适用的类型

除了 `string` 和 POCO 类型以外，你还可以使用具有字节数组或 `BrokeredMessage` 对象的 `ServiceBusTrigger` 属性。

## <a id="create"></a> 如何创建 Service Bus 队列消息

若要编写一个函数来创建新的队列消息，请使用 `ServiceBus` 属性并将队列名称传入属性构造函数。 


### 在非异步函数中创建单个队列消息

以下代码示例使用输出参数在名为"outputqueue"的队列中创建新的消息，该消息的内容与名为"inputqueue"的队列中收到的队列消息相同。

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

用于创建单个队列消息的输出参数可以是以下任何类型：

* `string`
* `byte[]`
* `BrokeredMessage`
* 你定义的可序列化 POCO 类型。自动序列化为 JSON。

对于 POCO 类型参数，当函数结束时始终创建队列消息；如果参数为 null，则 SDK 将创建在接收和反序列化消息时返回 null 的队列消息。对于其他类型，如果该参数为 null，则不创建队列消息。

### 在异步函数中创建多个队列消息

若要创建多个消息，请如以下示例代码中所示，使用带 `ICollector<T>` 或 `IAsyncCollector<T>` 的 `ServiceBus`属性：

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

调用 `Add` 方法时，将立即创建每个队列消息。

## <a id="topics"></a>如何使用 Service Bus 主题

若要编写 SDK 在 Service Bus 主题上收到消息时调用的函数，请使用 `ServiceBusTrigger` 属性与构造函数接受的主题名称和订阅名称，如下面的代码示例中所示：

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

若要在主题上创建消息，请使用 `ServiceBus` 属性以及主题名称，就像使用队列名称一样。

## <a id="queues"></a>存储队列操作指南文章涵盖的相关主题

有关非特定于 Service Bus 的 WebJobs SDK 方案的信息，请参阅[如何通过 WebJobs SDK 使用 Azure 队列存储](../websites-dotnet-webjobs-sdk-storage-queues-how-to/)。 

该文章涵盖的主题包括：

* 异步函数
* 多个实例
* 正常关闭
* 在函数正文中使用 WebJobs SDK 属性
* 在代码中设置 SDK 连接字符串。
* 在代码中设置 WebJobs SDK 构造函数参数的值
* 手动触发函数
* 写入日志

## <a id="nextsteps"></a> 后续步骤

本指南提供的代码示例演示了如何处理常见方案以操作 Azure Service Bus。有关如何使用 Azure Web 作业和 WebJobs SDK 的详细信息，请参阅[有关 Azure Web 作业的推荐资源](http://go.microsoft.com/fwlink/?linkid=390226)。
