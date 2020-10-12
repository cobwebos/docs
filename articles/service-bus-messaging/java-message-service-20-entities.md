---
title: Azure 服务总线消息传递 - Java 消息服务实体（预览版）
description: 本文概述了可通过 Java 消息服务 API 访问的 Azure 服务总线消息传递实体。
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801455"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Java 消息服务 (JMS) 2.0 实体（预览版）

连接到 Azure 服务总线高级版并使用 [Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)的客户端应用程序可以利用以下实体。

## <a name="queues"></a>队列

JMS 中的队列在语义上与传统的[服务总线队列](service-bus-queues-topics-subscriptions.md#queues)类似。

若要创建队列，请使用 `JMSContext` 类中的以下方法：

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>主题

JMS 中的主题在语义上与传统的[服务总线主题](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)类似。

若要创建主题，请使用 `JMSContext` 类中的以下方法：

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>临时队列

当客户端应用程序需要在应用程序的生存期内存在的临时实体时，它可以使用临时队列。 这些队列以[请求-回复](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)模式使用。

若要创建临时队列，请使用 `JMSContext` 类中的以下方法：

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>临时主题

就像临时队列一样，可以使用临时主题通过在应用程序生存期内存在的临时实体来启用发布/订阅。

若要创建临时主题，请使用 `JMSContext` 类中的以下方法：

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Java 消息服务 (JMS) 订阅

尽管它们在语义上类似于[订阅](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)（即，存在于主题上并启用发布/订阅语义），但 Java 消息服务规范引入了给定订阅的**共享**、**非共享**、**持久**和**非持久**特性概念。

> [!NOTE]
> 以下订阅在 Azure 服务总线高级层预览版中可用，用于通过 [Azure 服务总线 JMS 库](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)连接到 Azure 服务总线的客户端应用程序。
>
> 对于公共预览版，无法使用 Azure 门户创建这些订阅。
>

### <a name="shared-durable-subscriptions"></a>共享持久订阅

当某个主题上发布的所有消息都要由某个应用程序接收并处理时，可使用共享持久订阅，无论该应用程序是否任何时候都主动使用该订阅中的消息。

由于这是一个共享订阅，因此，通过身份验证后可以从服务总线接收消息的任何应用程序都可以从订阅中接收消息。

若要创建共享持久订阅，请使用 `JMSContext` 类中的以下方法：

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

除非使用 `JMSContext` 类上的 `unsubscribe` 方法删除了共享持久订阅，否则该订阅将一直存在。

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>非共享持久订阅

与共享持久订阅一样，当某个主题上发布的所有消息都要由某个应用程序接收并处理时，可使用非共享持久订阅，无论该应用程序是否任何时候都主动使用该订阅中的消息。

不过，由于这是一个非共享的订阅，因此只有创建该订阅的应用程序才能从其中接收消息。

若要创建非共享持久订阅，请使用 `JMSContext` 类中的以下方法： 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 功能当前不受支持，将被忽略。
>

除非使用 `JMSContext` 类上的 `unsubscribe` 方法删除了非共享持久订阅，否则该订阅将一直存在。

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>共享非持久订阅

当多个客户端应用程序需要接收和处理来自单个订阅的消息时，会使用共享非持久订阅，直到它们主动使用/接收其中的消息为止。

由于订阅不是持久的，因此不会持久保存。 在没有主动使用者的情况下，此订阅不会接收消息。

若要创建共享非持久订阅，请创建一个 `JmsConsumer`，如 `JMSContext` 类中的以下方法中所示：

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

只要有主动使用者从中接收消息，共享非持久订阅就会一直存在。

### <a name="unshared-non-durable-subscriptions"></a>非共享非持久订阅

当客户端应用程序需要接收和处理来自订阅的消息时，会使用非共享非持久订阅，直到它主动使用其中的消息为止。 此订阅上只能有一个使用者，即创建了该订阅的客户端。

由于订阅不是持久的，因此不会持久保存。 在没有主动使用者的情况下，此订阅不会接收消息。

若要创建非共享非持久订阅，请创建一个 `JMSConsumer`，如 `JMSContext` 类中的以下方法中所示：

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal` 功能当前不受支持，将被忽略。
>

只要有主动使用者从中接收消息，非共享非持久订阅就会一直存在。

### <a name="message-selectors"></a>消息选择器

正如常规服务总线订阅存在**筛选器和操作**一样，JMS 订阅存在**消息选择器**。

消息选择器可在每个 JMS 订阅上设置，并作为消息标头属性的筛选条件存在。 只会传递其标头属性与消息选择器表达式匹配的消息。 如果值为 null 或空字符串，则表示不存在与 JMS 订阅/使用者对应的消息选择器。

## <a name="next-steps"></a>后续步骤

有关使用服务总线消息传送的详细信息和示例，请参阅以下高级主题：

* [服务总线消息传送概述](service-bus-messaging-overview.md)
* [将 Java 消息服务 2.0 API 与 Azure 服务总线高级版配合使用（预览版）](how-to-use-java-message-service-20.md)



