---
title: 通过 Azure 服务总线高级版使用 Java 消息服务 2.0 API
description: '如何在 Azure 服务总线中使用 Java 消息服务 (JMS) '
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801372"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>在 Azure 服务总线高级 (预览版中使用 Java 消息服务 2.0 API) 

本文介绍如何使用常见的**Java 消息服务 (JMS) 2.0** API 通过高级消息队列协议 (AMQP 1.0) 协议与 Azure 服务总线交互。

> [!NOTE]
> 对 Java 消息服务 (JMS) 2.0 API 的支持仅适用于**Azure 服务总线高级层**，目前为**预览版**。
>

## <a name="get-started-with-service-bus"></a>服务总线入门

本指南假定你已有一个 Service Bus 命名空间。 如果没有，则可以使用 [Azure 经典门户](https://portal.azure.com)[创建命名空间和队列](service-bus-create-namespace-portal.md)。 

有关如何创建服务总线命名空间和队列的详细信息，请参阅[通过 Azure 门户开始使用服务总线队列](service-bus-quickstart-portal.md)。

## <a name="what-jms-features-are-supported"></a>支持哪些 JMS 功能？

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a> (JMS) 客户端库下载 Java 消息服务

若要利用 Azure 服务总线高级层上提供的所有功能，必须将以下库添加到项目的生成路径。

[Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> 若要将[Azure Maven-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)添加到生成路径，请使用项目的首选依赖项管理工具，如 " [Maven](https://maven.apache.org/) " 或 " [Gradle](https://gradle.org/)"。
>

## <a name="coding-java-applications"></a>为 Java 应用程序编码

导入依赖项后，可以使用 JMS 提供程序不可知的方式编写 Java 应用程序。

### <a name="connecting-to-azure-service-bus-using-jms"></a>使用 JMS 连接到 Azure 服务总线

若要使用 JMS 客户端连接到 Azure 服务总线，需要在**主连接字符串**下的[Azure 门户](https://portal.azure.com)中的 "共享访问策略" 中提供**连接字符串**。

1. 实例化`ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. `ServiceBusJmsConnectionFactory`用适当的实例化 `ServiceBusConnectionString` 。

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. 使用 `ConnectionFactory` 创建一个 `Connection` ，然后`Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    或 `JMSContext` JMS 2.0 客户端的 () 

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### <a name="write-the-jms-application"></a>编写 JMS 应用程序

一旦 `Session` 或 `JMSContext` 已实例化，你的应用程序就可以使用熟悉的 JMS api 来执行管理和数据操作。

请参阅[支持的 JMS 功能](how-to-use-java-message-service-20.md#what-jms-features-are-supported)的列表，查看作为此预览版的一部分受支持的 api。

下面是一些用于实现 JMS 入门的示例代码片段-

#### <a name="sending-messages-to-a-queue-and-topic"></a>将消息发送到队列和主题

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>从队列接收消息

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>从主题的共享持久订阅接收消息

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>总结

本指南展示了使用 Java 消息服务 (JMS) over AMQP 1.0 的 Java 客户端应用程序如何与 Azure 服务总线交互。

也可以通过其他语言（包括 .NET、C、Python 和 PHP）使用 Service Bus AMQP 1.0。 使用这些不同语言构建的组件可以使用服务总线中的 AMQP 1.0 支持可靠且完全无损地交换消息。

## <a name="next-steps"></a>后续步骤

有关 Azure 服务总线的详细信息以及 Java 消息服务 (JMS) 实体的详细信息，请查看以下链接- 
* [服务总线-队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java 消息服务实体](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [服务总线 AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [Java 消息服务 API (外部 Oracle doc) ](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [了解如何从 ActiveMQ 迁移到服务总线](migrate-jms-activemq-to-servicebus.md)
