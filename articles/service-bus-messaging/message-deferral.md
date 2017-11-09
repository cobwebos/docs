---
title: "Azure 服务总线消息延迟 | Microsoft Docs"
description: "服务总线消息的延迟传送"
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
ms.date: 09/29/2017
ms.author: sethm
ms.openlocfilehash: f84b870de4b79399d5edc90284c9c56222156b5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="message-deferral"></a>消息延迟

当队列或订阅客户端收到一条它愿意处理，但由于应用程序中出现特殊状况而无法处理的消息时，该客户端可将该消息的检索“延迟”到将来的某个时间点。 该消息将保留在队列或订阅中，但会搁置处理。

延迟是专门针对工作流处理方案设计的功能。 工作流框架可能需要以特定的顺序处理某些操作，并且可能需要延迟某些所收到的消息的处理，直到已完成其他消息所通知的、以前预定的工作。

订单处理序列就是一个简单的演示示例：来自外部付款提供程序的付款通知先显示在系统中，然后将存储前端中的匹配采购订单传播到履行系统。 在这种情况下，履行系统可能会延迟付款通知的处理，直到出现可与该通知关联的订单。 在会合方案中，来自不同源的消息可推动工作流，实时执行顺序可能确实正确，但反映结果的消息可能不按顺序到达。

最终，延迟可将消息的到达顺序重新排列为处理顺序，同时将需要延迟处理的消息安全保留在消息存储中。

## <a name="message-deferral-apis"></a>消息延迟 API

.NET Framework 客户端中的 API 为 [BrokeredMessage.Defer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer) 或 [BrokeredMessage.DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync)，.NET 标准客户端中的 API 为 [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync)，Java 客户端中的 API 为 **mesageReceiver.defer** 或 **messageReceiver.deferSync**。 

延迟的消息连同其他所有活动消息保留在主队列中（与保留在子队列中的死信消息不同），但不再可以使用正则 Receive/ReceiveAsync 函数接收。 如果应用程序不再能跟踪延迟的消息，可以通过[消息浏览](message-browsing.md)来发现这些消息。

若要检索延迟的消息，其“所有者”在延迟它时需负责记住 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber)。 知道已延迟消息的 **SequenceNumber** 的任何接收器以后可使用 Receive(sequenceNumber) 显式接收该消息。

如果由于处理消息的特定资源暂时不可用，因而无法处理该消息，但同时又不能立即暂停消息处理，则将该消息搁置几分钟的适当方法是在要延后几分钟的[计划消息](message-sequencing.md)中记住 **SequenceNumber**，并在计划的消息到达时重新检索已延迟的消息。 请注意，如果消息处理程序依赖于使用某个数据库来执行所有操作，而该数据库暂时不可用，则不应使用延迟，而应全面暂停接收消息，直到数据库再次可用。

延迟消息不会影响消息过期，也就是说，延迟的消息仍会按照最初计划的时间过期，然后被移到死信队列（如果已采用此配置）。

## <a name="next-steps"></a>后续步骤

若要详细了解服务总线消息传送，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)