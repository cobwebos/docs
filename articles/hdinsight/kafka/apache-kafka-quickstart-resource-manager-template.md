---
title: 快速入门：使用 Azure 资源管理器的 Apache Kafka - HDInsight
description: 在此快速入门中，了解如何在 Azure HDInsight 上使用 Azure 资源管理器模板创建 Apache Kafka 群集。 还可以了解 Kafka 主题、订阅服务器和使用者。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: d908d210ff0448069a9abc76209c72d9b2a7595c
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242023"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-resource-manager-template"></a>快速入门：使用资源管理器模板在 Azure HDInsight 中创建 Apache Kafka 群集

[Apache Kafka](https://kafka.apache.org/) 是开源分布式流式处理平台。 通常用作消息代理，因为它可提供类似于发布-订阅消息队列的功能。 

在此快速入门中，了解如何在 Azure HDInsight 上使用 Azure 资源管理器模板创建 [Apache Kafka](https://kafka.apache.org) 群集。 还介绍了如何使用已包含的实用工具发送并接收使用 Kafka 的信息。 可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)中查看类似的模板。 可以在[此处](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions)找到模板参考。

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

仅可通过相同虚拟网络内的资源访问 Kafka API。 本快速入门使用 SSH 直接访问群集。 若要将其他服务、网络或虚拟机连接到 Kafka，则必须首先创建虚拟机，然后才能在网络中创建资源。 有关详细信息，请参阅[使用虚拟网络连接到 Apache Kafka](apache-kafka-connect-vpn-gateway.md) 文档。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="create-an-apache-kafka-cluster"></a>创建 Apache Kafka 群集

1. 单击下面的图像即可在 Azure 门户中打开该模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 若要创建 Kafka 群集，请使用以下值：

    | 属性 | 值 |
    | --- | --- |
    | Subscription | Azure 订阅。 |
    | Resource group | 在其中创建群集的资源组。 |
    | 位置 | 在其中创建群集的 Azure 区域。 |
    | 群集名称 | Kafka 群集的名称。 |
    | 群集登录用户名 | 此帐户名用于登录到群集上托管的基于 HTTP 的服务。 |
    | 群集登录密码 | 输入用户名的密码。 |
    | SSH 用户名 | SSH 用户名。 此帐户可以使用 SSH 访问群集。 |
    | SSH 密码 | 用于 SSH 用户的密码。 |

    ![模板属性的屏幕截图](./media/apache-kafka-quickstart-resource-manager-template/kafka-template-parameters.png)

3. 选择“我同意上述条款和条件”，选择“固定到仪表板”，并单击“购买”。    创建群集可能需要 20 分钟。

## <a name="connect-to-the-cluster"></a>连接至群集

1. 若要连接到 Kafka 群集的主要头节点，请使用以下命令。 将 `sshuser` 替换为 SSH 用户名。 将 `mykafka` 替换为 Kafka 群集的名称

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. 首次连接到群集时，SSH 客户端可能会显示一个警告，提示无法验证主机。 当系统提示时，请键入“yes”，然后按 Enter，将主机添加到 SSH 客户端的受信任服务器列表   。

3. 出现提示时，请输入 SSH 用户名密码。

    连接后，显示的信息类似于以下文本：
    
    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    83 packages can be updated.
    37 updates are security updates.
    
    
    Welcome to Kafka on HDInsight.
    
    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a id="getkafkainfo"></a>获取 Apache Zookeeper 主机和代理主机信息

使用 Kafka 时，必须了解 Apache Zookeeper 和代理主机   。 这些主机配合 Kafka API 和 Kafka 随附的许多实用工具一起使用。

在本部分中，可以从群集上的 Ambari REST API 获取主机信息。

1. 从 SSH 连接到群集，使用以下命令安装 `jq` 实用工具。 此实用工具用于分析 JSON 文档且有助于检索主机的信息：
   
    ```bash
    sudo apt -y install jq
    ```

2. 若要将环境变量设置为群集名称，请使用以下命令：

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    出现提示时，请输入 Kafka 群集的名称。

3. 若要使用 Zookeeper 主机信息来设置环境变量，请使用以下命令。 此命令检索所有 Zookeeper 主机，然后仅返回前两个条目。 这是由于某个主机无法访问时，需要一些冗余。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    出现提示时，请输入群集登录帐户（不是 SSH 帐户）的密码。

4. 若要验证是否已正确设置了环境变量，请使用以下命令：

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    此命令返回类似于以下文本的信息：

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. 若要使用 Kafka 代理主机信息来设置环境变量，请使用以下命令：

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    出现提示时，请输入群集登录帐户（不是 SSH 帐户）的密码。

6. 若要验证是否已正确设置了环境变量，请使用以下命令：

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    此命令返回类似于以下文本的信息：
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>管理 Apache Kafka 主题

Kafka 在主题中存储数据流  。 可以使用 `kafka-topics.sh` 实用工具来管理主题。

* 若要创建主题，请在 SSH 连接中使用以下命令  ：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    此命令使用存储在 `$KAFKAZKHOSTS` 中的主机信息连接到 Zookeeper， 然后创建名为 **test** 的 Kafka 主题。 

    * 本主题中存储的数据已分区到八个分区。

    * 每个分区在群集中的三个辅助角色节点上进行复制。

        如果在 Azure 区域中已创建提供三个容错域的群集，则复制因子使用 3。 否则，复制因子使用 4.
        
        在具有三个容错域的区域中，复制因子为 3 可让副本分布在容错域中。 在具有两个容错域的区域中，复制因子为 4 可将副本均匀分布在域中。
        
        有关区域中容错域数的信息，请参阅 [Linux 虚拟机的可用性](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)文档。

        Kafka 不识别 Azure 容错域。 在创建主题的分区副本时，它可能未针对高可用性正确分发副本。

        若要确保高可用性，请使用 [Apache Kafka 分区重新均衡工具](https://github.com/hdinsight/hdinsight-kafka-tools)。 必须通过 SSH 连接运行此工具，以便连接到 Kafka 群集的头节点。

        为确保 Kafka 数据的最高可用性，应在出现以下情况时为主题重新均衡分区副本：

        * 创建新主题或分区

        * 纵向扩展群集

* 若要列出主题，请使用以下命令  ：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    此命令列出 Kafka 群集上可用的主题。

* 若要删除主题，使用以下命令  ：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    此命令删除名为 `topicname` 的主题。

    > [!WARNING]  
    > 如果删除了之前创建的 `test` 主题，则必须重新创建。 稍后会在本文档中使用此主题。

有关适用于 `kafka-topics.sh` 实用工具的命令的详细信息，请使用以下命令：

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>生成和使用记录

Kafka 将记录  存储在主题中。 记录由生成者  生成，由使用者  使用。 生产者与使用者通过 Kafka 代理服务通信  。 HDInsight 群集中的每个工作节点都是 Kafka 代理主机。

若要将记录存储到之前创建的测试主题，并通过使用者对其进行读取，请使用以下步骤：

1. 若要为该主题写入记录，请从 SSH 连接使用 `kafka-console-producer.sh` 实用工具：
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    此命令之后是一个空行。

2. 在空行中键入文本消息，然后点击 Enter。 以这种方式输入一些消息，然后使用 **Ctrl + C** 返回到正常的提示符处。 每行均作为单独的记录发送到 Kafka 主题。

3. 若要读取该主题的记录，请从 SSH 连接使用 `kafka-console-consumer.sh` 实用工具：
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    此命令从主题中检索并显示记录。 使用 `--from-beginning` 告知使用者从流的开头开始，以检索所有记录。

    如果使用的是较旧版本的 Kafka，请将 `--bootstrap-server $KAFKABROKERS` 替换为 `--zookeeper $KAFKAZKHOSTS`。

4. 使用 __Ctrl + C__ 阻止使用者。

还可以以编程方式创建生产者和使用者。 有关如何使用此 API 的示例，请参阅[将 Apache Kafka 生产者和使用者 API 与 HDInsight 配合使用](apache-kafka-producer-consumer-api.md)文档。

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门创建的资源，可以删除资源组。 删除资源组也会删除相关联的 HDInsight 群集，以及与资源组相关联的任何其他资源。

若要使用 Azure 门户删除资源组，请执行以下操作：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。 
2. 找到要删除的资源组，然后右键单击列表右侧的“更多”按钮 (...)。 
3. 选择“删除资源组”，然后进行确认。 

> [!WARNING]  
> 创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集以每分钟按比例收费，因此无需再使用群集时，应始终将其删除。
> 
> 删除 Kafka on HDInsight 群集会删除存储在 Kafka 中的任何数据。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Apache Spark 与 Apache Kafka 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)