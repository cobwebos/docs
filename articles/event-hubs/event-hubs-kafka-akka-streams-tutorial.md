---
title: 将 Akka Streams 与用于 Kafka 生态系统的 Azure 事件中心配合使用 | Microsoft Docs
description: 将 Akka Streams 连接到已启用 Kafka 的事件中心
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302326"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>将 Akka Streams 与用于 Kafka 生态系统的事件中心配合使用

使用 Apache Kafka 的主要优势之一是它可连接的框架的生态系统。 已启用 Kafka 的事件中心将 Kafka 的灵活性与 Azure 生态系统的可伸缩性、一致性和支持结合在一起。

本教程演示如何在不更改协议客户端或运行自己的群集的情况下，将 Akka Streams 连接到已启用 Kafka 的事件中心。 用于 Kafka 生态系统的 Azure 事件中心支持 [Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保具备以下先决条件：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 (JDK) 1.8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * 在 Ubuntu 上运行 `apt-get install default-jdk`，以便安装 JDK。
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](http://maven.apache.org/download.cgi)和[安装](http://maven.apache.org/install.html) Maven 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

要从任何事件中心服务进行发送或接收，需要事件中心命名空间。 请参阅[创建已启用 Kafka 的事件中心](event-hubs-create-kafka-enabled.md)，了解有关获取事件中心 Kafka 终结点的信息。 请确保复制事件中心连接字符串，以供将来使用。

## <a name="clone-the-example-project"></a>克隆示例项目

获得已启用 Kafka 的事件中心连接字符串后，克隆 Azure 事件中心存储库并导航到 `akka` 子文件夹：

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Akka Streams 生产者

使用所提供的 Akka Streams 生产者事例，将消息发送到事件中心服务。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中心 Kafka 终结点

#### <a name="producer-applicationconf"></a>生产者 application.conf

更新 `producer/src/main/resources/application.conf` 中的 `bootstrap.servers` 和 `sasl.jaas.config` 值，以使用正确的身份验证将生产者定向到事件中心 Kafka 终结点。

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>通过命令行运行生产者

若要通过命令行运行生产者，生成 JAR，然后从 Maven 中运行（或使用 Maven 生成 JAR，然后通过向 classpath 添加必要的 Kafka JAR 在 Java 中运行）：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

生产者将开始向主题 `test` 中已启用 Kafka 的事件中心发送事件，并将事件输出到 stdout。

## <a name="akka-streams-consumer"></a>Akka Streams 使用者

使用所提供的使用者示例，接收来自已启用 Kafka 的事件中心的消息。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中心 Kafka 终结点

#### <a name="consumer-applicationconf"></a>使用者 application.conf

更新 `consumer/src/main/resources/application.conf` 中的 `bootstrap.servers` 和 `sasl.jaas.config` 值，以使用正确的身份验证将使用者定向到事件中心 Kafka 终结点。

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>通过命令行运行使用者

若要通过命令行运行使用者，生成 JAR，然后从 Maven 中运行（或使用 Maven 生成 JAR，然后通过向 classpath 添加必要的 Kafka JAR 在 Java 中运行）：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

如果已启用 Kafka 的事件中心具有事件（例如，如果生产者也在运行），则使用者将开始接收来自主题 `test` 的事件。 

请查看 [Akka Streams Kafka 指南](https://doc.akka.io/docs/akka-stream-kafka/current/home.html)，了解有关 Akka Streams 的更多详细信息。

## <a name="next-steps"></a>后续步骤

* [了解事件中心](event-hubs-what-is-event-hubs.md)
* [了解用于 Kafka 生态系统的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
* 使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) [将事件从本地 Kafka 流式传输到云端已启用 Kafka 的事件中心。](event-hubs-kafka-mirror-maker-tutorial.md)
* 了解如何使用[本机 Kafka 应用程序](event-hubs-quickstart-kafka-enabled-event-hubs.md)或 [Apache Flink](event-hubs-kafka-flink-tutorial.md) 流式传输到已启用 Kafka 的事件中心
