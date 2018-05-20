---
title: 教程：使用 Apache Kafka 生成者和使用者 API - Azure HDInsight | Microsoft Docs
description: 了解如何将 Apache Kafka 生成者和使用者 API 与 Kafka on HDInsight 配合使用。 本教程介绍如何从 Java 应用程序中将这些 API 与 Kafka on HDInsight 配合使用。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2018
ms.author: larryfr
ms.openlocfilehash: b602f8bfe316e9c11dbff18273f37c99407c3da6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>教程：使用 Apache Kafka 生成者和使用者 API

了解如何将 Kafka 生成者和使用者 API 与 Kafka on HDInsight 配合使用。

Kafka 生成者 API 允许应用程序将数据流发送到 Kafka 群集。 Kafka 使用者 API 允许应用程序从群集读取数据流。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置开发环境
> * 设置部署环境
> * 了解代码
> * 生成并部署应用程序
> * 在群集上运行应用程序

有关这些 API 的详细信息，请参阅有关[生成者 API](https://kafka.apache.org/documentation/#producerapi) 和[使用者 API](https://kafka.apache.org/documentation/#consumerapi) 的 Apache 文档。

## <a name="set-up-your-development-environment"></a>设置开发环境

必须在开发环境中安装以下组件：

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或类似程序，如 OpenJDK。

* [Apache Maven](http://maven.apache.org/)

* SSH 客户端和 `scp` 命令。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

* 文本编辑器或 Java IDE。

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，应该检查它们是否存在并且包含系统的正确值。

* `JAVA_HOME` - 应该指向 JDK 的安装目录。
* `PATH` - 应该包含以下路径：
  
    * `JAVA_HOME`（或等效路径）。
    * `JAVA_HOME\bin`（或等效路径）。
    * Maven 的安装目录。

## <a name="set-up-your-deployment-environment"></a>设置部署环境

本教程需要 Kafka on HDInsight 3.6。 若要了解如何创建 Kafka on HDInsight 群集，请参阅 [Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

## <a name="understand-the-code"></a>了解代码

示例应用程序位于 `Producer-Consumer` 子目录的 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 中。 该应用程序主要包含四个文件：

* `pom.xml`：此文件定义项目依赖项、Java 版本和打包方法。
* `Producer.java`：此文件使用生成者 API 将 100 万 (1,000,000) 个随机句子发送到 Kafka。
* `Consumer.java`：此文件使用使用者 API 从 Kafka 读取数据并将其发出到 STDOUT。
* `Run.java`：用于运行生成者和使用者代码的命令行接口。

### <a name="pomxml"></a>Pom.xml

在 `pom.xml` 文件中要了解的重要事项：

* 依赖项：此项目依赖于 `kafka-clients` 包提供的 Kafka 生成者和使用者 API。 以下 XML 代码定义此依赖项：

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > `${kafka.version}` 条目在 `pom.xml` 的 `<properties>..</properties>` 部分进行声明，并配置为 HDInsight 群集的 Kafka 版本。

* 插件：Maven 插件提供各种功能。 此项目使用了以下插件：

    * `maven-compiler-plugin`：用于将项目使用的 Java 版本设置为 8。 这是 HDInsight 3.6 的 Java 版本。
    * `maven-shade-plugin`：用于生成包含此应用程序以及任何依赖项的 uber jar。 它还用于设置应用程序的入口点，以便直接运行 Jar 文件，而无需指定主类。

### <a name="producerjava"></a>Producer.java

生成者与 Kafka 代理主机（辅助角色节点）进行通信，以将数据存储到 Kafka 主题中。 以下代码片段来自 `Producer.java` 文件：

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

此代码连接到 Kafka 代理主机（辅助角色节点），然后使用生成者 API 将 1,000,000 个句子发送到 Kafka。

### <a name="consumerjava"></a>Consumer.java

使用者与 Kafka 代理主机（辅助角色节点）进行通信，并在循环中读取记录。 以下代码片段来自 `Consumer.java` 文件：

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
        KafkaConsumer<String, String> consumer;
        // Configure the consumer
        Properties properties = new Properties();
        // Point it to the brokers
        properties.setProperty("bootstrap.servers", brokers);
        // Set the consumer group (all consumers must belong to a group).
        properties.setProperty("group.id", groupId);
        // Set how to serialize key/value pairs
        properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        // When a group is first created, it has no offset stored to start reading from. This tells it to start
        // with the earliest record in the stream.
        properties.setProperty("auto.offset.reset","earliest");
        consumer = new KafkaConsumer<>(properties);

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

在此代码中，使用者配置为从该主题的开头读取（`auto.offset.reset` 设置为 `earliest`）。

### <a name="runjava"></a>Run.java

`Run.java` 文件提供了命令行接口，可运行生成者或使用者代码。 必须提供 Kafka 代理主机信息作为参数。 可以选择包括组 ID 值，该值由使用者进程使用。 如果使用相同的组 ID 创建多个使用者实例，它们将对主题的读取进行负载均衡。

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>生成并部署示例

1. 从 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下载示例。

2. 将目录更改到 `Producer-Consumer` 目录的位置，然后执行以下命令：

    ```
    mvn clean package
    ```

    此命令创建一个名为 `target` 的目录，其中包含名为 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 的文件。

3. 使用以下命令将 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 文件复制到 HDInsight 群集：
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    将 **SSHUSER** 替换为群集的 SSH 用户，并将 **CLUSTERNAME** 替换为群集的名称。 出现提示时，请输入 SSH 用户密码。

## <a id="run"></a> 运行示例

1. 若要与群集建立 SSH 连接，请使用以下命令：

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    将 **SSHUSER** 替换为群集的 SSH 用户，并将 **CLUSTERNAME** 替换为群集的名称。 出现提示时，输入 SSH 用户帐户的密码。 有关在 HDInsight 中使用 `scp` 的详细信息，请参阅[在 HDInsight 中使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 若要创建此示例使用的 Kafka 主题，请使用以下步骤：

    1. 若要将群集名称保存到一个变量中并安装 JSON 分析实用工具 (`jq`)，请使用以下命令。 出现提示时，请输入 Kafka 群集名称：
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. 若要获取 Kafka 代理主机和 Zookeeper 主机，请使用以下命令。 出现提示时，输入群集登录（管理员）帐户的密码。
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. 若要创建 `test` 主题，请使用以下命令：

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```

3. 若要运行生成者并将数据写入到主题，请使用以下命令：

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. 在生成者完成后，使用以下命令从主题中读取：
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    将显示读取的记录以及记录计数。

5. 使用 __Ctrl + C__ 让使用者退出。

### <a name="multiple-consumers"></a>多个使用者

在读取记录时，Kafka 使用者使用使用者组。 让多个使用者使用同一个组会导致对主题的读取进行负载均衡操作。 组中的每个使用者接收一部分记录。

使用者应用程序接受一个用作组 ID 的参数。 例如，以下命令使用组 ID `mygroup` 启动一个使用者：
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

若要在操作中了解此过程，请使用以下命令：

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup' \; attach
```

此命令使用 `tmux` 将终端拆分为两列。 每列中都会启动使用者，且具有相同的组 ID 值。 使用者完成读取后，请注意，每个使用者仅读取记录的一部分。 使用 Ctrl+C 两次以退出 `tmux`。

同一组中客户端的使用情况通过主题的分区进行处理。 之前创建的 `test` 主题有 8 个分区。 如果启动 8 个使用者，则每个使用者都从主题的单个分区读取记录。

> [!IMPORTANT]
> 使用者组中存在的使用者实例不能比分区多。 此示例中，一个使用者组最多可包含八个使用者，因为这是本主题中的分区数。 也可拥有多个使用者组，每个组的使用者不能超过八个。

存储在 Kafka 中的记录都按在分区中接收的顺序进行存储。 若要在分区内实现记录的有序交付，请创建一个使用者组，其中的使用者实例数与分区数匹配。 若要在主题内实现记录的有序交付，请创建仅含一个使用者实例的使用者组。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Kafka 生成者和使用者 API 与 Kafka on HDInsight 配合使用。 使用以下内容，详细了解如何使用 Kafka：

* [分析 Kafka 日志](apache-kafka-log-analytics-operations-management.md)
* [在 Kafka 群集之间复制数据](apache-kafka-mirroring.md)
* [将 Kafka 流 API 与 HDInsight 配合使用](apache-kafka-streams-api.md)