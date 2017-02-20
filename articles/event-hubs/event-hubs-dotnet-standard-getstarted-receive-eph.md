---
title: "使用 .NET Standard 从 Azure 事件中心接收事件 | Microsoft 文档"
description: "使用 .NET Standard 中的 EventProcessorHost 接收消息入门"
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
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 31b86898552ef9eb6708c83968736f14597223b1

---

# <a name="get-started-receiving-messages-with-the-eventprocessorhost-in-net-standard"></a>使用 .NET Standard 中的 EventProcessorHost 接收消息入门

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleEphReceiver) 上提供了此示例。

## <a name="what-will-be-accomplished"></a>将要完成的任务

本教程演示如何创建现有解决方案 **SampleEphReceiver**（在此文件夹中）。 可以按原样运行该解决方案，并将 `EhConnectionString`、`EhEntityPath` 和 `StorageAccount` 字符串替换为事件中心和存储帐户的值，也可以按照本教程创建自己的解决方案。

在本教程中，我们将编写 .NET Core 控制台应用程序，以使用 **EventProcessorHost** 从事件中心接收消息。

## <a name="prerequisites"></a>先决条件

1. [Visual Studio 2015](http://www.visualstudio.com)。

2. [.NET Core Visual Studio 2015 工具](http://www.microsoft.com/net/core)。

3. Azure 订阅。

4. 事件中心命名空间。
    
## <a name="receive-messages-from-the-event-hub"></a>从事件中心接收消息

### <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio 并创建新的 .NET Core 控制台应用程序。

### <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

1. 右键单击新创建的项目，然后选择“管理 NuGet 包” 。

2. 单击“浏览”选项卡，然后搜索“Microsoft Azure 事件处理器主机”，并选择“Microsoft Azure 事件处理器主机”项。 单击“安装”以完成安装，然后关闭此对话框。

### <a name="implement-the-ieventprocessor-interface"></a>实现 IEventProcessor 接口

1. 创建一个名为“SimpleEventProcessor”的新类。

2. 在 SimpleEventProcessor.cs 文件的顶部添加以下 `using` 语句。

    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

3. 实现 `IEventProcessor` 接口。 此类应如下所示：

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Collections.Generic;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class SimpleEventProcessor : IEventProcessor
        {
            public Task CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
                return Task.CompletedTask;
            }
    
            public Task OpenAsync(PartitionContext context)
            {
                Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
                return Task.CompletedTask;
            }
    
            public Task ProcessErrorAsync(PartitionContext context, Exception error)
            {
                Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
                return Task.CompletedTask;
            }
    
            public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (var eventData in messages)
                {
                    var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                    Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
                }
    
                return context.CheckpointAsync();
            }
        }
    }
    ```

### <a name="write-a-main-console-method-that-uses-simpleeventprocessor-to-receive-messages-from-an-event-hub"></a>编写使用 `SimpleEventProcessor` 从事件中心接收消息的主控制台方法

1. 在 Program.cs 文件顶部添加以下 `using` 语句。
  
    ```cs
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    ```

2. 向 `Program` 类添加常量作为事件中心连接字符串、事件中心路径、存储容器名称、存储帐户名称和存储帐户密钥。 将占位符替换为其对应值。

    ```cs
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. 将名为 `MainAsync` 的新方法添加到 `Program` 类，如下所示：
    ```cs
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EhEntityPath,
            PartitionReceiver.DefaultConsumerGroupName,
            EhConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. 在 `Main` 方法中添加以下代码行：

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs 文件的内容如下所示：

    ```cs
    namespace SampleEphReceiver
    {
        using System;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
        using Microsoft.Azure.EventHubs.Processor;
    
        public class Program
        {
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";
    
            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
    
            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");
    
                var eventProcessorHost = new EventProcessorHost(
                    EhEntityPath,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EhConnectionString,
                    StorageConnectionString,
                    StorageContainerName);
    
                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
    
                Console.WriteLine("Receiving. Press enter key to stop worker.");
                Console.ReadLine();
    
                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```
  
4. 运行程序，并确保没有任何错误。
  
祝贺你！ 现在你已从事件中心接收消息。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


