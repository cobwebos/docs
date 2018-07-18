---
title: 镜像 Apache Kafka 主题 - Azure HDInsight | Microsoft Docs
description: 了解如何使用 Apache Kafka 的镜像功能，通过在辅助群集中创建主题的镜像，保留一个 Kafka on HDInsight 群集的副本。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 015d276e-f678-4f2b-9572-75553c56625b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2018
ms.author: larryfr
ms.openlocfilehash: 9fbf4364e22c0b25d224ee0961f7e7ee13ddcef8
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32778863"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>使用 MirrorMaker 通过 Kafka on HDInsight 复制 Apache Kafka 主题

了解如何使用 Apache Kafka 镜像功能将主题复制到辅助群集。 镜像可以作为连续进程运行，也可间断式地用作将数据从一个群集迁移到另一个群集的方法。

在此示例中，镜像用于在两个 HDInsight 群集之间复制主题。 这两个群集位于同一区域的 Azure 虚拟网络中。

> [!WARNING]
> 不应将镜像视为实现容错的方法。 源和目标群集的主题内项目的偏移量不同，因此客户端不能将两者互换使用。
>
> 如果担心容错，应在群集内为主题设置复制。 有关详细信息，请参阅 [Kafka on HDInsight 入门](apache-kafka-get-started.md)。

## <a name="how-kafka-mirroring-works"></a>Kafka 镜像的工作原理

镜像通过使用 MirrorMaker 工具（Apache Kafka 的一部分）来使用源群集上主题中的记录，并在目标群集上创建本地副本。 MirrorMaker 使用从源群集进行读取的一个（或多个）使用者，以及会写入本地（目标）群集的一个创建器。

下图说明镜像过程：

![镜像过程图示](./media/apache-kafka-mirroring/kafka-mirroring.png)

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 服务的权限。 Kafka 生成者或使用者必须与 Kafka 群集中的节点在同一 Azure 虚拟网络中。 对于此示例，Kafka 源和目标群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的源和目标 Kafka 群集的图示](./media/apache-kafka-mirroring/spark-kafka-vnet.png)

源和目标集群在节点数和分区数上可能不同，并且主题内的偏移量也不同。 镜像维护用于分区的密钥值，因此会按密钥来保留记录顺序。

### <a name="mirroring-across-network-boundaries"></a>跨网络边界执行镜像操作

如果需要在不同网络中的 Kafka 群集之间执行镜像操作，还需要考虑以下注意事项：

* **网关**：网络必须能够在 TCPIP 级别进行通信。

* **名称解析**：每个网络中的 Kafka 群集必须能够使用主机名相互连接。 这可能需要每个网络中的域名系统 (DNS) 服务器，这些网络配置为将请求转发到其他网络。

    创建 Azure 虚拟网络时，必须指定自定义 DNS 服务器和服务器的 IP 地址，而不是使用网络提供的自动 DNS。 创建虚拟网络后，必须创建一个使用该 IP 地址的 Azure 虚拟机，并在其上安装和配置 DNS 软件。

    > [!WARNING]
    > 在将 HDInsight 安装到虚拟网络之前，需先创建和配置自定义 DNS 服务器。 HDInsight 不需要再进行其他配置，便可使用为虚拟网络配置的 DNS 服务器。

有关连接两个 Azure 虚拟网络的详细信息，请参阅[配置 VNet 到 VNet 的连接](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="create-kafka-clusters"></a>创建 Kafka 群集

虽然可手动创建 Azure 虚拟网络和 Kafka 群集，但使用 Azure 资源管理器模板会更简单。 使用以下步骤将 Azure 虚拟网络和两个 Kafka 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，并在 Azure 门户中打开模板。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json" target="_blank"><img src="./media/apache-kafka-mirroring/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure 资源管理器模板位于 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json**。

    > [!WARNING]
    > 若要确保 Kafka on HDInsight 的可用性，群集必须至少包含 3 个辅助节点。 此模板创建的 Kafka 群集包含三个辅助角色节点。

2. 使用以下信息来填充“自定义部署”边栏选项卡上的项：
    
    ![HDInsight 自定义部署](./media/apache-kafka-mirroring/parameters.png)
    
    * **资源组**：创建一个资源组或选择现有的资源组。 此组包含 HDInsight 群集。

    * **位置**：选择在地理上邻近的位置。
     
    * **基群集名称**：此值将用作 Kafka 群集的基名称。 例如，输入 **hdi** 会创建名为 **source-hdi** 和 **dest-hdi** 的群集。

    * **群集登录用户名**：源和目标 Kafka 群集的管理员用户名。

    * **群集登录密码**：源和目标 Kafka 群集的管理员用户密码。

    * **SSH 用户名**：要创建的源和目标 Kafka 群集的 SSH 用户。

    * **SSH 密码**：源和目标 Kafka 群集的 SSH 用户的密码。

3. 阅读“条款和条件”，并选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，并选择“购买”。 创建群集大约需要 20 分钟时间。

> [!IMPORTANT]
> HDInsight 群集的名称为 source-BASENAME 和 dest-BASENAME，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。

## <a name="create-topics"></a>创建主题

1. 使用 SSH 连接到**源**群集：

    ```bash
    ssh sshuser@source-BASENAME-ssh.azurehdinsight.net
    ```

    用创建群集时使用的 SSH 用户名替换 **sshuser**。 用创建群集时使用的基名称替换 **BASENAME**。

    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 使用以下命令查找源群集的 Zookeeper 主机：

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export SOURCE_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    将 `$CLUSTERNAME` 替换为源群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

3. 若要创建名为 `testtopic` 的主题，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SOURCE_ZKHOSTS
    ```

3. 使用以下命令验证是否已创建该主题：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $SOURCE_ZKHOSTS
    ```

    响应包含 `testtopic`。

4. 使用以下命令查看此（**源**）群集的 Zookeeper 主机信息：

    ```bash
    echo $SOURCE_ZKHOSTS
    ```

    这会返回类似于以下文本的信息：

    `zk0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181,zk1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:2181`

    请保存此信息。 在下一部分中使用。

## <a name="configure-mirroring"></a>配置镜像

1. 使用另一个 SSH 会话连接到**目标**群集：

    ```bash
    ssh sshuser@dest-BASENAME-ssh.azurehdinsight.net
    ```

    用创建群集时使用的 SSH 用户名替换 **sshuser**。 用创建群集时使用的基名称替换 **BASENAME**。

    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

2. `consumer.properties` 文件用于配置与源群集的通信。 若要创建文件，请使用以下命令：

    ```bash
    nano consumer.properties
    ```

    将以下文本用作 `consumer.properties` 文件的内容：

    ```yaml
    zookeeper.connect=SOURCE_ZKHOSTS
    group.id=mirrorgroup
    ```

    使用**源**群集中的 Zookeeper 主机信息替换 **SOURCE_ZKHOSTS**。

    此文件描述从源 Kafka 群集读取时要使用的使用者信息。 有关使用者配置的详细信息，请参阅 kafka.apache.org 中的[使用者配置](https://kafka.apache.org/documentation#consumerconfigs)。

    要保存文件，请使用 **Ctrl + X**、**Y**，并按 **Enter**。

3. 配置与目标群集通信的创建器之前，必须找到**目标**群集的中转站主机。 使用以下命令检索此信息：

    ```bash
    sudo apt -y install jq
    DEST_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $DEST_BROKERHOSTS
    ```

    将 `$CLUSTERNAME` 替换为目标群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

    `echo` 命令将返回类似于以下文本的信息：

        wn0-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn1-dest.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092

4. `producer.properties` 文件用于与目标群集的通信。 若要创建文件，请使用以下命令：

    ```bash
    nano producer.properties
    ```

    将以下文本用作 `producer.properties` 文件的内容：

    ```yaml
    bootstrap.servers=DEST_BROKERS
    compression.type=none
    ```

    使用上一步中获取的中转站信息替换 **DEST_BROKERS**。

    有关创建器配置的详细信息，请参阅 kafka.apache.org 中的[创建器配置](https://kafka.apache.org/documentation#producerconfigs)。

5. 使用以下命令查找目标群集的 Zookeeper 主机：

    ```bash
    # Install jq if it is not installed
    sudo apt -y install jq
    # get the zookeeper hosts for the source cluster
    export DEST_ZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    将 `$CLUSTERNAME` 替换为目标群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

7. Kafka 在 HDInsight 上的默认配置不允许自动创建的主题。 在开始镜像过程之前，你必须使用以下选项之一：

    * **在目标群集上创建的主题**：此选项还允许您设置分区和复制因子的数目。

        可以使用以下命令提前创建新的主题：

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $DEST_ZKHOSTS
        ```

        将 `testtopic` 替换为要创建的主题的名称。

    * **将群集配置为自动主题创建**：此选项允许 MirrorMaker 来自动创建的主题，但它可能使用不同数量的分区或复制因子比源主题创建它们。

        若要配置目标群集来自动创建的主题，请执行以下步骤：

        1. 从 [Azure 门户](https://portal.azure.com)，选择目标 Kafka 群集。
        2. 从群集概述中，选择__群集仪表板__。 然后选择 __HDInsight 群集仪表板__。 出现提示时，进行身份验证使用群集的登录名 (admin) 凭据。
        3. 从页面左侧的列表选择 __Kafka__ 服务。
        4. 在中间页中选择__配置__。
        5. 在“筛选器”字段中输入值 `auto.create`。 这将筛选的属性，并显示列表`auto.create.topics.enable`设置。
        6. 更改的值`auto.create.topics.enable`为 true，然后选择__保存__。 添加注释，然后选择__保存__。
        7. 选择 __Kafka__ 服务，选择__重启__，然后选择__重启所有受影响的__。 出现提示时，选择“确认全部重启”。

## <a name="start-mirrormaker"></a>启动 MirrorMaker

1. 从**目标**群集的 SSH 连接开始，使用以下命令启动 MirrorMaker 过程：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    此示例中使用的参数有：

    * **--consumer.config**：指定包含使用者属性的文件。 这些属性用于创建从*源* Kafka 群集进行读取的使用者。

    * **--producer.config**：指定包含创建器属性的文件。 这些属性用于创建写入*目标* Kafka 群集的创建器。

    * **--whitelist**：MirrorMaker 从源群集复制到目标群集的主题的列表。

    * **--num.streams**：要创建的使用者线程数。

 启动时，MirrorMaker 返回类似于以下文本的信息：

    ```json
    {metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-3, security.protocol=PLAINTEXT}{metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-0, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-kafka.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-2, security.protocol=PLAINTEXT}
    metadata.broker.list=wn1-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092,wn0-source.aazwc2onlofevkbof0cuixrp5h.gx.internal.cloudapp.net:9092, request.timeout.ms=30000, client.id=mirror-group-1, security.protocol=PLAINTEXT}
    ```

2. 从**源**群集的 SSH 连接开始，使用以下命令启动创建器，并将消息发送到主题：

    ```bash
    SOURCE_BROKERHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

    将 `$CLUSTERNAME` 替换为源群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

     出现带有光标的空行时，请键入几条文本消息。 这些消息将发送到源群集上的主题。 完成后，使用 **Ctrl + C** 结束生成者进程。

3. 从**目标**群集的 SSH 连接开始，使用 **Ctrl + C** 结束 MirrorMaker 进程。 它可能需要几秒钟时间结束进程。 若要验证是否已将主题和消息复制到目标，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $DEST_ZKHOSTS --topic testtopic --from-beginning
    ```

    将 `$CLUSTERNAME` 替换为目标群集的名称。 出现提示时，输入群集登录（管理员）帐户的密码。

    主题列表现包含 `testtopic`，该条目创建于 MirrorMaster 将主题从源群集镜像到目标时。 从主题检索到的消息与在源群集上输入的消息相同。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

由于本文档中的步骤在相同的 Azure 资源组中创建两个群集，因此可在 Azure 门户中删除资源组。 删除资源组会删除按照本文档创建的所有资源（Azure 虚拟网络和群集使用的存储帐户）。

## <a name="next-steps"></a>后续步骤

本文档已介绍如何使用 MirrorMaker 创建 Kafka 群集的副本。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* cwiki.apache.org 上的 [Apache Kafka MirrorMaker 文档](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)。
* [Apache Kafka on HDInsight 入门](apache-kafka-get-started.md)
* [将 Apache Spark 与 Kafka on HDInsight 结合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](../hdinsight-apache-storm-with-kafka.md)
* [通过 Azure 虚拟网络连接到 Kafka](apache-kafka-connect-vpn-gateway.md)
