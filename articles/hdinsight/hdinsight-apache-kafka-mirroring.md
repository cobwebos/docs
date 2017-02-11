---
title: "创建 Apache Kafka on HDInsight 群集的镜像 |Microsoft Docs"
description: "了解如何使用 Kafka 的镜像功能，通过在辅助群集中创建主题的镜像，保留一个 Kafka on HDInsight 群集的副本。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: e1c99bbe9d6317d83cc5e71ca4f79d862223aa0a
ms.openlocfilehash: 6110432cba7703b95342c9e8bf3b71cb0a377ad0

---
# <a name="use-mirrormaker-to-create-a-replica-of-a-kafka-on-hdinsight-cluster-preview"></a>使用 MirrorMaker 创建 Kafka on HDInsight 群集的副本（预览）

Apache Kafka 包含镜像功能，可用于将主题从一个 Kafka 群集复制到另一个群集。 例如，在不同 Azure 区域中的 Kafka 集群之间复制记录。

镜像可以作为连续进程运行，也可间断式地用作将数据从一个群集迁移到另一个群集的方法。

> [!WARNING]
> 不应将镜像视为实现容错的方法。 源和目标群集的主题内项目的偏移量不同，因此客户端不能将两者互换使用。
> 
> 如果担心容错，应在群集内为主题设置复制。 有关详细信息，请参阅 [Kafka on HDInsight 入门](hdinsight-apache-kafka-get-started.md)。

## <a name="prerequisites"></a>先决条件

* Azure 虚拟网络：源和目标 Kafka 群集必须能够直接相互通信。 HDInsight 不会在 Internet 上公开 Kafka API，因此源和目标群集必须存在于同一 Azure 虚拟网络中。

* 两个 Kafka 群集：本文档使用 Azure Resource Manager 模板在 Azure 虚拟网络中创建两个 Kafka on HDInsight 群集。

## <a name="how-does-mirroring-work"></a>镜像如何工作？

镜像通过使用 MirrorMaker 工具（Apache Kafka 的一部分）来使用源群集上主题中的记录，然后在目标群集上创建本地副本。 MirrorMaker 使用从源群集进行读取的一个（或多个）使用者，以及会写入本地（目标）群集的一个创建器。

下图说明镜像过程：

![镜像过程图示](./media/hdinsight-apache-kafka-mirroring/kafka-mirroring.png)

源和目标集群在节点数和分区数上可能不同，并且主题内的偏移量也不同。 镜像维护用于分区的密钥值，因此会按密钥来保留记录顺序。

### <a name="mirroring-between-networks"></a>网络之间的镜像

如果需要在不同网络中的 Kafka 群集之间执行镜像操作，还需要考虑以下注意事项：

* **网关**：网络必须能够在 TCPIP 级别进行通信。

* **名称解析**：每个网络中的 Kafka 群集必须能够使用主机名相互连接。 这可能需要每个网络中的域名系统 (DNS) 服务器，这些网络配置为将请求转发到其他网络。 
  
    创建 Azure 虚拟网络时，必须指定自定义 DNS 服务器和服务器的 IP 地址，而不是使用网络提供的自动 DNS。 创建虚拟网络后，必须创建一个使用该 IP 地址的 Azure 虚拟机，然后在其上安装和配置 DNS 软件。
  
    > [!WARNING]
    > 在将 HDInsight 安装到虚拟网络之前，需先创建和配置自定义 DNS 服务器。 HDInsight 不需要再进行其他配置，便可使用为虚拟网络配置的 DNS 服务器。

有关连接两个 Azure 虚拟网络的详细信息，请参阅[配置 VNet 到 VNet 的连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="create-kafka-clusters"></a>创建 Kafka 群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 服务的权限。 与 Kafka 对话的任何内容都必须与 Kafka 群集中的节点位于同一 Azure 虚拟网络中。 对于此示例，Kafka 源和目标群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的源和目标 Kafka 群集的图示](./media/hdinsight-apache-kafka-mirroring/spark-kafka-vnet.png)

> [!NOTE]
> 虽然 Kafka 本身受限于虚拟网络中的通信，但可以通过 Internet 访问群集上的其他服务（例如 SSH 和 Ambari）。 有关可用于 HDInsight 的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

虽然可手动创建 Azure 虚拟网络和 Kafka 群集，但使用 Azure Resource Manager 模板会更简单。 使用以下步骤将 Azure 虚拟网络和两个 Kafka 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，然后在 Azure 门户中打开模板。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure Resource Manager 模板位于 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet.json**。

2. 使用以下信息来填充“自定义部署”边栏选项卡上的项：
    
    ![HDInsight 自定义部署](./media/hdinsight-apache-kafka-mirroring/parameters.png)
    
    * **资源组**：创建一个资源组或选择现有的资源组。 此组包含 HDInsight 群集。

    * **位置**：选择在地理上邻近的位置。 此位置必须匹配“设置”部分中的位置。
     
    * **基群集名称**：此值将用作 Kafka 群集的基名称。 例如，输入 **hdi** 会创建名为 **source-hdi** 和 **dest-hdi** 的群集。

    * **群集登录用户名**：源和目标 Kafka 群集的管理员用户名。

    * **群集登录密码**：源和目标 Kafka 群集的管理员用户密码。

    * **SSH 用户名**：要创建的源和目标 Kafka 群集的 SSH 用户。

    * **SSH 密码**：源和目标 Kafka 群集的 SSH 用户的密码。

    * **位置**：在其中创建群集的区域。

3. 阅读“条款和条件”，然后选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，然后选择“购买”。 创建群集大约需要 20 分钟时间。

创建资源后，会重定向到包含群集和 Web 仪表板的资源组边栏选项卡。

![虚拟网络和群集的“资源组”边栏选项卡](./media/hdinsight-apache-kafka-mirroring/groupblade.png)

> [!IMPORTANT]
> 请注意，HDInsight 群集的名称为 **source-BASENAME** 和 **dest-BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。

## <a name="create-topics"></a>创建主题

1. 使用 SSH 连接到**源**群集：
   
        ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
   
    用创建群集时使用的 SSH 用户名替换 **sshuser**。 用创建群集时使用的基名称替换 **BASENAME**。
   
    有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文档：
   
    * [在 Linux、Mac OS 或 Unix 客户端中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [在 Windows 客户端中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 使用以下命令查找 Zookeeper 主机，设置 `SOURCE_ZKHOSTS` 变量，然后创建数个名为 `testtopic` 的新主题：
   
        # Get a list of zookeeper hosts for the source cluster
        SOURCE_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
        # Create a topic on the source cluster
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS

3. 使用以下命令验证是否已创建该主题：
   
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
   
    响应包含 `testtopic`。

4. 使用以下命令查看此（**源**）群集的 Zookeeper 主机信息：
   
        echo $SOURCE_ZKHOSTS
   
    这会返回类似于以下文本的信息：
   
        zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk6-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181
   
    请保存此信息。 在下一部分中使用。

## <a name="configure-mirroring"></a>配置镜像

1. 使用 SSH 连接到**目标**群集：
   
        ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
   
    用创建群集时使用的 SSH 用户名替换 **sshuser**。 用创建群集时使用的基名称替换 **BASENAME**。
   
    有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文档：
   
    * [在 Linux、Mac OS 或 Unix 客户端中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * [在 Windows 客户端中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 使用以下命令创建介绍如何与**源**群集通信的 `consumer.config` 文件：
   
        nano consumer.config
   
    将以下文本用作 `consumer.config` 文件的内容：
   
        zookeeper.connect=SOURCE_ZKHOSTS
        group.id=mirrorgroup
   
    使用**源**群集中的 Zookeeper 主机信息替换 **SOURCE_ZKHOSTS**。
   
    此文件描述从源 Kafka 群集读取时要使用的使用者信息。 有关使用者配置的详细信息，请参阅 kafka.apache.org 中的[使用者配置](https://kafka.apache.org/documentation#consumerconfigs)。
   
    依次按 **Ctrl-X**、**Y** 和 Enter 以保存文件。

3. 配置与目标群集通信的创建器之前，必须找到**目标**群集的中转站主机。 使用以下命令检索此信息：
   
        # Install JQ for parsing JSON documents
        sudo apt -y install jq
        # Get the broker information for the destination cluster
        DEST_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
        # Display the information
        echo $DEST_BROKERHOSTS
   
    这些命令返回类似于以下的信息：
   
        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. 使用以下命令创建描述如何与**目标**群集通信的 `producer.config` 文件：
   
        bootstrap.servers=DEST_BROKERS
        compression.type=none
   
    使用上一步中获取的中转站信息替换 **DEST_BROKERS**。 
   
    有关创建器配置的详细信息，请参阅 kafka.apache.org 中的[创建器配置](https://kafka.apache.org/documentation#producerconfigs)。

## <a name="start-mirrormaker"></a>启动 MirrorMaker

1. 从**目标**群集的 SSH 连接开始，使用以下命令启动 MirrorMaker 过程：
   
        /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
   
    此示例中使用的参数有：
   
    * **--consumer.config**：指定包含使用者属性的文件。 这些属性用于创建从*源* Kafka 群集进行读取的使用者。

    * **--producer.config**：指定包含创建器属性的文件。 这些属性用于创建写入*目标* Kafka 群集的创建器。

    * **--whitelist**：MirrorMaker 从源群集复制到目标群集的主题的列表。
    
    * **--num.streams**：要创建的使用者线程数。
     
    启动时，MirrorMaker 返回类似于以下文本的信息：
        
    ```{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}  metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. From the SSH connection to the **source** cluster, use the following command to start a producer and send messages to the topic:
   
        # Install JQ for working with JSON
        sudo apt -y install jq
        # Retrieve the Kafka brokers
        SOURCE_BROKERHOSTS=`sudo bash -c 'ls /var/lib/ambari-agent/data/command-[0-9]*.json' | tail -n 1 | xargs sudo cat | jq -r '["\(.clusterHostInfo.kafka_broker_hosts[]):9092"] | join(",")'`
        # Start a producer
        /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
   
    When you arrive at a blank line with a cursor, type in a few text messages. These are sent to the topic on the **source** cluster. When done, use **Ctrl + C** to end the producer process.

3. From the SSH connection to the **destination** cluster, use **Ctrl + C** to end the MirrorMaker process. Then use the following commands to verify that the `testtopic` topic was created, and that data in the topic was replicated to this mirror:
   
        # Get a list of zookeeper hosts for the destination cluster
        DEST_ZKHOSTS=`grep -R zk /etc/hadoop/conf/yarn-site.xml | grep 2181 | grep -oPm1 "(?<=<value>)[^<]+"`
        # List topics on destination
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $DEST_ZKHOSTS
        # Retrieve messages from the `testtopic`
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
   
    The list of topics will now include `testtopic`, which is created when MirrorMaster mirrors the topic from the source cluster to the destination. The messages retrieved from the topic are the same as entered on the source cluster.

## Delete the cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Since the steps in this document create both clusters in the same Azure resource group, you can delete the resource group in the Azure portal. Deleting the resource group removes all resources created by following this document, the Azure Virtual Network, and storage account used by the clusters.

## Next Steps

In this document, you learned how to use MirrorMaker to create a replica of a Kafka cluster. Use the following links to discover other ways to work with Kafka:

* [Apache Kafka MirrorMaker documentation](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) at cwiki.apache.org.
* [Get started with Apache Kafka on HDInsight](hdinsight-apache-kafka-get-started.md)
* [Use Apache Spark with Kafka on HDInsight](hdinsight-apache-spark-with-kafka.md)
* [Use Apache Storm with Kafka on HDInsight](hdinsight-apache-storm-with-kafka.md)




<!--HONumber=Nov16_HO3-->


