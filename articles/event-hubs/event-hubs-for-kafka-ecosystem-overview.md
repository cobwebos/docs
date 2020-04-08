---
title: 使用 Apache Kafka 应用中的事件中心 - Azure 事件中心 | Microsoft Docs
description: 本文介绍有关 Azure 事件中心提供的 Apache Kafka 支持的信息。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 91e2d70bab8c1be4b3e5b400ce21122eccb1e9eb
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811375"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>使用 Apache Kafka 应用程序中的 Azure 事件中心
事件中心提供 Kafka 终结点，现有的基于 Kafka 的应用程序可将该终结点用作运行你自己的 Kafka 群集的替代方案。 事件中心支持 [Apache Kafka 协议 1.0 及更高版本](https://kafka.apache.org/documentation/)，并且可与现有 Kafka 应用程序（包括 MirrorMaker）配合使用。  

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>适用于 Kafka 的事件中心可提供什么？

适用于 Kafka 功能的事件中心基于 Azure 事件中心提供协议头，该协议头与 Kafka 1.0 和更高版本二进制兼容，可以读取和写入 Kafka 主题。 可以通过应用程序开始使用 Kafka 终结点，而无需更改任何代码，只需进行最小的配置更改。 更新配置中的连接字符串以指向事件中心公开的 Kafka 终结点，而不是指向 Kafka 群集。 然后，可以开始将使用 Kafka 协议的应用程序中的事件流式传输到事件中心。 此集成还支持 [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) 等框架，该框架目前处于预览状态。 

概念上 Kafka 和事件中心几乎相同：它们都是为流数据构建的分区日志。 下表映射 Kafka 和事件中心之间的概念。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 和事件中心概念映射

| Kafka 概念 | 事件中心概念|
| --- | --- |
| 群集 | 命名空间 |
| 主题 | 事件中心 |
| 分区 | 分区|
| 使用者组 | 使用者组 |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka 和事件中心之间的主要区别

[Apache Kafka](https://kafka.apache.org/) 是可在所选的任意位置运行的软件，而事件中心是类似 Azure Blob 存储的云服务。 没有需要管理的服务器或网络，也没有需要配置的代理。 你可创建一个命名空间（它是主题所在的 FQDN），然后在该命名空间内创建事件中心或主题。 有关事件中心和命名空间的详细信息，请参阅[事件中心功能](event-hubs-features.md#namespace)。 作为云服务，事件中心使用单个稳定的虚拟 IP 地址作为终结点，因此客户端不需要了解群集中的代理或计算机。 

事件中心的规模由购买的吞吐量单位数量控制，每个吞吐量单位可提供每秒 1 MB 的速度或每秒 1,000 个事件的流入量。 默认情况下，如果达到限制，事件中心会使用[自动膨胀](event-hubs-auto-inflate.md)功能按比例增加吞吐量单位；此功能也可与适用于 Kafka 功能的事件中心配合使用。 

### <a name="security-and-authentication"></a>安全性和身份验证
每次从 Kafka 的事件中心发布或使用事件时，客户端都尝试访问事件中心资源。 您希望确保使用授权实体访问资源。 当对客户端使用 Apache Kafka 协议时，可以使用 SASL 机制设置身份验证和加密的配置。 在 Kafka 使用事件中心时，需要 TLS 加密（因为与事件中心传输的所有数据都已加密 TLS）。 可以在配置文件中指定SASL_SSL选项。 

Azure 事件中心提供了多个选项来授权访问安全资源。 

- OAuth
- 共享访问签名 (SAS)

#### <a name="oauth"></a>OAuth
事件中心与 Azure 活动目录 （Azure AD） 集成，该目录提供**OAuth** 2.0 兼容的集中式授权服务器。 使用 Azure AD，可以使用基于角色的访问控制 （RBAC） 向客户端标识授予细粒度权限。 您可以通过为协议指定**SASL_SSL**和该机制的**OAUTHBEARER，** 将此功能与 Kafka 客户端一起使用。 有关 RBAC 角色和范围访问级别的详细信息，请参阅[使用 Azure AD 授权访问](authorize-access-azure-active-directory.md)。

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>共享访问签名 (SAS)
事件中心还提供**共享访问签名 （SAS），** 用于对 Kafka 资源的事件中心进行委派访问。 使用基于 OAuth 2.0 令牌的机制授权访问比 SAS 提供卓越的安全性和易用性。 内置角色还可以消除基于 ACL 的授权需求，这些授权必须由用户维护和管理。 通过将协议的**SASL_SSL**和**机制的 PLAIN**指定，可以将此功能与 Kafka 客户端一起使用。 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>示例 
有关创建事件中心并使用 SAS 或 OAuth 访问它的分步说明的**教程**，请参阅[快速入门：使用 Kafka 协议使用事件中心进行数据流。](event-hubs-quickstart-kafka-enabled-event-hubs.md)

有关演示如何将 OAuth 与 Kafka 事件中心配合**使用的示例，** 请参阅[GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka 可用的其他事件中心功能

借助适用于 Kafka 功能的事件中心，你能够使用一种协议写入并使用另一种协议读取，以便当前 Kafka 生成方可通过 Kafka 继续发布，而你可使用 Azure 流分析或 Azure Functions 等事件中心添加读取器。 此外，[捕获](event-hubs-capture-overview.md)和[异地灾难恢复](event-hubs-geo-dr.md)等事件中心功能也可与适用于 Kafka 功能的事件中心配合使用。

## <a name="features-that-are-not-yet-supported"></a>尚不支持的功能 

下面是尚不支持的 Kafka 功能列表：

*   幂等生成方
*   事务
*   压缩
*   基于大小的保留
*   日志压缩
*   将分区添加到现有主题
*   HTTP Kafka API 支持
*   Kafka Stream

## <a name="next-steps"></a>后续步骤
本文介绍了适用于 Kafka 的事件中心。 要了解更多信息，请参阅[Azure 活动中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)。


