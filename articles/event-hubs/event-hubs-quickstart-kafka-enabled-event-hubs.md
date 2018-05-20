---
title: 流式传输到用于 Kafka 生态系统的 Azure 事件中心 | Microsoft Docs
description: 使用 Kafka 协议和 API 流式传输到事件中心。
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>流式传输到用于 Kafka 生态系统的事件中心

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs) 上提供了此示例

本快速入门演示如何在不更改协议客户端或运行自己的群集的情况下，流式传输到已启用 Kafka 的事件中心。 你将了解如何只需更改应用程序配置，即可将生成者和使用者与已启用 Kafka 的事件中心通信。 用于 Kafka 生态系统的 Azure 事件中心支持 [Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保具备以下项：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [下载](http://maven.apache.org/download.cgi)并[安装](http://maven.apache.org/install.html) Maven 二进制存档。
* [Git](https://www.git-scm.com/)
* [已启用 Kafka 的事件中心命名空间](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>在事件中心内使用 Kafka 发送和接收消息

1. 克隆 [Azure 事件中心存储库](https://github.com/Azure/azure-event-hubs)。

2. 导航到 `azure-event-hubs/samples/kafka/quickstart/producer`。

3. 在 src/main/resources/producer.config 中更新生产者的配置详细信息，如下所示。

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. 运行生产者代码并流式传输到已启用 Kafka 的事件中心。
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. 导航到 azure-event-hubs/samples/kafka/quickstart/consumer。

6. 在 src/main/resources/consumer.config 中更新使用者的配置详细信息，如下所示。
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. 使用 Kafka 客户端运行使用者代码，并通过已启用 Kafka 的事件中心进行处理。

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

如果事件中心 Kafka 群集已将来自生产者的事件排队，则现在应开始从使用者接收这些事件。

## <a name="next-steps"></a>后续步骤

* [了解用于 Kafka 生态系统的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
* [了解事件中心](event-hubs-what-is-event-hubs.md)
* 使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 将事件从本地 Kafka 流式传输到云上已启用 Kafka 的事件中心。](event-hubs-kafka-mirror-maker-tutorial.md)
