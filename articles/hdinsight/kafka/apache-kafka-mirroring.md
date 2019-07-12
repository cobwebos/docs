---
title: 镜像 Apache Kafka 主题 - Azure HDInsight
description: 了解如何使用 Apache Kafka 的镜像功能，通过在辅助群集中创建主题的镜像，保留一个 Kafka on HDInsight 群集的副本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.openlocfilehash: bdc393d041bd40fd27493ccc8f3c4f39adfa35b2
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657160"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>使用 MirrorMaker 通过 Kafka on HDInsight 复制 Apache Kafka 主题

了解如何使用 Apache Kafka 镜像功能将主题复制到辅助群集。 镜像可以作为连续进程运行，也可间断式地用作将数据从一个群集迁移到另一个群集的方法。

在此示例中，镜像用于在两个 HDInsight 群集之间复制主题。 这两个群集位于不同数据中心内的不同虚拟网络。

> [!WARNING]  
> 不应将镜像视为实现容错的方法。 本主题中的项的偏移量是主要和次要群集之间的差异，因此客户端不能交替使用这两个。
>
> 如果担心容错，应在群集内为主题设置复制。 有关详细信息，请参阅 [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)。

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 镜像的工作原理

使用镜像的工作原理[MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)工具 （Apache Kafka 的一部分） 使用主群集上的主题中的记录，然后辅助群集上创建的本地副本。 MirrorMaker 使用一个 （或多个）*使用者*主群集中读取和一个*制造者*，它将写入到本地 （辅助） 群集。

灾难恢复的最有用的镜像设置利用不同 Azure 区域中的 Kafka 群集。 若要实现此目的，群集所在的位置的虚拟网络对等互连在一起。

下图说明镜像过程和通信群集之间的流动方式：

![镜像过程图示](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

主要和次要群集可以是不同的节点和分区，数并且主题内的偏移量也是不同。 镜像维护用于分区的密钥值，因此会按密钥来保留记录顺序。

### <a name="mirroring-across-network-boundaries"></a>跨网络边界执行镜像操作

如果需要在不同网络中的 Kafka 群集之间执行镜像操作，还需要考虑以下注意事项：

* **网关**：网络必须能够在 TCP/IP 级别进行通信。

* **服务器寻址**:您可以选择地址在群集节点上使用其 IP 地址或完全限定的域名。

    * **IP 地址**：如果配置 Kafka 群集，使用 IP 地址播发，您可以继续进行镜像设置使用的代理节点和 zookeeper 节点的 IP 地址。
    
    * **域名**:如果未配置为 IP 地址播发 Kafka 群集，群集必须能够通过使用完全限定域名 (Fqdn) 连接到对方。 这要求每个配置为将请求转发到其他网络的网络中的域名系统 (DNS) 服务器。 创建 Azure 虚拟网络时，必须指定自定义 DNS 服务器和服务器的 IP 地址，而不是使用网络提供的自动 DNS。 创建虚拟网络后，必须创建一个使用该 IP 地址的 Azure 虚拟机，并在其上安装和配置 DNS 软件。

    > [!WARNING]  
    > 在将 HDInsight 安装到虚拟网络之前，需先创建和配置自定义 DNS 服务器。 HDInsight 不需要再进行其他配置，便可使用为虚拟网络配置的 DNS 服务器。

有关连接两个 Azure 虚拟网络的详细信息，请参阅[配置 VNet 到 VNet 的连接](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="mirroring-architecture"></a>镜像体系结构

此体系结构具有两个群集位于不同的资源组和虚拟网络：**主**并**辅助**。

### <a name="creation-steps"></a>创建步骤

1. 创建两个新资源组：

    |资源组 | Location |
    |---|---|
    | kafka-primary-rg | 美国中部 |
    | kafka-secondary-rg | 美国中北部 |

1. 创建新的虚拟网络**kafka 主 vnet**中**kafka 主 rg**。 保留默认设置。
1. 创建新的虚拟网络**kafka 辅助 vnet**中**kafka 辅助 rg**，还使用默认设置。

1. 创建两个新的 Kafka 群集：

    | 群集名称 | 资源组 | 虚拟网络 | 存储帐户 |
    |---|---|---|---|
    | kafka-primary-cluster | kafka-primary-rg | kafka-primary-vnet | kafkaprimarystorage |
    | kafka-secondary-cluster | kafka-secondary-rg | kafka-secondary-vnet | kafkasecondarystorage |

1. 创建虚拟网络对等互连。 此步骤将创建两个对等互连： 一个来自**kafka 主 vnet**到**kafka 辅助 vnet**和一个从回复**kafka 辅助 vnet**到**kafka 主 vnet**。
    1. 选择**kafka 主 vnet**虚拟网络。
    1. 单击**对等互连**下**设置**。
    1. 单击“添加”  。
    1. 上**添加对等互连**屏幕上，输入的详细信息，如下面的屏幕截图中所示。

        ![添加 vnet 对等互连](./media/apache-kafka-mirroring/add-vnet-peering.png)

1. 配置 IP 播发：
    1. 转到主群集的 Ambari 仪表板： `https://PRIMARYCLUSTERNAME.azurehdinsight.net`。
    1. 单击**Services** > **Kafka**。 单击“配置”  选项卡。
    1. 将以下配置行添加到底部**于 kafka-env 模板**部分。 单击“保存”  。
    
        ```
        # Configure Kafka to advertise IP addresses instead of FQDN
        IP_ADDRESS=$(hostname -i)
        echo advertised.listeners=$IP_ADDRESS
        sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
        echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
        ```

    1. 输入的注释**保存配置**屏幕上，单击**保存**。
    1. 如果系统提示配置警告，请单击**仍然继续**。
    1. 单击**确定**上**保存配置更改**。
    1. 单击**重新启动** > **重启所有受影响**中**需要重启**通知。 单击**确认重启所有**。

        ![重启 kafka 节点](./media/apache-kafka-mirroring/ambari-restart-notification.png)

1. 配置 Kafka 侦听所有网络接口。
    1. 请选择**Configs**选项卡上的**Services** > **Kafka**。 在中**Kafka 中转站**部分设置**侦听器**属性设置为`PLAINTEXT://0.0.0.0:9092`。
    1. 单击“保存”  。
    1. 单击**重新启动**，并**确认重新启动所有**。

1. 记录代理 IP 地址和主群集的 Zookeeper 地址。
    1. 单击**主机**Ambari 仪表板上。
    1. 记下的中转站和 Zookeeper 的 IP 地址。 代理节点具有**wn**作为前两个字母的主机名和 zookeeper 节点具有**zk**作为主机名的前两个字母。

        ![查看 ip 地址](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. 对第二个群集重复前面三个步骤**kafka 辅助群集**： 配置 IP 播发、 设置侦听器并记下的中转站和 Zookeeper IP 地址。

## <a name="create-topics"></a>创建主题

1. 连接到**主**使用 SSH 群集：

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    用创建群集时使用的 SSH 用户名替换 **sshuser**。 将 **BASENAME** 替换为创建群集时使用的基名称。

    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用以下命令以创建与主群集的 Apache Zookeeper 主机的变量。 字符串喜欢`ZOOKEEPER_IP_ADDRESS1`必须替换为之前记录的实际 IP 地址等`10.23.0.11`和`10.23.0.7`。 如果使用自定义 DNS 服务器使用 FQDN 解析，请按照[这些步骤](apache-kafka-get-started.md#getkafkainfo)获取中转站和 zookeeper 名称。:

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

3. 若要创建名为 `testtopic` 的主题，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

3. 使用以下命令验证是否已创建该主题：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    响应包含 `testtopic`。

4. 使用以下命令查看此的 Zookeeper 主机信息 (**主**) 群集：

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    这会返回类似于以下文本的信息：

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    请保存此信息。 在下一部分中使用。

## <a name="configure-mirroring"></a>配置镜像

1. 连接到**辅助**群集使用不同的 SSH 会话：

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    用创建群集时使用的 SSH 用户名替换 **sshuser**。 替换**SECONDARYCLUSTER**与创建群集时使用的名称。

    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 一个`consumer.properties`文件用于配置与通信**主**群集。 若要创建文件，请使用以下命令：

    ```bash
    nano consumer.properties
    ```

    将以下文本用作 `consumer.properties` 文件的内容：

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    替换**PRIMARY_ZKHOSTS** Zookeeper IP 地址从**主**群集。

    此文件描述从主要的 Kafka 群集读取时要使用的使用者信息。 有关使用者配置的详细信息，请参阅 kafka.apache.org 中的[使用者配置](https://kafka.apache.org/documentation#consumerconfigs)。

    要保存文件，请使用 **Ctrl + X**、**Y**，并按 **Enter**。

3. 在配置与辅助群集通信的生成者之前, 安装的代理 IP 地址的变量**辅助**群集。 使用以下命令创建此变量：

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    该命令`echo $SECONDARY_BROKERHOSTS`应返回类似于以下文本的信息：

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

4. 一个`producer.properties`文件用于通信**辅助**群集。 若要创建文件，请使用以下命令：

    ```bash
    nano producer.properties
    ```

    将以下文本用作 `producer.properties` 文件的内容：

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    替换**SECONDARY_BROKERHOSTS**与上一步中使用的代理 IP 地址。

    有关创建器配置的详细信息，请参阅 kafka.apache.org 中的[创建器配置](https://kafka.apache.org/documentation#producerconfigs)。

5. 使用以下命令来与辅助群集的 Zookeeper 主机的 IP 地址创建一个环境变量：

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

7. Kafka 在 HDInsight 上的默认配置不允许自动创建的主题。 在开始镜像过程之前，你必须使用以下选项之一：

    * **辅助群集上创建的主题**:还可使用此选项设置分区和复制因子的数目。

        可以使用以下命令提前创建新的主题：

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        将 `testtopic` 替换为要创建的主题的名称。

    * **将群集配置为自动创建主题**：此选项允许 MirrorMaker 来自动创建的主题，但它可能使用不同数量的分区或复制因子比主要主题创建它们。

        若要配置辅助群集来自动创建的主题，请执行以下步骤：

        1. 转到辅助群集的 Ambari 仪表板： `https://SECONDARYCLUSTERNAME.azurehdinsight.net`。
        1. 单击**Services** > **Kafka**。 单击“配置”  选项卡。
        5. 在“筛选器”  字段中输入值 `auto.create`。 这将筛选的属性，并显示列表`auto.create.topics.enable`设置。
        6. 更改的值`auto.create.topics.enable`为 true，然后选择__保存__。 添加注释，然后选择__保存__。
        7. 选择 __Kafka__ 服务，选择__重启__，然后选择__重启所有受影响的__。 出现提示时，选择“确认全部重启”  。

        ![配置自动创建主题](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>启动 MirrorMaker

1. 通过 SSH 连接到**辅助**群集，请使用以下命令启动 MirrorMaker 过程：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    此示例中使用的参数有：

    * **--consumer.config**：指定包含使用者属性的文件。 这些属性用于创建使用者，用于读取来自*主*Kafka 群集。

    * **--producer.config**：指定包含创建者属性的文件。 这些属性用于创建将写入到生成者*辅助*Kafka 群集。

    * **--whitelist**：MirrorMaker 将从主群集复制到辅助数据库的主题的列表。

    * **--num.streams**：要创建的使用者线程数。

    辅助节点上的使用者现在正在等待接收消息。

2. 通过 SSH 连接到**主**群集，请使用以下命令启动生成者并将消息发送到主题：

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     出现带有光标的空行时，请键入几条文本消息。 将消息发送到主题**主**群集。 完成后，使用 **Ctrl + C** 结束生成者进程。

3. 通过 SSH 连接到**辅助**群集，请使用**Ctrl + C**结束 MirrorMaker 进程。 它可能需要几秒钟时间结束进程。 若要验证消息已复制到辅助数据库，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    主题列表现在包括`testtopic`，它在 MirrorMaster 镜像到辅助数据库的主群集中的主题时创建。 从主题检索到的消息都与您在主群集输入的相同。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

本文档中的步骤在不同的 Azure 资源组中创建群集。 若要删除所有创建的资源，可以删除创建的两个资源组： **kafka 主 rg**并**kafka secondary_rg**。 删除资源组中移除所有按照本文档中，包括群集、 虚拟网络和存储帐户创建的资源。

## <a name="next-steps"></a>后续步骤

本文档已介绍如何使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 创建 [Apache Kafka](https://kafka.apache.org/) 群集的副本。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* cwiki.apache.org 上的 [Apache Kafka MirrorMaker 文档](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)。
* [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)
* [将 Apache Spark 与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Storm 与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-storm-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Apache Kafka](apache-kafka-connect-vpn-gateway.md)
