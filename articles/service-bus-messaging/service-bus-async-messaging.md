---
title: 服务总线异步消息传送 | Microsoft 文档
description: 介绍 Azure 服务总线异步消息传送。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531111"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>异步消息传送模式和高可用性

可以通过多种不同的方式实现异步消息传送。 对于队列、主题和订阅，Azure 服务总线通过存储和转发机制支持异步。 在正常（同步）操作中，会将消息发送到队列和主题，并从队列和主题接收消息。 编写的应用程序依赖于这些始终可用的实体。 当实体运行状况因各种环境而发生变化时，需要一种能够提供满足大多数需求的缩减功能实体的方式。

应用程序通常使用异步消息传送模式来实现大量通信方案。 可以构建一些应用程序，以便客户端在其中可以向服务发送消息（即使该服务未运行）。 对于将经历大量通信的应用程序，队列可以通过提供缓冲通信的场所，帮助对负载进行分级。 最后，可以获得一个简单而高效的负载均衡器，从而在多台计算机间分发消息。

为了维护任何这些实体的可用性，请考虑表达这些实体可能不可用的多种方式，从而构建持久的消息传送系统。 一般而言，发现实体对应用程序不可用时，有以下表达方式：

* 无法发送消息。
* 无法接收消息。
* 无法管理实体（创建、检索、更新或删除实体）。
* 无法与服务取得联系。

对于以上每种故障，存在不同的故障模式，从而使应用程序能够在某种程度功能缩减的情况下继续执行工作。 例如，可以发送消息但无法接收消息的系统仍可以从客户接收指令，但无法处理这些指令。 本主题讨论可能发生的潜在问题以及如何缓解这些问题。 服务总线引入了必须选择加入的大量缓解措施，本主题还介绍了管理这些选择性加入缓解措施的规则。

## <a name="reliability-in-service-bus"></a>服务总线可靠性
可通过多种方式来处理消息和实体问题，有一套对这些缓解措施的恰当使用进行管理的准则。 要了解这些准则，必须先了解服务总线中可能出现的故障。 由于 Azure 系统的设计，所有这些故障往往都是短期的。 在高级别中，引起不可用的各种原因如下所示：

* 来自服务总线所依赖的外部系统的限制。 与存储和计算资源的交互存在限制。
* 服务总线所依赖的系统出现问题。 例如，存储的给定部分可能遇到问题。
* 单个子系统上出现服务总线故障。 在此情况下，计算节点可能会陷入不一致状态而必须重新启动其自身，从而导致它负责处理的所有实体负载均衡到其他节点。 这又可能导致短时间内消息处理变慢。
* Azure 数据中心内的服务总线故障。 这是“灾难性故障”，无论故障时间是数分钟还是几小时，在此期间都无法访问系统。

> [!NOTE]
> “存储”  这一术语既能表示 Azure 存储又能表示 SQL Azure。
> 
> 

服务总线包含了针对这些问题的许多缓解措施。 以下各节介绍了每个问题及其相应的缓解措施。

### <a name="throttling"></a>限制
通过服务总线，设置限制可以实现协作消息速率管理。 每个单独的服务总线节点包含许多实体。 其中每个实体都需要在 CPU、内存、存储和其他方面占用系统。 当上述任一方面检测到超出定义阈值的使用情况时，服务总线可以拒绝给定的请求。 调用方会接收到 [ServerBusyException][ServerBusyException]，并在 10 秒后重试。

作为一种缓解措施，该代码必须读取错误并停止该消息的任何重试至少 10 秒。 由于此错误可能发生在多个客户应用程序之间，所以最好使每个应用程序独立执行重试逻辑。 该代码可以通过对队列或主题启用分区来减少受限概率。

### <a name="issue-for-an-azure-dependency"></a>Azure 依赖项的问题
Azure 中的其他组件可能偶尔会发生服务问题。 例如，当服务总线使用的系统正在升级时，该系统可能会暂时出现功能缩减。 为了解决这些类型的问题，服务总线会定期进行调查并实施缓解措施。 这些缓解措施的副作用的确存在。 例如，为了处理存储的暂时性问题，服务总线将实现系统来允许消息发送操作持续工作。 由于缓解措施的性质，已发送的消息可能需要最多 15 分钟的时间才能出现在受影响的队列或订阅中和准备好进行接收操作。 一般而言，大多数实体不会遇到此问题。 但是，考虑到 Azure 服务总线中的实体数，有时需要为服务总线客户的一小部分实施此缓解措施。

### <a name="service-bus-failure-on-a-single-subsystem"></a>单个子系统上的服务总线故障
使用任何应用程序时，环境都可能导致服务总线的内部组件出现不一致。 当服务总线检测到这种不一致时，它将从该应用程序收集数据以辅助诊断问题。 收集到数据后，将重新启动该应用程序以尝试使其返回一致状态。 此过程发生得相当迅速，并且会导致实体长达数分钟不可用，而典型的停机时间则要短得多。

在这些情况下，客户端应用程序将生成 [System.TimeoutException][System.TimeoutException] 或 [MessagingException][MessagingException] 异常。 服务总线通过自动客户端重试逻辑来缓解该问题。 如果重试周期用尽而未能传递消息，可以尝试使用关于[处理中断和灾难问题][handling outages and disasters]的文章中描述的其他功能进行研究。

## <a name="next-steps"></a>后续步骤
了解服务总线中的异步消息传送的基础知识后，可阅读有关[处理中断和灾难问题][handling outages and disasters]的详细信息。

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
