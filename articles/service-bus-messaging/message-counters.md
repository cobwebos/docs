---
title: Azure 服务总线消息计数 | Microsoft Docs
description: 检索 Azure Service Bus 消息计数。
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 43689ec92fe20e61fdfd7a145d3028a3b1ee956d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848085"
---
# <a name="message-counters"></a>消息计数器

可以在 .NET Framework SDK 中使用 Azure 资源管理器和服务总线 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) API 来检索队列和订阅中含有的消息计数。

借助 PowerShell，可以按如下所示获取计数：

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>消息计数详细信息

对于确定队列是否累积了需要的处理资源比当前部署资源更多的积压工作，了解活动消息计数会十分有用。 [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) 类提供了以下计数器详细信息：

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount)：队列或订阅中处于活动状态且已准备好进行传递的消息。
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount)：死信队列中的信息。
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount)：计划状态中的消息。
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount)：未能传输到其他队列或主题并且已移动到传输死信队列中的消息。
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount)：等待传输到其他队列或主题的消息。

如果应用程序要基于队列长度缩放资源，则应按照慎重的进度执行此操作。 获取消息计数器是消息代理中成本高昂的操作，频繁执行它会直接对实体性能产生负面影响。

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)