---
title: "Azure 服务总线主题和订阅入门 | Microsoft Docs"
description: "编写一个 C# 控制台应用程序，以便使用服务总线消息传递主题和订阅。"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 79573909e02a9743c732f8c1dd48c53966df3f0c
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="get-started-with-service-bus-topics"></a>服务总线主题入门

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本教程涵盖以下步骤：

1. 使用 Azure 门户创建服务总线命名空间。
2. 使用 Azure 门户创建服务总线主题。
3. 使用 Azure 门户创建该主题的服务总线订阅。
4. 编写一个控制台应用程序，用于向主题发送消息。
5. 编写一个控制台应用程序，用于从订阅接收该消息。

## <a name="prerequisites"></a>先决条件

1. [Visual Studio 2015 或更高版本](http://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017。
2. Azure 订阅。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.使用 Azure 门户创建命名空间

如果已创建服务总线消息传递命名空间，请跳转到[使用 Azure 门户创建主题](#2-create-a-topic-using-the-azure-portal)部分。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2.使用 Azure 门户创建主题

1. 登录到 [Azure 门户][azure-portal]。
2. 在门户左侧的导航窗格中，单击“服务总线”（如果未看到“服务总线”，请单击“更多服务”或“所有资源”）。
3. 单击要在其中创建主题的命名空间。 此时会显示命名空间概览边栏选项卡：
   
    ![创建主题][createtopic1]
4. 在“服务总线命名空间”边栏选项卡中，单击“主题”，然后单击“添加主题”。
   
    ![选择主题][createtopic2]
5. 输入主题的名称，然后取消选中“启用分区”选项。 将其他选项保留默认值。
   
    ![选择“新建”][createtopic3]
6. 单击边栏选项卡底部的“创建” 。

## <a name="3-create-a-subscription-to-the-topic"></a>3.创建主题的订阅

1. 在门户资源窗格中，单击在步骤 1 中创建的命名空间，然后单击在步骤 2 中创建的主题的名称。
2. 在概览窗格顶部单击“订阅”旁边的加号，以便添加该主题的订阅。

    ![创建订阅][createtopic4]

3. 输入订阅的名称。 将其他选项保留默认值。

## <a name="4-send-messages-to-the-topic"></a>4.将消息发送到主题

为了将消息发送到主题中，我们使用 Visual Studio 编写了一个 C# 控制台应用程序。

### <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio 并创建新的控制台应用 (.NET Framework) 项目。

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
2. 单击“浏览”选项卡，搜索“WindowsAzure.ServiceBus”，然后选择“WindowsAzure.ServiceBus”项。 单击“安装”以完成安装，并关闭此对话框。
   
    ![选择 NuGet 包][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>编写一些代码以向主题发送消息

1. 在 Program.cs 文件顶部添加以下 `using` 语句。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. 将以下代码添加到 `Main` 方法中。 将 `connectionString` 变量设置为在创建命名空间时获得的连接字符串，并将 `topicName` 设置为在创建主题时使用的名称。
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    client.Send(message);

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

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

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
                
                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. 运行该程序并检查 Azure 门户：在命名空间“概览”边栏选项卡中单击主题的名称。 此时会显示主题的“基本信息”边栏选项卡。 请注意，在列在边栏选项卡底部附近的订阅中，每个订阅的“消息计数”值现在应该为 1。 每次运行发件人应用程序而没有检索消息（如下一部分所述）时，该值会增加 1。 另请注意，每次该应用将消息添加到主题/订阅，主题的当前大小就会递增，增量为“基本信息”边栏选项卡上的“当前”值。
   
      ![消息大小][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5.从订阅接收消息

1. 若要接收刚发送的一个或多个消息，请创建新的控制台应用程序并添加对服务总线 NuGet 包的引用，类似于前面的发件人应用程序。
2. 在 Program.cs 文件顶部添加以下 `using` 语句。
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. 将以下代码添加到 `Main` 方法中。 将 `connectionString` 变量设置为在创建命名空间时获得的连接字符串，并将 `topicName` 设置为在创建主题时使用的名称。 另请确保将 `<your subscription name>` 替换为在步骤 3 创建的订阅的名称。 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
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
4. 运行该程序，并再次检查门户。 请注意，“消息计数”和“当前”值现在为 0。
   
    ![主题长度][topic-message-receive]

祝贺你！ 你现在已创建主题和订阅，发送了一条消息并接收了该消息。

## <a name="next-steps"></a>后续步骤

查看 [GitHub 存储库](https://github.com/Azure/azure-service-bus/tree/master/samples)中的示例，了解服务总线消息传送的一些更高级的功能。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
