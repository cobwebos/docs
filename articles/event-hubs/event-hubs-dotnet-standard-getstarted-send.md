---
title: 使用 .NET Core 发送事件 - Azure 事件中心 | Microsoft Docs
description: 本文提供了创建 .NET Core 应用程序的演练，该应用程序用于将事件发送到 Azure 事件中心。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 2bf5825d3f13044d1c9bde2b05d1c93ecd967a0f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782031"
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-core"></a>在 .NET Core 中开始将消息发送到 Azure 事件中心
事件中心是一个服务，可用于处理来自连接设备和应用程序的大量事件数据（遥测）。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。 这种大规模事件收集和处理功能是现代应用程序体系结构（包括物联网 (IoT)）的重要组件。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何使用控制台应用程序（通过 .NET Core 以 C# 编写）将事件发送到事件中心。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName`字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>必备组件
* [Microsoft Visual Studio 2015 或 2017](https://www.visualstudio.com)。 本教程中的示例使用 Visual Studio 2017，但也支持 Visual Studio 2015。
* [.NET Core Visual Studio 2015 或 2017 工具](https://www.microsoft.com/net/core)。 

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心
第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。

按照以下文章中的说明获取事件中心命名空间的连接字符串：[获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 本教程后面的步骤将使用此连接字符串。 

## <a name="create-a-console-application"></a>创建控制台应用程序

启动 Visual Studio。 在“文件”菜单中，单击“新建”，并单击“项目”。 创建 .NET Core 控制台应用程序。

![新建项目][1]

## <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

通过执行以下步骤，将 [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET Core 库 NuGet 包添加到项目中： 

1. 右键单击新创建的项目，并选择“管理 NuGet 包” 。
2. 单击“浏览”选项卡，然后搜索“Microsoft.Azure.EventHubs”，并选择“Microsoft.Azure.EventHubs”包。 单击“安装”以完成安装，并关闭此对话框。

## <a name="write-code-to-send-messages-to-the-event-hub"></a>编写代码以将消息发送到事件中心

1. 在 Program.cs 文件顶部添加以下 `using` 语句：

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. 向 `Program` 类添加常量作为事件中心连接字符串和实体路径（单个事件中心名称）。 将括号中的占位符替换为在创建事件中心时获得的相应值。 请确保 `{Event Hubs connection string}` 是命名空间级别的连接字符串，而不是事件中心字符串。 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. 将名为 `MainAsync` 的新方法添加到 `Program` 类，如下所示：

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. 将名为 `SendMessagesToEventHub` 的新方法添加到 `Program` 类，如下所示：

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
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

5. 在 `Program` 类的 `Main` 方法中添加以下代码：

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs 文件的内容如下所示。

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
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

6. 运行程序，并确保没有任何错误。

祝贺你！ 现在已向事件中心发送消息。

## <a name="next-steps"></a>后续步骤
在本快速入门中，你已使用 Node.js 向事件中心发送了消息。 若要了解如何使用 .NET Core 从事件中心接收事件，请参阅[从事件中心接收事件 - .NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md)。

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png
