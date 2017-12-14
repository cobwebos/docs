---
title: "开始使用队列存储和 Visual Studio 连接服务 (ASP.NET Core) | Microsoft Docs"
description: "如何开始在 Visual Studio 中的 ASP.NET Core 项目中使用 Azure 队列存储"
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 04977069-5b2d-4cba-84ae-9fb2f5eb1006
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: d83ad26e4f96e1a7670c5212b7e9ca8182b7cec4
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2017
---
# <a name="get-started-with-queue-storage-and-visual-studio-connected-services-aspnet-core"></a>开始使用队列存储和 Visual Studio 连接服务 (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

本文介绍通过使用 Visual Studio 中的“连接服务”功能在 ASP.NET Core 项目中创建或引用 Azure 存储帐户之后，如何开始在 Visual Studio 中使用 Azure 队列存储。 执行“连接服务”操作会安装相应的 NuGet 程序包，以访问项目中的 Azure 存储，并将存储帐户的连接字符串添加到项目配置文件中。 （有关 Azure 存储的常规信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)。）

Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。 一条队列消息的大小最多可为 64 千字节 (KB)，一个队列中可以包含数百万条消息，直至达到存储帐户的总容量限值。 有关以编程方式操作队列的详细信息，另请参阅[通过 .NET 开始使用 Azure 队列存储](../storage/queues/storage-dotnet-how-to-use-queues.md)。

若要开始，首先在存储帐户中创建 Azure 队列。 本文随后会演示如何运用 C# 创建队列，以及如何执行基本的队列操作，如添加、修改、读取和删除队列消息。  代码使用适用于 .NET 的 Azure 存储客户端库。 有关 ASP.NET 的详细信息，请参阅 [ASP.NET](http://www.asp.net)。

某些 Azure 存储 API 为异步，而本文中的代码假定正在使用异步方法。 有关详细信息，请参阅[异步编程](https://docs.microsoft.com/dotnet/csharp/async)。

## <a name="access-queues-in-code"></a>使用代码访问队列

要访问 ASP.NET Core 项目中的队列，请将下列事项包含在访问 Azure 队列存储的任何 C# 源文件中。 在接下来的部分中，会在代码的前面使用全部这些代码。

1. 添加必要的 `using` 语句：
    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. 获取表示存储帐户信息的 `CloudStorageAccount` 对象。 使用下面的代码获取存储连接字符串和 Azure 服务配置中的存储帐户信息：

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. 获取 `CloudQueueClient` 对象，以引用存储帐户中的队列对象：

    ```cs
    // Create the CloudQueueClient object for the storage account.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. 获取 `CloudQueue` 对象，以引用特定队列：

    ```cs
    // Get a reference to the CloudQueue named "messagequeue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messagequeue");
    ```

### <a name="create-a-queue-in-code"></a>使用代码创建队列

若要在代码中创建 Azure 队列，请调用 ``CreateIfNotExistsAsync`：

```cs
// Create the CloudQueue if it does not exist.
await messageQueue.CreateIfNotExistsAsync();
```

## <a name="add-a-message-to-a-queue"></a>向队列添加消息

要在现有队列中插入消息，请创建新的 `CloudQueueMessage` 对象，并调用 `AddMessageAsync` 方法。 可从字符串（UTF-8 格式）或字节数组创建 `CloudQueueMessage` 对象。

```cs
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await messageQueue.AddMessageAsync(message);
```

## <a name="read-a-message-in-a-queue"></a>读取队列中的消息

通过调用 `PeekMessageAsync`，可以扫视队列前面的消息，而不会从队列中删除它：

```cs
// Peek the next message in the queue.
CloudQueueMessage peekedMessage = await messageQueue.PeekMessageAsync();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>读取和删除队列中的消息

代码分两步从队列中删除消息（取消对消息的排队）。

1. 调用 `GetMessageAsync` 以获取队列中的下一条消息。 从 `GetMessageAsync` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息将持续 30 秒不可见。
1. 若要完成从队列中删除消息，请调用 `DeleteMessageAsync`。

此删除消息的两步过程可确保，如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。 以下代码会在消息得到处理后立即调用 `DeleteMessageAsync`：

```cs
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();

// Process the message in less than 30 seconds.

// Then delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
```

## <a name="additional-options-for-dequeuing-messages"></a>用于取消对消息进行排队的其他选项

可以通过两种方式自定义队列中的消息检索。 首先，可以获取一批消息（最多 32 个）。 其次，可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少时间来完全处理每个消息。 以下代码示例使用 `GetMessages` 方法在一个调用中获取 20 条消息。 然后，使用 `foreach` 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。 请注意，这 5 分钟计时器对于所有消息都是同时开始的，因此在 5 分钟后，尚未删除的任何消息都将再次变为可见。

```cs
// Retrieve 20 messages at a time, keeping those messages invisible for 5 minutes, 
//   delete each message after processing.

foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>获取队列长度

可以获取队列中消息的估计数。 使用 `FetchAttributes` 方法可请求队列服务来检索队列属性，包括消息计数。 `ApproximateMethodCount` 属性将返回 `FetchAttributes` 方法检索到的最后一个值，而不会调用队列服务。

```cs
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display the number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-queue-apis"></a>共同使用 Async Await 模式和公用队列 API

此示例演示如何将异步等待模式与以 `Async` 结尾的公用队列 API 结合使用。 使用异步方法时，异步等待模式将暂停本地执行，直到调用完成。 此行为允许当前的线程执行其他工作，这有助于避免性能瓶颈并提高应用程序的整体响应能力。

```cs
// Create a message to add to the queue.
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message.
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message.
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message.
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>删除队列

若要删除队列及其内含的所有消息，请对队列对象调用 `Delete` 方法：

```cs
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
