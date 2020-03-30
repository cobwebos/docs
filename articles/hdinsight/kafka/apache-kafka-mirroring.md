---
title: 镜像 Apache Kafka 主题 - Azure HDInsight
description: 了解如何使用 Apache Kafka 的镜像功能，通过在辅助群集中创建主题的镜像，保留一个 Kafka on HDInsight 群集的副本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425878"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>使用 MirrorMaker 通过 Kafka on HDInsight 复制 Apache Kafka 主题

了解如何使用 Apache Kafka 镜像功能将主题复制到辅助群集。 镜像可以作为连续进程运行，也可间断式地用作将数据从一个群集迁移到另一个群集的方法。

在此示例中，镜像用于在两个 HDInsight 群集之间复制主题。 这两个群集位于不同数据中心内的不同虚拟网络中。

> [!WARNING]  
> 不应将镜像视为实现容错的方法。 主题中项的偏移在主要群集与辅助群集之间有所不同，因此客户端不能换用这两种群集。
>
> 如果担心容错，应在群集内为主题设置复制。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)。

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 镜像的工作原理

镜像通过使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 工具（Apache Kafka 的一部分）来使用主要群集上主题中的记录，然后在辅助群集上创建本地副本。 MirrorMaker 使用一个或多个使用者从主要群集读取记录，使用生成者将记录写入本地（辅助）群集。****

最有用的灾难恢复镜像设置利用不同 Azure 区域中的 Kafka 群集。 为实现此目的，群集所在的虚拟网络将对等互连到一起。

下图演示了镜像过程以及群集之间的通信流动方式：

![镜像过程图示](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

主要和辅助群集在节点与分区数目方面可以不同，主题中的偏移也可以不同。 镜像维护用于分区的密钥值，因此会按密钥来保留记录顺序。

### <a name="mirroring-across-network-boundaries"></a>跨网络边界执行镜像操作

如果需要在不同网络中的 Kafka 群集之间执行镜像操作，还需要考虑以下注意事项：

* **网关**：网络必须能够在 TCP/IP 级别进行通信。

* **服务器寻址**：您可以选择使用群集节点的 IP 地址或完全限定的域名寻址。

    * **IP 地址**：如果将 Kafka 群集配置为使用 IP 地址广告，则可以使用代理节点和动物园管理员节点的 IP 地址继续进行镜像设置。
    
    * **域名**：如果您未为 IP 地址广告配置 Kafka 群集，群集必须能够使用完全限定域名 （FQDN） 相互连接。 这需要在每个网络中设置一台域名系统 (DNS) 服务器，并将其配置为向其他网络转发请求。 创建 Azure 虚拟网络时，必须指定自定义 DNS 服务器和服务器的 IP 地址，而不是使用网络提供的自动 DNS。 创建虚拟网络后，必须创建一个使用该 IP 地址的 Azure 虚拟机，并在其上安装和配置 DNS 软件。

    > [!WARNING]  
    > 在将 HDInsight 安装到虚拟网络之前，需先创建和配置自定义 DNS 服务器。 HDInsight 不需要再进行其他配置，便可使用为虚拟网络配置的 DNS 服务器。

有关连接两个 Azure 虚拟网络的详细信息，请参阅[配置 VNet 到 VNet 的连接](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="mirroring-architecture"></a>镜像体系结构

此体系结构在不同的资源组和虚拟网络中配置两个群集：**主要群集**和**辅助群集**。

### <a name="creation-steps"></a>创建步骤

1. 创建两个新的资源组：

    |资源组 | 位置 |
    |---|---|
    | kafka-primary-rg | 美国中部 |
    | kafka-secondary-rg | 美国中北部 |

1. 在 **kafka-primary-rg** 中创建新的虚拟网络 **kafka-primary-vnet**。 保留默认设置。
1. 在 **kafka-secondary-rg** 中创建新的虚拟网络 **kafka-secondary-vnet**，也保留默认设置。

1. 创建两个新的 Kafka 群集：

    | 群集名称 | 资源组 | 虚拟网络 | 存储帐户 |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. 创建虚拟网络对等互连。 此步骤创建两个对等互连：一个从 **kafka-primary-vnet** 连接到 **kafka-secondary-vnet**，一个从 **kafka-secondary-vnet** 连接回到 **kafka-primary-vnet**。
    1. 选择“kafka-primary-vnet”虚拟网络。****
    1. 在“设置”**** 下，选择“对等互连”****。
    1. 选择“添加”****。
    1. 在“添加对等互连”屏幕上输入详细信息，如以下屏幕截图所示。****

        ![HDInsight 卡夫卡添加 vnet 对等互连](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>配置 IP 广告

配置 IP 广告以使客户端能够使用代理 IP 地址而不是域名进行连接。

1. 转到主要群集的 Ambari 仪表板：`https://PRIMARYCLUSTERNAME.azurehdinsight.net`。
1. 选择**服务** > **卡夫卡**。 单击“配置”选项卡 ****。
1. 将以下配置行添加到底部的 **kafka-env template** 节。 选择“保存”。****

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 在“保存配置”屏幕上输入备注，然后单击“保存”。********
1. 如果出现配置警告提示，请单击“仍然继续”。****
1. 在“保存配置更改”屏幕上选择“确定”。********
1. 选择 **"重新启动** > **重新启动所有受影响的"****通知。** 选择 **"确认全部重新启动**"。

    ![Apache Ambari 重新启动所有受影响的服务](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>将 Kafka 配置为侦听所有网络接口。
    
1. 不要关闭“服务” > “Kafka”下的“配置”选项卡。************ 在“Kafka 代理”部分，将“侦听器”属性设置为 `PLAINTEXT://0.0.0.0:9092`。********
1. 选择“保存”。****
1. 依次选择“重启”、“确认全部重启”。********

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>记下主要群集的代理 IP 地址和 Zookeeper 地址。

1. 在 Ambari 仪表板上选择“主机”。****
1. 记下代理和 Zookeeper 的 IP 地址。 代理节点主机名的前两个字母为 **wn**，Zookeeper 节点主机名的前两个字母为 **zk**。

    ![阿帕奇·安巴里视图节点 ip 地址](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. 对第二个群集 **kafka-secondary-cluster** 重复上述三个步骤：配置 IP 播发、设置侦听器并记下代理和 Zookeeper 的 IP 地址。

## <a name="create-topics"></a>创建主题

1. 使用 SSH 连接到**主要**群集：

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    用创建群集时使用的 SSH 用户名替换 **sshuser**。 将 **PRIMARYCLUSTER** 替换为创建群集时使用的基名称。

    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 使用以下命令创建一个变量，其中包含主要群集的 Apache Zookeeper 主机。 必须将类似于 `ZOOKEEPER_IP_ADDRESS1` 的字符串替换为前面记下的实际 IP 地址，例如 `10.23.0.11` 和 `10.23.0.7`。 如果您使用 FQDN 解析与自定义 DNS 服务器，请按照[以下步骤](apache-kafka-get-started.md#getkafkainfo)获取代理和动物园管理员名称：

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. 若要创建名为 `testtopic` 的主题，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. 使用以下命令验证是否已创建该主题：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    响应包含 `testtopic`。

1. 使用以下命令查看此（**主要**）群集的 Zookeeper 主机信息：

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    这会返回类似于以下文本的信息：

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    请保存此信息。 它用于下一节。

## <a name="configure-mirroring"></a>配置镜像

1. 使用不同的 SSH 会话连接到**辅助**群集：

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    用创建群集时使用的 SSH 用户名替换 **sshuser**。 将 **SECONDARYCLUSTER** 替换为创建群集时使用的名称。

    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

1. `consumer.properties` 文件用于配置与**主要**群集的通信。 若要创建文件，请使用以下命令：

    ```bash
    nano consumer.properties
    ```

    将以下文本用作 `consumer.properties` 文件的内容：

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    将 **PRIMARY_ZKHOSTS** 替换为**主要**群集中的 Zookeeper IP 地址。

    此文件说明从 Kafka 主要群集读取记录时要使用的使用者信息。 有关使用者配置的详细信息，请参阅 kafka.apache.org 中的[使用者配置](https://kafka.apache.org/documentation#consumerconfigs)。

    要保存文件，请使用 **Ctrl + X**、**Y**，并按 **Enter**。

1. 在配置与辅助群集通信的生产者之前，为**辅助**群集的代理 IP 地址设置变量。 使用以下命令创建此变量：

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    命令 `echo $SECONDARY_BROKERHOSTS` 返回的信息应类似于以下文本：

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. `producer.properties` 文件用于与**辅助**群集通信。 若要创建文件，请使用以下命令：

    ```bash
    nano producer.properties
    ```

    将以下文本用作 `producer.properties` 文件的内容：

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    将 **SECONDARY_BROKERHOSTS** 替换为在上一步骤中使用的代理 IP 地址。

    有关创建器配置的详细信息，请参阅 kafka.apache.org 中的[创建器配置](https://kafka.apache.org/documentation#producerconfigs)。

1. 使用以下命令创建一个环境变量，其中包含辅助群集的 Zookeeper 主机 IP 地址：

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. HDInsight 上的 Kafka 的默认配置不允许自动创建主题。 在开始镜像过程之前，你必须使用以下选项之一：

    * **在辅助群集上创建主题**：此选项还允许您设置分区数和复制因子。

        可以使用以下命令提前创建新的主题：

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        将 `testtopic` 替换为要创建的主题的名称。

    * **为自动主题创建配置群集**：此选项允许 MirrorMaker 自动创建主题，但它可能会使用与主主题不同的分区或复制因子创建主题。

        若要配置辅助群集以自动创建主题，请执行以下步骤：

        1. 转到辅助群集的 Ambari 仪表板：`https://SECONDARYCLUSTERNAME.azurehdinsight.net`。
        1. 单击**服务** > **卡夫卡**。 单击“配置”**** 选项卡。
        1. 在“筛选器”____ 字段中输入值 `auto.create`。 这将筛选的属性，并显示列表`auto.create.topics.enable`设置。
        1. 更改的值`auto.create.topics.enable`为 true，然后选择__保存__。 添加注释，然后选择__保存__。
        1. 选择 __Kafka__ 服务，选择__重启__，然后选择__重启所有受影响的__。 当出现提示时，选择 __"确认全部重新启动__"。

        ![卡夫卡启用自动创建主题](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>启动 MirrorMaker

1. 与**辅助**群集建立 SSH 连接后，使用以下命令启动 MirrorMaker 进程：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    此示例中使用的参数有：

    |参数 |描述 |
    |---|---|
    |--consumer.config|指定包含使用者属性的文件。 这些属性用于创建可从*主要* Kafka 群集读取记录的使用者。|
    |--producer.config|指定包含创建者属性的文件。 这些属性用于创建可向*辅助* Kafka 群集写入记录的生成者。|
    |--whitelist|MirrorMaker 从主要群集复制到辅助群集的主题列表。|
    |--num.streams|要创建的使用者线程数。|

    现在，辅助节点上的使用者正在等待接收消息。

2. 与**主要**群集建立 SSH 连接后，使用以下命令启动生成者，并向主题发送消息：

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     出现带有光标的空行时，请键入几条文本消息。 这些消息将发送到**主要**群集上的主题。 完成后，使用 **Ctrl + C** 结束生成者进程。

3. 与**辅助**群集建立 SSH 连接后，使用 **Ctrl + C** 结束 MirrorMaker 进程。 它可能需要几秒钟时间结束进程。 若要验证是否已将主题和消息复制到辅助群集，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    主题列表现在包含 `testtopic`，该条目是在 MirrorMaster 将主题从主要群集镜像到辅助群集时创建的。 从主题检索到的消息与在主要群集上输入的消息相同。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

本文档中的步骤已在不同的 Azure 资源组中创建了群集。 若要删除创建的所有资源，可以删除创建的两个资源组：**kafka-primary-rg** 和 **kafka-secondary_rg**。 删除资源组会删除遵循本文档创建的所有资源，包括群集、虚拟网络和存储帐户。

## <a name="next-steps"></a>后续步骤

本文档已介绍如何使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 创建 [Apache Kafka](https://kafka.apache.org/) 群集的副本。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* cwiki.apache.org 上的 [Apache Kafka MirrorMaker 文档](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)。
* [卡夫卡镜制造商最佳实践](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)
* [将 Apache Spark 与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Apache Kafka](apache-kafka-connect-vpn-gateway.md)
