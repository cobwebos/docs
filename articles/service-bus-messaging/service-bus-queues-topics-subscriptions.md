---
title: Azure 服务总线消息传送队列、主题和订阅
description: 本文概述了 Azure 服务总线消息实体（队列、主题和订阅）。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: deeebf56d6e2f4ccfac37c70170a0d1cb4d272a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119167"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>服务总线队列、主题和订阅

Microsoft Azure 服务总线支持一组基于云的、面向消息的中间件技术，包括可靠的消息队列和持久的发布/订阅消息。 这些 "中转" 消息传送功能可被视为分离式消息传送功能，支持使用服务总线消息传送工作负载的发布-订阅、临时分离和负载均衡方案。 分离式通信具有很多优点；例如，客户端和服务器可以根据需要进行连接并以异步方式执行其操作。

构成服务总线消息传送功能核心的消息传送实体包括队列、主题/订阅、规则/操作。

## <a name="queues"></a>队列

队列为一个或多个竞争使用方提供*先入先出* (FIFO) 消息传递方式。 也就是说，接收方通常会按照消息添加到队列中的顺序来接收并处理消息，并且每条消息仅由一个消息使用方接收并处理。 使用队列的主要优点是，实现应用程序组件的“临时分离”。 换而言之，创建方（发送方）和使用方（接收方）无需同时发送和接收消息，因为消息持久存储在队列中。 此外，创建方不必等待使用方的答复即可继续处理并发送更多消息。

相关的优点是“负载分级”，它允许创建方和使用方以不同速率发送和接收消息。 在许多应用程序中，系统负载随时间而变化，而每个工作单元所需的处理时间通常为常量。 使用队列在消息创建方与使用方之间中继意味着，只需将消费应用程序预配为处理平均负载而非最大负载。 队列深度将随传入负载的变化而加大和减小。 此功能会直接根据为应用程序加载提供服务所需的基础结构的数目来节省成本。 随着负载增加，可添加更多的工作进程以从队列中读取。 每条消息仅由一个辅助进程处理。 另外，可通过基于拉取的该负载均衡，以最合理的方式使用辅助计算机，即使这些辅助计算机具有不同的处理能力（因为它们以其最大速率拉取消息）也是如此。 此模式通常称为“使用方竞争”模式。

使用队列在消息创建方与使用方之间中继可在各组件之间提供固有的松散耦合。 由于创建方和使用方互不相识，因此，可升级使用方，不会对创建方产生任何影响。

### <a name="create-queues"></a>创建队列

使用 [Azure 门户](service-bus-quickstart-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-quickstart-cli.md) 或[资源管理器模板](service-bus-resource-manager-namespace-queue.md)创建队列。 然后使用 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 对象发送和接收消息。

若要快速了解如何创建队列，然后向/从队列发送/接收消息，请参阅每个方法的[快速入门](service-bus-quickstart-portal.md)。 有关如何使用队列的更深入教程，请参阅[服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)。

有关工作示例，请参阅 GitHub 上的 [BasicSendReceiveUsingQueueClient 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)。

### <a name="receive-modes"></a>接收模式

可以指定服务总线接收消息所用的两种不同模式：ReceiveAndDelete  或 PeekLock  。 在 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式下，接收操作是单次执行的；也就是说，当服务总线收到来自使用者的请求时，它会将该消息标记为“正在使用”，并将其返回给使用者应用程序。 **ReceiveAndDelete** 模式是最简单的模式，最适合应用程序允许在出现故障时不处理消息的方案。 为了理解此方案，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线会将消息标记为“已使用”，因此当应用程序重新启动并重新开始使用消息时，它会漏掉在发生崩溃前使用的消息。

使用 [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式时，接收操作分成了两步，从而有可能支持无法容忍遗漏消息的应用程序。 当服务总线收到请求时，它会找到要使用的下一个消息，将其锁定以防其他使用方接收它，并将该消息返回给应用程序。 应用程序完成消息处理（或可靠地存储消息以供将来处理）后，它将通过对收到的消息调用 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 完成接收过程的第二个阶段。 服务总线发现 **CompleteAsync** 调用时会将消息标记为“正在使用”。

如果应用程序出于某种原因无法处理消息，则它可对收到的消息调用 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 方法（而不是 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 方法）。 此方法可使服务总线解锁消息并使其能够重新被同一个使用方或其他竞争使用方接收。 此外，还存在与锁定关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），则服务总线将解锁该消息并使其可再次被接收（实质上是默认执行一个 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 操作）。

如果应用程序在处理消息之后，但在发出 **CompleteAsync** 请求之前发生崩溃，则在应用程序重新启动时会将该消息重新传送给它。 此过程通常称作“至少处理一次”  ，即每条消息将至少被处理一次。 但是，在某些情况下，同一消息可能会被重新传送。 如果方案不允许重复处理，则应用程序中需具备其他逻辑用于检测重复，这可通过消息中的 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 属性实现（在整个传输尝试中，该属性保持不变）。 此功能称为“仅一次”  处理。

## <a name="topics-and-subscriptions"></a>主题和订阅

与每条消息都由单个使用方处理的队列相比，主题  和订阅  通过发布/订阅  模式提供“一对多”通信方式。 这对于扩展到大量接收方而言十分有用，每个发布的消息对向该主题注册的每个订阅均可用。 系统会将消息发送到主题并传递到一个或多个相关联的订阅，具体取决于每个订阅上可以设置的筛选规则。 此订阅可以使用其他筛选器来限制其想要接收的消息。 可以采用与发送至队列的相同方式将消息发送至主题，但不可直接从主题接收消息。 而是从订阅接收消息。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 从订阅接收消息的方式与从队列接收相同。

通过比较，队列的消息发送功能直接映射到主题，而其消息接收功能映射到订阅。 此外，此功能意味着订阅支持本部分中前面有关队列所述的相同模式：竞争使用者、临时分离、负荷量和负载均衡。

### <a name="create-topics-and-subscriptions"></a>创建主题和订阅

创建主题与创建队列类似，如前一部分中所述。 然后使用 [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) 类发送消息。 若要接收消息，可以创建主题的一个或多个订阅。 与队列类似，通过 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) 对象而不是 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 对象从订阅接收消息。 创建订阅客户端，将主题名称、订阅名称和接收模式（可选）作为参数传递。

有关完整的工作示例，请参阅 GitHub 上的 [BasicSendReceiveUsingTopicSubscriptionClient 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient)。

### <a name="rules-and-actions"></a>规则和操作

在许多情况下，必须以不同方式处理具有特定特征的消息。 若要启用此处理，可配置订阅以找到具有所需属性的消息，并对这些属性执行某些修改。 虽然服务总线订阅可以看到发送到主题的所有消息，但你仅可以将这些消息的一个子集复制到虚拟订阅队列。 可使用订阅筛选器完成此筛选。 此类修改称为筛选器操作  。 创建订阅后，可提供一个对消息属性进行操作的筛选器表达式，其中属性包括系统属性（例如 **Label**）和自定义应用程序属性（例如 **StoreName**）SQL 筛选器表达式在此示例中为可选；如果没有 SQL 筛选器表达式，会对该订阅的所有消息执行在订阅上定义的任何筛选器操作。

有关完整的工作示例，请参阅GitHub上的 [TopicSubscriptionWithRuleOperationsSample 示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)。

有关可能的筛选器值的详细信息，请参阅文档 [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) 和 [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) 类。

## <a name="java-message-service-jms-20-entities-preview"></a>Java 消息服务（JMS）2.0 实体（预览版）

连接到 Azure 服务总线高级版并利用[Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)的客户端应用程序可利用以下实体。

### <a name="queues"></a>队列

JMS 中的队列在语义上与前面讨论的传统服务总线队列类似。

若要创建队列，请在类中使用以下方法 `JMSContext` -

```java
Queue createQueue(String queueName)
```

### <a name="topics"></a>主题

JMS 中的主题在语义上与前面讨论的传统服务总线主题非常类似。

若要创建主题，请在类中使用以下方法 `JMSContext` -

```java
Topic createTopic(String topicName)
```

### <a name="temporary-queues"></a>临时队列

当客户端应用程序需要在应用程序的生存期内存在的临时实体时，可以使用临时队列。 它们在[请求-答复](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)模式中使用。

若要创建临时队列，请在类中使用以下方法 `JMSContext` -

```java
TemporaryQueue createTemporaryQueue()
```

### <a name="temporary-topics"></a>临时主题

就像临时队列一样，存在用于启用通过应用程序生存期存在的临时实体发布/订阅的临时主题。

若要创建一个临时主题，请在类中使用以下方法 `JMSContext` -

```java
TemporaryTopic createTemporaryTopic()
```

### <a name="java-message-service-jms-subscriptions"></a>Java 消息服务（JMS）订阅

虽然它们在语义上类似于上面所述的订阅（即存在于主题上并启用发布/订阅语义），但 Java 消息服务规范介绍了给定订阅的**共享**、非**共享**、**持久**和**非持久**属性的概念。

> [!NOTE]
> 以下订阅适用于 Azure 服务总线高级层，用于预览使用[Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)连接到 Azure 服务总线的客户端应用程序。
>
> 对于公共预览版，无法使用 Azure 门户创建这些订阅。
>

#### <a name="shared-durable-subscriptions"></a>共享持久订阅

当某个主题上发布的所有消息都要由应用程序接收并处理时，无论该应用程序是否在任何时候都主动使用该订阅，都将使用共享持久订阅。

由于这是共享的订阅，因此从服务总线接收身份验证的任何应用程序都可从订阅接收。

若要创建共享持久订阅，请在类中使用以下方法 `JMSContext` -

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

共享持久订阅继续存在，除非使用 `unsubscribe` 类上的方法删除 `JMSContext` 。

```java
void unsubscribe(String name)
```

#### <a name="unshared-durable-subscriptions"></a>非共享持久订阅

与共享持久订阅一样，当某个主题上发布的所有消息都要由应用程序接收和处理时，将使用非共享持久订阅，而不管应用程序是否在任何时候都正在积极地从订阅中使用。

不过，由于这是一个不共享的订阅，因此只有创建该订阅的应用程序才能接收该订阅。

若要创建非共享持久订阅，请从类中使用以下方法 `JMSContext` - 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> 此 `noLocal` 功能当前不受支持，将被忽略。
>

非共享持久订阅将继续存在，除非使用 `unsubscribe` 类上的方法删除 `JMSContext` 。

```java
void unsubscribe(String name)
```

#### <a name="shared-non-durable-subscriptions"></a>共享的非持久订阅

当多个客户端应用程序需要从单个订阅接收和处理消息时，将使用共享的非持久订阅，这一订阅仅在它们主动使用/接收消息时使用。

由于订阅不是持久的，因此不会持久保存。 如果此订阅没有活动的使用者，则不会收到消息。

若要创建共享的非持久订阅，请创建一个， `JmsConsumer` 如以下方法中的类所示 `JMSContext` -

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

共享的非持久订阅将继续存在，直到有可用的使用者接收它。

#### <a name="unshared-non-durable-subscriptions"></a>非持久订阅

当客户端应用程序需要从订阅接收和处理消息时，将使用非共享的非持久订阅，这只是因为它在主动地使用它。 此订阅上只能有一个使用者，即创建了该订阅的客户端。

由于订阅不是持久的，因此不会持久保存。 如果此订阅没有活动的使用者，则不会收到消息。

若要创建非持久的非持久订阅，请 `JMSConsumer` 从 "JMSContext 类- 

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> 此 `noLocal` 功能当前不受支持，将被忽略。
>

不共享的非持久订阅将继续存在，直到有一个活动使用者从该订阅接收。

#### <a name="message-selectors"></a>消息选择器

与常规服务总线订阅的**筛选器和操作**一样，**消息选择器**对于 JMS 订阅存在。

消息选择器可在每个 JMS 订阅上设置，并作为消息标头属性的筛选条件存在。 只传递其标头属性与消息选择器表达式匹配的消息。 如果值为 null 或空字符串，则指示不存在 JMS 订阅/使用者的消息选择器。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [快速入门：使用 Azure 门户和 .NET 发送和接收消息](service-bus-quickstart-portal.md)
* [教程：使用 Azure 门户和主题/订阅更新清单](service-bus-tutorial-topics-subscriptions-portal.md)


