---
title: 使用 .NET Framework 接收事件 - Azure 事件中心 | Microsoft Docs
description: 按照本教程的说明，使用 .NET Framework 从 Azure 事件中心接收事件。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 8052b797707f7913fdd678f4dd51822754623104
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077241"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>使用 .NET Framework 从 Azure 事件中心接收事件

## <a name="introduction"></a>介绍

事件中心是一个服务，可用于处理来自连接设备和应用程序的大量事件数据（遥测）。 将数据采集到事件中心后，可以使用任何实时分析提供程序或存储群集来转换和存储数据。 这种大规模事件收集和处理功能是现代应用程序体系结构（包括物联网 (IoT)）的重要组件。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程演示如何编写 .NET Framework 控制台应用程序，以使用[事件处理程序主机](event-hubs-event-processor-host.md)从事件中心接收消息。 [事件处理程序主机](event-hubs-event-processor-host.md)是一个 .NET 类，它通过从事件中心管理持久检查点和并行接收来简化从那些事件中心接收事件的过程。 使用事件处理程序主机，可跨多个接收方拆分事件，即使在不同节点中托管时也是如此。 此示例演示如何为单一接收方使用事件处理程序主机。 [扩大事件处理][Scale out Event Processing with Event Hubs]示例显示如何将事件处理程序主机用于多个接收方。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* [Microsoft Visual Studio 2017 或更高版本](https://visualstudio.com)。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心
第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的步骤进行操作，然后继续执行本教程的以下步骤。

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>创建控制台应用程序

在 Visual Studio 中，使用 **控制台应用程序** 项目模板创建一个新的 Visual C# 桌面应用项目。 将该项目命名为 **Receiver**。
   
![创建控制台应用程序](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>添加事件中心 NuGet 包

1. 在解决方案资源管理器中，右键单击“Receiver”项目，并单击“为解决方案管理 NuGet 包”。
2. 单击“浏览”选项卡，并搜索 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`。 单击“安装” 并接受使用条款。
   
    ![搜索事件处理器主机 NuGet 包](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio 下载、安装 [Azure 服务总线事件中心 - EventProcessorHost NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)及其所有依赖项并添加对它们的引用。

## <a name="implement-the-ieventprocessor-interface"></a>实现 IEventProcessor 接口

1. 右键单击 **Receiver** 项目，单击“添加”，并单击“类”。 将新类命名为 **SimpleEventProcessor**，然后单击“添加”以创建该类。
   
    ![添加 SimpleEventProcessor 类](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. 在 SimpleEventProcessor.cs 文件的顶部添加以下语句：
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. 用以下代码替换该类的正文：
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      此类由 EventProcessorHost 调用，用于处理从事件中心接收的事件。 `SimpleEventProcessor` 类使用秒表定期对 EventProcessorHost 上下文调用检查点方法。 此操作确保接收方重启时，其丢失的处理工作不会超过五分钟。

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>更新 Main 方法以使用 SimpleEventProcessor

1. 在 **Program** 类中，在文件顶部添加以下 `using` 语句：
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. 将 `Program` 类中的 `Main` 方法替换为以下代码，从而替换为以前保存的事件中心名称和命名空间级别连接字符串，以及在前面部分复制的存储帐户和密钥。 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. 运行程序，并确保没有任何错误。
  
祝贺你！ 现在已使用事件处理程序主机从事件中心接收消息。


> [!NOTE]
> 本教程使用单个 [EventProcessorHost 实例](event-hubs-event-processor-host.md)。 若要增加吞吐量，建议运行多个 [EventProcessorHost](event-hubs-event-processor-host.md) 实例，如[扩展的事件处理](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3)示例中所示。 在这些情况下，为了对接收的事件进行负载均衡，多个实例会自动相互协调。 

## <a name="next-steps"></a>后续步骤
在本快速入门，你已创建从事件中心接收消息的 .NET Framework 应用程序。 若要了解如何使用 .NET Framework 将事件发送到事件中心，请参阅[从事件中心发送事件 - .NET Framework](event-hubs-dotnet-framework-getstarted-send.md)。

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
