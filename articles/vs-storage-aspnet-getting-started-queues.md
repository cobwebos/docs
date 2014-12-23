<properties title="Getting Started with Azure Storage" pageTitle="Azure 存储入门" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [入门](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [发生了什么情况](/documentation/articles/vs-storage-aspnet-what-happened/)

## Azure 存储入门（ASP.NET 项目）

> [AZURE.SELECTOR]
> - [Blob](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [队列](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [表](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。一条队列消息的大小可达 64 KB，一个队列中可以包含数百万条消息，直至达到存储帐户的总容量限值。有关详细信息，请参阅[如何通过 .NET 使用队列存储](http://windowsazure.cn/zh-cn/documentation/articles/storage-dotnet-how-to-use-queues/)。

在您希望以编程方式访问 Azure 存储的任何 C# 文件中，将以下代码命名空间声明添加到文件的顶部。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####获取存储连接字符串
在你可以使用队列执行任何操作之前，你需要先获取将存放队列的存储帐户的连接字符串。你可以使用 **CloudStorageAccount** 类型来表示你的存储帐户信息。对于 ASP.NET 项目，你可以使用 **ConfigurationManager** 类型从 Azure 服务配置检索你的存储连接字符串和存储帐户信息，如以下代码所示。

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

有关详细信息，请参阅 [ASP.NET](http://www.asp.net)。
