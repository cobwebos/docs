---
title: Azure 服务总线消息会话 | Microsoft Docs
description: 使用会话处理一系列 Azure 服务总线消息。
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
ms.date: 01/02/2018
ms.author: sethm
ms.openlocfilehash: cc7ef24a3e1781969284a3d1e683fe73ce48b551
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216387"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>消息会话：先进先出 (FIFO) 

使用 Microsoft Azure 服务总线会话，能够以连贯有序的方式处理一系列无限多的相关消息。 若要在服务总线中实现 FIFO 保证，请使用会话。 服务总线没有规定消息之间的关系性质，也没有定义用于确定消息序列开始或结束位置的特定模型。

任何发送程序都可以在将消息提交到主题或队列时创建会话，方法是将 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 属性设置为会话专属的由应用程序定义的某标识符。 在 AMQP 1.0 协议一级，此值映射到 group-id 属性。

在会话感知队列或订阅中，如果有至少一个消息包含会话的 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)，会话就诞生了。 一旦会话诞生，就没有规定会话何时过期或消失的已定义时间或 API。 理论上讲，服务总线认为，今天可以针对会话接收的消息，与一年时间内 SessionId 相同的下一个消息使用的会话是相同的。

然而，通常情况下，应用程序都很清楚一组相关消息的开始和结束位置。 服务总线不设置任何具体规则。

例如，若要有意设置文件的传输序列，请将第一个、中间一个和最后一个消息的 Label 属性分别设置为 start、content 和 end。 content 消息相对位置的计算方式为，当前消息的 SequenceNumber 与 start 消息的 SequenceNumber 的增量值。

借助服务总线中的会话功能，可以 C# 和 Java API 编写的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 形式执行特定接收操作。 可以通过 Azure 资源管理器或在门户中设置标志，为队列或订阅设置 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 属性，从而启用此功能。 若要尝试执行相关 API 操作，必须启用此功能。

在门户中，选中下图中展示的复选框设置标志：

![][2]

会话 API 存在于队列和订阅客户端上。 可以使用一个命令性模型，控制会话和消息的接收时间；还可以使用一个基于处理程序的模型（类似于 OnMessage），此模型简化了接收循环的管理操作。

## <a name="session-features"></a>会话功能

会话支持对交错消息流进行并发解多路复用，同时保留和保证有序传递。

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 接收程序是由接受会话的客户端创建。 客户端调用 C# 编写的 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 或 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)。 在反应回调模型中，它会注册会话处理程序。

当 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 对象被接受同时由客户端保留时，此客户端会对队列或订阅中的包含相应会话 [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) 的所有消息，以及在会话保留期间仍在到达且包含相应 SessionId 的所有消息一直施加排他锁。

调用 Close 或 CloseAsync 时，或当锁定期满导致应用程序无法执行关闭操作时，将会解除锁定。 应将会话锁定视为对文件施加的排他锁。也就是说，应用程序应在不再需要它时和/或不需要再处理其他任何消息时关闭会话。

当多个并发接收程序从队列中拉取消息时，属于特定会话的消息会被分派到当前让相应会话一直处于锁定状态的特定接收程序。 通过此操作，驻留在一个队列或订阅中的交错消息流可以明确解多路复用到各个接收程序，这些接收程序也可以驻留在不同的客户端计算机上，因为锁定管理是在服务总线内的服务端执行。

上图显示三个并发会话接收程序。 某个 `SessionId` = 4 的会话不具有活动的、所属的客户端，这意味着此特定会话不传递任何消息。 会话在很多方面都起着一个子队列的作用。

会话接收程序保留的会话锁定是速览锁定安排模式使用的消息锁的保护伞。 接收程序不能同时有两个消息“在传输”，而必须依序处理消息。 只有在上一个消息已完成或成为死信时，才可以获取新消息。 如果放弃消息，则会在下一次执行接收操作时再次处理相同的消息。

## <a name="message-session-state"></a>消息会话状态

在高度可缩放、高度可用的云系统中处理工作流时，与特定会话相关的工作流处理程序不仅必须能够从意外故障中恢复，还必须能够在不同的进程或计算机中从工作开始位置恢复部分完成的工作。

借助会话状态工具，可以在中转站内为消息会话添加应用程序定义的注释。这样一来，当会话被新处理程序获取时，相对于此会话的已记录处理状态就会立即可用。

从服务总线的角度来看，消息会话状态是一个不透明的二进制对象，可以保留一个消息大小的数据（对于服务总线标准版，大小为 256KB；对于服务总线高级版，大小为 1MB）。 相对于会话的处理状态可以保留在会话状态中，会话状态也可以指向保留此类信息的某存储位置或数据库记录。

用于管理会话状态的 API [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) 存在于 C# 和 Java API 的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 对象中。 之前没有设置会话状态的会话将对 GetState 返回空引用。 可以使用 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 清除之前设置的会话状态。

请注意，只要不清除会话状态，会话状态将保留（返回 **null**），即使会话中的所有消息都已使用，也是如此。

可以使用 Java API 中的 SessionBrowser 方法、[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 上的 [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) 以及 .NET 客户端中的 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient)，枚举队列或订阅中的所有现有会话。

队列或订阅中保留的会话状态计入相应实体的存储配额。 因此，当应用程序完成会话时，建议应用程序清理保留的状态，以杜绝外部管理成本。

## <a name="next-steps"></a>后续步骤

- [完整示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)展示了如何使用 .NET Standard 库发送和接收服务总线队列中基于会话的消息。
- [示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)展示了如何使用 .NET Framework 客户端处理会话感知消息。 

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png