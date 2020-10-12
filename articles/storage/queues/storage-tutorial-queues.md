---
title: 教程 - 在 .NET 中使用 Azure 存储队列
description: 有关如何使用 Azure 队列服务创建队列以及如何使用 .NET 代码插入、获取和删除消息的教程。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 23684dbbc5cb8c2d5fc4880ae8fe1999450928e0
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400564"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>教程：在 .NET 中使用 Azure 存储队列

Azure 队列存储实现基于云的队列以在分布式应用程序的组件之间实现通信。 每个队列维护一个可由发送方组件添加的、由接收方组件处理的消息列表。 使用队列时，应用程序可根据需求立即缩放。 本文介绍有关使用 Azure 存储队列的基本步骤。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> - 创建 Azure 存储帐户
> - 创建应用程序
> - 添加 Azure 客户端库
> - 支持异步代码的支持
> - 创建队列
> - 将消息插入队列
> - 取消消息的排队
> - 删除空队列
> - 检查命令行参数
> - 生成并运行应用

## <a name="prerequisites"></a>先决条件

- 获取跨平台 [Visual Studio Code](https://code.visualstudio.com/download) 编辑器的免费副本。
- 下载并安装 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.1 版或更高版本。
- 如果你没有最新的 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

首先创建 Azure 存储帐户。 有关创建存储帐户的分步指南，请参阅[创建存储帐户](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)快速入门。 这是在先决条件中创建免费的 Azure 帐户后执行的单独的步骤。

## <a name="create-the-app"></a>创建应用

创建名为 **QueueApp** 的 .NET Core 应用程序。 为方便起见，此应用将通过队列发送和接收消息。

1. 在控制台窗口（例如 CMD、PowerShell 或 Azure CLI）中，使用 `dotnet new` 命令创建名为 **QueueApp** 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：**Program.cs**。

   ```console
   dotnet new console -n QueueApp
   ```

2. 切换到新建的 **QueueApp** 文件夹，并生成应用以验证一切是否正常。

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   应看到结果类似于以下输出：

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-the-azure-client-libraries"></a>添加 Azure 客户端库

1. 使用 `dotnet add package` 命令将 Azure 存储客户端库添加到项目。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   在控制台窗口中从项目文件夹执行以下命令。

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   在控制台窗口中从项目文件夹执行以下命令。

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>添加 using 语句

1. 在项目目录中的命令行下，键入 `code .` 以在当前目录中打开 Visual Studio Code。 请将命令行窗口保持打开状态。 稍后需要执行更多的命令。 如果系统提示是否要添加用于生成和调试的 C# 资产，请单击“是”按钮。

1. 打开 Program.cs 源文件，紧接在 `using System;` 语句的后面添加以下命名空间。 此应用将使用这些命名空间中的类型来连接 Azure 存储和使用队列。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. 保存 **Program.cs** 文件。

## <a name="add-support-for-asynchronous-code"></a>支持异步代码的支持

由于该应用使用云资源，因此代码将以异步方式运行。

1. 更新 Main 方法以异步运行。 将 **void** 替换为**异步任务**返回值。

   ```csharp
   static async Task Main(string[] args)
   ```

1. 保存 **Program.cs** 文件。

## <a name="create-a-queue"></a>创建队列

在对 Azure API 进行任何调用之前，必须从 Azure 门户获取凭据。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>将连接字符串添加到应用

将连接字符串添加到应用中，使应用可以访问存储帐户。

1. 切回到 Visual Studio Code。

1. 在 Main 方法中，将 `Console.WriteLine("Hello World!");` 代码替换为以下行，该行从环境变量中获取连接字符串。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. 将以下代码添加到 Main 以创建 queue 对象，稍后要将它传入到 send 和 receive 方法。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. 保存文件。

## <a name="insert-messages-into-the-queue"></a>将消息插入队列

创建一个新方法用于将消息发送到队列。

1. 将以下 InsertMessageAsync 方法添加到 Program 类 。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   向此方法传递一个队列引用。 通过调用 [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) 创建新队列（如果尚不存在）。 然后，它通过调用 [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync)，将 newMessage 添加到队列中。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   向此方法传递一个队列引用。 通过调用 [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) 创建新队列（如果尚不存在）。 然后，它通过调用 [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)，将 newMessage 添加到队列中。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. （可选）默认情况下，消息的最大生存时间默认设置为 7 天。 可以为消息生存时间指定任何正数。 下面的代码片段添加一个永不过期的消息。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    若要添加未过期的消息，请在对 SendMessageAsync 的调用中使用 `Timespan.FromSeconds(-1)`。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    若要添加未过期的消息，请在对 **AddMessageAsync** 的调用中使用 `Timespan.FromSeconds(-1)`。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. 保存文件。

队列消息必须采用与使用 UTF-8 编码的 XML 请求兼容的格式。 消息的大小最大可为 64 KB。 如果消息包含二进制数据，则对消息进行 [Base64 编码](/dotnet/api/system.convert.tobase64string)。

## <a name="dequeue-messages"></a>取消消息的排队

创建一个新方法，用于从队列检索消息。 成功收到消息后，必须从队列中删除该消息，以免再次处理该消息。

1. 将名为 RetrieveNextMessageAsync 的新方法添加到 Program 类中 。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   此方法通过调用 [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) 接收来自队列的消息，在第一个参数中传递 1，仅检索队列中的下一条消息。 收到消息后，请调用 [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) 从队列中删除该消息。

   当你使用 v12 以前的 SDK 版本将消息发送到队列时，系统会自动对其进行 Base64 编码。 从 v12 开始，该功能已删除。 当你使用 v12 SDK 检索消息时，系统不会自动对其进行 Base64 解码。 你必须自行对内容进行显式 [Base64 解码](/dotnet/api/system.convert.frombase64string)。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   此方法通过调用 [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) 从队列接收消息。 收到消息后，请调用 [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) 从队列中删除该消息。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. 保存文件。

## <a name="delete-an-empty-queue"></a>删除空队列

在项目结束时，最好是确定是否仍然需要所创建的资源。 持续运行资源可能会产生费用。 如果存在空队列，请询问用户是否要删除该队列。

1. 扩展 RetrieveNextMessageAsync 方法以包含有关是否删除空队列的提示。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. 保存文件。

## <a name="check-for-command-line-arguments"></a>检查命令行参数

如果在应用中传入了任何命令行参数，系统会假设这些参数是要添加到队列的消息。 将参数联接到一起以构成一个字符串。 调用前面添加的 InsertMessageAsync 方法，将此字符串添加到消息队列。

如果没有任何命令行参数，请尝试检索操作。 调用 RetrieveNextMessageAsync 方法检索队列中的下一条消息。

最后，等待用户输入，然后调用 **Console.ReadLine** 退出。

1. 扩展 **Main** 方法以检查命令行参数并等待用户输入。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. 保存文件。

## <a name="complete-code"></a>完整代码

下面是此项目的完整代码列表。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 从项目目录中的命令行运行以下 dotnet 命令以生成项目。

   ```console
   dotnet build
   ```

1. 项目成功生成后，运行以下命令将第一个消息添加到队列。

   ```console
   dotnet run First queue message
   ```

   你应该会看到以下输出：

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

1. 不结合任何命令行参数运行该应用可以接收和删除队列中的第一个消息。

   ```console
   dotnet run
   ```

1. 继续运行应用，直到已删除所有消息。 如果多次运行该应用，将会收到一条指出队列为空的消息，以及一条有关是否要删除该队列的提示。

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

1. 创建队列
1. 在队列中添加和删除消息
1. 删除 Azure 存储队列

查看 Azure 队列快速入门了解详细信息。

> [!div class="nextstepaction"]
> [适用于门户的队列快速入门](storage-quickstart-queues-portal.md)

- [适用于 .NET 的队列快速入门](storage-quickstart-queues-dotnet.md)
- [适用于 Java 的队列快速入门](storage-quickstart-queues-java.md)
- [适用于 Python 的队列快速入门](storage-quickstart-queues-python.md)
- [适用于 JavaScript 的队列快速入门](storage-quickstart-queues-nodejs.md)
