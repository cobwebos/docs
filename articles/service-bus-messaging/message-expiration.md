---
title: "Azure 服务总线消息过期 | Microsoft Docs"
description: "Azure 服务总线消息的过期时间和生存时间"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 6e1f6177ccacf24955763982189bcdb1ef69c788
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="message-expiration-time-to-live"></a>消息过期时间（生存时间）

消息中的有效负载，或者由消息传递给接收方的命令或查询，几乎总是附带了某种形式的应用程序级过期截止时间。 此截止时间过后，便不再传送内容，或不再执行请求的操作。

对于经常在应用程序或应用程序部件部分运行轮次的上下文中使用队列与主题的开发和测试环境，还需要对滞留的测试消息自动进行垃圾回收，使下一轮测试运行能够从新启动。

可以通过设置 [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) 系统属性（指定相对持续时间）来控制任何一条消息的过期时间。 在实体中将消息排队后，过期时间即成为一个绝对时刻。 此时，[ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) 属性取值为 [(**EnqueuedTimeUtc**](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc#Microsoft_ServiceBus_Messaging_BrokeredMessage_EnqueuedTimeUtc) + [**TimeToLive**)](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)。

在 **ExpiresAtUtc** 时刻过后，消息不可检索。 过期时间不会影响当前已锁定等待传送的消息；这些消息仍会按正常方式得到处理。 如果锁已过期或者消息被丢弃，则过期时间立即生效。

消息被锁定时，应用程序可以拥有已过期的消息。 应用程序是要继续处理还是选择丢弃消息，则由实施者决定。

## <a name="entity-level-expiration"></a>实体级过期时间

发送到队列或主题中的所有消息附带一个默认的过期时间，该时间是使用 [defaultMessageTimeToLive](/azure/templates/microsoft.servicebus/namespaces/queues) 属性在实体级别设置的，或者也可以在创建期间在门户中设置，并且以后可以调整。 默认过期时间是针对发送到未显式设置 [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) 的实体的所有消息使用的。 默认过期时间还充当 **TimeToLive** 值的上限。 **TimeToLive** 过期时间长于默认值的消息在排队之前，会以无提示方式调整为 **defaultMessageTimeToLive** 值。

可以选择性地通过设置 [EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) 属性或者在门户中选中相应的框，将已过期的消息移到[死信队列](service-bus-dead-letter-queues.md)。 如果保持禁用该选项，将会丢弃已过期的消息。 可以通过评估由中转站存储在用户属性部分中的 [DeadletterReason](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) 属性（在本例中，该值为 [TTLExpiredException](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq)），将已移到死信队列的已过期消息与其他死信消息区分开来。

在前面所述的例子中，消息已被防止过期并被锁定；如果在实体上设置了相应的标志，则锁被丢弃或过期时，会将该消息移到死信队列。 但是，如果已成功解决该消息，因而认为应用程序已成功处理该消息，则不会移动该消息，尽管它在名义上已过期。

[TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) 与过期时自动（和根据事务）加入死信队列的组合是一种极为有用的工具，让我们确信在到达期限时，能够检索在期限范围内提供给一个或一组处理程序的作业进行处理。

例如，假设某个网站需要在规模受限的后端上可靠执行作业，并且偶尔会遇到流量高峰，或者需要受到隔离，以防止该后端在某段时间遇到可用性问题。 在一般情况下，所提交用户数据的服务器端处理程序会将信息推入队列，随后在回复队列中接收确认已成功处理事务的回复。 如果出现流量高峰并且后端处理程序无法及时处理其积压工作项，则已过期的作业会返回到死信队列中。 可以告知交互用户请求的操作所花费的时间比平时稍长，然后可将请求放到不同队列的处理路径，并通过电子邮件将其中的最终处理结果发送给用户。 

## <a name="temporary-entities"></a>临时实体

可将服务总线队列、主题和订阅创建为临时实体，在指定的时间段内未使用这些实体时，系统会自动将其删除。
 
在动态创建实体并且由于测试或调试运行中断，导致用后不会清理实体的开发和测试方案中，自动清理功能十分有用。 当应用程序创建动态实体（例如回复队列）用于在 Web 服务器进程或者生存期相对较短的对象（对象实例消失时难以可靠清理这些实体）中接收响应时，此功能也很有用。

可以使用 [autoDeleteOnIdle](/azure/templates/microsoft.servicebus/namespaces/queues) 属性启用此功能：将此属性设置为自动删除某个实体之前，该实体必须处于空闲（未使用）状态的持续时间。 最短持续时间为 5 分钟。
 
必须通过 Azure 资源管理器操作或 .NET Framework 客户端 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API 设置此 autoDeleteOnIdle 属性。 不能通过门户设置此属性。


## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)