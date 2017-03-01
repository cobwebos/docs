---
title: "使用 .NET Standard 将事件发送到 Azure 事件中心 | Microsoft 文档"
description: "使用 .NET Standard 将事件发送到事件中心入门"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/31/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 57175ddc53d5856cd3492d4c631a92d4bf9247c4
ms.openlocfilehash: a6c5ff034450c9c6a01feb4ae6d84cebd75a5682
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>使用 .NET Standard 将消息发送到事件中心入门

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender) 上提供了此示例。

## <a name="what-will-be-accomplished"></a>将要完成的任务

本教程演示如何创建现有解决方案 **SampleSender**（在此文件夹中）。 可以按原样运行该解决方案，并将 `EhConnectionString`、`EhEntityPath` 和 `StorageAccount` 字符串替换为事件中心的值，也可以按照本教程创建自己的解决方案。

在本教程中，我们将编写 .NET Core 控制台应用程序，以将消息发送到事件中心。

## <a name="prerequisites"></a>先决条件

1. [Visual Studio 2015](http://www.visualstudio.com)。

2. [.NET Core Visual Studio 2015 工具](http://www.microsoft.com/net/core)。

3. Azure 订阅。

4. 事件中心命名空间。

## <a name="send-messages-to-an-event-hub"></a>将消息发送到事件中心

为将消息发送到事件中心，我们将使用 Visual Studio 编写 C# 控制台应用程序。

### <a name="create-a-console-application"></a>创建控制台应用程序

* 启动 Visual Studio 并创建新的 .NET Core 控制台应用程序。

### <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

* 将 [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet 包添加到项目。

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>编写一些代码将消息发送到事件中心

1. 在 Program.cs 文件顶部添加以下 `using` 语句。

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. 向 `Program` 类添加常量作为事件中心连接字符串和实体路径（单个事件中心名称）。 将括号中的占位符替换为在创建事件中心时获得的相应值。

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. 将名为 `MainAsync` 的新方法添加到 `Program` 类，如下所示：

    ```cs
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. 将名为 `SendMessagesToEventHub` 的新方法添加到 `Program` 类，如下所示：

    ```cs
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. 在 `Program` 类的 `Main` 方法中添加以下代码。

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 文件的内容如下所示。

    ```cs
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
    
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
    
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
    
                await SendMessagesToEventHub(100);
    
                await eventHubClient.CloseAsync();
    
                Console.WriteLine("Press any key to exit.");
                Console.ReadLine();
            }
    
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
    
                    await Task.Delay(10);
                }
    
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. 运行程序，并确保没有引发任何错误。
  
祝贺你！ 现在你已向事件中心发送消息。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [从事件中心接收事件](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)
