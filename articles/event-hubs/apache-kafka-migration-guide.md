---
title: 迁移到 Azure 事件中心以进行 Apache Kafka
description: 本文介绍使用不同协议（AMQP、Apache Kafka 和 HTTPS）的使用者和生成者在使用 Azure 事件中心时如何交换事件。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 32b08e565b86af8f6373c9848211646128bb346d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677358"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>迁移到 Azure 事件中心以 Apache Kafka 生态系统
Azure 事件中心公开 Apache Kafka 终结点，使你能够使用 Kafka 协议连接到事件中心。 通过对现有的 Kafka 应用程序进行少量的更改，可以连接到 Azure 事件中心并获得 Azure 生态系统的优势。 Kafka 支持的事件中心[Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html)及更高版本。

## <a name="pre-migration"></a>预迁移 

### <a name="create-an-azure-account"></a>创建 Azure 帐户
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
按照[创建事件中心一](event-hubs-create.md)文中的分步说明创建事件中心命名空间和事件中心。 

### <a name="connection-string"></a>连接字符串
按照[门户文章获取连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)中的步骤进行操作。 并记下连接字符串，供以后使用。 

### <a name="fully-qualified-domain-name-fqdn"></a>完全限定的域名 (FQDN)
你可能还需要指向事件中心命名空间的 FQDN。 可以在连接字符串中找到 FQDN，如下所示：

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

如果事件中心命名空间部署在非公有云上，则你的域名可能不同（例如， \*. servicebus.chinacloudapi.cn、 \*. servicebus.usgovcloudapi.net 或\*servicebus.cloudapi.de）。

## <a name="migration"></a>迁移 

### <a name="update-your-kafka-client-configuration"></a>更新 Kafka 客户端配置

若要连接到已启用 Kafka 的事件中心，需要更新 Kafka 客户端配置。 如果找不到你的问题，请尝试搜索你`bootstrap.servers`的应用程序中设置的位置。

在应用程序中的任何位置插入以下配置。 请确保更新`bootstrap.servers`和`sasl.jaas.config`值，以将客户端定向到具有正确身份验证的事件中心 Kafka 终结点。 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

如果`sasl.jaas.config`框架中的配置不受支持，请查找用于设置 SASL 用户名和密码的配置并改为使用这些配置。 将 "用户名" `$ConnectionString`和 "密码" 设置为事件中心连接字符串。

## <a name="post-migration"></a>迁移后
运行 Kafka 应用程序，该应用程序将事件发送到事件中心。 然后，验证事件中心是否接收到使用 Azure 门户的事件。 在事件中心命名空间的 "**概述**" 页上，切换到 "**指标**" 部分中的 "**消息**" 视图。 刷新页面以更新图表。 可能需要在几秒钟后才会显示已收到消息。 

[![验证事件中心是否已收到消息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [Apache Kafka 事件中心的故障排除指南](apache-kafka-troubleshooting-guide.md)
- [常见问题-事件中心 Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Azure 事件中心 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [建议的配置](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)