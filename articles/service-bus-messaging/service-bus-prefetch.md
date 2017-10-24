---
title: "Azure 服务总线预提取消息 | Microsoft Docs"
description: "通过预提取 Azure 服务总线消息提升性能。"
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
ms.date: 10/04/2017
ms.author: sethm
ms.openlocfilehash: 4a4a06f90c2c48d35d836f0be89fec9cc47f32c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="prefetch-azure-service-bus-messages"></a>预提取 Azure 服务总线消息

如果在任何正式的服务总线客户端中启用了预提取，接收方会静默地获取更多消息，直至达到 [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 上限，超出应用程序起初请求的量。

因此，单个初始 [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) 或 [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) 调用将获取及时返回的可供立即使用的消息。 然后，客户端在后台获取更多信息，填充预提取缓冲区。

## <a name="enable-prefetch"></a>启用预提取

在 .NET 中，通过将 MessageReceiver、QueueClient 或 SubscriptionClient 的 [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) 属性设置为大于零的数字，可启用预提取功能。 将该值设为零可关闭预提取。

可轻松向 [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) 或 [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) 示例的设置的接收方添加此设置来查看这些上下文中的效果。

尽管消息在预提取缓存区中提供，但后续的任何 Receive/ReceiveAsync 调用均可从缓冲区立即完成，并且随着空间变得可用，缓冲区也会在后台得到补充。 如果没有消息用于传送，接收操作将清空缓存区，并按预期进行等待或阻止。

预提取也同样适用于 [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) 和 [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync) API。

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>既然预提取更快，为何不是默认选项？

预提取可加快消息流程，方法是在应用程序请求消息时及请求消息前，准备好消息用于本地检索。 这种吞吐量提升有赖于应用程序作者明确作出某种权衡：

通过 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete) 接收模式，预提取缓存区获取的所有消息在队列中不再可用，仅驻留在内存中预提取缓存区，直到应用程序通过 Receive/ReceiveAsync 或 OnMessage/OnMessageAsync API 接收到它们。 如果在应用程序接收到消息前终止应用程序，这些消息将丢失，且不可恢复。

在 [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock) 接收模式下，提取到预提取缓存区的消息将以锁定状态进入缓存区，并且将超时时钟用于锁定计时。 如果预提取缓存区很大，且处理所需时间过长，以致消息锁定在驻留于预提取缓存区，甚至应用程序还在处理消息时就到期，可能出现一些令人困惑的事件要应用程序处理。

应用程序可能收到包含到期或即将到期的锁定的消息。 如果是这样，应用程序可能处理该消息，但随后发现，因锁定到期而无法完成处理。 应用程序可查看 [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) 属性（受代理时钟和本地计算机时钟之间的时钟偏差约束）。 如果消息锁定已到期，则应用程序必须忽略该消息，不应对该消息或通过该消息调用任何 API。 如果消息未到期但即将到期，可通过调用 [message.RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) 延续和扩展又一默认锁定时间段

如果锁定在预提取缓冲区静默地到期，则视为已放弃该消息，且可再次将消息用于从队列进行检索。 这可能导致将消息提取到预提取缓冲区，并置于末尾。 如果在消息过期期间往往无法使用预提取缓存区，这将导致重复预提取消息，但始终无法将其以可用（有效锁定）状态有效送达，并最终在超出最大传送数后移动到死信队列。

如果消息处理需要高度的可靠性，且处理需要大量精力和时间，则建议谨慎使用或者丝毫不用预提取功能。

如果需要较高吞吐量且消息处理通常比较便宜，则预提取会产生显著的吞吐量优势。

需要均衡对队列或订阅配置的最大预提取数和锁定持续时间，以便锁定超时至少超出最大预提取缓存区大小外加一条消息的累积预期消息处理时间。 同时，锁定超时不应过长，防止消息在被意外丢弃后超出其最大 [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive)，因此需要消息的锁定在重新传送消息前到期。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
