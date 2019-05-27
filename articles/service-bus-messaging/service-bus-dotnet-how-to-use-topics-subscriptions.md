---
title: Azure 服务总线主题和订阅入门 | Microsoft Docs
description: 编写一个 C# .NET Core 控制台应用程序，以便使用服务总线消息传递主题和订阅。
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 2ca8f0e34b63802453c8876f878b531e78e66d76
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991779"
---
# <a name="get-started-with-service-bus-topics"></a>服务总线主题入门

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本教程涵盖以下步骤：

1. 编写 .NET Core 控制台应用程序，向主题发送一组消息。
2. 编写 .NET Core 控制台应用程序，从订阅接收这些消息。

## <a name="prerequisites"></a>必备组件

1. Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 可以[激活 Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或者注册[免费试用帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
2. 按照[快速入门：使用 Azure 门户创建服务总线主题和主题的订阅](service-bus-quickstart-topics-subscriptions-portal.md)来执行以下任务：
    1. 创建一个服务总线**命名空间**。
    2. 获取**连接字符串**。
    3. 在此命名空间中创建一个**主题**。
    4. 在此命名空间中创建对此主题的**一个订阅**。
3. [Visual Studio 2017 Update 3（版本 15.3 (26730.01)）](https://www.visualstudio.com/vs)或更高版本。
4. [NET Core SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。
 
## <a name="send-messages-to-the-topic"></a>将消息发送到主题

为了将消息发送到主题中，请使用 Visual Studio 编写一个 C# 控制台应用程序。

### <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio 并创建新的**控制台应用 (.NET Core)** 项目。

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
2. 单击“浏览”选项卡，搜索 [Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)，然后选择“Microsoft.Azure.ServiceBus”项。 单击“安装”以完成安装，并关闭此对话框。
   
    ![选择 NuGet 包][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>编写将消息发送到主题的代码

1. 在 Program.cs 中将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. 在 `Program` 类中声明以下变量。 将 `ServiceBusConnectionString` 变量设置为在创建命名空间时获得的连接字符串，并将 `TopicName` 设置为在创建主题时使用的名称：
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. 将 `Main()` 的默认内容替换为以下代码行：

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. 在 `Main()` 之后直接添加以下异步 `MainAsync()` 方法，以调用“发送消息”方法：

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. 在 `MainAsync()` 方法后直接添加以下 `SendMessagesAsync()` 方法，以便执行发送 `numberOfMessagesToSend` 所指定的消息数（当前设置为 10）的工作：

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. 发件人 Program.cs 文件的内容应如下所示。
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. 运行该程序并检查 Azure 门户：在命名空间“概览”窗口中单击主题的名称。 此时会显示主题的“基本信息”屏幕。 请注意，在列在窗口底部附近的订阅中，订阅的“消息计数”值现在应该为 **10**。 每次运行发件人应用程序而没有检索消息（如下一部分所述）时，该值会增加 10。 另请注意，每次该应用将消息添加到主题，主题的当前大小就会递增，增量为“基本信息”窗口中的“当前”值。
   
      ![消息大小][topic-message]

## <a name="receive-messages-from-the-subscription"></a>从订阅接收消息

若要接收你发送的消息，请创建另一个 .NET Core 控制台应用程序并安装 **Microsoft.Azure.ServiceBus** NuGet 包，类似于前面的发件人应用程序。

### <a name="write-code-to-receive-messages-from-the-subscription"></a>编写从订阅接收消息的代码

1. 在 Program.cs 中将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. 在 `Program` 类中声明以下变量。 将 `ServiceBusConnectionString` 变量设置为在创建命名空间时获得的连接字符串，将 `TopicName` 设置为在创建主题时使用的名称，将 `SubscriptionName` 设置为在创建主题的订阅时使用的名称：
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. 将 `Main()` 的默认内容替换为以下代码行：

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. 在 `Main()` 之后直接添加以下异步 `MainAsync()` 方法，以调用 `RegisterOnMessageHandlerAndReceiveMessages()` 方法：

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. 在 `MainAsync()` 方法后直接添加以下方法，以便注册消息处理程序并接收发件人应用程序发送的消息：

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. 直接在前面的方法后添加以下 `ProcessMessagesAsync()` 方法，以便处理接收的消息：
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. 最后，添加以下方法，用于处理可能发生的任何异常：
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. 收件人 Program.cs 文件的内容应如下所示：
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. 运行该程序，并再次检查门户。 请注意，“消息计数”和“当前”值现在为 **0**。
   
    ![主题长度][topic-message-receive]

祝贺你！ 现已使用 .NET Standard 库创建主题和订阅，发送了 10 条消息，并接收到了这些消息。

> [!NOTE]
> 你可以管理与服务总线资源[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)。 服务总线资源管理器允许用户连接到服务总线命名空间并轻松管理消息实体。 该工具提供高级的功能，如导入/导出功能或测试主题、 队列、 订阅、 中继服务、 通知中心和事件中心的功能。 

## <a name="next-steps"></a>后续步骤

查看服务总线 [GitHub 存储库](https://github.com/Azure/azure-service-bus/tree/master/samples)中的示例，了解服务总线消息传送的一些更高级的功能。

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
