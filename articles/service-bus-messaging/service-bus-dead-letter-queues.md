---
title: 服务总线死信队列 | Microsoft 文档
description: Azure 服务总线死信队列概述
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: a82d70e7bf776bf470d14e7f061774ccbb136316
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
ms.locfileid: "28925656"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>服务总线死信队列概述

Azure 服务总线队列和主题订阅提供一个名为“死信队列 (DLQ)”的辅助子队列。 死信队列不需要显式创建，并且不能删除或以其他方式独立于主实体进行管理。

本文介绍服务总线中的死信队列。 GitHub 上的[死信队列示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue)对很多讨论进行了说明。
 
## <a name="the-dead-letter-queue"></a>死信队列

死信队列的用途是存放无法传递给任何接收方的消息或存放无法处理的消息。 然后，可从 DLQ 中删除和检查这些消息。 应用程序可能会在操作员的帮助下，更正问题并重新提交消息，记录出错的实际情况和执行更正操作。 

从 API 和协议的角度看，DLQ 与任何其他队列都极其相似，不同的是，消息只能通过父实体的死信操作提交。 此外，无法查看生存时间，而且不能将 DLQ 中的消息设为死信。 死信队列完全支持扫视锁定传递和事务性操作。

请注意，DLQ 不自动执行清理。 消息将保留在 DLQ 中，直到显式从 DLQ 中检索它们以及对死信消息调用 [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 为止。

## <a name="moving-messages-to-the-dlq"></a>将消息移到 DLQ

服务总线中有几个活动会导致从消息引擎本身将消息推送到 DLQ。 应用程序也可以显式将消息移到 DLQ。 

如果消息是由代理移动的，在代理对消息调用其内部版本的 [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) 方法时，会将两个属性添加到消息：`DeadLetterReason` 和 `DeadLetterErrorDescription`。

应用程序可以为 `DeadLetterReason` 属性定义自己的代码，但系统设置以下值。

| 条件 | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| 始终 |HeaderSizeExceeded |已超过此流的大小配额。 |
| !TopicDescription。<br />EnableFilteringMessagesBeforePublishing 和 SubscriptionDescription。<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |消息过期并已设为死信。 |
| SubscriptionDescription.RequiresSession |会话ID 为 null。 |启用会话的实体不允许使用会话标识符为 null 的消息。 |
| !死信队列 |MaxTransferHopCountExceeded |Null |
| 应用程序显式设为死信 |由应用程序指定 |由应用程序指定 |

## <a name="exceeding-maxdeliverycount"></a>超过 MaxDeliveryCount

每个队列和订阅都具有 [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 和 [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount) 属性；默认值为 10。 只要消息在 ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)) 锁下传递，但已显式放弃或锁已过期，消息 [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 就会递增。 [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 超过 [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 时，该消息将移到 DLQ，并指定 `MaxDeliveryCountExceeded` 原因代码。

无法禁止此行为，但可将 [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) 设置为非常大的数。

## <a name="exceeding-timetolive"></a>超过 TimeToLive

[QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) 或 [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) 属性设置为 **true**（默认值是 **false**）时，所有到期的消息将移到 DLQ，并指定 `TTLExpiredException` 原因代码。

请注意，如果主队列或订阅中至少有一个活动的接收器正在拉取，则会清除过期的消息，该消息会被移动到 DLQ；该行为是设计使然。

## <a name="errors-while-processing-subscription-rules"></a>处理订阅规则时的错误

为订阅启用了 [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) 属性时，会在 DLQ 中捕获执行订阅的 SQL 筛选器规则时出现的任何错误以及有问题的消息。

## <a name="application-level-dead-lettering"></a>应用程序级死信

除了系统提供的死信功能外，应用程序也可以使用 DLQ 显式拒绝无法接受的消息。 这可能包括由于任何类型的系统问题而导致无法正确处理的消息、存储格式错误的有效负载的消息或在使用某种消息级安全方案时未通过身份验证的消息。

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>ForwardTo 或 SendVia 方案中的死信

在下列情况下，消息将发送到传输死信队列：

- 消息传过 3 个以上[链接在一起](service-bus-auto-forwarding.md)的队列或主题。
- 目标队列或主题被禁用或删除。
- 目标队列或主题超出最大实体大小。

若要检索这些死信消息，可以使用 [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath) 实用方法创建接收器。

## <a name="example"></a>示例

下面的代码片段将创建一个消息接收器。 在主队列的接收循环中，此代码使用 [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_) 检索消息，该方法请求代理立即返回随时可用的任何消息或返回空结果。 如果此代码接收到一条消息，则会立即将其放弃，从而使 `DeliveryCount` 递增。 系统将此消息移动到 DLQ 后，[ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) 返回 **null**，主队列为空，且循环退出。

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>后续步骤

有关服务总线队列的详细信息，请参阅以下文章：

* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Azure 队列和服务总线队列比较](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

