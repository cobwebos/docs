<properties title="Getting Started with Azure Storage" pageTitle="Azure 存储入门" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [入门](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [发生了什么情况](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Azure 存储入门（ASP.NET 5 项目）

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [队列](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [表](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。一条队列消息的大小可达 64 KB，一个队列中可以包含数百万条消息，直至达到存储帐户的总容量限值。有关详细信息，请参阅[如何通过 .NET 使用队列存储](http://windowsazure.cn/zh-cn/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET")。

若要以编程方式访问 ASP.NET 5 项目中的队列，你需要添加以下项（如果尚未存在）。

1. 在您希望以编程方式访问 Azure 存储的任何 C# 文件中，将以下代码命名空间声明添加到文件的顶部。

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. 使用以下代码获取配置设置。

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####获取存储连接字符串
在你可以使用队列执行任何操作之前，你需要先获取将存放队列的存储帐户的连接字符串。你可以使用 **CloudStorageAccount** 类型来表示你的存储帐户信息。如果你使用的是 ASP.NET 5 项目，则可以调用 Configuration 对象的 get 方法从 Azure 服务配置获取存储连接字符串和存储帐户信息，如以下代码所示。

**注意：** 在 ASP.NET 5 中执行调出 Azure 存储空间的 API 是异步的。有关详细信息，请参阅[使用 Async 和 Await 进行异步编程](http://msdn.microsoft.com/library/hh191443.aspx)。下面的代码假定正在使用异步编程方法。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####创建队列
利用 **CloudQueueClient** 对象，可以获得队列的引用对象。以下代码将创建 **CloudQueueClient** 对象。本主题中的所有代码都使用存储在 Azure 应用程序的服务配置中的存储连接字符串。还可采用其他方法创建 **CloudStorageAccount** 对象。有关详细信息，请参阅 [CloudStorageAccount](http://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") 文档。

**注意：** 在 ASP.NET 5 中执行调出 Azure 存储空间的 API 是异步的。有关详细信息，请参阅[使用 Async 和 Await 进行异步编程](http://msdn.microsoft.com/library/hh191443.aspx)。下面的代码假定正在使用异步编程方法。

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

使用 **queueClient** 对象获取对要使用的队列的引用。该代码将尝试引用名为"myqueue"的队列。如果找不到该名称的队列，它将创建一个。

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	await queue.CreateIfNotExistsAsync();

**注意：** 在接下来的部分中，将在代码的前面使用全部此代码。

#####在队列中插入消息
若要将消息插入现有队列，请先创建一个新的 **CloudQueueMessage** 对象。接下来，调用 AddMessageAsync() 方法。可从字符串（UTF-8 格式）或字节数组创建 **CloudQueueMessage** 对象。以下代码将创建队列（如果队列不存在）并插入消息"Hello, World"。

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####扫视下一条消息
通过调用 PeekMessageAsync() 方法，可以查看队列前面的消息，而不必从队列中将其删除。

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####删除下一条消息
你的代码可以分两步从队列中删除消息（取消对消息的排队）。 


1. 调用 GetMessageAsync() 以获取队列中的下一条消息。从 GetMessageAsync() 返回的消息将变为对从此队列读取消息的任何其他代码不可见。默认情况下，此消息将持续 30 秒不可见。 
2.	若要完成从队列中删除消息，请调用 DeleteMessageAsync()。 

此删除消息的两步过程可确保当你的代码因硬件或软件故障而无法处理消息时，你的其他代码实例可以获取同一消息并重试。以下代码将在处理消息后立即调用 DeleteMessageAsync()。

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[了解有关 Azure 存储空间的详细信息](http://azure.microsoft.com/documentation/services/storage/)
另请参阅[在服务器资源管理器中浏览存储资源](http://msdn.microsoft.com/zh-cn/library/azure/ff683677.aspx)以及 [ASP.NET 5](http://www.asp.net/vnext)。
