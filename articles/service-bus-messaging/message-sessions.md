---
title: Azure 服务总线消息会话 | Microsoft Docs
description: 本文说明了如何使用会话实现对无限的相关消息序列的连贯有序处理。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 05efc550e119186a2925c13d3fcfed11bec17251
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511290"
---
# <a name="message-sessions"></a>消息会话
使用 Microsoft Azure 服务总线会话，能够以连贯有序的方式处理一系列无限多的相关消息。 可以在“先进先出 (FIFO)”和“请求-响应”模式下使用会话。 本文展示了如何在使用服务总线时使用会话来实现这些模式。 

> [!NOTE]
> 服务总线的基本层不支持会话。 标准层和高级层支持会话。 有关这些层之间的差异，请参阅[服务总线定价](https://azure.microsoft.com/pricing/details/service-bus/)。

## <a name="first-in-first-out-fifo-pattern"></a>先进先出 (FIFO) 模式
若要在服务总线中实现 FIFO 保证，请使用会话。 服务总线没有规定消息之间的关系性质，也没有定义用于确定消息序列开始或结束位置的特定模型。

任何发送程序都可以在将消息提交到主题或队列时创建会话，方法是将 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) 属性设置为会话专属的由应用程序定义的某标识符。 在 AMQP 1.0 协议一级，此值映射到 group-id 属性。

在会话感知队列或订阅中，如果有至少一个消息包含会话的 [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)，会话就诞生了。 一旦会话诞生，就没有规定会话何时过期或消失的已定义时间或 API。 理论上讲，服务总线认为，今天可以针对会话接收的消息，与一年时间内 SessionId 相同的下一个消息使用的会话是相同的。

然而，通常情况下，应用程序都很清楚一组相关消息的开始和结束位置。 服务总线未设置任何具体规则。

例如，若要有意设置文件的传输序列，请将第一个、中间一个和最后一个消息的 Label 属性分别设置为 start、content 和 end。 content 消息相对位置的计算方式为，当前消息的 SequenceNumber 与 start 消息的 SequenceNumber 的增量值。

借助服务总线中的会话功能，可以 C# 和 Java API 编写的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 形式执行特定接收操作。 可以通过 Azure 资源管理器或在门户中设置标志，为队列或订阅设置 [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) 属性，从而启用此功能。 若要尝试执行相关 API 操作，必须启用此功能。

在门户中，选中下图中展示的复选框设置标志：

![“创建队列”对话框的屏幕截图，其中的“启用会话”选项处于选中状态并用红色标出。][2]

> [!NOTE]
> 在队列或订阅上启用会话时，客户端应用程序可以***不再***发送/接收常规消息。 所有消息必须作为会话的一部分发送（通过设置会话 ID），并通过接收会话来接收。

会话 API 存在于队列和订阅客户端上。 可以使用一个命令性模型，控制会话和消息的接收时间；还可以使用一个基于处理程序的模型（类似于 OnMessage），此模型简化了接收循环的管理操作。

### <a name="session-features"></a>会话功能

会话支持对交错消息流进行并发解多路复用，同时保留和保证有序传递。

![示意图，显示会话功能如何保持按序送达。][1]

[MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 接收程序是由接受会话的客户端创建。 客户端调用 C# 编写的 [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) 或 [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync)。 在反应回调模型中，它会注册会话处理程序。

当 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 对象被接受同时由客户端保留时，此客户端会对队列或订阅中的包含相应会话 [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) 的所有消息，以及在会话保留期间仍在到达且包含相应 SessionId 的所有消息一直施加排他锁。

调用 Close 或 CloseAsync 时，或当锁定期满导致应用程序无法执行关闭操作时，将会解除锁定 。 应将会话锁定视为对文件施加的排他锁。也就是说，应用程序应在不再需要它时和/或不需要再处理其他任何消息时关闭会话。

当多个并发接收程序从队列中拉取消息时，属于特定会话的消息会被分派到当前让相应会话一直处于锁定状态的特定接收程序。 通过此操作，位于一个队列或订阅中的交错消息流可以明确解多路复用到各个接收程序，这些接收程序也可以驻留在不同的客户端计算机上，因为锁定管理是在服务总线内的服务端执行。

上图显示三个并发会话接收程序。 某个 `SessionId` = 4 的会话不具有活动的、所属的客户端，这意味着此特定会话不传递任何消息。 会话在很多方面都起着一个子队列的作用。

会话接收程序保留的会话锁定是速览锁定安排模式使用的消息锁的保护伞。 只有一个接收程序可以在会话上使用锁。 一个接收程序可能有许多正在传送中的消息，但会按顺序接收消息。 如果放弃消息，则会在下一次执行接收操作时再次处理相同的消息。

### <a name="message-session-state"></a>消息会话状态

在高度可缩放、高度可用的云系统中处理工作流时，与特定会话相关的工作流处理程序不仅必须能够从意外故障中恢复，还必须能够在不同的进程或计算机中从工作开始位置恢复部分完成的工作。

借助会话状态工具，可以在中转站内为消息会话添加应用程序定义的注释。这样一来，当会话被新处理程序获取时，相对于此会话的已记录处理状态就会立即可用。

从服务总线的角度来看，消息会话状态是一个不透明的二进制对象，可以保留一个消息大小的数据（对于服务总线标准版，大小为 256KB；对于服务总线高级版，大小为 1MB）。 相对于会话的处理状态可以保留在会话状态中，会话状态也可以指向保留此类信息的某存储位置或数据库记录。

用于管理会话状态的 API [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 和 [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState) 存在于 C# 和 Java API 的 [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) 对象中。 之前没有设置会话状态的会话将对 GetState 返回空引用。 可以使用 [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) 清除之前设置的会话状态。

只要不清除会话状态，会话状态将保留（返回 null），即使会话中的所有消息都已使用，也是如此。

可以使用 Java API 中的 SessionBrowser 方法、[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) 上的 [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) 以及 .NET Framework 客户端中的 [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient)，枚举队列或订阅中的所有现有会话。

队列或订阅中保留的会话状态计入相应实体的存储配额。 因此，当应用程序完成会话时，建议应用程序清理保留的状态，以杜绝外部管理成本。

### <a name="impact-of-delivery-count"></a>传送计数的影响

在会话上下文中，每条消息的传递计数的定义与在没有会话的情况下的定义略有不同。 下表汇总了递增传送计数的时间。

| 方案 | 消息的传递计数是否递增 |
|----------|---------------------------------------------|
| 接受会话，但会话锁已过期（由于超时） | 是 |
| 接受会话，会话中的消息未完成（即使它们已锁定），并且会话已关闭 | 否 |
| 会话被接受，消息完成，然后显式关闭会话 | 不适用（它是标准流。 此处的消息将从会话中删除） |

## <a name="request-response-pattern"></a>“请求-响应”模式
[“请求-回复”模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)是一种成熟的集成模式，它使发送方应用程序能够发送请求，并为接收程序提供了一种正确地将响应发送回发送方应用程序的方法。 此模式通常需要一个生存期较短的队列或主题，以便应用程序向其发送响应。 在此方案中，会话提供了一个具有可比较语义的简单替代解决方案。 

多个应用程序可以将其请求发送到单个请求队列，并将特定标头参数设置为唯一标识发送程序应用程序。 接收程序应用程序可以处理传入队列的请求并在启用了会话的队列上发送答复，将会话 ID 设置为发送程序已在请求消息上发送的唯一标识符。 然后，发送请求的应用程序可以在特定会话 ID 上接收消息，并正确地处理答复。

> [!NOTE]
> 发送初始请求的应用程序应了解会话 ID，并使用 `SessionClient.AcceptMessageSession(SessionID)` 锁定需要响应的会话。 一个不错的做法是使用可独一无二地将应用程序实例标识为会话 ID 的 GUID。队列上不应存在会话处理程序或 `AcceptMessageSession(timeout)`，这是为了确保响应可由特定接收程序锁定和处理。

## <a name="next-steps"></a>后续步骤

- 有关使用 .NET Framework 客户端处理会话感知消息的示例，请参见 [Microsoft.Azure.ServiceBus 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions)或 [Microsoft.ServiceBus.Messaging 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions)。 

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
