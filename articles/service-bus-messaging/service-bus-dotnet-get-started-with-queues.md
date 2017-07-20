---
title: "Azure 服务总线队列入门 | Microsoft Docs"
description: "编写一个 C# 控制台应用程序，以便使用服务总线消息传递队列。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 02d0ce093bc42cffa4f3993826c61c8aeca4d033
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---
# <a name="get-started-with-service-bus-queues"></a>服务总线队列入门
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>将要完成的任务
本教程涵盖以下步骤：

1. 使用 Azure 门户创建服务总线命名空间。
2. 使用 Azure 门户创建服务总线队列。
3. 编写一个控制台应用程序用于发送消息。
4. 编写一个控制台应用程序，用于接收在上一步发送的消息。

## <a name="prerequisites"></a>先决条件
1. [Visual Studio 2015 或更高版本](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017。
2. Azure 订阅。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 门户创建命名空间
如果已创建服务总线消息传递命名空间，请跳转到[使用 Azure 门户创建队列](#2-create-a-queue-using-the-azure-portal)部分。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2.使用 Azure 门户创建队列
如果你已创建服务总线队列，请跳转到[将消息发送到队列](#3-send-messages-to-the-queue)部分。

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3.将消息发送到队列
为了将消息发送到队列中，我们将使用 Visual Studio 编写一个 C# 控制台应用程序。

### <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio 并创建新的控制台应用 (.NET Framework) 项目。

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包
1. 右键单击新创建的项目，然后选择“管理 NuGet 包” 。
2. 单击“浏览”选项卡，搜索“Microsoft Azure 服务总线”，然后选择“WindowsAzure.ServiceBus”项。 单击“安装”以完成安装，然后关闭此对话框。
   
    ![选择 NuGet 包][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>编写一些代码以向队列发送消息
1. 在 Program.cs 文件顶部添加以下 `using` 语句。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. 将以下代码添加到 `Main` 方法中。 将 `connectionString` 变量设置为在创建命名空间时获得的连接字符串，并将 `queueName` 设置为在创建队列时使用的队列名称。
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs 文件的内容如下所示。
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. 运行该程序并检查 Azure 门户：在命名空间“概览”边栏选项卡中单击队列的名称。 此时会显示队列的“基本信息”边栏选项卡。 请注意，“活动消息计数”值现在应为 1。 每次运行发件人应用程序而没有检索消息时，该值会增加 1。 另请注意，每次应用向队列添加消息时，队列的当前大小都会递增。
   
      ![消息大小][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4.从队列接收消息

1. 若要接收刚发送的消息，请创建新的控制台应用程序并添加对服务总线 NuGet 包的引用，类似于前面的发件人应用程序。
2. 在 Program.cs 文件顶部添加以下 `using` 语句。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. 将以下代码添加到 `Main` 方法中。 将 `connectionString` 变量设置为在创建命名空间时获得的连接字符串，并将 `queueName` 设置为在创建队列时使用的队列名称。
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Program.cs 文件的内容如下所示：
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. 运行该程序，并再次检查门户。 请注意，“活动消息计数”和“当前”值现在为 0。
   
    ![队列长度][queue-message-receive]

祝贺你！ 你已创建队列、发送和接收消息。

## <a name="next-steps"></a>后续步骤

查看 [GitHub 存储库](https://github.com/Azure/azure-service-bus/tree/master/samples)中的示例，了解服务总线消息传送的一些更高级的功能。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

