---
title: 可用性和一致性 - Azure 事件中心 | Microsoft Docs
description: 如何使用分区在 Azure 事件中心中提供最大程度的可用性和一致性。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2020
ms.author: shvija
ms.openlocfilehash: 0546adb6131479a8f5d2e7e31819483200586839
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397338"
---
# <a name="availability-and-consistency-in-event-hubs"></a>事件中心中的可用性和一致性

## <a name="overview"></a>概述
Azure 事件中心使用[分区模型](event-hubs-scalability.md#partitions)在单个事件中心内提高可用性和并行化。 例如，如果事件中心具有四个分区，并且其中一个分区要在负载均衡操作中从一台服务器移动到另一台服务器，则仍可以通过其他三个分区进行发送和接收。 此外，具有更多分区可以让更多并发读取器处理数据，从而提高聚合吞吐量。 了解分布式系统中分区和排序的意义是解决方案设计的重要方面。

为了帮助说明排序与可用性之间的权衡，请参阅 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)（也称为 Brewer 的定理）。 此定理论述了如何在一致性、可用性和分区容差之间进行选择。 它指出对于由网络分区的系统，始终在一致性与可用性之间作出权衡。

Brewer 的定理按如下所示定义一致性和可用性：
* 分区容差：系统即使在出现分区故障时也能继续处理数据的数据处理能力。
* 可用性：非故障节点在合理时间量内返回合理响应（没有错误或超时）。
* 一致性：保证读取针对给定客户端返回最新写入。

## <a name="partition-tolerance"></a>分区容差
事件中心在分区数据模型的基础上构建。 可以在设置过程中配置事件中心内的分区数，但以后无法更改此值。 由于必须对事件中心使用分区，因此需要在应用程序的可用性和一致性方面进行决策。

## <a name="availability"></a>可用性
开始使用事件中心的最简单方法是使用默认行为。 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs（5.0.0 或更高版本）](#tab/latest)
如果创建新**[的 EventHub 创建者客户端](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient?view=azure-dotnet)** 对象并使用**[SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync?view=azure-dotnet)** 方法，则事件将自动分布在事件中心的分区之间。 此行为可实现最大运行时间量。

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs（4.1.0 或更早版本）](#tab/old)
如果创建新的 **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** 对象并使用 **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** 方法，会自动在事件中心内的各个分区之间分发这些事件。 此行为可实现最大运行时间量。

---

对于需要最大运行时间的用例，此模型是首选模型。

## <a name="consistency"></a>一致性
在某些方案中，事件的排序可能十分重要。 例如，可能希望后端系统在删除命令之前处理更新命令。 在这种情况下，您可以设置事件的分区键，或者使用`PartitionSender`对象（如果您使用的是旧的 Microsoft.Azure.消息库）仅将事件发送到特定分区。 这样做可确保从分区读取这些事件时，按顺序读取它们。 如果使用**Azure.消息.事件集线库**，有关详细信息，请参阅[将代码从分区Sender迁移到事件Hub创建者，以便将事件发布到分区](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition)。

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs（5.0.0 或更高版本）](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs（4.1.0 或更早版本）](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

使用此配置，请记住，如果发送到的特定分区不可用，则会收到错误响应。 作为对比，如果未与单个分区关联，则事件中心服务会将事件发送到下一个可用分区。

确保排序的一个可能解决方法（同时还最大限度地延长运行时间）是将事件作为事件处理应用程序的一部分进行聚合。 实现此目的的最简单方法是使用自定义序号属性标记事件。 以下代码展示一个示例：

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs（5.0.0 或更高版本）](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs（4.1.0 或更早版本）](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

此示例将事件发送到事件中心内的一个可用分区，并从应用程序设置对应序号。 此解决方案要求处理应用程序保持状态，不过会为发送者提供更可能可用的终结点。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心服务概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
