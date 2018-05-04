---
title: 使用 Apache Kafka 生成者和使用者 API - Azure HDInsight | Microsoft Docs
description: 了解如何将 Apache Kafka 生成者和使用者 API 与 Kafka on HDInsight 配合使用。 可以使用这些 API 开发向 Apache Kafka 中进行写入以及从中进行读取的应用程序。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 01592401c4c88adeed49b11df4e7963e27b1bcee
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>Apache Kafka 生成者和使用者 API

了解如何创建将 Kafka 生成者和使用者 API 与 Kafka on HDInsight 配合使用的应用程序。

有关这些 API 的文档，请参阅[生成者 API](https://kafka.apache.org/documentation/#producerapi) 和[使用者 API](https://kafka.apache.org/documentation/#consumerapi)。

## <a name="set-up-your-development-environment"></a>设置开发环境

必须在开发环境中安装以下组件：

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或类似程序，如 OpenJDK。

* [Apache Maven](http://maven.apache.org/)

* SSH 客户端和 `scp` 命令。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

## <a name="set-up-your-deployment-environment"></a>设置部署环境

此示例需要 Kafka on HDInsight 3.6。 若要了解如何创建 Kafka on HDInsight 群集，请参阅 [Kafka on HDInsight 入门](apache-kafka-get-started.md)文档。

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

2. 若要创建此示例使用的 Kafka 主题，请使用以下命令：

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    将 __your cluster name__ 替换为你的 HDInsight 群集的名称。 出现提示时，输入 HDInsight 群集登录帐户的密码。

    > [!NOTE]
    > 如果你的群集登录名不同于默认值 `admin`，请将前面的命令中的 `admin` 值替换为你的群集登录名。

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

若要在操作中了解此过程，请使用以下步骤：

1. 重复[运行示例](#run)部分中的步骤 1 和 2 来打开一个新的 SSH 会话。

2. 在这两个 SSH 会话中，都输入以下命令：

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > 同时输入这两个命令，以便它们同时运行。

    注意，读取的消息数与前面的部分不同，前面的部分只有一个使用者。 在此部分中，读取的消息拆分到两个实例中。

同一组中客户端的使用情况通过主题的分区进行处理。 之前创建的 `test` 主题有 8 个分区。 若打开 8 个 SSH 会话，并在所有会话中启动一个使用者，每个使用者都将从主题的单个分区中读取记录。

> [!IMPORTANT]
> 使用者组中存在的使用者实例不能比分区多。 此示例中，一个使用者组最多可包含八个使用者，因为这是本主题中的分区数。 也可拥有多个使用者组，每个组的使用者不能超过八个。

存储在 Kafka 中的记录都按在分区中接收的顺序进行存储。 若要在分区内实现记录的有序交付，请创建一个使用者组，其中的使用者实例数与分区数匹配。 若要在主题内实现记录的有序交付，请创建仅含一个使用者实例的使用者组。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Kafka 生成者和使用者 API 与 Kafka on HDInsight 配合使用。 使用以下内容，详细了解如何使用 Kafka：

* [分析 Kafka 日志](apache-kafka-log-analytics-operations-management.md)
* [在 Kafka 群集之间复制数据](apache-kafka-mirroring.md)
* [将 Kafka 流 API 与 HDInsight 配合使用](apache-kafka-streams-api.md)
* [将 Apache Spark 流式处理 (DStream) 与 Kafka on HDInsight 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Spark 结构化流式处理与 Kafka on HDInsight 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [使用 Apache Spark 结构化流式处理将数据从 Kafka on HDInsight 移到 Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](../hdinsight-apache-storm-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md)