---
title: "Apache Kafka 入门 - Azure HDInsight | Microsoft Docs"
description: "了解如何在 Azure HDInsight 上创建 Apache Kafka 群集。 了解如何创建主题、订阅服务器和使用者。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: 6191d81d6b55f5ffe943f800be542d7ea4614eaf
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2018
---
# <a name="start-with-apache-kafka-on-hdinsight"></a>Apache Kafka on HDInsight 入门

了解如何在 Azure HDInsight 上创建和使用 [Apache Kafka](https://kafka.apache.org) 群集。 Kafka 是开源分布式流式处理平台，可与 HDInsight 配合使用。 通常将其用作消息代理，因为它可提供类似于发布-订阅消息队列的功能。 Kafka 通常与 Apache Spark 和 Apache Storm 一起使用。

> [!NOTE]
> 目前，有两个 Kafka 版本可用于 HDInsight；0.9.0 (HDInsight 3.4) 和 0.10.0（HDInsight 3.5 和 3.6）。 本文档中的步骤假设使用 Kafka on HDInsight 3.6。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="create-a-kafka-cluster"></a>创建 Kafka 群集

使用以下步骤创建 Kafka on HDInsight 群集：

1. 从 [Azure 门户](https://portal.azure.com)依次选择“+ 创建资源”、“数据 + 分析”、“HDInsight”。
   
    ![创建 HDInsight 群集](./media/apache-kafka-get-started/create-hdinsight.png)

2. 在“基本信息”中输入以下信息：

    * **群集名称**：HDInsight 群集的名称。
    * **订阅**：选择要使用的订阅。
    * **群集登录用户名**和**群集登录密码**：通过 HTTPS 访问群集时的登录凭据。 可以使用这些凭据访问 Ambari Web UI 或 REST API 等服务。
    * **安全外壳 (SSH) 用户名**：通过 SSH 访问群集时使用的登录名。 默认情况下，密码与群集登录密码相同。
    * **资源组**：要在其中创建群集的资源组。
    * **位置**：要在其中创建群集的 Azure 区域。

        > [!IMPORTANT]
        > 为实现数据的高可用性，我们建议选择包含__三个容错域__的位置（区域）。 有关详细信息，请参阅[数据高可用性](#data-high-availability)部分。
   
 ![选择订阅](./media/apache-kafka-get-started/hdinsight-basic-configuration.png)

3. 选择“群集类型”，然后在“群集配置”中设置以下值：
   
    * **群集类型**：Kafka
    * 版本：Kafka 0.10.0 (HDI 3.6)

    最后使用“选择”按钮保存设置。
     
 ![选择群集类型](./media/apache-kafka-get-started/set-hdinsight-cluster-type.png)

4. 选择群集类型后，使用“选择”按钮设置群集类型。 接下来，使用“下一步”按钮完成基本配置。

5. 在“存储”中选择或创建存储帐户。 对于本文档中的步骤，请让其他字段保留默认值。 使用“下一步”按钮保存存储配置。

    ![设置 HDInsight 的存储帐户设置](./media/apache-kafka-get-started/set-hdinsight-storage-account.png)

6. 在“应用程序(可选)”中，选择“下一步”继续。 此示例不需要任何应用程序。

7. 在“群集大小”中，选择“下一步”继续。

    > [!WARNING]
    > 若要确保 Kafka on HDInsight 的可用性，群集必须至少包含 3 个辅助节点。 有关详细信息，请参阅[数据高可用性](#data-high-availability)部分。

    ![设置 Kafka 群集大小](./media/apache-kafka-get-started/kafka-cluster-size.png)

    > [!IMPORTANT]
    > “每个辅助角色节点的磁盘数”条目控制 Kafka on HDInsight 的可伸缩性。 Kafka on HDInsight 在群集中使用虚拟机的本地磁盘。 由于 Kafka 的 I/O 很高，因此会使用 [Azure 托管磁盘](../../virtual-machines/windows/managed-disks-overview.md)提供高吞吐量，并为每个节点提供更多存储。 托管磁盘的类型可以为“标准”(HDD) 或“高级”(SSD)。 高级磁盘可与 DS 和 GS 系列 VM 一起使用。 所有其他的 VM 类型使用“标准”。

8. 在“高级设置”中，选择“下一步”继续。

9. 在“摘要”中，查看群集的配置。 使用“编辑”链接更改不正确的设置。 最后，使用“创建”按钮创建群集。
   
    ![群集配置摘要](./media/apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > 创建群集可能需要 20 分钟。

## <a name="connect-to-the-cluster"></a>连接至群集

> [!IMPORTANT]
> 执行以下步骤时，必须使用 SSH 客户端。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)文档。

使用 SSH 从客户端连接到群集：

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

将 **SSHUSER** 替换为在群集创建期间提供的 SSH 用户名。 将 **CLUSTERNAME** 替换为群集的名称。

出现提示时，输入用于 SSH 帐户的密码。

有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a id="getkafkainfo"></a>获取 Zookeeper 主机和代理主机信息

使用 Kafka 时，必须了解两个主机值；Zookeeper 主机和代理主机。 这些主机配合 Kafka API 和 Kafka 随附的许多实用工具一起使用。

使用以下步骤创建包含的主机信息的环境变量。 这些环境变量在本文档的步骤中使用。

1. 与群集建立 SSH 连接后，使用以下命令安装 `jq` 实用工具。 此实用工具用于分析 JSON 文档且有助于检索代理主机的信息：
   
    ```bash
    sudo apt -y install jq
    ```

2. 若要使用从 Ambari 检索的信息设置环境变量，请执行以下命令：

    ```bash
    CLUSTERNAME='your cluster name'
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    > [!IMPORTANT]
    > 将 `CLUSTERNAME=` 设置为 Kafka 群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

    以下文本是 `$KAFKAZKHOSTS` 的内容示例：
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    以下文本是 `$KAFKABROKERS` 的内容示例：
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

    > [!NOTE]
    > `cut` 命令用于将主机列表剪裁为两个主机条目。 创建 Kafka 使用者或生成者时，不需提供主机的完整列表。
   
    > [!WARNING]
    > 不要期望从此会话中返回的信息始终准确。 若缩放群集，将添加或删除新的代理。 如果发生故障且替换节点，节点的主机名可能会改变。
    >
    > 应在检索 Zookeeper 和中转站主机信息后尽快使用这些信息，确保信息有效。

## <a name="create-a-topic"></a>创建主题

Kafka 在名为 topics 的类别中存储数据流。 与群集头节点建立 SSH 连接后，使用随 Kafka 提供的脚本创建主题：

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

此命令使用存储在 `$KAFKAZKHOSTS` 中的主机信息连接到 Zookeeper，并创建名为 **test** 的 Kafka 主题。 通过使用以下脚本列出主题可确认已创建该主题：

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

此命令的输出列出了 Kafka 主题，其中包含 **test** 主题。

## <a name="produce-and-consume-records"></a>生成和使用记录

Kafka 将记录存储在主题中。 记录由生成者生成，由使用者使用。 生成者将记录生成到 Kafka 代理。 HDInsight 群集中的每个辅助角色节点都是 Kafka 代理。

使用以下步骤将记录存储到之前创建的测试主题，并通过使用者对其进行读取：

1. 从 SSH 会话，使用随 Kafka 提供的脚本将记录写入主题：
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    执行此命令后不会返回到该提示符。 而是键入一些文本消息，并使用 **Ctrl + C** 停止发送到该主题。 每行均作为单独的记录发送。

2. 使用随 Kafka 提供的脚本从主题中读取记录：
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    此命令从主题中检索并显示记录。 使用 `--from-beginning` 告知使用者从流的开头开始，以检索所有记录。

    > [!NOTE]
    > 如果使用的是较旧版本的 Kafka，可能需要将 `--bootstrap-server $KAFKABROKERS` 替换为 `--zookeeper $KAFKAZKHOSTS`。

3. 使用 __Ctrl + C__ 阻止使用者。

还可以以编程方式创建生产者和使用者。 有关如何使用此 API 的示例，请参阅[将 Kafka 生产者和使用者 API 与 HDInsight 配合使用](apache-kafka-producer-consumer-api.md)文档。

## <a name="data-high-availability"></a>数据高可用性

每个 Azure 区域（位置）均提供_容错域_。 容错域是 Azure 数据中心基础硬件的逻辑分组。 每个容错域共享公用电源和网络交换机。 在 HDInsight 群集中实现节点的虚拟机和托管磁盘跨这些容错域分布。 此体系结构可限制物理硬件故障造成的潜在影响。

有关区域中容错域数的信息，请参阅 [Linux 虚拟机的可用性](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)文档。

> [!IMPORTANT]
> 建议使用包含三个容错域的 Azure 区域，并使用 3 作为复制因子。

如果必须使用只包含两个容错域的区域，请使用 4 作为复制因子，将副本均衡地分布到两个容错域中。

### <a name="kafka-and-fault-domains"></a>Kafka 和容错域

Kafka 不识别容错域。 在创建主题的分区副本时，它可能未针对高可用性正确分发副本。 若要确保高可用性，请使用 [Kafka 分区重新均衡工具](https://github.com/hdinsight/hdinsight-kafka-tools)。 必须通过 SSH 会话运行此工具，以便连接到 Kafka 群集的头节点。

若要确保 Kafka 数据的最高可用性，应该在以下时间为主题重新均衡分区副本：

* 创建新主题或分区时

* 扩展群集时

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](../hdinsight-administer-use-portal-linux.md#create-clusters)。

## <a name="next-steps"></a>后续步骤

在本文档中，已了解在 HDInsight 上使用 Apache Kafka 的基本知识。 使用以下内容，详细了解如何使用 Kafka：

* [分析 Kafka 日志](apache-kafka-log-analytics-operations-management.md)
* [在 Kafka 群集之间复制数据](apache-kafka-mirroring.md)
* [将 Kafka 生产者和使用者 API 与 HDInsight 配合使用](apache-kafka-producer-consumer-api.md)
* [将 Kafka 流 API 与 HDInsight 配合使用](apache-kafka-streams-api.md)
* [将 Apache Spark 流式处理 (DStream) 与 Kafka on HDInsight 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Spark 结构化流式处理与 Kafka on HDInsight 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [使用 Apache Spark 结构化流式处理将数据从 Kafka on HDInsight 移到 Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](../hdinsight-apache-storm-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md)
