---
title: 迁移到 Apache Kafka 的 Azure 事件中心
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
ms.openlocfilehash: 806a1f16327ad72a7f3527c813b355e1ba807dda
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606751"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>迁移到阿帕奇卡夫卡生态系统的 Azure 事件中心
Azure 事件中心公开 Apache Kafka 终结点，使您能够使用 Kafka 协议连接到事件中心。 通过对现有 Kafka 应用程序进行最少的更改，可以连接到 Azure 事件中心并享受 Azure 生态系统的优势。 卡夫卡的事件中心支持[阿帕奇卡夫卡版本1.0](https://kafka.apache.org/10/documentation.html)及更高版本。

## <a name="pre-migration"></a>预迁移 

### <a name="create-an-azure-account"></a>创建 Azure 帐户
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

### <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
按照["创建事件中心](event-hubs-create.md)"文章中的分步说明创建事件中心命名空间和事件中心。 

### <a name="connection-string"></a>连接字符串
按照门户文章中获取[连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)的步骤操作。 并且，记下连接字符串，以便以后使用。 

### <a name="fully-qualified-domain-name-fqdn"></a>完全限定的域名 (FQDN)
您可能还需要指向事件中心命名空间的 FQDN。 FQDN 可以在连接字符串中找到，如下所示：

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

如果事件中心命名空间部署在非公共云上，则域名可能会有所不同（例如.servicebus.chinacloudapi.cn、.servicebus.usgovcloudapi.net\*\*或\*.servicebus.cloudapi.de）。

## <a name="migration"></a>迁移 

### <a name="update-your-kafka-client-configuration"></a>更新卡夫卡客户端配置

要连接到启用卡夫卡的事件中心，您需要更新 Kafka 客户端配置。 如果您在查找您的应用程序时遇到问题，请尝试在应用程序中搜索设置的位置`bootstrap.servers`。

在应用程序中任何有意义的位置插入以下配置。 请确保更新`bootstrap.servers`和`sasl.jaas.config`值，以便使用正确的身份验证将客户端定向到事件中心 Kafka 终结点。 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

如果`sasl.jaas.config`框架中不支持的配置，请找到用于设置 SASL 用户名和密码的配置，然后改用它们。 将用户名`$ConnectionString`和密码设置为事件中心连接字符串。

## <a name="post-migration"></a>迁移后
运行将事件发送到事件中心的 Kafka 应用程序。 然后，验证事件中心是否使用 Azure 门户接收事件。 在事件中心命名空间的 **"概述"** 页上，切换到 **"指标"** 部分中的 **"消息**"视图。 刷新页面以更新图表。 可能需要在几秒钟后才会显示已收到消息。 

[![验证事件中心是否已收到消息](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>后续步骤
若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下文章：  

- [事件中心的阿帕奇卡夫卡故障排除指南](apache-kafka-troubleshooting-guide.md)
- [常见问题 - 阿帕奇卡夫卡事件中心](apache-kafka-frequently-asked-questions.md)
- [Azure 活动中心 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
- [推荐的配置](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
- 