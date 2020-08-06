---
title: 'Azure 服务总线消息传递-Java 消息服务实体 (预览) '
description: 本文概述了可通过 Java 消息服务 API 访问的 Azure 服务总线消息传送实体。
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801455"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Java 消息服务 (JMS) 2.0 实体 (预览) 

连接到 Azure 服务总线高级版并利用[Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)的客户端应用程序可利用以下实体。

## <a name="queues"></a>队列

JMS 中的队列在语义上相当于传统的[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)。

若要创建队列，请在类中使用以下方法 `JMSContext` -

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>主题

JMS 中的主题在语义上类似于传统的[服务总线主题](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)。

若要创建主题，请在类中使用以下方法 `JMSContext` -

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>临时队列

当客户端应用程序需要在应用程序的生存期内存在的临时实体时，可以使用临时队列。 它们在[请求-答复](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)模式中使用。

若要创建临时队列，请在类中使用以下方法 `JMSContext` -

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>临时主题

就像临时队列一样，存在用于启用通过应用程序生存期存在的临时实体发布/订阅的临时主题。

若要创建一个临时主题，请在类中使用以下方法 `JMSContext` -

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java 消息服务 (JMS) 订阅

尽管这些订阅在语义上类似于[订阅](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (即存在于主题上并启用发布/订阅语义) ，Java 消息服务规范介绍了给定订阅的**共享**、非**共享**、**持久**和**非持久**属性的概念。

> [!NOTE]
> 以下订阅适用于 Azure 服务总线高级层，用于预览使用[Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)连接到 Azure 服务总线的客户端应用程序。
>
> 对于公共预览版，无法使用 Azure 门户创建这些订阅。
>

### <a name="shared-durable-subscriptions"></a>共享持久订阅

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

### <a name="unshared-durable-subscriptions"></a>非共享持久订阅

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

### <a name="shared-non-durable-subscriptions"></a>共享的非持久订阅

当多个客户端应用程序需要从单个订阅接收和处理消息时，将使用共享的非持久订阅，这一订阅仅在它们主动使用/接收消息时使用。

由于订阅不是持久的，因此不会持久保存。 如果此订阅没有活动的使用者，则不会收到消息。

若要创建共享的非持久订阅，请创建一个， `JmsConsumer` 如以下方法中的类所示 `JMSContext` -

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

共享的非持久订阅将继续存在，直到有可用的使用者接收它。

### <a name="unshared-non-durable-subscriptions"></a>非持久订阅

当客户端应用程序需要从订阅接收和处理消息时，将使用非共享的非持久订阅，这只是因为它在主动地使用它。 此订阅上只能有一个使用者，即创建了该订阅的客户端。

由于订阅不是持久的，因此不会持久保存。 如果此订阅没有活动的使用者，则不会收到消息。

若要创建非持久的非持久订阅，请创建一个， `JMSConsumer` 如以下方法中的 `JMSContext` 类所示-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> 此 `noLocal` 功能当前不受支持，将被忽略。
>

不共享的非持久订阅将继续存在，直到有一个活动使用者从该订阅接收。

### <a name="message-selectors"></a>消息选择器

与常规服务总线订阅的**筛选器和操作**一样，**消息选择器**对于 JMS 订阅存在。

消息选择器可在每个 JMS 订阅上设置，并作为消息标头属性的筛选条件存在。 只传递其标头属性与消息选择器表达式匹配的消息。 如果值为 null 或空字符串，则指示不存在 JMS 订阅/使用者的消息选择器。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [在 Azure 服务总线高级 (预览版中使用 Java 消息服务 2.0 API) ](how-to-use-java-message-service-20.md)



