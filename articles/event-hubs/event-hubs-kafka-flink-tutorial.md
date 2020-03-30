---
title: 将 Apache Flink 用于 Apache Kafka - Azure事件中心 | Microsoft Docs
description: 本文提供了有关如何将 Apache Flink 连接到已启用 Apache Kafka 的 Azure 事件中心的信息
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db877279bcfa7e132841e342cfc25b66bb3ec384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283593"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>将 Apache Flink 与适用于 Apache Kafka 的 Azure 事件中心配合使用
本教程介绍如何在不更改协议客户端或运行自己的群集的情况下将 Apache Flink 连接到事件中心。 Azure 事件中心支持 [Apache Kafka 版本 1.0](https://kafka.apache.org/10/documentation.html)。

使用 Apache Kafka 的主要优势之一是它可连接的框架的生态系统。 事件中心将 Kafka 的灵活性与 Azure 生态系统的可扩展性、一致性和支持相结合。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 创建事件中心命名空间
> * 克隆示例项目
> * 运行 Flink 制造者 
> * 运行 Flink 使用者

> [!NOTE]
> 此示例在[GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)上可用

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保具备以下先决条件：

* 通读[用于 Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)一文。 
* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 （JDK） 1.7+](https://aka.ms/azure-jdks)
    * 在 Ubuntu 上运行 `apt-get install default-jdk`，以便安装 JDK。
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](https://maven.apache.org/download.cgi)并[安装](https://maven.apache.org/install.html)Maven 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

要从任何事件中心服务进行发送或接收，需要事件中心命名空间。 请参阅[创建已启用 Kafka 的事件中心](event-hubs-create.md)，了解有关获取事件中心 Kafka 终结点的信息。 请确保复制事件中心连接字符串，以供将来使用。

## <a name="clone-the-example-project"></a>克隆示例项目

现在，您拥有事件中心连接字符串，可以克隆 Kafka 存储库的 Azure 事件中心并`flink`导航到子文件夹：

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>运行 Flink 制造者

使用提供的 Flink 制造者事例，将消息发送到事件中心服务。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中心 Kafka 终结点

#### <a name="producerconfig"></a>producer.config

更新 `producer/src/main/resources/producer.config` 中的 `bootstrap.servers` 和 `sasl.jaas.config` 值，以使用正确的身份验证将生产者定向到事件中心 Kafka 终结点。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>通过命令行运行生产者

若要通过命令行运行生产者，生成 JAR，然后从 Maven 中运行（或使用 Maven 生成 JAR，然后通过向 classpath 添加必要的 Kafka JAR 在 Java 中运行）：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

制造者现在将开始向主题 `test` 中已启用 Kafka 的事件中心发送事件，并将事件打印到 stdout。

## <a name="run-flink-consumer"></a>运行 Flink 使用者

使用提供的使用者示例，接收来自已启用 Kafka 的事件中心的消息。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中心 Kafka 终结点

#### <a name="consumerconfig"></a>consumer.config

更新 `consumer/src/main/resources/consumer.config` 中的 `bootstrap.servers` 和 `sasl.jaas.config` 值，以使用正确的身份验证将使用者定向到事件中心 Kafka 终结点。

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>通过命令行运行使用者

若要通过命令行运行使用者，生成 JAR，然后从 Maven 中运行（或使用 Maven 生成 JAR，然后通过向 classpath 添加必要的 Kafka JAR 在 Java 中运行）：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

如果事件中心具有事件（例如，如果生产者也在运行），则使用者现在开始从主题`test`接收事件。

查看 [ Flink 的 Kafka 连接器指南](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)，了解有关将 Flink 连接到 Kafka 的更为详细的信息。

## <a name="next-steps"></a>后续步骤
在本教程中，您学习了如何在不更改协议客户端或运行自己的群集的情况下将 Apache Flink 连接到事件中心。 已在本教程中执行了以下步骤： 

> [!div class="checklist"]
> * 创建事件中心命名空间
> * 克隆示例项目
> * 运行 Flink 制造者 
> * 运行 Flink 使用者

若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下主题：  

- [了解事件中心](event-hubs-what-is-event-hubs.md)
- [用于 Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
- [如何创建启用 Kafka 的事件中心](event-hubs-create.md)
- [从 Kafka 应用程序流式传输到事件中心](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [在事件中心镜像 Kafka 代理](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Spark 连接到事件中心](event-hubs-kafka-spark-tutorial.md)
- [将卡夫卡连接与事件中心集成](event-hubs-kafka-connect-tutorial.md)
- [将 Akka 流连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)
