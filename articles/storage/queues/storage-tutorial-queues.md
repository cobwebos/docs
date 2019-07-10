---
title: 教程 - 使用 Azure 存储队列 - Azure 存储
description: 有关如何使用 Azure 队列服务创建队列以及插入、获取和删除消息的教程。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 08ef140eb860637cc0c09619abe7051cc007e99f
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540296"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>教程：使用 Azure 存储队列

Azure 队列存储实现基于云的队列以在分布式应用程序的组件之间实现通信。 每个队列维护一个可由发送方组件添加的、由接收方组件处理的消息列表。 使用队列时，应用程序可根据需求立即缩放。 本文介绍有关使用 Azure 存储队列的基本步骤。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> - 创建 Azure 存储帐户
> - 创建应用程序
> - 支持异步代码的支持
> - 创建队列
> - 将消息插入队列
> - 取消消息的排队
> - 删除空队列
> - 检查命令行参数
> - 生成并运行应用

## <a name="prerequisites"></a>先决条件

- 获取跨平台 [Visual Studio Code](https://code.visualstudio.com/download) 编辑器的免费副本。
- 下载并安装 [.NET Core SDK](https://dotnet.microsoft.com/download)。
- 如果你没有最新的 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

首先创建 Azure 存储帐户。 有关创建存储帐户的分步指南，请参阅[创建存储帐户](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)快速入门。

## <a name="create-the-app"></a>创建应用程序

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

   应会看到如下所示的结果：

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>支持异步代码的支持

由于该应用使用云资源，因此代码将以异步方式运行。 但是，在 C# 7.1 推出之前，C# 中的 **async** 和 **await** 在 **Main** 方法中不是有效的关键字。 可以通过 **csproj** 文件中的一个标志轻松切换到该编译器。

1. 在项目目录中的命令行下，键入 `code .` 以在当前目录中打开 Visual Studio Code。 请将命令行窗口保持打开状态。 稍后需要执行更多的命令。 如果系统提示是否要添加用于生成和调试的 C# 资产，请单击“是”按钮。 

2. 在编辑器中打开 **QueueApp.csproj** 文件。

3. 将 `<LangVersion>7.1</LangVersion>` 添加到生成文件中的第一个 **PropertyGroup**。 请确保只添加 **LangVersion** 标记，因为你的 **TargetFramework** 可能有所不同，具体取决于安装的 .NET 版本。

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. 保存 **QueueApp.csproj** 文件。

5. 打开 **Program.cs** 源文件，并将 **Main** 方法更新为以异步方式运行。 将 **void** 替换为**异步任务**返回值。

   ```csharp
   static async Task Main(string[] args)
   ```

6. 保存 **Program.cs** 文件。

## <a name="create-a-queue"></a>创建队列

1. 使用 `dotnet add package` 命令将 Microsoft.Azure.Storage.Common 和 Microsoft.Azure.Storage.Queue 包安装到项目   。 在控制台窗口中从项目文件夹执行以下 dotnet 命令。

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. 在 **Program.cs** 文件的顶部，紧接在 `using System;` 语句的后面添加以下命名空间。 此应用将使用这些命名空间中的类型来连接 Azure 存储和使用队列。

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. 保存 **Program.cs** 文件。

### <a name="get-your-connection-string"></a>获取连接字符串

客户端库使用连接字符串来建立连接。 Azure 门户中存储帐户的“设置”部分提供了该连接字符串。 

1. 在 Web 浏览器中登录到 [Azure 门户](https://portal.azure.com/)。

2. 导航到 Azure 门户中的存储帐户。

3. 选择“访问密钥”。 

4. 单击“连接字符串”字段右侧的“复制”按钮。  

![连接字符串](media/storage-tutorial-queues/get-connection-string.png)

连接字符串是按以下格式：

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>将连接字符串添加到应用

将连接字符串添加到应用中，使应用可以访问存储帐户。

1. 切回到 Visual Studio Code。

2. 在 **Program** 类中，添加一个 `private const string connectionString =` 成员用于保存连接字符串。

3. 在等号的后面，粘贴先前在 Azure 门户中复制的字符串值。 **connectionString** 值是与你的帐户相关的唯一值。

4. 从 **Main** 中删除“Hello World”代码。 代码应如下所示，但使用了唯一的连接字符串值。

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. 更新 **Main** 以创建 **CloudQueue** 对象，稍后要将它传入到 send 和 receive 方法。

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. 保存文件。

## <a name="insert-messages-into-the-queue"></a>将消息插入队列

创建一个新方法用于将消息发送到队列。 将以下方法添加到 **Program** 类。 此方法获取队列引用，然后通过调用 [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) 创建一个新队列（如果尚不存在）。 然后，它通过调用 [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) 将消息添加到该队列。

1. 将以下 **SendMessageAsync** 方法添加到 **Program** 类。

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. 保存文件。

消息必须采用可包含在 XML 请求中的 UTF-8 编码格式，大小不能超过 64 KB。 如果消息包含二进制数据，则我们建议对消息进行 Base64 编码。

消息的最大生存时间默认设置为 7 天。 可以为消息生存时间指定任何正数。 若要添加未过期的消息，请在对 **AddMessageAsync** 的调用中使用 `Timespan.FromSeconds(-1)`。

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>取消消息的排队

创建名为 **ReceiveMessageAsync** 的新方法。 此方法通过调用 [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) 从队列接收消息。 成功收到消息后，必须从队列中删除该消息，以免再次处理该消息。 收到消息后，请调用 [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) 从队列中删除该消息。

1. 将以下 **ReceiveMessageAsync** 方法添加到 **Program** 类。

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. 保存文件。

## <a name="delete-an-empty-queue"></a>删除空队列

在项目结束时，最好是确定是否仍然需要所创建的资源。 持续运行资源可能会产生费用。 如果存在空队列，请询问用户是否要删除该队列。

1. 扩展 **ReceiveMessageAsync** 方法以包含有关是否删除空队列的提示。

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. 保存文件。

## <a name="check-for-command-line-arguments"></a>检查命令行参数

如果在应用中传入了任何命令行参数，系统会假设这些参数是要添加到队列的消息。 将参数联接到一起以构成一个字符串。 调用前面添加的 **SendMessageAsync** 方法，将此字符串添加到消息队列。

如果没有任何命令行参数，请执行检索操作。 调用 **ReceiveMessageAsync** 方法检索队列中的第一个消息。

最后，等待用户输入，然后调用 **Console.ReadLine** 退出。

1. 扩展 **Main** 方法以检查命令行参数并等待用户输入。

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. 保存文件。

## <a name="complete-code"></a>完整代码

下面是此项目的完整代码列表。

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 从项目目录中的命令行运行以下 dotnet 命令以生成项目。

   ```console
   dotnet build
   ```

2. 项目成功生成后，运行以下命令将第一个消息添加到队列。

   ```console
   dotnet run First queue message
   ```

应该会看到以下输出：

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. 不结合任何命令行参数运行该应用可以接收和删除队列中的第一个消息。

   ```console
   dotnet run
   ```

4. 继续运行应用，直到已删除所有消息。 如果多次运行该应用，将会收到一条指出队列为空的消息，以及一条有关是否要删除该队列的提示。

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

本教程介绍了如何：

1. 创建队列
2. 在队列中添加和删除消息
3. 删除 Azure 存储队列

查看 Azure 队列快速入门了解详细信息。

> [!div class="nextstepaction"]
> [队列快速入门](storage-quickstart-queues-portal.md)
