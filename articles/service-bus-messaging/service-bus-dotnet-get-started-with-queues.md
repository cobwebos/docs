---
title: Azure 服务总线队列入门 | Microsoft Docs
description: 在本教程中，你将创建 .NET Core 控制台应用程序来向服务总线队列发送消息以及从中接收消息。
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 477d9d5a23e50d9b303d560b5530cbc22104c5cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337548"
---
# <a name="get-started-with-service-bus-queues"></a>服务总线队列入门
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
在本教程中，你将创建 .NET Core 控制台应用程序来向服务总线队列发送消息以及从中接收消息。

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2019](https://www.visualstudio.com/vs)。
- [NET Core SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。
- Azure 订阅。 若要完成本教程，需要一个 Azure 帐户。 可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)或[注册免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。

  - 阅读服务总线队列的快速概述。
  - 创建一个服务总线命名空间。
  - 获取连接字符串。
  - 创建服务总线队列。

## <a name="send-messages-to-the-queue"></a>将消息发送到队列

为了将消息发送到队列中，请使用 Visual Studio 编写一个 C# 控制台应用程序。

### <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio 并创建新的用于 C# 的**控制台应用 (.NET Core)** 项目。 此示例将应用命名为 *CoreSenderApp*。

### <a name="add-the-service-bus-nuget-package"></a>添加服务总线 NuGet 包

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
1. 选择“浏览”。 搜索并选择 **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** 。
1. 选择“安装”以完成安装，然后关闭“NuGet 包管理器”。

    ![选择 NuGet 包][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>编写将消息发送到队列的代码

1. 在 *Program.cs* 中将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. 在 `Program` 类中声明以下变量：

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    以 `ServiceBusConnectionString` 变量的形式输入命名空间的连接字符串。 输入队列名称。

1. 将 `Main()` 方法替换为以下 **async** `Main` 方法。 它调用 `SendMessagesAsync()` 方法（将在下一步中添加），以将消息发送到队列。 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. 在 `MainAsync()` 方法后直接添加以下 `SendMessagesAsync()` 方法，以便执行发送 `numberOfMessagesToSend` 所指定的消息数（当前设置为 10）的工作：

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

*Program.cs* 文件的内容如下所示。

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
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

运行该程序，并检查 Azure 门户。

选择命名空间“概览”窗口中的队列名称，以便显示队列**基本信息**。

![收到的消息，包含计数和大小][queue-message]

队列的“活动消息计数”值现在为 **10**。 每次运行此发件人应用而没有检索消息时，该值会增加 10。

每次该应用将消息添加到队列，队列的当前大小就会递增，增量为“基本信息”中的“当前”值。 

下一部分介绍如何检索这些消息。

## <a name="receive-messages-from-the-queue"></a>从队列接收消息

若要接收发送的消息，请创建另一**控制台应用 (.NET Core)** 应用程序。 安装 **Microsoft.Azure.ServiceBus** NuGet 包，就像为发件人应用程序所做的那样。

### <a name="write-code-to-receive-messages-from-the-queue"></a>编写从队列接收消息的代码

1. 在 *Program.cs* 中将以下 `using` 语句添加到命名空间定义顶部，位于类声明之前：

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. 在 `Program` 类中声明以下变量：

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    以 `ServiceBusConnectionString` 变量的形式输入命名空间的连接字符串。 输入队列名称。

1. 将 `Main()` 方法替换为以下代码：

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. 在 `MainAsync()` 方法后直接添加以下方法，以便注册消息处理程序并接收发件人应用程序发送的消息：

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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. 直接在前面的方法后添加以下 `ProcessMessagesAsync()` 方法，以便处理接收的消息：

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. 最后，添加以下方法，用于处理可能发生的任何异常：

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

*Program.cs* 文件的内容如下所示：

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
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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

运行该程序，并再次检查门户。 “活动消息计数”和“当前”值现在为 **0**。 

![收到消息后的队列][queue-message-receive]

祝贺！ 你现在已创建队列、将一组消息发送到该队列，以及从该队列接收这些消息。

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户轻松连接到服务总线命名空间并管理消息传送实体。 该工具提供高级功能，例如导入/导出功能，或者用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。

## <a name="next-steps"></a>后续步骤

查看 [GitHub 存储库](https://github.com/Azure/azure-service-bus/tree/master/samples)中的示例，了解服务总线消息传送的一些更高级的功能。

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

