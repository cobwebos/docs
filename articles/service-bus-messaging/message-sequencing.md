---
title: Azure 服务总线消息序列化和时间戳 | Microsoft Docs
description: 通过时间戳保持服务总线消息的序列和顺序
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
ms.openlocfilehash: a67a9d01f686c6aa8a569239667ae14d7bf087a9
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
ms.locfileid: "28200728"
---
# <a name="message-sequencing-and-timestamps"></a>消息序列化和时间戳

序列化和时间戳是所有服务总线实体上始终启用的两项功能，通过收到或检索到的消息的 [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) 和 [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) 属性体现。

如果消息的绝对顺序至关重要，和/或使用者需要消息的可信唯一标识符，中转站会向消息分发相对于队列或主题的无间隔递增序列号。 对于已分区实体，序列号是相对于分区进行分发。

SequenceNumber 值是在中转站接受并存储消息时分配给消息的唯一 64 位整数，可用作内部标识符。 对于已分区实体，最前面的 16 位数反映的是分区标识符。 在 48-64 位范围用尽后，序列号会回滚到 0。

序列号可作为唯一标识符受到信任，因为它是由中央主管中立机构（而不是客户端）分配。 它还表示真正的到达顺序，比作为顺序条件的时间戳更为精确，因为时间戳在消息传递速度极快时的解析度可能不够高，并且可能会在中转站所有权跨节点转让时受到时钟偏差的影响（不过影响很小）。

绝对到达顺序非常重要。例如，在供货量有限的业务方案中，在供应期内货物供应遵循“先到先得”的原则；音乐会门票销售就是一个例子。

时间戳功能起到中立可信机构的作用，可准确捕获消息的到达时间 (UTC)，通过 EnqueuedTimeUtc 属性体现。 此值非常适用于依赖截止时间、但处理进度远远落后于队列积压工作 (backlog) 的业务方案，如需要确定工作项是否在特定日期的午夜前提交。

## <a name="scheduled-messages"></a>计划的消息

可以将消息提交到队列或主题以供延迟处理；例如，将作业安排为在特定时间可供系统处理。 此功能实现了可靠的分布式时间计划程序。

在定义的排队时间前，计划的消息不会在队列中具体化。 在此之前，可以取消计划的消息。 取消操作会将消息删除。

可以在通过常规发送路径发送消息时设置 [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) 属性，也可以明确使用 [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) API 来安排消息。 后一种方法立即返回计划的消息的 SequenceNumber，稍后可用于根据需要取消计划的消息。 也可以使用[消息浏览](message-browsing.md)，发现计划的消息及其序列号。

只有当计划的消息处于此状态时，消息的 SequenceNumber 才有效。 当消息转换为有效状态时，消息就会被追加到队列中，就像瞬时排入队列一样，包括分配新的 SequenceNumber。

因为此功能与各个消息绑定在一起，并且消息只能排入队列一次，所以服务总线不支持定期安排消息。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线基础知识](service-bus-fundamentals-hybrid-solutions.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)