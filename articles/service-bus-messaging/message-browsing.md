---
title: Azure 服务总线消息浏览 | Microsoft Docs
description: 浏览和速览服务总线消息
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
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 124c4592a41bf9f3e2a148ba5c3b928bb051d160
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
ms.locfileid: "28196614"
---
# <a name="message-browsing"></a>消息浏览

通过消息浏览（“速览”），服务总线客户端可以枚举队列或订阅中驻留的所有消息，通常用于诊断和调试目的。

速览操作返回队列或订阅消息日志中驻留的所有消息，而不仅仅是可通过 `Receive()` 或 `OnMessage()` 循环立即获取的消息。 每个消息的 `State` 属性指明消息是有效（可供接收）、[延迟](message-deferral.md)还是[已计划](message-sequencing.md)。

已使用和过期的消息通过异步运行“垃圾回收”操作进行清理，但这不一定与消息过期完全同步。因此，`Peek` 可能确实会返回已过期的消息，如果接下来对队列或订阅调用接收操作，这些消息将会遭到删除或成为死信。

如果试图从队列中恢复延迟的消息，请务必注意这一点。 一旦超过 [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc)，便无法再通过其他任何方式定期检索消息，即使是 Peek 返回的消息，也不例外。 返回这些消息是有意而为之，因为 Peek 是反映日志当前状态的诊断工具。

Peek 还会返回锁定的消息，以及当前由其他接收程序处理但尚未完成的消息。 不过，由于 Peek 返回的快照已断开连接，因此无法通过速览消息观测到消息的锁定状态，如果应用程序尝试读取 [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) 和 [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) 属性时，它们会抛出 [InvalidOperationException](/dotnet/api/system.invalidoperationexception)。

## <a name="peek-apis"></a>Peek API

[Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) 和 [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) 方法存在于所有 .NET 和 Java 客户端库以及所有接收程序对象中：MessageReceiver、MessageSession、QueueClient 和 SubscriptionClient。 Peek 适用于所有队列、订阅及其对应的死信队列。

如果重复调用，Peek 方法会按序号从低到高的顺序，枚举队列或订阅日志中的所有消息。 这是消息的排队顺序，并不是消息的最终检索顺序。

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) 可以检索多个消息，并以枚举形式返回它们。 如果没有消息可返回，枚举对象为空，而不是 NULL。

也可以使用表示开始位置的 [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) 播种方法重载，再调用无参数方法重载来进一步枚举。 PeekBatch 的运作方式基本相同，不同之处在于它是一次性检索一组消息。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)