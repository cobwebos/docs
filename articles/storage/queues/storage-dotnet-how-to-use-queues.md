---
title: 通过 .NET 开始使用 Azure 队列存储 - Azure 存储
description: Azure 队列用于在应用程序组件之间进行可靠的异步消息传送。 应用程序组件可以利用云消息传送进行独立缩放。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: afdd9b1b063d0a82c8cdb27ef01b412daaa9f1df
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198899"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>通过 .NET 开始使用 Azure 队列存储

## <a name="overview"></a>概述

Azure 队列存储用于在应用程序组件之间进行云消息传送。 在设计规模应用程序时，应用程序组件通常是分离的，因此它们可以独立缩放。 队列存储在应用程序组件之间提供异步消息传送，无论它们是在云中、在桌面上、在本地服务器上运行还是在移动设备上运行。 队列存储还支持管理异步任务以及构建过程工作流。

### <a name="about-this-tutorial"></a>关于本教程

本教程演示如何针对使用 Azure 队列存储一些常见情形编写 .NET 代码。 涉及的方案包括创建和删除队列、添加、读取和删除队列消息。

**估计完成时间：** 45 分钟

### <a name="prerequisites"></a>先决条件

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [适用于 .NET 的 Azure 存储通用客户端库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
- [适用于 .NET 的 Azure 存储队列客户端库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
- [适用于 .NET 的 Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
- 一个 [Azure 存储帐户](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

接下来在 Visual Studio 中设置开发环境，即可试用本指南中的代码示例。

### <a name="create-a-windows-console-application-project"></a>创建 Windows 控制台应用程序项目

在 Visual Studio 中创建新的 Windows 控制台应用程序。 以下步骤演示了如何在 Visual Studio 2019 中创建控制台应用程序。 在其他版本的 Visual Studio 中，这些步骤是类似的。

1. 选择“文件” > “新建” > “项目”   
2. 选择“平台”   >   “Windows”
3. 选择“控制台应用(.NET Framework)” 
4. 选择“下一步” 
5. 在“项目名称”字段中输入应用程序的名称 
6. 选择“创建” 

本教程中的所有代码示例都可以添加到控制台应用程序的 **Program.cs** 文件的 **Main()** 方法。

可以在任意类型的 .NET 应用程序（包括 Azure 云服务或 Web 应用，以及桌面和移动应用程序）中使用 Azure 存储客户端库。 为简单起见，我们在本指南中使用控制台应用程序。

### <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 安装所需包

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

若要完成本教程，需要在项目中引用以下四个包：

- [适用于 .net 的 Azure 核心库](https://www.nuget.org/packages/Azure.Core/)：此包为新式 .NET Azure SDK 客户端库提供共享的基元、抽象和帮助程序。
- [适用于 .net 的 Azure 存储公用客户端库](https://www.nuget.org/packages/Azure.Storage.Common/)：此包提供其他 Azure 存储客户端库共享的基础结构。
- [适用于 .net 的 Azure 存储队列库](https://www.nuget.org/packages/Azure.Storage.Queues/)：此包允许使用 azure 存储队列服务来存储客户端可能访问的消息。
- [适用于 .net 的 Configuration Manager 库](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)：此包用于访问客户端应用程序的配置文件。

可以使用 NuGet 获取这些包。 请执行这些步骤：

1. 右键单击 "**解决方案资源管理器**中的项目，然后选择"**管理 NuGet 包**"。
1. 选择**浏览**
1. 在线搜索 "Azure"，并选择 "**安装**" 以安装存储客户端库及其依赖项。 这也会安装 Azure. Common 和 Azure Core 库，它们是队列库的依赖项。
1. 在线搜索 "ConfigurationManager"，并选择 "**安装**" 以安装 Configuration Manager。

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

为完成此教程，需要在项目中引用下述三个包：

- [Microsoft Azure 存储适用于 .net 的常见客户端库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)：此包提供以编程方式访问存储帐户中的数据资源的权限。
- [Microsoft Azure 存储适用于 .net 的队列库](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)：此客户端库允许使用 Microsoft Azure 存储队列服务来存储客户端可能访问的消息。
- [适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)：此包提供用于分析配置文件中连接字符串的类，而不考虑应用程序在何处运行。

可以使用 NuGet 获取这些包。 请执行这些步骤：

1. 右键单击 "**解决方案资源管理器**中的项目，然后选择"**管理 NuGet 包**"。
1. 选择**浏览**
1. 在线搜索“Microsoft.Azure.Storage.Queue”，并选择“安装”**** 以安装存储客户端库和依赖项。 这样也会安装 Microsoft.Azure.Storage.Common 库，该库是队列库的依赖项。
1. 在线搜索“Microsoft.Azure.ConfigurationManager”，并选择“安装”**** 以安装 Azure Configuration Manager。

---

> [!NOTE]
> [用于 .NET 的 Azure SDK](https://azure.microsoft.com/downloads/)中也包含存储客户端库包。 但是我们建议同时从 NuGet 安装存储客户端库，以确保始终使用最新版本。
>
> 适用于 .NET 的存储客户端库中的 ODataLib 依赖项通过 NuGet（而非 WCF 数据服务）上提供的 ODataLib 包来解析。 ODataLib 库可直接下载或者通过 NuGet 由代码项目引用。 存储空间客户端库使用的具体 ODataLib 包是 [OData](https://nuget.org/packages/Microsoft.Data.OData/)、[Edm](https://nuget.org/packages/Microsoft.Data.Edm/) 和 [Spatial](https://nuget.org/packages/System.Spatial/)。 尽管这些库由 Azure 表存储类使用，但是用存储空间客户端库进行编程时，它们是必需的依赖项。

### <a name="determine-your-target-environment"></a>确定目标环境

可从两个环境中选择用于运行本指南中示例的环境：

- 可针对云中的 Azure 存储帐户运行代码。
- 可以针对 Azurite 存储模拟器运行代码。 Azurite 是模拟云中 Azure 存储帐户的本地环境。 当应用程序正在开发时，Azurite 是用于测试和调试代码的免费选项。 模拟器使用已知帐户和密钥。 有关详细信息，请参阅[将 Azurite 模拟器用于本地 Azure 存储开发和测试](../common/storage-use-azurite.md)。

> [!NOTE]
> 可以指向存储模拟器以避免引发与 Azure 存储有关的任何费用。 但是，如果确实选择指向云中的 Azure 存储帐户，则执行此教程的费用会忽略不计。

## <a name="get-your-storage-connection-string"></a>获取存储连接字符串

用于 .NET 的 Azure 存储客户端库支持使用存储连接字符串来配置终结点和用于访问存储服务的凭据。 有关详细信息，请参阅[管理存储帐户访问密钥](../common/storage-account-keys-manage.md)。

### <a name="copy-your-credentials-from-the-azure-portal"></a>从 Azure 门户复制凭据

此示例代码需要对存储帐户访问进行授权。 请为应用程序提供连接字符串形式的存储帐户凭据，以便进行授权。 若要查看存储帐户凭据，请执行以下操作：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在存储帐户概述的“设置”部分，选择“访问密钥”。******** 此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
4. 找到“密钥 1”下面的“连接字符串”值，单击“复制”按钮复制该连接字符串。************ 下一步需将此连接字符串值添加到某个环境变量。

    ![显示如何从 Azure 门户复制连接字符串的屏幕截图](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

有关连接字符串的详细信息，请参阅[配置 Azure 存储的连接字符串](../common/storage-configure-connection-string.md)。

> [!NOTE]
> 存储帐户密钥类似于存储帐户的根密码。 始终要小心保护存储帐户密钥。 避免将其分发给其他用户、对其进行硬编码或将其保存在其他人可以访问的纯文本文件中。 如果认为密钥可能已泄漏，请使用 Azure 门户重新生成密钥。

维护存储连接字符串的最佳方法在配置文件中。 若要配置连接字符串，请从 Visual Studio 中的解决方案资源管理器打开 *app.config* 文件。 添加 `\<appSettings\>` 元素的内容，如下所示。 将*连接字符串*替换为你在门户中从存储帐户复制的值：

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

例如，配置设置看起来类似于：

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

若要以 Azurite 存储模拟器为目标，可使用映射到已知帐户名称和密钥的快捷方式。 在这种情况下，连接字符串设置如下所示：

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>添加 using 指令

将以下 `using` 指令添加到 `Program.cs` 文件顶部：

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>创建队列服务客户端

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

[QueueClient](/dotnet/api/azure.storage.queues.queueclient)类使你能够检索存储在队列存储中的队列。 下面是创建服务客户端的一种方法：

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

[CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy) 类使你能够检索存储在队列存储中的队列。 下面是创建服务客户端的一种方法：

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

现在，已准备好编写从队列存储读取数据并将数据写入队列存储的代码。

## <a name="create-a-queue"></a>创建队列

此示例演示如何创建队列：

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>在队列中插入消息

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

若要在现有队列中插入消息，请调用[SendMessage](/dotnet/api/azure.storage.queues.queueclient.sendmessage)方法。 消息可以是 `string` （utf-8 格式）或 `byte` 数组。 下面的代码将创建一个队列（如果该队列不存在）并插入一条消息：

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

要将消息插入现有队列，请先创建一个新的 [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy)。 接下来，调用[AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy)方法。 `CloudQueueMessage`可以从 `string` （utf-8 格式）或 `byte` 数组创建。 以下代码将创建一个队列（如果该队列不存在）并插入消息 "Hello，World"：

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>扫视下一条消息

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

通过调用[PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages)方法，可以查看队列中的消息，而不必从队列中将其删除。 如果未传递*maxMessages*参数的值，则默认值为查看一条消息。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

通过调用[PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy)方法，可以查看队列前面的消息，而不必从队列中将其删除。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

可以更改队列中现有消息的内容。 如果消息表示工作任务，则可以使用此功能来更新该工作任务的状态。 以下代码使用新内容更新队列消息，并将可见性超时设置为再延长 60 秒。 这会保存与消息关联的工作的状态，并额外为客户端提供一分钟的时间来继续处理消息。 可使用此方法跟踪队列消息上的多步骤工作流，即使处理步骤因硬件或软件故障而失败，也无需从头开始操作。 通常，还可以保留重试计数，如果某条消息的重试次数超过 *n*，将删除此消息。 这可避免每次处理某条消息时都触发应用程序错误。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="de-queue-the-next-message"></a>取消对下一条消息的排队

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

在两个步骤中，将消息从队列中取消排队。 调用[ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages)时，将获取队列中的下一条消息。 从 `ReceiveMessages` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息将持续 30 秒不可见。 要从队列中删除消息，还必须调用 [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage)。 此删除消息的两步过程可确保，如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。 代码在处理消息后会立即调用 `DeleteMessage`。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

代码通过两个步骤来取消对队列中某条消息的排队。 调用 [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy)时，将获取队列中的下一条消息。 从 `GetMessage` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 默认情况下，此消息将持续 30 秒不可见。 要从队列中删除消息，还必须调用 [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy)。 此删除消息的两步过程可确保，如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。 代码在处理消息后会立即调用 `DeleteMessage`。

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>将 Async-Await 模式与公用队列存储 API 配合使用

此示例演示如何将 Async-Await 模式和公用队列存储 API 配合使用。 示例调用每个给定方法的异步版本，如每个方法的 *Async* 后缀所示。 使用异步方法时，async-await 模式将暂停本地执行，直到调用完成。 此行为允许当前的线程执行其他工作，这有助于避免性能瓶颈并提高应用程序的整体响应能力。 有关在 .NET 中使用 Async Await 模式的更多详细信息[，请参阅 async And Await （c # 和 Visual Basic）](https://msdn.microsoft.com/library/hh191443.aspx)

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="leverage-additional-options-for-de-queuing-messages"></a>使用其他方法取消对消息的排队

可以通过两种方式自定义队列中的消息检索。 首先，可以获取一批消息（最多 32 个）。 其次，可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少时间来完全处理每个消息。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

下面的代码示例使用[ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages)方法在一次调用中获取20条消息。 然后，使用 `foreach` 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。 请注意，5分钟会同时为所有消息启动，因此，在调用后5分钟后 `ReceiveMessages` ，任何尚未删除的消息都将再次变得可见。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

以下代码示例使用 [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy) 方法在一次调用中获取 20 条消息。 然后，使用 `foreach` 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。 请注意，5分钟会同时为所有消息启动，因此，在调用后5分钟后 `GetMessages` ，任何尚未删除的消息都将再次变得可见。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>获取队列长度

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

可以获取队列中消息的估计数。 [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties)方法会要求队列服务检索队列属性，包括消息计数。 [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount)属性包含队列中消息的大致数目。 该数字不低于队列中的实际消息数，但可能高于实际消息数。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

可以获取队列中消息的估计数。 使用 [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy) 方法可请求队列服务检索队列属性，包括消息计数。 [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy)属性返回方法检索到的最后一个值 `FetchAttributes` ，而不会调用队列服务。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>删除队列

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

若要删除队列及其包含的所有消息，请对队列对象调用 [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) 方法。

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.净 v11](#tab/dotnetv11)

若要删除队列及其包含的所有消息，请对队列对象调用 [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy) 方法。

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>后续步骤

现在，已了解有关队列存储的基础知识，可单击下面的链接来了解更复杂的存储任务。

- 查看队列服务参考文档，了解有关可用 API 的完整详细信息：
  - [.NET 存储客户端库参考](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [REST API 参考](https://msdn.microsoft.com/library/azure/dd179355)
- 了解如何通过使用 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)简化为使用 Azure 存储而写的代码。
- 查看更多功能指南，以了解在 Azure 中存储数据的其他方式。
  - [通过 .NET 开始使用 Azure 表存储](../../cosmos-db/table-storage-how-to-use-dotnet.md) 来存储结构化数据。
  - [通过 .NET 开始使用 Azure Blob 存储](../blobs/storage-dotnet-how-to-use-blobs.md) 来存储非结构化数据。
  - [使用.NET (C#) 连接到 SQL 数据库](../../sql-database/sql-database-connect-query-dotnet-core.md)，存储关系数据。

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
