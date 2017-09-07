---
title: "Azure 服务总线配对命名空间 | Microsoft Docs"
description: "配对命名空间实现的详细信息和成本"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: bdd4c7948608c03447d1e040a746ed0eb7b0771b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>配对命名空间实现详细信息和成本影响
使用 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 实例的 [PairNamespaceAsync][PairNamespaceAsync] 方法代表你执行可见任务。 因为使用该功能时需考虑到成本，所以理解这些任务是有用的，以便在行为发生时可以预期到。 该 API 代表你进行下列自动行为：

* 创建积压工作队列。
* 创建创建与队列或主题交谈的 [MessageSender][MessageSender] 对象。
* 当一个消息传送实体变为不可用，向该实体发送 ping 消息以尝试检测该实体何时再次变为可用。
* 可以选择创建一组“消息泵”，将消息从积压工作队列移至主要队列。
* 协调主要和辅助 [MessagingFactory][MessagingFactory] 实例的关闭/故障。

在高级别中，该功能按如下方式工作：若主要实体是正常的，则不会发生行为更改。 当 [FailoverInterval][FailoverInterval] 持续时间过去，且主要实体在非瞬态的 [MessagingException][MessagingException] 或 [TimeoutException][TimeoutException] 之后没有看到成功的发送，将出现以下行为：

1. 到主要实体的发送操作被禁用，且系统对主要实体执行 ping 命令，直到 ping 成功发送。
2. 选择一个随机积压工作队列。
3. [BrokeredMessage][BrokeredMessage] 对象被路由到所选的积压工作队列。
4. 如果到所选的积压工作队列的发送操作失败，则从轮转提取队列并选择新的队列。 [MessagingFactory][MessagingFactory] 实例上的所有发送方会知道该失败。

以下各图描述了顺序。 首先，发送方发送消息。

![成对命名空间][0]

发送给主要队列失败时，发送方开始向随机选择的积压工作队列发送消息。 与此同时，启动 ping 任务。

![成对命名空间][1]

这个时候消息仍在辅助队列，尚未传递给主要队列。 一旦主要队列再次恢复正常，至少一个进程应运行管道。 管道会从所有不同的积压工作队列将消息传递到相应的目标实体（队列和主题）。

![成对命名空间][2]

本主题剩余部分将讨论其工作原理的具体信息。

## <a name="creation-of-backlog-queues"></a>创建积压工作队列
传递到 [PairNamespaceAsync][PairNamespaceAsync] 方法的 [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] 对象指示要使用的积压工作队列的数量。 然后会创建积压工作队列，且每个队列都具有显式设置的以下属性（所有其他值设置为 [QueueDescription][QueueDescription] 默认值）：

| 路径 | [primary namespace]/x-servicebus-transfer/[index] where [index] is a value in [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| LockDuration |1 分钟 |
| EnableDeadLetteringOnMessageExpiration |是 |
| EnableBatchedOperations |是 |

例如，为命名空间 **contoso** 创建的第一个积压工作队列名为 `contoso/x-servicebus-transfer/0`。

创建队列时，代码将首先检查是否存在此类队列。 如果队列不存在，将创建队列。 此代码不清理“额外的”积压工作队列。 具体而言，如果具有主命名空间 **contoso`contoso/x-servicebus-transfer/7` 的应用程序请求五个积压工作队列，但具有路径** 的积压工作队列存在，则该额外积压工作队列仍然存在，但未被使用。 系统显式允许额外积压工作队列存在，但它们不会被使用。 作为命名空间所有者，负责清除任何未使用/不需要的积压工作队列。 此决策的原因是服务总线无法知道命名空间中的所有队列都存在何种目的。 此外，如果队列以给定名称存在，但不符合假定的 [QueueDescription][QueueDescription]，那么更改默认行为的原因是出于自身。 无法为通过代码对积压工作队列进行的修改提供保证。 请确保全面测试你所做的更改。

## <a name="custom-messagesender"></a>自定义 MessageSender
在发送时，所有消息都将通过内部 [MessageSender][MessageSender] 对象，该对象在一切正常运转时正常行动，并在事情“中断”时重定向至积压工作队列。 一旦收到非瞬态故障，计时器启动。 在由 [FailoverInterval][FailoverInterval] 属性值构成的 [TimeSpan][TimeSpan] 时段之后（在此时段中未成功发送消息），将发生故障转移。 此时，每个实体会发生下列情况：

* ping 任务执行每个 [PingPrimaryInterval][PingPrimaryInterval] 以检查实体是否可用。 此任务成功之后，所有使用该实体的客户端代码立即开始发送新消息到主要命名空间。
* 将来从任何其他发送方发送到同一实体的请求将导致将要发送去修改的 [BrokeredMessage][BrokeredMessage] 进入积压工作队列。 该修改将从 [BrokeredMessage][BrokeredMessage] 对象中删除一些属性并将其存储在其他位置。 已清理以下属性并添加在新的别名下，从而允许服务总线和 SDK 统一处理消息：

| 旧属性名称 | 新属性名称 |
| --- | --- |
| SessionId |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

原始目标路径也作为名为 x-ms-path 的属性存储在消息中。 此设计允许多个实体的消息共存于单个积压工作队列中。 属性将通过管道重新转换。

消息接近 256-KB 限制且发生故障转移时，自定义 [MessageSender][MessageSender] 对象会遇到问题。 自定义 [MessageSender][MessageSender] 对象将所有队列和主题的消息一起存储在积压工作队列中。 此对象在积压工作队列中将来自许多主体的消息混合。 要在互相不知道对方的多个客户端中处理负载均衡，SDK 将随机为每个用代码创建的 [QueueClient][QueueClient] 或 [TopicClient][TopicClient] 选择一个积压工作队列。

## <a name="pings"></a>Ping
一条 Ping 消息是一个空 [BrokeredMessage][BrokeredMessage]，其 [ContentType][ContentType] 属性设置为 application/vnd.ms-servicebus-ping 且其 [TimeToLive][TimeToLive] 值为 1 秒。 服务总线中，此 ping 有一个特殊的特性：任何调用方请求 [BrokeredMessage][BrokeredMessage] 时，服务器永远不会发送 ping 请求。 因此，永远不需要了解如何接收和忽略这些消息。 每个客户端的每个 [MessagingFactory][MessagingFactory] 实例的每个实体（唯一的队列或主题）被视为不可用时，将对其执行 ping 命令。 默认情况下，每分钟发生一次。 Ping 消息被视为常规服务总线消息，并可能会产生针对带宽和消息的收费。 只要客户端检测到系统可用，消息将停止。

## <a name="the-syphon"></a>管道
应用程序中至少有一个可执行程序应活跃地运行管道。 管道会执行持续 15 分钟的长时间轮询接收。 当所有实体均可用且你有 10 个积压工作队列时，托管管道的应用程序每小时 40 次、每天 960 次，30 天 28800 次调用接收操作。 当管道活跃地将消息从积压工作向主要队列中移动时，每条消息会产生以下费用（在所有阶段中按照消息大小和带宽收取标准费用）：

1. 发送到积压工作。
2. 从积压工作接收。
3. 发送到主体。
4. 从主体接收。

## <a name="closefault-behavior"></a>关闭/故障行为
在托管管道的应用程序中，一旦主要或辅助 [MessagingFactory][MessagingFactory] 发生故障或关闭，同时其合作伙伴队列并未发生故障或关闭，且管道检测到此状态，则管道将发挥作用。 如果其他 [MessagingFactory][MessagingFactory] 在 5 秒内未关闭，管道将导致仍处于打开状态的 [MessagingFactory][MessagingFactory] 发生故障。

## <a name="next-steps"></a>后续步骤
有关服务总线异步消息传送的详细讨论，请参阅[异步消息传递模式和高可用性][Asynchronous messaging patterns and high availability]。 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png

