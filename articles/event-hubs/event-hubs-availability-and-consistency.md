---
title: 可用性和一致性-Azure 事件中心 |Microsoft Docs
description: 如何使用分区为 Azure 事件中心提供最大的可用性和一致性。
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
ms.date: 01/29/2020
ms.author: shvija
ms.openlocfilehash: 808e813ad90626acec893a021634566f091c895f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904480"
---
# <a name="availability-and-consistency-in-event-hubs"></a>事件中心中的可用性和一致性

## <a name="overview"></a>概述
Azure 事件中心使用[分区模型](event-hubs-scalability.md#partitions)在单个事件中心内提高可用性和并行化。 例如，如果事件中心具有四个分区，并且其中一个分区在负载均衡操作中从一台服务器移动到另一台服务器，则仍可以从其他三个分区进行发送和接收。 此外，通过更多分区可以让更多并发读取器处理数据，从而提高聚合吞吐量。 了解分布式系统中分区和排序的意义是解决方案设计的一个重要方面。

若要帮助说明排序与可用性之间的权衡，请参阅[CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)（也称为 Brewer 的定理）。 此定理讨论了一致性、可用性和分区容差之间的选择。 这表明，对于通过网络进行分区的系统，始终在一致性与可用性之间进行权衡。

Brewer 的定理按如下所示定义一致性和可用性：
* 分区容差：即使在出现分区故障时，数据处理系统也能继续处理数据。
* 可用性：非故障节点在合理时间内返回合理响应（没有错误或超时）。
* 一致性：保证读取操作返回给定客户端的最新写入。

## <a name="partition-tolerance"></a>分区容差
事件中心在分区数据模型的基础上构建。 可以在安装过程中配置事件中心内的分区数，但以后无法更改此值。 由于必须对事件中心使用分区，因此必须决定应用程序的可用性和一致性。

## <a name="availability"></a>可用性
开始使用事件中心的最简单方法是使用默认行为。 如果创建一个新的 **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** 对象并使用 **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** 方法，则事件会自动在事件中心内的分区之间分布。 此行为允许最大的正常运行时间。

对于需要最大运行时间的用例，此模型是首选模型。

## <a name="consistency"></a>起见
在某些情况下，事件的排序可能十分重要。 例如，你可能希望后端系统在删除命令之前处理更新命令。 在此实例中，可以对事件设置分区键，也可以使用 `PartitionSender` 对象仅将事件发送到特定分区。 这样做可确保从分区读取这些事件时，按顺序读取它们。

使用此配置时，请记住，如果发送到的特定分区不可用，则会收到错误响应。 作为比较，如果没有与单个分区的相似性，事件中心服务会将事件发送到下一个可用分区。

一种可能的解决方案是确保排序，同时最大程度地提高时间，将事件作为事件处理应用程序的一部分进行聚合。 实现此目的的最简单方法是使用自定义序号属性标记事件。 下面的代码演示了一个示例：

#### <a name="azuremessagingeventhubs-500-or-latertablatest"></a>[EventHubs （5.0.0 或更高）](#tab/latest)

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

#### <a name="microsoftazureeventhubs-410-or-earliertabold"></a>[EventHubs （4.1.0 或更早版本）](#tab/old)
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

此示例将事件发送到事件中心内的一个可用分区，并从应用程序设置相应的序列号。 此解决方案要求处理应用程序保持状态，但为发送方提供更有可能可用的终结点。

## <a name="next-steps"></a>后续步骤
可以访问以下链接，了解有关事件中心的详细信息：

* [事件中心服务概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
