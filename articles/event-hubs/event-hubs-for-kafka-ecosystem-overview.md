---
title: 使用 Apache Kafka 应用中的事件中心 - Azure 事件中心 | Microsoft Docs
description: 本文介绍有关 Azure 事件中心提供的 Apache Kafka 支持的信息。
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 5c49f8f87d8d399cda33a332f7464ed340ae3a0f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761492"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>使用 Apache Kafka 应用程序中的 Azure 事件中心
事件中心提供与 Apache Kafka®制造者和使用者 Api 兼容的终结点，大多数现有 Apache Kafka 客户端应用程序可以使用这些 Api 作为运行自己 Apache Kafka 群集的替代方法。 事件中心支持版本1.0 及更高版本的 Apache Kafka 的生成者和使用者 Api 客户端。

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>适用于 Kafka 的事件中心可提供什么？

用于 Apache Kafka 功能的事件中心在 Azure 事件中心之上提供了一个协议标头，该协议标头与为 Apache Kafka 服务器版本1.0 和更高版本生成的 Apache Kafka 客户端兼容，并支持从事件中心读取和写入到与 Apache Kafka 主题等效的事件中心。 

与现有的 Kafka 设置相比，你通常可以使用应用程序中的事件中心 Kafka 终结点，而无需更改代码，只需修改配置：将配置中的连接字符串更新为指向事件中心公开的 Kafka 终结点，而不是指向 Kafka 群集。 然后，可以开始将使用 Kafka 协议的应用程序中的事件流式传输到事件中心。 

从概念上讲，Kafka 和事件中心非常类似：它们都是为流式处理数据而生成的已分区日志，因此，客户端可以控制要读取的保留日志部分。 下表映射 Kafka 和事件中心之间的概念。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 和事件中心概念映射

| Kafka 概念 | 事件中心概念|
| --- | --- |
| 群集 | 命名空间 |
| 主题 | 事件中心 |
| 分区 | 分区|
| 使用者组 | 使用者组 |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka 和事件中心之间的主要差异

尽管 [Apache Kafka](https://kafka.apache.org/) 是需要安装和操作的软件，但事件中心是一种完全托管的云本机服务。 没有要管理和监视的服务器、磁盘或网络，也不需要考虑或配置任何代理。 创建一个命名空间，该命名空间是具有完全限定的域名的终结点，然后创建事件中心 (该命名空间中的主题) 。 

有关事件中心和命名空间的详细信息，请参阅[事件中心功能](event-hubs-features.md#namespace)。 作为云服务，事件中心使用单一稳定的虚拟 IP 地址作为终结点，因此客户端无需了解群集中代理或计算机的情况。 尽管事件中心实现了同一协议，但这种差异意味着所有分区的所有 Kafka 流量都可通过此终结点以可预测方式路由，而无需对群集的所有代理进行防火墙访问。   

事件中心的规模控制于你所购买的吞吐量单位的数量，每个吞吐量单位 entitling 每秒1兆字节，每秒传入1000个事件，两个传入的流量。 如果使用了 [自动](event-hubs-auto-inflate.md) 扩展功能，则在达到吞吐量限制时，事件中心可以自动增加吞吐量单位;此功能也适用于 Apache Kafka 协议支持。  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka 适合你的工作负荷的解决方案吗？

自构建使用 Apache Kafka 的应用程序也很有用，它也有助于了解 Azure 事件中心是一群服务的一部分，这些服务还包括 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)和 [azure 事件网格](../event-grid/overview.md)。 

虽然某些 Apache Kafka 的商业分发提供商可能会建议 Apache Kafka 是所有消息平台需求的一站式商店，但事实是 Apache Kafka 不实现，例如， [竞争使用者](/azure/architecture/patterns/competing-consumers) 队列模式不支持  [发布-订阅](/azure/architecture/patterns/publisher-subscriber) ，它允许订阅者根据服务器评估的规则而不是纯偏移量访问传入消息，并且没有任何工具可以跟踪消息启动的作业的生命周期，或将错误消息 sidelining 到死信队列中，所有这些都是许多企业消息传递方案的基础。

若要了解模式之间的差异以及哪个模式最适合使用哪种服务，请查看 [Azure 指南中的异步消息传送选项](/azure/architecture/guide/technology-choices/messaging) 。 作为 Apache Kafka 的用户，你可能会发现，通过 Kafka 实现的通信路径已经实现，使用事件网格或服务总线可以实现的基本复杂性更少，但功能更强大。 

如果需要 Apache Kafka 的特定功能，这些功能在 Apache Kafka 接口的事件中心中不可用，或者如果实现模式超出 [事件中心配额](event-hubs-quotas.md)，则也可以 [在 Azure HDInsight 中运行本机 Apache Kafka 群集](../hdinsight/kafka/apache-kafka-introduction.md)。  

## <a name="security-and-authentication"></a>安全性和身份验证
每次你发布或使用来自用于 Kafka 的事件中心的事件时，客户端都会尝试访问事件中心资源。 你希望确保使用已授权的实体来访问资源。 在客户端上使用 Apache Kafka 协议时，可以使用 SASL 机制设置用于身份验证和加密的配置。 使用用于 Kafka 的事件中心时，需要进行 TLS 加密（因为使用事件中心传输的所有数据均经过 TLS 加密）。 可以在配置文件中指定 SASL_SSL 选项来完成此设置。 

Azure 事件中心提供了多个选项来授予对安全资源的访问权限。 

- OAuth 2.0
- 共享访问签名 (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
事件中心与 Azure Active Directory (Azure AD) ，后者提供与 **OAuth 2.0** 兼容的集中授权服务器。 使用 Azure AD，可以通过基于角色的访问控制 (RBAC) 向客户端标识授予细粒度权限。 可以指定“SASL_SSL”作为协议，并指定“OAUTHBEARER”作为机制，通过这种方式将此功能用于 Kafka 客户端。 有关 Azure 角色和范围访问级别的详细信息，请参阅[使用 Azure AD 授予访问权限](authorize-access-azure-active-directory.md)。

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>共享访问签名 (SAS)
事件中心还提供了共享访问签名 (SAS)，方便你对用于 Kafka 的事件中心资源进行委派访问。 与 SAS 相比，使用 OAuth 2.0 基于令牌的机制授予访问权限具有更好的安全性和易用性。 内置角色还可以消除基于 ACL 的授权（用户必须对其进行维护和管理）的需要。 可以指定“SASL_SSL”作为协议，并指定“PLAIN”作为机制，通过这种方式将此功能用于 Kafka 客户端。 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> 对 Kafka 客户端使用 SAS 身份验证时，在重新生成 SAS 密钥时，已建立的连接不会断开。 

#### <a name="samples"></a>示例 
有关创建事件中心并使用 SAS 或 OAuth 对其进行访问的分步说明教程，请参阅[快速入门：使用 Kafka 协议通过事件中心进行数据流式传输](event-hubs-quickstart-kafka-enabled-event-hubs.md)。

如需更多示例来演示如何在用于 Kafka 的事件中心使用 OAuth，请参阅 [GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。

## <a name="other-event-hubs-features"></a>其他事件中心功能 

Apache Kafka 功能的事件中心是在 Azure 事件中心并发提供的三个协议之一，用于补充 HTTP 和 AMQP。 可以用任何一种协议进行编写，并将其与其他任何协议一起读取，以便您的当前 Apache Kafka 创建者可以通过 Apache Kafka 继续发布，但您的读者可以从与事件中心的本机集成的 AMQP 接口获益，如 Azure 流分析或 Azure Functions。 Reversely，你可以轻松地将 Azure 事件中心集成到 AMQP 路由网络作为目标终结点，还可以通过 Apache Kafka 集成来读取数据。  

此外，事件中心功能（如 [捕获](event-hubs-capture-overview.md)）通过 Azure Blob 存储和 Azure Data Lake Storage 实现极其经济高效的长期存档， [异地灾难恢复](event-hubs-geo-dr.md) 还适用于 Kafka 功能的事件中心。

## <a name="apache-kafka-feature-differences"></a>Apache Kafka 功能差异 

Apache Kafka 事件中心的目标是为锁定到 Apache Kafka API 的应用程序提供对 Azure 事件中心功能的访问权限，否则必须由 Apache Kafka 群集提供支持。 

[如上所述，Azure](#is-apache-kafka-the-right-solution-for-your-workload)邮件服务组针对多种消息传递方案提供丰富且可靠的覆盖范围，但以下功能目前不受 Apache Kafka API 的事件中心支持，我们指出了所需功能的位置和方式。

### <a name="transactions"></a>事务

[Azure 服务总线](../service-bus-messaging/service-bus-transactions.md) 提供了可靠的事务支持，允许接收和使用消息和会话，同时将消息处理所产生的出站消息发送到事务的一致性保护下的多个目标实体。 此功能集不仅允许对序列中的每条消息进行一次性处理，而且还避免了其他使用者无意中重新处理相同的消息，就像 Apache Kafka 的情况一样。 对于事务消息工作负荷，建议使用服务总线。

### <a name="compression"></a>压缩

的客户端 [压缩](https://cwiki.apache.org/confluence/display/KAFKA/Compression) 功能 Apache Kafka 将一批多条消息压缩为制造者端的一条消息，并在使用者端上解压该批。 Apache Kafka broker 将该批视为一条特殊消息。

此功能在本质上是与 Azure 事件中心的多协议模型同时出现的，它允许从代理和任何协议单独检索消息，甚至允许批处理发送的消息。 

任何事件中心事件的负载均为字节流，可以使用所选的算法压缩内容。 Apache Avro 编码格式支持本机压缩。

### <a name="log-compaction"></a>日志压缩

Apache Kafka 日志压缩是一项功能，它允许从分区逐出每个键的所有记录，但会将 Apache Kafka 主题有效地转换为键值存储，其中最后一个值的添加将覆盖前一个值。 与 [Azure Cosmos DB](../cosmos-db/introduction.md)的数据库服务相比，密钥值存储模式（甚至是频繁更新）的支持更好。

日志压缩功能由 Kafka Connect 和 Kafka 流式处理客户端框架使用。

### <a name="kafka-streams"></a>Kafka Stream

Kafka 流是流分析的一个客户端库，它是 Apache Kafka 开源项目的一部分，但独立于 Apache Kafka 事件流代理。 

Azure 事件中心客户要求提供 Kafka 流支持的最常见原因是，他们对 Confluent 的 "ksqlDB" 产品感兴趣。 "ksqlDB" 是一种专用的共享源项目，该项目已 [获得许可](https://github.com/confluentinc/ksql/blob/master/LICENSE) ，使其不提供软件即服务、平台即服务、基础结构即服务或与 Confluent 产品或服务竞争的其他类似联机服务，或提供 "ksqlDB" 支持。 在实际情况下，如果你使用 ksqlDB，则必须自己操作 Kafka，或者必须使用 Confluent 的云产品/服务。 许可条款可能还会影响为许可证所排除用途提供服务的 Azure 客户。

独立且没有 ksqlDB，Kafka 流比许多替代框架和服务具有更少的功能，其中大多数都具有内置的流式处理 SQL 接口，并与今天的 Azure 事件中心集成：

- [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)
- [通过事件中心捕获 (的 Azure Synapse 分析) ](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- Apache Spark[](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka 流](event-hubs-kafka-akka-streams-tutorial.md)

列出的服务和框架通常可以通过适配器直接从不同的源集中获取事件流和引用数据。 Kafka 流只能从 Apache Kafka 获取数据，因此分析项目将被锁定到 Apache Kafka 中。 若要使用来自其他源的数据，需要先使用 Kafka 连接框架将数据导入到 Apache Kafka 中。

如果必须使用 Azure 上的 Kafka 流框架，则 [在 HDInsight 上 Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md) 将提供该选项。 HDInsight 上的 Apache Kafka 提供完全控制 Apache Kafka 的所有配置方面，同时与 Azure 平台的各个方面（从故障/更新域位置到网络隔离、监视集成）完全集成。 

## <a name="next-steps"></a>后续步骤
本文介绍了适用于 Kafka 的事件中心。 若要了解详细信息，请参阅[针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)。
