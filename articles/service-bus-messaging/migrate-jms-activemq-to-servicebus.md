---
title: 将 (JMS) 应用程序从 Apache ActiveMQ 迁移到 Azure 服务总线 |Microsoft Docs
description: 本文介绍如何迁移与 Apache ActiveMQ 交互的现有 JMS 应用程序，以便与 Azure 服务总线交互。
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
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: 1b07faa5b2540aafafc27a51192d824d4445ce35
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067148"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>将现有 Java 消息服务 (JMS) 2.0 应用程序从 Apache ActiveMQ 迁移到 Azure 服务总线

本文介绍了如何修改现有 Java 消息服务 (与 JMS 代理交互的 JMS) 2.0 应用程序，以便与 Azure 服务总线交互。 具体而言，本文介绍从 Apache ActiveMQ 或 Amazon MQ 进行迁移。

Azure 服务总线支持 Java 2 平台、企业版和弹簧工作负载，这些工作负荷使用 JMS 2.0 API over 高级消息队列协议 (AMQP) 。

## <a name="before-you-start"></a>开始之前

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure 服务总线和 Apache ActiveMQ 之间的差异

Azure 服务总线和 Apache ActiveMQ 都是消息代理，充当用于客户端应用程序的 JMS 提供程序，用于向发送消息以及从中接收消息。 它们都使用队列启用点到点语义，并使用主题和订阅实现发布-订阅语义。 

尽管如此，这两者之间也存在一些差异，如下表所示：

| 类别 | ActiveMQ | Azure 服务总线 |
| --- | --- | --- |
| 应用程序分层 | 群集单体架构 | 双层 <br>  (网关 + 后端)  |
| 协议支持 | <ul> <li>AMQP</li> <li> 阻碍 </li> <li> OpenWire </li> </ul> | AMQP |
| 预配模式 | <ul> <li> 基础结构即服务 (IaaS) ，本地 </li> <li> Amazon MQ (托管平台即服务)  </li> | 托管平台即服务 (PaaS)  |
| 消息大小 | 客户可配置 | 1 MB (高级层)  |
| 高可用性 | 由客户管理 | 平台托管 |
| 灾难恢复 | 由客户管理 | 平台托管 | 

### <a name="current-supported-and-unsupported-features"></a>当前支持和不支持的功能

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>注意事项

Azure 服务总线的两层性质提供各种业务连续性功能 (高可用性和灾难恢复) 。 但是，在使用 JMS 功能时，有一些注意事项。

#### <a name="service-upgrades"></a>服务升级

在服务总线升级和重启时，将删除临时队列或主题。 如果你的应用程序对临时队列或主题的数据丢失敏感，请不要使用临时队列或主题。 请改用持久性队列、主题和订阅。

#### <a name="data-migration"></a>数据迁移

在将客户端应用程序迁移和修改为与 Azure 服务总线交互的过程中，ActiveMQ 中保存的数据不会迁移到服务总线。 你可能需要自定义应用程序来排出 ActiveMQ 队列、主题和订阅，然后将消息重播到服务总线的队列、主题和订阅。

#### <a name="authentication-and-authorization"></a>身份验证和授权

基于角色的访问控制 (RBAC) （由 Azure Active Directory 提供支持）是服务总线的首选身份验证机制。 由于 Apache QPID JMS 目前不支持 RBAC 或基于声明的身份验证，但是，你应该使用 SAS 密钥进行身份验证。

## <a name="pre-migration"></a>迁移前

### <a name="version-check"></a>版本检查

编写 JMS 应用程序时，可以使用以下组件和版本： 

| 组件 | 版本 |
|---|---|
|  (JMS) API 的 Java 消息服务 | 1.1 或更高版本 |
| AMQP 协议 | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>确保 AMQP 端口已打开

Service Bus 支持通过 AMQP 协议进行通信。 为此，请通过端口 5671 (AMQP) 和 443 (TCP) 启用通信。 根据客户端应用程序的托管位置，你可能需要一个支持票证来允许通过这些端口进行通信。

> [!IMPORTANT]
> 服务总线仅支持 AMQP 1.0 协议。

### <a name="set-up-enterprise-configurations"></a>设置企业配置

服务总线可实现各种企业安全性和高可用性功能。 有关详情，请参阅： 

  * [虚拟网络服务终结点](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [通过客户托管密钥进行服务端加密 (BYOK) ](configure-customer-managed-key.md)
  * [专用终结点](private-link-service.md)
  * [身份验证和授权](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>监视、警报和跟踪

对于每个服务总线命名空间，请将度量值发布到 Azure Monitor。 可以使用这些度量值来对分配给命名空间的资源进行警报和动态缩放。

若要详细了解不同的指标以及如何在这些指标上设置警报，请参阅[中的服务总线指标 Azure Monitor](service-bus-metrics-azure-monitor.md)。 你还可以找到有关管理操作[的数据操作](service-bus-end-to-end-tracing.md)和[操作/诊断日志记录](service-bus-diagnostic-logs.md)的客户端跟踪的详细信息。

### <a name="metrics---new-relic"></a>指标-新 Relic

可以将 ActiveMQ 映射中的哪些指标关联到 Azure 服务总线中的指标。 请参阅以下从新的 Relic 网站：

  * [ActiveMQ/Amazon MQ New Relic 指标](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure 服务总线新 Relic 指标](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 目前，新的 Relic 与 ActiveMQ 无缝集成，但它们具有可用于 Amazon MQ 的指标。 由于 Amazon MQ 派生自 ActiveMQ，下表将新的 Relic 指标从 Amazon MQ 映射到 Azure 服务总线。
>

|度量值分组| Amazon MQ/ActiveMQ 指标 | Azure 服务总线指标 |
|------------|---------------------------|--------------------------|
|代理|`CpuUtilization`|`CPUXNS`|
|代理|`MemoryUsage`|`WSXNS`|
|代理|`CurrentConnectionsCount`|`activeConnections`|
|代理|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|代理|`InactiveDurableTopicSubscribersCount`|使用订阅指标|
|代理|`TotalMessageCount`|使用队列/主题/订阅级别`activeMessages`|
|队列/主题|`EnqueueCount`|`incomingMessages`|
|队列/主题|`DequeueCount`|`outgoingMessages`|
|队列|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>迁移

若要迁移现有的 JMS 2.0 应用程序以与 Service Bus 交互，请按照以下几节中的步骤进行操作。

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>从 ActiveMQ 导出拓扑，并在服务总线中创建实体 (可选) 

若要确保客户端应用程序可以无缝连接到服务总线，请将拓扑 (包括从 Apache ActiveMQ 到 Service Bus) 的队列、主题和订阅。

> [!NOTE]
> 对于 JMS 应用程序，可以创建队列、主题和订阅作为运行时操作。 大多数 JMS 提供程序 (消息代理) 使你能够在运行时创建它们。 这就是为什么将此导出步骤视为可选的。 若要确保应用程序具有在运行时创建拓扑的权限，请使用具有 SAS 权限的连接字符串 `Manage` 。

若要实现此目的，请执行以下操作：

1. 使用[ActiveMQ 命令行工具](https://activemq.apache.org/activemq-command-line-tools-reference)导出拓扑。
1. 使用[Azure 资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)重新创建相同的拓扑。
1. 运行 Azure 资源管理器模板。


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>导入服务总线 JMS 实现的 maven 依赖项

若要确保与服务总线的无缝连接，请将 `azure-servicebus-jms` 包作为依赖项添加到 Maven `pom.xml` 文件，如下所示：

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>应用程序服务器配置更改

此部分自定义为托管客户端应用程序（连接到 ActiveMQ）的应用程序服务器。

#### <a name="tomcat"></a>Tomcat

在这里，你将从特定于 ActiveMQ 的配置开始，如文件中所示 `/META-INF/context.xml` 。

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

调整此内容以指向服务总线，如下所示：

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>春季应用程序

##### <a name="update-the-applicationproperties-file"></a>更新 `application.properties` 文件

如果使用春季 boot 应用程序连接到 ActiveMQ，则需要删除文件中特定于 ActiveMQ 的属性 `application.properties` 。

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

然后，将特定于服务总线的属性添加到该 `application.properties` 文件。

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>将 `ActiveMQConnectionFactory` 替换为 `ServiceBusJmsConnectionFactory`

下一步是将的实例替换 `ActiveMQConnectionFactory` 为 `ServiceBusJmsConnectionFactory` 。

> [!NOTE] 
> 实际代码更改特定于应用程序和依赖关系的管理方式，但以下示例提供有关应更改的内容的指导。
>

以前，你可能已经实例化了的对象 `ActiveMQConnectionFactory` ，如下所示：

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

现在，你要将其更改为实例化对象 `ServiceBusJmsConnectionFactory` ，如下所示：

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>迁移后

现在，你已将应用程序修改为开始发送和接收来自服务总线的消息，因此应该验证它是否按预期方式工作。 完成此操作后，你可以继续进一步优化并现代化应用程序堆栈。

## <a name="next-steps"></a>后续步骤

使用[适用于 Azure 服务总线 JMS 的春季 Boot 入门](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus)，实现与服务总线的无缝集成。

若要了解有关服务总线消息传送和 JMS 的详细信息，请参阅：

* [服务总线 JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)