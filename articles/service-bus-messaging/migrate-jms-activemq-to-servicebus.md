---
title: 将 Java 消息服务（JMS）应用程序从 ActiveMQ 迁移到 Azure 服务总线 |Microsoft Docs
description: 本文介绍如何迁移与活动 MQ 交互的现有 JMS 应用程序，以便与 Azure 服务总线交互。
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
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122263"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>将现有 Java 消息服务（JMS）2.0 应用程序从主动 MQ 迁移到 Azure 服务总线

Azure 服务总线支持通过高级消息队列协议（AMQP）协议利用 Java 消息服务（JMS） 2.0 API 的 Java/J2EE 和弹簧工作负荷。

本指南介绍当你要修改与 JMS 代理交互的现有 Java 消息服务（JMS）2.0 应用程序（特别是 Apache ActiveMQ 或 Amazon MQ）以与 Azure 服务总线交互时应注意的事项。

## <a name="before-you-start"></a>开始之前

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Azure 服务总线和 Apache ActiveMQ 之间的差异

Azure 服务总线和 Apache ActiveMQ 都是作为 JMS 提供程序的消息代理，客户端应用程序可以将消息发送到和接收来自的消息。 它们都通过**队列**和**主题**和**订阅**的发布-订阅语义启用点到点语义。 

尽管如此，两者还是有一些不同之处。

| 类别 | 活动 MQ | Azure 服务总线 |
| --- | --- | --- |
| 应用程序分层 | 群集单体架构 | 双层 <br> （网关 + 后端） |
| 协议支持 | <ul> <li>AMQP</li> <li> 阻碍 </li> <li> OpenWire </li> </ul> | AMQP |
| 预配模式 | <ul> <li> IaaS （本地） </li> <li> Amazon MQ （托管 PaaS） </li> | 托管 PaaS |
| 消息大小 | 客户可配置 | 1 MB （高级层） |
| 高可用性 | 由客户管理 | 平台托管 |
| 灾难恢复 | 由客户管理 | 平台托管 | 

### <a name="current-supported-and-unsupported-features"></a>当前支持和不支持的功能

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>注意事项

Azure 服务总线的两层性质提供各种业务连续性功能（高可用性和灾难恢复）。 但是，使用 JMS 功能时有一些注意事项。

#### <a name="service-upgrades"></a>服务升级

如果服务总线升级和重启，将删除临时队列或主题。

如果应用程序对临时队列或主题的数据丢失敏感，则建议**不要**使用临时队列或主题，而是使用持久队列、主题和订阅。

#### <a name="data-migration"></a>数据迁移

在迁移/修改客户端应用程序以与 Azure 服务总线交互的过程中，ActiveMQ 中保存的数据不会迁移到服务总线。

可能需要自定义应用程序来排出 ActiveMQ 队列、主题和订阅，并将消息重播到服务总线的队列、主题和订阅。

#### <a name="authentication-and-authorization"></a>身份验证和授权

Azure ActiveDirectory 支持的基于角色的访问控制（RBAC）是 Azure 服务总线的首选身份验证机制。

不过，由于当前不支持 RBAC，因为 Apache QPID JMS 缺少基于声明的身份验证支持。

目前，仅 SAS 密钥支持身份验证。

## <a name="pre-migration"></a>预迁移

### <a name="version-check"></a>版本检查

下面是在编写 JMS 应用程序和支持的特定版本时使用的组件。 

| 组件 | 版本 |
|---|---|
| Java 消息服务（JMS） API | 1.1 或更高版本 |
| AMQP 协议 | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>确保 AMQP 端口已打开

Azure 服务总线支持通过 AMQP 协议进行通信。 为此，需要启用经由端口5671（AMQP）和443（TCP）的通信。 根据客户端应用程序的托管位置，你可能需要一个支持票证来允许通过这些端口进行通信。

> [!IMPORTANT]
> Azure 服务总线**仅**支持 AMQP 1.0 协议。

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>设置企业配置（VNET、防火墙、专用终结点等）

Azure 服务总线支持各种企业安全性和高可用性功能。 若要了解有关详细信息，请参阅以下文档链接。

  * [虚拟网络服务终结点](service-bus-service-endpoints.md)
  * [防火墙](service-bus-ip-filtering.md)
  * [通过客户托管密钥进行服务端加密（BYOK）](configure-customer-managed-key.md)
  * [专用终结点](private-link-service.md)
  * [身份验证和授权](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>监视、警报和跟踪

将每个服务总线命名空间的指标发布到 Azure Monitor，并可用于向命名空间分配资源的警报和动态缩放。

阅读有关不同指标的详细信息以及如何在[Azure Monitor 中的服务总线指标](service-bus-metrics-azure-monitor.md)上设置警报的相关信息。

此外，请阅读有关[针对数据操作的客户端跟踪](service-bus-end-to-end-tracing.md)和[管理操作的操作/诊断日志记录的](service-bus-diagnostic-logs.md)详细信息

### <a name="metrics---newrelic"></a>指标-NewRelic

下面是一个可方便使用的指南，其中 ActiveMQ 映射到 Azure 服务总线中的指标。 下面从 NewRelic 中引用。

  * [ActiveMQ/Amazon MQ NewRelic 指标](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Azure 服务总线 NewRelic 指标](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> 目前，NewRelic 不与 ActiveMQ 直接集成，但它们具有可用于 Amazon MQ 的指标。
> 由于 Amazon MQ 派生自 ActiveMQ，下面的指南将 NewRelic 度量值从 AmazonMQ 映射到 Azure 服务总线。
>

|度量值分组| AmazonMQ/主动 MQ 指标 | Azure 服务总线指标 |
|------------|---------------------------|--------------------------|
|代理|`CpuUtilization`|`CPUXNS`|
|代理|`MemoryUsage`|`WSXNS`|
|代理|`CurrentConnectionsCount`|`activeConnections`|
|代理|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|代理|`InactiveDurableTopicSubscribersCount`|利用订阅指标|
|代理|`TotalMessageCount`|利用队列/主题/订阅级别`activeMessages`|
|队列/主题|`EnqueueCount`|`incomingMessages`|
|队列/主题|`DequeueCount`|`outgoingMessages`|
|队列|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>迁移

若要迁移现有的 JMS 2.0 应用程序以便与 Azure 服务总线交互，需执行以下步骤。

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>从 ActiveMQ 导出拓扑并在 Azure 服务总线中创建实体（可选）

若要确保客户端应用程序可与 Azure 服务总线无缝连接，拓扑（包括队列、主题和订阅）需要从**Apache ActiveMQ**迁移到**Azure 服务总线**。

> [!NOTE]
> 对于 Java 消息服务（JMS）应用程序，队列、主题和订阅的创建是运行时操作。 大多数 Java 消息服务（JMS）提供程序（消息代理）提供了在运行时创建*队列*、*主题*和*订阅*的功能。
>
> 因此，上述步骤是可选的。
>
> 若要确保应用程序具有在运行时创建拓扑的权限，请确保使用带有***SAS "管理"*** 权限的连接字符串。

要执行此操作 
  * 利用[ActiveMQ 命令行工具](https://activemq.apache.org/activemq-command-line-tools-reference)导出拓扑
  * 使用[Azure 资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)重新创建相同的拓扑
  * 执行 Azure 资源管理器模板。


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>导入服务总线 JMS 实现的 maven 依赖项

若要确保与 Azure 服务总线的无缝连接，需要将***Azure Maven-jms***包作为依赖项添加到 `pom.xml` 文件，如下所示。

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

此部分自定义到托管客户端应用程序的应用程序服务器，该服务器将连接到主动 MQ。

#### <a name="tomcat"></a>Tomcat

在此，我们从特定于活动 MQ 的配置开始，如文件中所示 `/META-INF/context.xml` 。

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

可按如下所示进行调整以指向 Azure 服务总线

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

##### <a name="update-applicationproperties-file"></a>更新 `application.properties` 文件

如果使用春季 boot 应用程序连接到 ActiveMQ。

此处的目标是***删除***文件中的 ActiveMQ 特定属性 `application.properties` 。

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

然后，将服务总线特定属性***添加***到该 `application.properties` 文件。

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>将 ActiveMQConnectionFactory 替换为 ServiceBusJmsConnectionFactory

下一步是将 ActiveMQConnectionFactory 的实例替换为 ServiceBusJmsConnectionFactory。

> [!NOTE] 
> 实际代码更改特定于应用程序和依赖关系的管理方式，但下面的示例提供了有关应更改的***内容***的指导。
>

以前，你可能会按如下所示实例化 ActiveMQConnectionFactory 的对象。

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

这会更改为实例化 ServiceBusJmsConnectionFactory 的对象

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>迁移后

现在，你已将应用程序修改为开始从 Azure 服务总线发送和接收消息，因此应该验证它是否按预期方式工作。 完成此操作后，你可以继续进一步优化并现代化应用程序堆栈。

## <a name="next-steps"></a>后续步骤

利用[适用于 Azure 服务总线 JMS 的春季 Boot 入门](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus)，实现与 Azure 服务总线的无缝集成。

若要了解有关服务总线消息传送和 Java 消息服务（JMS）的详细信息，请参阅以下主题：

* [服务总线 JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
