---
title: Azure 事件中心 .NET Standard API 概述 | Microsoft 文档
description: .NET Standard API 概述
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: d44cdf9204ac041a12cecce995efef71272204e6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007039"
---
# <a name="event-hubs-net-standard-api-overview"></a>事件中心 .NET Standard API 概述

本文汇总了一些重要的 Azure 事件中心 [.NET Standard 客户端 API](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)。 目前有两个适用于事件中心的 .NET Standard 客户端库：

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)：提供基本运行时的所有操作。
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)：添加了其他功能，该功能可以跟踪处理的事件，并且是从事件中心读取数据的最简单方法。

## <a name="event-hubs-client"></a>事件中心客户端

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) 是发送事件、创建接收器，以及获取运行时信息时使用的主对象。 此客户端链接到特定的事件中心，并创建与事件中心终结点的新连接。

### <a name="create-an-event-hubs-client"></a>创建事件中心客户端

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) 对象从连接字符串创建。 下面的示例显示了实例化新客户端的最简单方法：

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

要以编程方式编辑连接字符串，可以使用 [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) 类，并将连接字符串作为参数传递给 [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_)。

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>发送事件

要将事件发送到事件中心，请使用 [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) 类。 主体必须是 `byte` 数组，或 `byte` 数组段。

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>接收事件

从事件中心接收事件的建议方法是使用 [Event Processor Host](#event-processor-host-apis)，它提供相关功能来自动跟踪事件中心偏移量和分区信息。 但是，在某些情况下，可能需要利用核心事件中心库的灵活性来接收事件。

#### <a name="create-a-receiver"></a>创建接收器

接收器将绑定到特定分区，因此为了接收事件中心内的所有事件，必须创建多个实例。 好的做法是以编程方式获取分区信息，而不是对分区 ID 进行硬编码。 为此，可以使用 [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync) 方法。

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

由于事件永远不会从事件中心删除（而只会过期），因此必须指定适当的起始点。 以下示例演示可能的组合：

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>使用事件

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>事件处理程序主机 API

这些 API 通过在可用工作进程之间分布分区，为可能变得不可用的工作进程提供复原能力：

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

下面是 [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) 接口的示例实现：

```csharp
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
```

## <a name="next-steps"></a>后续步骤

若要了解有关事件中心方案的详细信息，请访问以下链接：

* [什么是 Azure 事件中心？](event-hubs-what-is-event-hubs.md)
* [可用的事件中心 API](event-hubs-api-overview.md)

下面提供了 .NET API 参考：

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)