---
title: Azure 服务总线消息、有效负载和序列化 | Microsoft Docs
description: 概述了服务总线消息有效负载
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: sethm
ms.openlocfilehash: 9ac7e71002a375961b8d06b44bbccce2919129e4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200057"
---
# <a name="messages-payloads-and-serialization"></a>消息、有效负载和序列化

Microsoft Azure 服务总线负责处理消息。 消息传递键值对属性形式的有效负载和元数据，描述了有效负载，并向服务总线和应用程序提供了处理指令。 有时，光有元数据就足以传递发送程序想要与接收程序沟通的信息，此时有效负载一直为空。

适用于 .NET 和 Java 的正式版服务总线客户端对象模型反映了抽象的服务总线消息结构，此结构与服务总线支持的线路协议相互映射。
 
服务总线消息包括服务总线从未在服务端以任何形式处理过的二进制有效负载部分，以及两组属性。 中转站属性由系统预定义。 这些预定义属性要么控制中转站内的消息级功能，要么映射到常见的标准化元数据项。 用户属性是一组可由应用程序定义和设置的键值对。
 
下表列出了预定义的中转站属性。 这些属性名可与所有正式版客户端 API 配合使用，同时还位于 HTTP 协议映射的 [BrokerProperties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Properties_) JSON 对象中。
 
括号内列出的是在 AMQP 协议一级使用的等效名称。 

| 属性名称                         | 说明                                                                                                                                                                                                                                                                                                                                                                                                                               |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|  [ContentType](/dotnet/api/microsoft.azure.servicebus.message.contenttype) (content-type)           | 视需要描述消息的有效负载，采用符合 RFC2045 第 5 部分格式的描述符；例如，`application/json`。                                                                                                                                                                                                                                                                                             |
|  [CorrelationId](/dotnet/api/microsoft.azure.servicebus.message.correlationid#Microsoft_Azure_ServiceBus_Message_CorrelationId) (correlation-id)       | 使应用程序可出于关联目的指定消息的上下文。例如，反映正在答复的消息的 MessageId。                                                                                                                                                                                                                                                                  |
| [DeadLetterSource](/dotnet/api/microsoft.azure.servicebus.message.deadlettersource)                      | 仅在已成为死信并随后从死信队列自动转发至其他实体的消息中设置。 指明已成为死信的消息所在的实体。 此属性为只读。                                                                                                                                                                                                                                  |
| [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deliverycount)                         | 已尝试传递此消息的次数。 当消息锁期满，或接收程序明确放弃消息时，此计数递增。 此属性为只读。                                                                                                                                                                                                                                                  |
| [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber)                | 对于已自动转发的消息，此属性反映的是在原始提交点首次分配给消息的序列号。 此属性为只读。                                                                                                                                                                                                                                                                |
| [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc)                       | 实体接受并存储消息的即时 UTC。 如果接收程序不想信任发送程序的时钟，可以将此值用作权威的中性到达时间指示器。 此属性为只读。                                                                                                                                                                                                   |
|  [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc) (absolute-expiry-time) | 由于其已过期而标记消息以供删除且无法再从实体中检索消息的即时 UTC。 此属性是通过将 EnqueuedTimeUtc 和控制是否过期的 TimeToLive 相加计算得出。 此属性为只读。                                                                                                                                                                           |
| [ForcePersistence](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence)                      | 对于设置了 [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress) 标志的队列或主题，可以将此属性设置为，指明在消息获得承认前，必须先将消息暂留到磁盘中。 这是所有非快速实体的标准行为。                                                                                                                                                                                                         |
| [Label](/dotnet/api/microsoft.azure.servicebus.message.label) (subject)                       | 借助此属性，应用程序可以类似于电子邮件主题行的标准化方式，向接收程序指明消息目的。                                                                                                                                                                                                                                                                                  |
| [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc)                        | 对于检索到的处于锁定状态（速览锁定接收模式，而非预先安排）的消息，此属性反映消息在队列/订阅中解除锁定的即时 UTC。 锁定期满后，[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deliverycount) 递增，消息再次可供检索。 此属性为只读。                                                                                                                         |
| [LockToken](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.locktoken)                             | 锁定令牌引用了处于速览锁定接收模式的中转站保留的锁定。 此令牌可用于通过 [Deferral](message-deferral.md) API 永久固定锁定，随后并从常规传递状态流中取出消息。 此属性为只读。                                                                                                                                                               |
| [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) (message-id)                | 消息标识符是应用程序定义的值，可用于唯一标识消息及其有效负载。 此标识符是自由格式字符串，可反映 GUID 或派生自应用程序上下文的标识符。 启用后，[重复检测](duplicate-detection.md)功能会发现并删除第二次和后续提交的 MessageId 相同的消息。                                                                |
| [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)                          | 对于[已分区实体](service-bus-partitioning.md)，设置此值后，可以将相关消息分配到同一内部分区，以便能够正确记录提交序列顺序。 分区是由哈希函数通过此值进行选择，无法直接选择。 对于会话感知实体，SessionId 属性替代此值。                                                                                                       |
| [ReplyTo](/dotnet/api/microsoft.azure.servicebus.message.replyto) (reply-to)                    | 应用程序定义的这一可选值是一种标准方法，可用于向消息接收程序明示答复路径。 如果发送程序希望收到答复，它会将此值设置为，要将答复发送到的队列或主题的绝对或相关路径。                                                                                                                                           |
| [ReplyToSessionId](/dotnet/api/microsoft.azure.servicebus.message.replytosessionid) (reply-to-group-id)  | 此值补充了 ReplyTo 信息，并指定了应为发送给答复实体的答复设置的 SessionId。                                                                                                                                                                                                                                                                            |
| [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc)               | 对于仅在延迟后才可供检索的消息，此属性定义了按逻辑将消息排入队列、序列化且消息随后可供检索的即时 UTC。                                                                                                                                                                                                                 |
| [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber)                        | 序列号是在中转站接受并存储消息时分配给消息的唯一 64 位整数，可用作真正的标识符。 对于已分区实体，最前面的 16 位数反映的是分区标识符。 序列号单调递增且无间隔。 在 48-64 位范围用尽后，序列号会回滚到 0。 此属性为只读。                                                                |
| [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) (group-id)                  | 对于会话感知实体，应用程序定义的此值指定了消息的会话附属关系。 会话标识符相同的消息会处于摘要锁定状态，并确切启用依序处理和解多路复用。 对于非会话感知实体，将忽略此值。                                                                                                                                     |
| [大小](/dotnet/api/microsoft.azure.servicebus.message.size)                                  | 反映了中转站日志内存储的消息的大小（以字节为单位），因为它计入存储配额。 此属性为只读。                                                                                                                                                                                                                                                                                                       |
| [State](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.state)                                 | 指明日志中消息的状态。 此属性仅在消息浏览（“速览”）期间才相关，可确定消息是“有效”（在到达队列顶部时可供检索）、是延迟还是在等待排期。 此属性为只读。                                                                                                                                           |
| [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive)                            | 此值是相对持续时间，自中转站接受并存储消息时（如 EnqueueTimeUtc 所捕获）开始算起，期满后则消息过期。 如果未明确设置，假设值为适用于相应队列或主题的 DefaultTimeToLive。 消息级 TimeToLive 值不得超过实体的 DefaultTimeToLive 设置。 否则，将进行无提示调整。 |
| [To](/dotnet/api/microsoft.azure.servicebus.message.to) (to)                               | 此属性已保留，以供将来用于路由方案，当前被中转站本身忽略。 应用程序可以在规则驱动的自动转发链方案中使用此值，指明消息的预期逻辑目标。                                                                                                                                                                                   |
| [ViaPartitionKey](/dotnet/api/microsoft.azure.servicebus.message.viapartitionkey)                       | 如果消息是通过事务范围内的传输队列发送，此值选择传输队列分区。                                                                                                                                                                                                                                                                                                                 |

借助抽象消息模型，可以通过 HTTP（其实始终为 HTTPS）将消息发布到队列，并能通过 AMQP 检索消息。 无论属于上述哪种情况，在相应协议的上下文中，消息看上去都很正常。 中转站属性会根据需要进行转换，用户属性会映射到相应协议消息模型上最合适的位置。 在 HTTP 中，用户属性直接与 HTTP 头相互映射；在 AMQP 中，用户属性与“应用程序-属性”映射相互映射。

## <a name="message-routing-and-correlation"></a>消息路由和关联

上面介绍的部分中转站属性（特别是 [To](/dotnet/api/microsoft.azure.servicebus.message.to)、[ReplyTo](/dotnet/api/microsoft.azure.servicebus.message.replyto)、[ReplyToSessionId](/dotnet/api/microsoft.azure.servicebus.message.replytosessionid)、[MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)、[CorrelationId](/dotnet/api/microsoft.azure.servicebus.message.correlationid) 和 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid)）有助于应用程序将消息路由至特定目标。 为了说明这一点，假设有以下几种模式：

- **简单请求/答复**：发布者向队列发送消息，并希望收到消息使用者的答复。 为了接收答复，发布者拥有答复预期传递到的队列。 此队列的地址通过出站消息的 ReplyTo 属性表示。 如果使用者响应，它会将已处理消息的 MessageId 复制到答复消息的 CorrelationId 属性，并将消息传递到 ReplyTo 属性指明的目标。 一个消息可以有多个答复，具体视应用程序上下文而定。
- **多播请求/答复**：上一种模式的变体。在此模式中，发布者将消息发送到主题，多个订阅者都有资格使用消息。 每个订阅者都可能会以先前描述的方式响应。 此模式用于发现或滚动呼叫方案，回应者通常通过用户属性或在有效负载内进行自我识别。 如果 ReplyTo 指向主题，可以向受众分发这样的一组发现响应。
- **多路复用**：借助此会话功能，可以通过一个队列或订阅对相关消息流进行多路复用，以便相关消息的所有会话/组（与 SessionId 值匹配）可以路由到特定接收程序，同时接收程序能够保持会话处于锁定状态。 若要详细了解会话，请单击[此处](message-sessions.md)。
- **多路复用请求/答复**：借助此会话功能，可以启用多路复用答复，让多个发布者能够共享答复队列。 通过设置 ReplyToSessionId，发布者可以指示使用者将此值复制到答复消息的 SessionId 属性。 发布队列或主题并不需要是会话感知。 在消息发送的同时，发布者可以专门等待具有给定 SessionId 的会话，以便有条件地接受会话接收程序，在队列上实现具体化。 

可以使用自动转发链和主题订阅规则，实现服务总线命名空间内路由。 跨命名空间路由可以[使用 Azure LogicApps](https://azure.microsoft.com/services/logic-apps/) 实现。 如上面的列表所示，To 属性已保留，以供将来使用，最后可能会被已启用特别功能的中转站解析。 要实现路由的应用程序在这样做时，应以用户属性（而不是 To 属性）为依据；不过，现在这样做也不会导致兼容性问题。

## <a name="payload-serialization"></a>有效负载序列化

在服务总线内传输或存储时，有效负载始终为不透明的二进制块。 使用 [ContentType](/dotnet/api/microsoft.azure.servicebus.message.contenttype) 属性，应用程序可以描述有效负载，对属性值采用符合 IETF RFC2045 MIME 内容类型描述的建议格式；例如，`application/json;charset=utf-8`。

与 Java 或 .NET Standard 变体不同，服务总线 API 的 .NET Framework 版本支持将任意 .NET 对象传递到构造函数，创建 BrokeredMessage 实例。 

如果使用的是旧版 SBMP 协议，这些对象使用默认二进制序列化程序，或使用外部提供的序列化程序进行序列化。 如果使用的是 AMQP 协议，对象会被序列化为 AMQP 对象。 接受器可使用 [GetBody<T>()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 方法检索这些对象，并提供预期类型。 使用 AMQP，对象都被序列化为 ArrayList 和 IDictionary<string,object> 对象的 AMQP 图，任何 AMQP 客户端都可以将其解码。 

尽管这种隐藏序列化的神奇操作十分方便，但应用程序应明确控制对象序列化，并先将对象图转为流，再将它们添加到消息中（在接收程序端，操作执行顺序相反）。 这样生产的是交互结果。 还应指出，尽管 AMQP 有功能强大的二进制编码模型，但它与 AMQP 消息生态系统关联，导致 HTTP 客户端无法解码此类有效负载。 

通常建议使用 JSON 和 Apache Avro 作为结构化数据的有效负载格式。

.NET Standard 和 Java API 变体仅接受字节数组。也就是说，应用程序必须控制对象序列化。 

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)