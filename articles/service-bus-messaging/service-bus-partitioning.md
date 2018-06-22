---
title: 创建分区的 Azure 服务总线队列和主题 | Microsoft 文档
description: 介绍如何使用多个消息中转站对服务总线队列和主题进行分区。
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/06/2018
ms.author: sethm
ms.openlocfilehash: f9fb5f53496ea3f98a9d3341e77db283a3e3b570
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824364"
---
# <a name="partitioned-queues-and-topics"></a>分区的队列和主题

Azure 服务总线使用多个消息中转站来处理消息，并用多个消息传送存储来存储消息。 传统的队列或主题由单个消息中转站进行处理并存储在一个消息存储中。 服务总线*分区*允许跨多个消息中转站和消息存储对队列和主题或*消息实体*进行分区。 分区意味着分区实体的总吞吐量不再受单个消息中转站或消息存储的性能所限制。 此外，消息传送存储的临时中断不会导致分区的队列或主题不可用。 分区的队列和主题可以包含所有先进的服务总线功能，如事务和会话支持。

> [!NOTE]
> 分区在为基本或标准 SKU 中的所有队列和主题创建实体时可用。 它不可用于高级消息传送 SKU，但高级命名空间中任何先前已存在的分区实体将继续按预期方式工作。
 
无法更改任何现有队列或主题中的分区选项，只能在创建实体时设置此选项。

## <a name="how-it-works"></a>工作原理

每个分区的队列或主题由多个片段构成。 每个片段存储在不同的消息传送存储中并由不同的消息中转站进行处理。 当向分区的队列或主题发送消息时，服务总线会将该消息分配到其中一个片段。 选择是通过服务总线或发送方可以指定的分区键随机完成的。

客户端要从分区队列或从分区主题的订阅接收消息时，服务总线查询所有片段以获取消息，并将自任何消息存储获取的第一条消息返回到接收方。 服务总线缓存其他消息并在收到其他接收请求时将它们返回。 接收客户端无法识别分区；分区队列或主题的面向客户端的行为（例如，读取、完成、延迟、死信、预提取）与常规实体行为相同。

向分区队列或主题发送一条消息，或从分区队列或主题接收消息时无需额外付费。

## <a name="enable-partitioning"></a>启用分区

要将分区队列和主题用于 Azure 服务总线，请使用 Azure SDK 2.2 版或更高版本，或在 HTTP 请求中指定 `api-version=2013-10` 或更高版本。

### <a name="standard"></a>标准

在标准消息传送层中，可以创建 1、2、3、4 或 5 GB 大小的服务总线队列和主题（默认值为 1 GB）。 已启用分区，服务总线指定的每个 GB 的实体创建 16 个副本（16 个分区）。 因此，如果创建了一个大小为 5 GB 的队列，共有 16 个分区，最大队列大小为 (5 \* 16) = 80 GB。 可通过在 [Azure 门户][Azure portal]中分区队列或主题的“概述”边栏选项卡中查看该实体的条目来了解该队列或主题的最大大小。

### <a name="premium"></a>高级

在高级层命名空间中，不支持分区实体。 但是，仍然可以创建 1、2、3、4、5、10、20、40 或 80 GB 大小的服务总线队列和主题（默认值为 1 GB）。 可通过在 [Azure 门户][Azure portal]中队列或主题的“概述”边栏选项卡中查看该实体的条目来了解该队列或主题的大小。

### <a name="create-a-partitioned-entity"></a>创建分区实体

有多种方法可以创建分区的队列或主题。 从应用程序创建队列或主题时，可以通过分别将 [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] 或 [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] 属性设置为 **true** 来启用队列或主题的分区。 这些属性必须在创建队列或主题时设置，并仅在较旧的 [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 库中可用。 如前面所述，无法更改现有队列或主题的这些属性。 例如：

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

或者，可以在 [Azure 门户][Azure portal]中创建分区队列或主题。 在门户中创建队列或主题时，队列或主题的“创建”对话框中的“启用分区”选项是默认选中的。 只能在标准层实体中禁用此选项；在高级层分区中，分区不受支持，该复选框无效。 

## <a name="use-of-partition-keys"></a>使用分区键

一条消息在分区队列或主题中排队时，服务总线检查是否存在分区键。 如果找到，它将选择基于该密钥的片段。 如果找不到分区密钥，它将选择基于内部算法的片段。

### <a name="using-a-partition-key"></a>使用分区键

某些应用场景（例如会话或事务）中，要求将消息存储在特定的片段中。 所有这些应用场景都需要使用分区键。 使用相同的分区键的所有消息都分配到同一片段中。 如果该片段暂时不可用，服务总线将返回一条错误消息。

根据应用场景，将不同的消息属性用作分区键：

SessionId：如果消息已设置 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 属性，则服务总线会将 SessionID 用作分区键。 这样一来，属于同一会话的所有消息都由同一消息中转站处理。 会话使服务总线得以保证消息顺序以及会话状态的一致性。

PartitionKey：如果消息已设置 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性但未设置 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 属性，则服务总线将 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性值用作分区键。 如果消息同时具有 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 和 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性集，这两个属性必须相同。 如果 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性设置为与 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 属性不同的值，则服务总线返回无效操作异常。 如果发送方发送非会话感知事务消息，应使用 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性。 分区键可确保事务中所发送的所有消息都由同一个消息传递中转站处理。

MessageId：如果队列或主题将 [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) 属性设置为“true”且未设置 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 或 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性，则 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 属性值将充当分区键。 （如果发送应用程序不这样做，Microsoft .NET 和 AMQP 库会自动分配消息 ID。）在这种情况下，同一消息的所有副本都由同一消息中转站处理。 此 ID 使服务总线能够检测并消除重复的消息。 如果 [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) 属性未设置为“true”，服务总线不考虑将 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 属性用作分区键。

### <a name="not-using-a-partition-key"></a>不使用分区键

如果没有分区键，服务总线以轮循机制将消息分发到分区队列或主题的所有片段。 如果所选的片段不可用，服务总线会将消息分配给不同的片段。 这样一来，尽管消息存储暂时不可用，发送操作仍可成功。 但是，无法实现分区键提供的保证排序。

有关可用性（没有分区键）和一致性（使用分区键）之间的权衡的更深入讨论，请参阅[此文](../event-hubs/event-hubs-availability-and-consistency.md)。 此信息同样适用于已分区服务总线实体。

要给服务总线足够的时间将消息排入不同片段的队列中，客户端指定的发送消息的 [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) 值必须大于 15 秒。 建议将 [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) 属性设置为 60 秒的默认值。

分区键会将消息“固定”到特定片段。 如果保存此片段的消息传送存储不可用，服务总线将返回一条错误消息。 如果没有分区键，服务总线可以选择其他片段且操作将成功。 因此，建议除非必需，否则不要提供分区键。

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>高级主题：将事务用于分区实体

作为事务一部分发送的消息必须指定分区键。 键可以是以下属性之一：[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)、[PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)，或 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)。 所有作为同一事务一部分发送的消息必须指定相同的分区键。 如果尝试在事务中发送一条没有分区键的消息，服务总线会返回无效操作异常。 如果尝试在同一事务中发送多条具有不同分区键的消息，服务总线也会返回无效操作异常。 例如：

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

如果设置了任何作为分区键的属性，服务总线会将消息固定到特定片段。 无论是否使用事务，该行为都会发生。 建议如非必须不要指定分区键。

## <a name="using-sessions-with-partitioned-entities"></a>会话用于分区实体

要将事务消息发送到会话感知的主题或队列，消息必须设置 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 属性。 如果还指定了 [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 属性，该属性必须与 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) 属性相同。 如果不同，则服务总线会返回无效操作异常。

与常规（非分区）的队列或主题不同，不可能使用单一事务来将多条消息发送到不同会话。 如果进行尝试，服务总线会返回无效操作异常。 例如：

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>使用分区实体自动进行消息转发

服务总线支持从分区实体、向分区实体或在分区实体之间进行消息自动转发。 若要启用消息自动转发，请在源队列或订阅上设置 [QueueDescription.ForwardTo][QueueDescription.ForwardTo] 属性。 如果该消息指定分区键（[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)、[PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) 或 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)），则该分区键用于目标实体。

## <a name="considerations-and-guidelines"></a>注意事项和指南
* **高度一致性功能**：如果实体使用会话、重复检测或显式控制分区键等功能，则消息传送操作一定会路由至特定的片段。 如果任何片段遇到过高的流量，或基础存储处于不正常状态，这些操作会失败，而且可用性会降低。 整体来说，一致性仍然远高于非分区实体；只有一部分流量会遇到问题，而不是所有流量。 有关详细信息，请参阅此处[对可用性和一致性的讨论](../event-hubs/event-hubs-availability-and-consistency.md)。
* **管理**：必须对实体的所有片段执行创建、更新及删除等操作。 如果任何片段处于不正常状态，可能会导致这些操作失败。 以“获取”操作来说，必须汇总来自所有片段的信息，例如消息计数。 如果任何片段处于不正常状态，则实体可用性状态会报告为受限制。
* **少量消息的情况**：对于这类情况，尤其是使用 HTTP 协议时，可能必须执行多次接收操作，才能获取所有消息。 对于接收请求，前端会在所有片段上执行接收，并缓存所有收到的响应。 相同连接上的后续接收请求将受益于此缓存，而且接收延迟会缩短。 不过，如果有多个连接或使用 HTTP，则会针对每个请求建立新的连接。 因此，不保证抵达相同的节点。 如果现有的所有消息均被锁定，而且在另一个前端中缓存，则接收操作返回 **null**。 消息最后会到期，可以再次接收它们。 建议使用 HTTP 保持连接。
* 浏览/扫视消息：仅在较旧的 [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 库中可用。 [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) 不一定返回 [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) 属性中指定的消息数目。 此行为有两个常见的原因。 其中一个原因是消息集合的汇总大小超过设置的 256 KB 上限。 另一个原因是，如果队列或主题的 [EnablePartitioning 属性](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning)设为 **true**，则分区可能没有足够的消息来完成所请求的消息数目。 一般情况下，如果应用程序要接收特定数目的消息，则应该重复调用 [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch)，直到获得该数目的消息，或者已没有更多消息可速览为止。 有关详细信息，包括代码示例，请参阅 [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) 或 [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API 文档。

## <a name="latest-added-features"></a>最新添加的功能

* 分区实体现在支持添加或删除规则。 与非分区实体不同的是，不支持在事务下执行这些操作。 
* AMQP 现在支持往返于分区实体发送和接收消息。
* AMQP 现在支持以下操作：[成批发送](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch)、[成批接收](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch)、[按序列号接收](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive)、[速览](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek)、[续订锁定](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock)、[计划消息](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync)、[取消计划的消息](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync)、[添加规则](/dotnet/api/microsoft.azure.servicebus.ruledescription)、[删除规则](/dotnet/api/microsoft.azure.servicebus.ruledescription)、[会话续订锁定](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock)、[设置会话状态](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate)、[获取会话状态](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)和[枚举会话](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)。

## <a name="partitioned-entities-limitations"></a>分区实体限制

当前，服务总线对分区的队列和主题施加以下限制：

* 高级消息传送层不支持分区队列和主题。
* 分区的队列和主题不支持在单个事务中发送属于不同会话的消息。
* 服务总线当前允许为每个命名空间最多创建 100 个分区的队列或主题。 每个分区的队列或主题都将计入每个命名空间的 10,000 个实体的配额（不适用于高级层）。

## <a name="next-steps"></a>后续步骤

在 [AMQP 1.0 协议指南](service-bus-amqp-protocol-guide.md)中了解 AMQP 1.0 消息传送规范的核心概念。

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
