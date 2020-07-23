---
title: 将 AMQP 与 Java 消息服务 API 及 Azure 服务总线配合使用
description: 了解如何将 Java 消息服务 (JMS) 用于 Azure 服务总线和高级消息队列协议 (AMQP) 1.0。
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119151"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>将 Java 消息服务 (JMS) 用于 Azure 服务总线和 AMQP 1.0

支持 Azure 服务总线中的**高级消息队列协议（AMQP） 1.0**协议，这意味着可以使用有效的二进制协议，从一系列平台使用队列和发布/订阅中转消息传送功能。 此外，还可以生成由结合使用多个语言、框架和操作系统构建的组件组成的应用程序。

本文介绍如何通过 AMQP 协议使用常用**Java 消息服务（JMS）** API，从 java 应用程序使用 Azure 服务总线消息传送功能（队列和发布/订阅主题）。

## <a name="get-started-with-service-bus"></a>服务总线入门
此指南假定已有包含名为 `basicqueue` 的队列的服务总线命名空间。 如果没有，则可以使用 [Azure 经典门户](https://portal.azure.com)[创建命名空间和队列](service-bus-create-namespace-portal.md)。 有关如何创建服务总线命名空间和队列的详细信息，请参阅[服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)。

> [!NOTE]
> 分区队列和主题也支持 AMQP。 有关详细信息，请参阅[分区消息实体](service-bus-partitioning.md)和[针对服务总线分区队列和主题的 AMQP 1.0 支持](service-bus-partitioned-queues-and-topics-amqp-overview.md)。
> 
>

## <a name="what-jms-features-are-supported"></a>支持哪些 JMS 功能？

下面是 Azure 服务总线支持的 JMS 功能。

| 功能 | Azure Service Bus 标准层-JMS 1。1 | Azure 服务总线高级层-JMS 2.0 （预览版） |
|---|---|---|
| 通过 AMQP 自动创建实体 | 不支持 | **支持** |
| 队列 | **支持** | **支持** |
| 主题 | **支持** | **支持** |
| 临时队列 | 不支持 <br/> （改为创建具有*AutoDeleteOnIdle*设置的常规队列） | **支持** |
| 临时主题 | 不支持 | **支持** |
| 消息选择器 | 不支持 | **支持** |
| 队列浏览器 | 不支持 <br/> （使用服务总线 API 的*速览*功能） | **支持** |
| 共享持久订阅 | **支持** | **支持**|
| 非共享持久订阅 | 不支持 | **支持** |
| 共享的非持久订阅 | 不支持 | **支持** |
| 非持久订阅 | 不支持 | **支持** |
| 订阅持久订阅 | 不支持 | **支持** |
| ReceiveNoWait | 不支持 | **支持** |
| 分布式事务 | 不支持 | 不支持 |
| 持久终点 | 不支持 | 不支持 |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>有关服务总线标准级别的其他注意事项
每个会话只允许一个 MessageProducer 或 MessageConsumer。   如果需要在应用程序中创建多个 MessageProducers 或 MessageConsumers，请分别对其创建专用会话。  

## <a name="downloading-the-java-message-service-jms-client-library"></a>下载 Java 消息服务（JMS）客户端库

若要通过 AMQP 连接到 Azure 服务总线并利用 Java 消息服务（JMS） API，需要利用以下库。 必须使用项目的首选依赖项管理工具将这些项添加到生成路径。

所需的客户端库取决于所使用的定价层。

### <a name="premium-tier---jms-20-over-amqp-preview"></a>高级层-JMS 2.0 over AMQP （预览版）

若要利用 Azure 服务总线高级层上提供的所有预览功能，请使用[azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

### <a name="standard-tier---jms-11-over-amqp"></a>标准层-JMS 1.1 over AMQP

若要利用服务总线标准层支持的 JMS 功能（请参阅[支持哪些 jms 功能？](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)），请使用以下库：

* [Geronimo-jms JMS 1.1 规范](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Qpid JMS 客户端](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMS JAR 名称和版本可能已更改。 有关详细信息，请参阅 [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)。
>

## <a name="coding-java-applications"></a>为 Java 应用程序编码

导入依赖项后，可以使用 JMS 提供程序不可知的方式编写 Java 应用程序。

由于 Azure 服务总线标准版和高级版在依赖关系和它们支持的 JMS 功能的数目上不同，这两种编程模型的编程模型略有不同。

> [!IMPORTANT]
> 下面的指南展示了如何在给定简单应用程序的情况下连接到 Azure 服务总线。
>
> 假设大多数企业应用程序体系结构都可以通过自定义方式来管理依赖项和配置，请使用下面的作为指南来了解所需的内容并适当地适应应用程序。
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>使用 JMS 连接到 Azure 服务总线

若要使用 JMS 客户端连接到 Azure 服务总线，需要**ConnectionString** ，该 ConnectionString 在 "**主连接字符串**" 下的 " [Azure 门户](https://portal.azure.com)中的" 共享访问策略 "中提供。


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>通过 JMS 2.0 （预览版）连接到 Azure 服务总线高级版

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
    或 `JMSContext` （适用于 JMS 2.0 客户端）

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>通过 JMS 1.1 连接到 Azure 服务总线标准

1. 将 Azure 服务总线配置插入到 JNDI properties 文件**中。**
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. 设置 JNDI 上下文并配置 ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. 使用 `ConnectionFactory` 创建一个 `Connection` ，然后创建一个 `Session` 。
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>编写 JMS 应用程序

一旦 `Session` 或 `JMSContext` 已实例化，你的应用程序就可以利用熟悉的 JMS api 来执行管理和数据操作。

请参阅标准层和高级层[支持的 JMS 功能](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)列表，查看每个层将支持哪些 api。

## <a name="summary"></a>总结
本指南展示了使用 Java 消息服务（JMS）通过 AMQP 1.0 的 Java 客户端应用程序如何与 Azure 服务总线交互。

也可以通过其他语言（包括 .NET、C、Python 和 PHP）使用 Service Bus AMQP 1.0。 使用这些不同语言构建的组件可以使用服务总线中的 AMQP 1.0 支持可靠且完全无损地交换消息。

## <a name="next-steps"></a>后续步骤

有关 Azure 服务总线的详细信息以及有关 Java 消息服务（JMS）实体的详细信息，请查看以下链接- 
* [Service Bus-队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java 消息服务实体](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Azure 服务总线中的 AMQP 1.0 支持](service-bus-amqp-overview.md)
* [服务总线 AMQP 1.0 开发人员指南](service-bus-amqp-dotnet.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)

