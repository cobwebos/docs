---
title: Azure 服务总线-消息延迟
description: 本文介绍如何延迟传输 Azure 服务总线消息。 该消息将保留在队列或订阅中，但会搁置处理。
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
ms.openlocfilehash: 5e32c461902c1e340c6cece22669a59847e660cd
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538390"
---
# <a name="message-deferral"></a>消息延迟

当队列或订阅客户端收到一条它愿意处理，但由于应用程序中出现特殊状况而无法处理的消息时，该客户端可将该消息的检索“延迟”到将来的某个时间点。 该消息将保留在队列或订阅中，但会搁置处理。

延迟是专门针对工作流处理方案设计的功能。 工作流框架可能需要以特定的顺序处理某些操作，并且可能需要延迟某些所收到的消息的处理，直到已完成其他消息所通知的、以前预定的工作。

订单处理序列就是一个简单的演示示例：来自外部付款提供程序的付款通知先显示在系统中，然后将存储前端中的匹配采购订单传播到履行系统。 在这种情况下，履行系统可能会延迟付款通知的处理，直到出现可与该通知关联的订单。 在会合方案中，来自不同源的消息可推动工作流，实时执行顺序可能确实正确，但反映结果的消息可能不按顺序到达。

最终，延迟有助于将消息的到达顺序重新排列为处理顺序，同时将需要延迟处理的消息安全保留在消息存储中。

## <a name="message-deferral-apis"></a>消息延迟 API

API 在 .NET Standard 客户端中的 .NET Framework 客户端、 [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync)或 DeferAsync 中是[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.defer?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_Defer)或[DeferAsync](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deferasync?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeferAsync) ，在 Java 客户端中为[IMessageReceiver](/java/api/com.microsoft.azure.servicebus.imessagereceiver.defer?view=azure-java-stable)或[IMessageReceiver。](/java/api/com.microsoft.azure.servicebus.imessagereceiver.deferasync?view=azure-java-stable) 

延迟的消息连同其他所有活动消息保留在主队列中（与保留在子队列中的死信消息不同），但不再可以使用正则 Receive/ReceiveAsync 函数接收。 如果应用程序不再能跟踪延迟的消息，可以通过[消息浏览](message-browsing.md)来发现这些消息。

若要检索延迟的消息，其所有者在延迟它时需负责记住 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber)。 知道已延迟消息的序列号的任何接收器以后可使用 `Receive(sequenceNumber)` 显式接收该消息。

如果由于处理消息的特定资源暂时不可用，因而无法处理该消息，但同时又不能立即暂停消息处理，则将该消息搁置几分钟的方法是在要延后几分钟的[计划消息](message-sequencing.md)中记住 SequenceNumber，并在计划的消息到达时重新检索已延迟的消息。 如果消息处理程序依赖于使用某个数据库来执行所有操作，而该数据库暂时不可用，则不应使用延迟，而应全面暂停接收消息，直到数据库再次可用。


## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
