---
title: "使用 Apache Kafka Streams API - Azure HDInsight | Microsoft Docs"
description: "了解如何将 Apache Kafka Streams API 与 Kafka on HDInsight 配合使用。 此 API 允许在 Kafka 中的主题之间执行流处理。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: be6ed6d4c0c3a5fa55166b84b128881d434c4ab2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="apache-kafka-streams-api"></a>Apache Kafka Streams API

了解如何创建一个使用 Kafka Streams API 的应用程序并在 Kafka on HDInsight 上运行该应用程序。

使用 Apache Kafka 时，流处理通常使用 Apache Spark 或 Storm 完成。 Kafka 版本 0.10.0（在 HDInsight 3.5 和 3.6 中）引入了 Kafka Streams API。 此 API 允许使用 Kafka 上运行的应用程序，在输入和输出主题之间转换数据流。 在某些情况下，这可以作为创建 Spark 或 Storm 流式处理解决方案的替代方法。 有关 Kafka Streams 的详细信息，请参阅 Apache.org 上的 [Streams 简介](https://kafka.apache.org/10/documentation/streams/)文档。

## <a name="set-up-your-development-environment"></a>设置开发环境

必须在开发环境中安装以下组件：

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或类似程序，如 OpenJDK。

* [Apache Maven](http://maven.apache.org/)

* SSH 客户端和 `scp` 命令。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

## <a name="set-up-your-deployment-environment"></a>设置部署环境

此示例需要 Kafka on HDInsight 3.6。 若要了解如何创建 Kafka on HDInsight 群集，请参阅 [Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

## <a name="build-and-deploy-the-example"></a>生成并部署示例

使用以下步骤生成项目并将其部署到 Kafka on HDInsight 群集。

1. 从 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下载示例。

2. 将目录切换到 `Streaming` 目录，然后使用以下命令创建 jar 包：

    ```bash
    mvn clean package
    ```

    此命令创建一个名为 `target` 的目录，其中包含名为 `kafka-streaming-1.0-SNAPSHOT.jar` 的文件。

3. 使用以下命令将 `kafka-streaming-1.0-SNAPSHOT.jar` 文件复制到 HDInsight 群集：
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    将 **SSHUSER** 替换为群集的 SSH 用户，并将 **CLUSTERNAME** 替换为群集的名称。 出现提示时，输入 SSH 用户帐户的密码。 有关在 HDInsight 中使用 `scp` 的详细信息，请参阅[在 HDInsight 中使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="run-the-example"></a>运行示例

1. 若要与群集建立 SSH 连接，请使用以下命令：

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    将 **SSHUSER** 替换为群集的 SSH 用户，并将 **CLUSTERNAME** 替换为群集的名称。 出现提示时，输入 SSH 用户帐户的密码。 有关在 HDInsight 中使用 `scp` 的详细信息，请参阅[在 HDInsight 中使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

4. 若要创建此示例使用的 Kafka 主题，请使用以下命令：

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    将 __your cluster name__ 替换为你的 HDInsight 群集的名称。 出现提示时，输入 HDInsight 群集登录帐户的密码。

    > [!NOTE]
    > 如果你的群集登录名不同于默认值 `admin`，请将上述命令中的 `admin` 值替换为你的群集登录名。

5. 若要运行此示例，必须执行三项操作：

    * 启动 `kafka-streaming.jar` 中包含的 Streams 解决方案。
    * 启动用于写入到 `test` 主题的 producer。
    * 启动 consumer，以便显示写入到 `wordcounts` 主题的输出

    > [!NOTE]
    > 需要在 Kafka 中转站配置文件中确认 `auto.create.topics.enable` 属性已设置为 `true`。 可以使用 Ambari Web UI 在高级 Kafka 中转站配置文件中查看和修改此属性。 否则，在使用以下命令运行此示例之前，需要手动创建中间主题 `RekeyedIntermediateTopic`：
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic  --zookeeper $KAFKAZKHOSTS
    ```
    
    可以通过打开三个 SSH 会话来完成这些操作。 但之后必须在每个 SSH 会话中运行此部分的步骤 4，以便为每个会话设置 `$KAFKABROKERS` 和 `$KAFKAZKHOSTS`。 更简单的解决方案是使用 `tmux` 实用工具，它可以将当前 SSH 显示拆分为多个部分。 若要使用 `tmux` 启动 stream、producer 和 consumer，请使用以下命令：

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    此命令将 SSH 显示拆分为三个部分：

    * 左侧部分运行控制台 consumer，用于从 `wordcounts` 主题中读取消息：`/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > `--property` 参数指示控制台 consumer 输出关键字（字）以及计数（值）。 此参数还配置从 Kafka 中读取这些值时要使用的反序列化程序。

    * 右上部分运行 Streams API 解决方案：`java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * 右下部分运行控制台 producer，并等待用户输入消息以发送到 `test` 主题：`/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. 在 `tmux` 命令拆分显示后，光标位于右下部分。 开始输入句子。 每输入一个句子，都会更新左窗格以显示非重复字词的计数。 输出与以下文本类似：
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > 每遇到一个字，计数就会增加。

7. 使用 __Ctrl + C__ 退出 producer。 继续使用 __Ctrl + C__ 退出应用程序和 consumer。

## <a name="next-steps"></a>后续步骤

在本文档中，已了解如何将 Kafka Streams API 与 Kafka on HDInsight 配合使用。 使用以下内容，详细了解如何使用 Kafka：

* [分析 Kafka 日志](apache-kafka-log-analytics-operations-management.md)
* [在 Kafka 群集之间复制数据](apache-kafka-mirroring.md)
* [将 Kafka Producer 和 Consumer API 与 HDInsight 配合使用](apache-kafka-producer-consumer-api.md)
* [将 Apache Spark 流式处理 (DStream) 与 Kafka on HDInsight 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Spark 结构化流式处理与 Kafka on HDInsight 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [使用 Apache Spark 结构化流式处理将数据从 Kafka on HDInsight 移到 Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](../hdinsight-apache-storm-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md)
