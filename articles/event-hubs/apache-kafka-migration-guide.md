---
title: 迁移到用于 Apache Kafka 的 Azure 事件中心
description: 本文介绍使用不同协议（AMQP、Apache Kafka 和 HTTPS）的使用者和生成者在使用 Azure 事件中心时如何交换事件。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cf585cdc0a43c595ec013ba78a2e3ae191c40b8d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090648"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>迁移到用于 Apache Kafka 的 Azure 事件中心生态系统
Azure 事件中心公开了一个 Apache Kafka 终结点，使你能够使用 Kafka 协议连接到事件中心。 对现有 Kafka 应用程序稍作更改即可连接到 Azure 事件中心并充分利用 Azure 生态系统的优势。 用于 Kafka 的事件中心支持 [Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html) 及更高版本。

## <a name="pre-migration"></a>预迁移 

### <a name="create-an-azure-account"></a>创建 Azure 帐户
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
按照[创建事件中心](event-hubs-create.md)一文中的分步说明创建事件中心命名空间和事件中心。 

### <a name="connection-string"></a>连接字符串
按照[从门户获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)一文中的步骤进行操作。 另外，请记下连接字符串供以后使用。 

### <a name="fully-qualified-domain-name-fqdn"></a>完全限定的域名 (FQDN)
你可能还需要指向事件中心命名空间的 FQDN。 可以在连接字符串中找到 FQDN，如下所示：

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

如果事件中心命名空间部署在非公有云上，则你的域名可能不同（例如， \* . servicebus.chinacloudapi.cn、 \* . servicebus.usgovcloudapi.net 或 \* servicebus.cloudapi.de）。

## <a name="migration"></a>迁移 

### <a name="update-your-kafka-client-configuration"></a>更新 Kafka 客户端配置

若要连接到支持 Kafka 的事件中心，需要更新 Kafka 客户端配置。 如果在查找配置时遇到问题，请尝试在应用程序中搜索 `bootstrap.servers` 的设置位置。

在应用程序中的适当位置插入以下配置。 确保更新 `bootstrap.servers` 和 `sasl.jaas.config` 值，以使用正确的身份验证将客户端定向到事件中心 Kafka 终结点。 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

如果 `sasl.jaas.config` 不是框架中受支持的配置，请查找用于设置 SASL 用户名和密码的配置，改为使用这些配置。 将用户名设置为 `$ConnectionString`，并将密码设置为事件中心连接字符串。

## <a name="post-migration"></a>迁移后
运行可将事件发送到事件中心的 Kafka 应用程序。 然后，使用 Azure 门户验证事件中心是否收到事件。 在事件中心命名空间的“概览”页上，切换到“指标”部分中的“消息”视图。 刷新页面以更新图表。 可能需要在几秒钟后才会显示已收到消息。 

[![验证事件中心是否已收到消息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [针对事件中心的 Apache Kafka 故障排除指南](apache-kafka-troubleshooting-guide.md)
- [常见问题解答 - 用于 Apache Kafka 的事件中心](apache-kafka-frequently-asked-questions.md)
- [针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [建议的配置](apache-kafka-configurations.md)