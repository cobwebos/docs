---
title: Azure 服务总线消息会话 | Microsoft Docs
description: 本文说明了如何使用会话实现对无限的相关消息序列的连贯有序处理。
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314041"
---
# <a name="message-sessions"></a>消息会话
使用 Microsoft Azure 服务总线会话，能够以连贯有序的方式处理一系列无限多的相关消息。 会话可用于先出、先出 （FIFO） 和请求-响应模式。 本文演示如何在使用服务总线时使用会话来实现这些模式。 

## <a name="first-in-first-out-fifo-pattern"></a>先出先出 （FIFO） 模式
要在服务总线中实现 FIFO 保证，请使用会话。 服务总线并不规定消息之间的关系的性质，也没有定义用于确定消息序列开始或结束位置的特定模型。

> [!NOTE]
> 服务总线的基本层不支持会话。 标准层和高级层支持会话。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

任何发送程序都可以在将消息提交到主题或队列时创建会话，方法是将 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 属性设置为会话专属的由应用程序定义的某标识符。 在 AMQP 1.0 协议一级，此值映射到 group-id** 属性。

在会话感知队列或订阅中，当会话的[SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)至少有一条消息时，会话将存在。 会话存在后，没有定义会话过期或消失的时间或 API。 理论上讲，服务总线认为，今天可以针对会话接收的消息，与一年时间内 SessionId**** 相同的下一个消息使用的会话是相同的。

然而，通常情况下，应用程序都很清楚一组相关消息的开始和结束位置。 服务总线不设置任何特定规则。

例如，若要有意设置文件的传输序列，请将第一个、中间一个和最后一个消息的 Label**** 属性分别设置为 start****、content**** 和 end****。 content 消息相对位置的计算方式为，当前消息的 SequenceNumber 与 start 消息的 SequenceNumber 的增量值。********

借助服务总线中的会话功能，可以 C# 和 Java API 编写的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 形式执行特定接收操作。 可以通过 Azure 资源管理器或在门户中设置标志，为队列或订阅设置 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 属性，从而启用此功能。 在尝试使用相关的 API 操作之前，需要这样做。

在门户中，选中下图中展示的复选框设置标志：

![][2]

> [!NOTE]
> 在队列或订阅上启用会话时，客户端应用程序可以***不再***发送/接收常规消息。 所有消息必须作为会话的一部分发送（通过设置会话 ID），并通过接收会话来接收。

会话 API 存在于队列和订阅客户端上。 有一个命令模型，用于控制何时接收会话和消息，以及一个基于处理程序的模型（类似于*OnMessage）* 隐藏管理接收循环的复杂性。

### <a name="session-features"></a>会话功能

会话支持对交错消息流进行并发解多路复用，同时保留和保证有序传递。

![][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 接收程序是由接受会话的客户端创建。 客户端调用 C# 编写的 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 或 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)。 在反应回调模型中，它会注册会话处理程序。

当[接受 MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession)对象且由客户端保留时，该客户端对队列或订阅中存在该会话的[SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId)的所有消息以及该**会话 Id**仍位于会话保持期间到达的所有消息持有独占锁。

当调用**Close**或**CloseAsync**时，或在应用程序无法执行关闭操作的情况下，锁过期时释放锁。 会话锁应视为文件上的独占锁，这意味着应用程序应在不再需要会话时立即关闭会话和/或不希望收到任何进一步的消息。

当多个并发接收程序从队列中拉取消息时，属于特定会话的消息会被分派到当前让相应会话一直处于锁定状态的特定接收程序。 通过此操作，一个队列或订阅中的交错消息流可以干净地除以多路复用到不同的接收器，并且这些接收器也可以驻留在不同的客户端计算机上，因为锁管理发生在服务总线内的服务端。

上图显示三个并发会话接收程序。 某个 `SessionId` = 4 的会话不具有活动的、所属的客户端，这意味着此特定会话不传递任何消息。 会话在很多方面都起着一个子队列的作用。

会话接收程序保留的会话锁定是速览锁定** 安排模式使用的消息锁的保护伞。 接收方不能同时"在飞行中"有两条消息，但必须按顺序处理消息。 只有在上一个消息已完成或成为死信时，才可以获取新消息。 如果放弃消息，则会在下一次执行接收操作时再次处理相同的消息。

### <a name="message-session-state"></a>消息会话状态

在大规模高可用性云系统中处理工作流时，与特定会话关联的工作流处理程序必须能够从意外故障中恢复，并且可以从工作开始地的不同进程或计算机上恢复部分已完成的工作。

借助会话状态工具，可以在中转站内为消息会话添加应用程序定义的注释。这样一来，当会话被新处理程序获取时，相对于此会话的已记录处理状态就会立即可用。

从服务总线的角度来看，消息会话状态是一个不透明的二进制对象，可以保留一个消息大小的数据（对于服务总线标准版，大小为 256KB；对于服务总线高级版，大小为 1MB）。 相对于会话的处理状态可以保留在会话状态中，会话状态也可以指向保留此类信息的某存储位置或数据库记录。

用于管理会话状态的 API [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) 存在于 C# 和 Java API 的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 对象中。 之前没有设置会话状态的会话将对 GetState**** 返回空**** 引用。 可以使用 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 清除之前设置的会话状态。

会话状态保持，只要它未清除（返回**null），** 即使会话中的所有消息都已使用。

可以使用 Java API 中的**会话浏览器**方法以及 .NET 框架客户端中的[队列客户端](/dotnet/api/microsoft.servicebus.messaging.queueclient)和[订阅客户端](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient)上的[GetMessage 会话](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions)来枚举队列或订阅中的所有现有会话。

队列或订阅中保留的会话状态计入相应实体的存储配额。 因此，当应用程序完成会话时，建议应用程序清理保留的状态，以杜绝外部管理成本。

### <a name="impact-of-delivery-count"></a>传递计数的影响

在会话上下文中，每条消息的传递计数的定义与在没有会话的情况下的定义略有不同。 下面的表总结了传递计数何时递增。

| 场景 | 消息的传递计数是否递增 |
|----------|---------------------------------------------|
| 接受会话，但会话锁已过期（由于超时） | 是 |
| 会话被接受，会话中的消息未完成（即使它们已锁定），会话已关闭 | 否 |
| 接受会话，完成消息，然后显式关闭会话 | 不适用（这是标准流）。 此处的消息将从会话中删除） |

## <a name="request-response-pattern"></a>请求-响应模式
[请求-回复模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)是一种成熟的集成模式，它使发送方应用程序能够发送请求，并为接收方提供正确发送响应的方法。 此模式通常需要短时间队列或主题，应用程序才能向其发送响应。 在这种情况下，会话提供了具有类似语义的简单替代解决方案。 

多个应用程序可以将其请求发送到单个请求队列，并设置特定的标头参数以唯一标识发件人应用程序。 接收方应用程序可以处理队列中的请求，并在启用会话的队列上发送答复，将会话 ID 设置为发件人在请求消息上发送的唯一标识符。 然后，发送请求的应用程序可以接收特定会话 ID 上的消息并正确处理答复。

> [!NOTE]
> 发送初始请求的应用程序应了解会话 ID，并用于`SessionClient.AcceptMessageSession(SessionID)`锁定它期望响应的会话。 最好使用将应用程序实例唯一标识为会话 ID 的 GUID。不应在会话处理程序或`AcceptMessageSession(timeout)`队列中设置任何会话处理程序，以确保响应可供特定接收方锁定和处理。

## <a name="next-steps"></a>后续步骤

- 有关使用 .NET Framework 客户端处理会话感知消息的示例，请参见 [Microsoft.Azure.ServiceBus 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions)或 [Microsoft.ServiceBus.Messaging 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)。 

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
