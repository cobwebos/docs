---
title: Azure 服务总线消息传送队列、主题和订阅概述 | Microsoft Docs
description: 服务总线消息传送实例概述。
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/18/2018
ms.author: sethm
ms.openlocfilehash: 424004a2a39bd0d05bce515dc17685e60f7a0c9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231570"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>服务总线队列、主题和订阅

Microsoft Azure 服务总线支持一组基于云的、面向消息的中间件技术，包括可靠的消息队列和持久的发布/订阅消息。 这些“中转”消息传送功能可被视为分离式消息传送功能，支持使用服务总线消息传送工作负载的发布-订阅、临时分离和负载均衡方案。 分离式通信具有很多优点；例如，客户端和服务器可以根据需要进行连接并以异步方式执行其操作。

服务总线中构成消息传送功能的核心的消息传送实体是队列、主题和订阅，以及规则/操作。

## <a name="queues"></a>队列

队列为一个或多个竞争使用方提供*先入先出* (FIFO) 消息传递方式。 也就是说，接收方通常会按照消息添加到队列中的顺序来接收并处理消息，并且每条消息仅由一个消息使用方接收并处理。 使用队列的主要优点是，实现应用程序组件的“临时分离”。 换而言之，由于消息持久存储在队列中，因此创建方（发送方）和使用方（接收方）不必同时发送和接收消息。 此外，创建方不必等待使用方的答复即可继续处理并发送更多消息。

相关的优点是“负载分级”，它允许创建方和使用方以不同速率发送和接收消息。 在许多应用程序中，系统负载随时间而变化，而每个工作单元所需的处理时间通常为常量。 使用队列在消息创建者与使用者之间中继意味着，只需将使用方应用程序预配为适应平均负载而非最大负载。 队列深度将随传入负载的变化而加大和减小。 此功能会直接根据为应用程序加载提供服务所需的基础结构的数目来节省成本。 随着负载增加，可添加更多的工作进程以从队列中读取。 每条消息仅由一个辅助进程处理。 另外，可通过基于拉取的该负载均衡，以最合理的方式使用辅助计算机，即使这些辅助计算机具有不同的处理能力（因为它们以其最大速率拉取消息）也是如此。 此模式通常称为“使用者竞争”模式。

使用队列在消息创建方与使用方之间中继可在个组件之间提供固有的松散耦合。 由于创建方和使用方互不相识，因此，可升级使用方，不会对创建方产生任何影响。

### <a name="create-queues"></a>创建队列

使用 [Azure 门户](service-bus-quickstart-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-quickstart-cli.md) 或[资源管理器模板](service-bus-resource-manager-namespace-queue.md)创建队列。 然后使用 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 对象发送和接收消息。 

若要快速了解如何创建队列，然后向/从队列发送/接收消息，请参阅每个方法的[快速入门](service-bus-quickstart-portal.md)。 有关如何使用队列的更深入教程，请参阅[服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)。 

有关工作示例，请参阅 GitHub 上的 [BasicSendReceiveUsingQueueClient 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)。

### <a name="receive-modes"></a>接收模式

可以指定 Service Bus 接收消息所用的两种不同模式：*ReceiveAndDelete* 或 *PeekLock*。 使用 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式时，接收操作是一个单一快照。即，当服务总线收到请求时，会将该消息标记为“已使用”并将其返回给应用程序。 **ReceiveAndDelete** 模式是最简单的模式，最适合应用程序允许在出现故障时不处理消息的方案。 为了理解此方案，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“已使用”，因此当应用程序重新启动并重新开始使用消息时，它会漏掉在发生崩溃前使用的消息。

使用 [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式时，接收操作分成了两步，从而有可能支持无法容忍遗漏消息的应用程序。 当服务总线收到请求时，它会找到要使用的下一个消息，将其锁定以防其他使用方接收它，然后将该消息返回给应用程序。 应用程序完成消息处理（或可靠地存储消息以供将来处理）后，它将通过对收到的消息调用 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 完成接收过程的第二个阶段。 服务总线发现 **CompleteAsync** 调用时会将消息标记为“正在使用”。

如果应用程序出于某种原因无法处理消息，则它可对收到的消息调用 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 方法（而不是 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 方法）。 此方法可使服务总线解锁消息并使其能够重新被同一个使用方或其他竞争使用方接收。 此外，还存在与锁定关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），则服务总线将解锁该消息并使其可再次被接收（实质上是默认执行一个 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 操作）。

如果应用程序在处理消息之后，但在发出 **CompleteAsync** 请求之前发生崩溃，则在应用程序重新启动时会将该消息重新传送给它。 此过程通常称作“至少处理一次”，即每条消息将至少被处理一次。 但是，在某些情况下，同一消息可能会被重新传送。 如果方案不允许重复处理，则应用程序中需具备其他逻辑用于检测重复，这可通过消息中的 MessageId 属性实现（在整个传输尝试中，该属性保持不变）[](/dotnet/api/microsoft.azure.servicebus.message.messageid)。 此功能称为“仅一次”处理。

## <a name="topics-and-subscriptions"></a>主题和订阅

与每条消息都由单个使用方处理的队列相比，主题和订阅通过发布/订阅模式提供“一对多”通信方式。 这对于扩展到大量接收方而言十分有用，每个发布的消息对向该主题注册的每个订阅均可用。 取决于可在每个订阅时设置的筛选器规则，消息会发送到一个主题和传送到一个或多个关联订阅。 订阅可使用其他筛选器限制其要接收的消息。 消息发送到主题的方式和发送到队列的方式相同，但不会直接从主题被接收。 相反，会从订阅接收。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 从订阅接收消息的方式与从队列接收的方式相同。

通过比较，队列的消息发送功能直接映射到主题，其消息接收功能会映射到订阅。 此外，此功能意味着订阅支持本部分中前面有关队列所述的相同模式：竞争使用者、临时分离、负荷量和负载均衡。

### <a name="create-topics-and-subscriptions"></a>创建主题和订阅

创建主题与创建队列类似，如前一部分中所述。 然后使用 [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) 类发送消息。 若要接收消息，可以创建主题的一个或多个订阅。 与队列类似，通过 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) 对象而不是 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 对象从订阅接收消息。 创建订阅客户端，将主题名称、订阅名称和接收模式（可选）作为参数传递。 

有关完整的工作示例，请参阅 Github 上的 [BasicSendReceiveUsingTopicSubscriptionClient 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient)。

### <a name="rules-and-actions"></a>规则和操作

在许多情况下，必须以不同方式处理具有特定特征的消息。 若要启用此处理，可配置订阅以找到具有所需属性的消息，并对这些属性执行某些修改。 虽然服务总线订阅可看到发送到主题的所有消息，但你可仅将这些消息的一个子集复制到虚拟订阅队列。 可使用订阅筛选器完成此筛选。 此类修改称为筛选器操作。 创建订阅后，可提供一个对消息属性进行操作的筛选器表达式，其中属性包括系统属性（例如 **Label**）和自定义应用程序属性（例如 **StoreName**）这种情况下，SQL 筛选器表达式为可选项；如没有 SQL 筛选器表达式，订阅上定义的任何筛选器会对该订阅的所有消息执行。

有关完整的工作示例，请参阅GitHub上的 [TopicSubscriptionWithRuleOperationsSample 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)。

有关可能的筛选器值的详细信息，请参阅文档 [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) 和 [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) 类。 

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [快速入门：使用 Azure 门户和 .NET 发送和接收消息](service-bus-quickstart-portal.md)
* [教程：使用 Azure 门户和主题/订阅更新清单](service-bus-tutorial-topics-subscriptions-portal.md)


