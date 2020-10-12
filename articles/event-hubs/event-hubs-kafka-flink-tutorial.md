---
title: 将 Apache Flink 用于 Apache Kafka - Azure事件中心 | Microsoft Docs
description: 本文介绍如何将 Apache Flink 连接到 Azure 事件中心
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: f2e6eeb74c5a334d1692357edec0fd363349c7c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061642"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>将 Apache Flink 与适用于 Apache Kafka 的 Azure 事件中心配合使用
本教程演示如何在不更改你的协议客户端或运行你自己的群集的情况下，将 Apache Flink 连接到事件中心。 有关事件中心对 Apache Kafka 使用者协议的支持的详细信息，请参阅 [Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)。


在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 创建事件中心命名空间
> * 克隆示例项目
> * 运行 Flink 制造者 
> * 运行 Flink 使用者

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) 上提供了此示例

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保具备以下先决条件：

* 通读[用于 Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)一文。 
* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java 开发工具包 (JDK) 1.7+](https://aka.ms/azure-jdks)
    * 在 Ubuntu 上运行 `apt-get install default-jdk`，以便安装 JDK。
    * 请确保设置 JAVA_HOME 环境变量，使之指向在其中安装了 JDK 的文件夹。
* [下载](https://maven.apache.org/download.cgi)和[安装](https://maven.apache.org/install.html) Maven 二进制存档
    * 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，可以通过运行 `sudo apt-get install git` 来安装 Git。

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

要从任何事件中心服务进行发送或接收，需要事件中心命名空间。 有关创建命名空间和事件中心的说明，请参阅[创建事件中心](event-hubs-create.md)。 请确保复制事件中心连接字符串，以供将来使用。

## <a name="clone-the-example-project"></a>克隆示例项目

获得事件中心连接字符串后，克隆适用于 Kafka 的 Azure 事件中心存储库并导航到 `flink` 子文件夹：

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

生产者现在将开始向主题 `test` 中的事件中心发送事件，并将事件输出到 stdout。

## <a name="run-flink-consumer"></a>运行 Flink 使用者

使用所提供的使用者示例，接收来自事件中心的消息。 

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

如果事件中心具有事件（例如，如果生产者也在运行），则使用者现在将开始接收来自主题 `test` 的事件。

查看 [ Flink 的 Kafka 连接器指南](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)，了解有关将 Flink 连接到 Kafka 的更为详细的信息。

## <a name="next-steps"></a>后续步骤
若要详细了解适用于 Kafka 的事件中心，请参阅以下文章：  

- [在事件中心镜像 Kafka 中转站](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Spark 连接到事件中心](event-hubs-kafka-spark-tutorial.md)
- [将 Kafka Connect 与事件中心集成](event-hubs-kafka-connect-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)
- [将 Akka Streams 连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)