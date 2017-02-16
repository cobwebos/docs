---
title: "Apache Kafka on HDInsight 入门 | Microsoft Docs"
description: "了解在 HDInsight 上创建和使用 Kafka 的基础知识。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 34c1138a9c3b9313a12ffbf4bc4c3141db0a016e
ms.openlocfilehash: 98b11144c049f9db780c7665610c83a753f23b21

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Apache Kafka on HDInsight（预览版）入门

[Apache Kafka](https://kafka.apache.org) 是开源分布式流式处理平台，可与 HDInsight 配合使用。 通常将其用作消息代理，因为它可提供类似于发布-订阅消息队列的功能。 本文档将介绍如何创建 Kafka on HDInsight 群集，然后从 Java 应用程序发送和接收数据。

> [!NOTE]
> 目前，有两个 Kafka 版本可用于 HDInsight；0.9.0 (HDInsight 3.4) 和 0.10.0 (HDInsight 3.5)。 本文档中的步骤假设使用 Kafka on HDInsight 3.5。

## <a name="prerequisite"></a>先决条件

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

你必须具备以下条件才能成功完成本 Apache Storm 教程：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* **熟悉 SSH 和 SCP**。 有关如何将 SSH 和 SCP 与 HDInsight 配合使用的详细信息，请参阅以下文档：
  
   * **Linux、Unix 或 OS X 客户端**：请参阅 [在 Linux、OS X 或 Unix 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
   
   * **Windows 客户端**：请参阅 [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或类似程序，如 OpenJDK。

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>访问控制要求

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>创建 Kafka 群集

使用以下步骤创建 Kafka on HDInsight 群集：

1. 从 [Azure 门户](https://portal.azure.com)，选择“+ 新建”**、“智能 + 分析”**，然后选择“HDInsight”。
   
    ![创建 HDInsight 群集](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. 从“新建 HDInsight 群集”边栏选项卡，输入一个**群集名称**，然后选择配合此群集使用的**订阅**。
   
    ![选择订阅](./media/hdinsight-apache-kafka-get-started/new-hdinsight-cluster-blade.png)

3. 在“群集类型配置”边栏选项卡上，使用“选择群集类型”并选择以下值：
   
    * **群集类型**：Kafka

    * **版本**：Kafka 0.10.0 (HDI 3.5)

    * **群集层**：标准
     
    最后使用“选择”按钮保存设置。
     
    ![选择群集类型](./media/hdinsight-apache-kafka-get-started/cluster-type.png)

    > [!NOTE]
    > 如果 Azure 订阅无权访问 Kafka 预览版，将显示有关如何获取预览版的访问权限的说明。 将显示类似于下图的说明：
    >
    > ![预览版消息：如果要在 HDInsight 上部署托管 Apache Kafka 群集，请向我们发送请求访问预览版的电子邮件](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. 使用“凭据”配置群集登录和 SSH 用户凭据。  使用“选择”按钮保存设置。
   
    > [!NOTE]
    > 使用 HTTPS 通过 Internet 访问群集时，会使用此群集登录。 SSH 用户用于连接到群集并以交互方式运行命令。
   
    ![配置群集登录](./media/hdinsight-apache-kafka-get-started/cluster-credentials.png)
   
    有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文档：
   
    * [在 Linux、Unix 或 MacOS 客户端中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
   
    * [在 Windows 客户端中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

5. 使用“数据源”为群集配置主数据存储。 从“数据源”边栏选项卡，使用以下信息为群集创建数据存储：
   
    * 选择“新建”，然后输入存储帐户的名称。
    
    * 选择“位置”，然后选择地理上靠近你的位置。 此位置用于创建存储帐户和 HDInsight 群集。
     
   最后使用“选择”按钮保存设置。
     
    ![配置存储](./media/hdinsight-apache-kafka-get-started/configure-storage.png)

6. 使用“定价”，然后将“辅助角色节点数量”设置为 2。 使用 2 个辅助角色节点可帮助降低群集的成本，且对于此示例已足够。 使用“选择”按钮保存设置。
   
    ![定价](./media/hdinsight-apache-kafka-get-started/pricing.png)
   
    > [!NOTE]
    > 在门户中显示的价格可能不同于屏幕截图中的价格。

7. 使用“资源组”创建组，并在字段中输入名称。 同样选择“固定到仪表板”。 完成后，选择“创建”来创建群集。
   
    ![资源组字段](./media/hdinsight-apache-kafka-get-started/resource-group.png)
   
    > [!NOTE]
    > 创建群集可能需要 20 分钟。

## <a name="connect-to-the-cluster"></a>连接至群集

使用 SSH 从客户端连接到群集。 如果使用的是 Linux、Unix、MacOS 或 Windows 10 上的 Bash，请使用以下命令：

    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net

将 **SSHUSER** 替换为在群集创建期间提供的 SSH 用户名。 将 **CLUSTERNAME** 替换为群集的名称。

出现提示时，输入用于 SSH 帐户的密码。

有关如何将 SSH 与 HDInsight 配合使用的信息，请参阅以下文档：

* [在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)

* [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="a-idgetkafkainfoaget-the-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>获取 Zookeeper 主机和代理主机信息

使用 Kafka 时，必须了解两个主机值；Zookeeper 主机和代理主机。 这些主机配合 Kafka API 和 Kafka 随附的许多实用工具一起使用。

使用以下步骤创建包含的主机信息的环境变量。 这些环境变量在本文档的步骤中使用。

1. 与群集建立 SSH 连接后，使用以下命令安装 `jq` 实用工具。 此实用工具用于分析 JSON 文档且有助于检索代理主机的信息：
   
        sudo apt -y install jq

2. 使用以下命令，通过从 Ambari 检索的信息设置环境变量。 使用 Kafka 群集的名称替换 __KAFKANAME__。 使用创建群集时使用的登录（管理员）密码替换 __PASSWORD__。

        export KAFKAZKHOSTS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

        export KAFKABROKERS=`curl --silent -u admin:PASSWORD -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

        echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
        echo '$KAFKABROKERS='$KAFKABROKERS

    以下文本是 `$KAFKAZKHOSTS` 的内容示例：
   
        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181
   
    以下文本是 `$KAFKABROKERS` 的内容示例：
   
        wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092
   
    > [!WARNING]
    > 不要期望从此会话中返回的信息始终准确。 若缩放群集，将添加或删除新的代理。 如果发生故障且替换节点，节点的主机名可能会改变。 
    > 
    > 要使用 Zookeeper 和代理主机信息前，应始终先检索该信息，确保所拥有的信息有效。

## <a name="create-a-topic"></a>创建主题

Kafka 在名为 topics 的类别中存储数据流。 与群集头节点建立 SSH 连接后，使用随 Kafka 提供的脚本创建主题：

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

此命令使用存储在 `$KAFKAZKHOSTS` 中的主机信息连接到 Zookeeper，然后创建名为 **test** 的 Kafka 主题。 通过使用以下脚本列出主题可确认已创建该主题：

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS

此命令的输出列出了 Kafka 主题，其中包含 **test** 主题。

## <a name="produce-and-consume-records"></a>生成和使用记录

Kafka 将记录存储在主题中。 记录由生成者生成，由使用者使用。 生成者从 Kafka 代理检索记录。 HDInsight 群集中的每个辅助角色节点都是 Kafka 代理。

使用以下步骤将记录存储到之前创建的测试主题，并通过使用者对其进行读取：

1. 从 SSH 会话，使用随 Kafka 提供的脚本将记录写入主题：
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
   
    执行此命令后不会返回到该提示符。 而是键入一些文本消息，然后使用 **Ctrl + C** 停止发送到该主题。 每行均作为单独的记录发送。

2. 使用随 Kafka 提供的脚本从主题中读取记录：
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
   
    这将从主题检索记录并显示记录。 使用 `--from-beginning` 告知使用者从流的开头开始，以检索所有记录。

3. 使用 __Ctrl + C__ 阻止使用者。

## <a name="producer-and-consumer-api"></a>生成者和使用者 API

还可使用 [Kafka API](http://kafka.apache.org/documentation#api) 以编程方式生成和使用记录。 使用以下步骤下载、构建基于 Java 的生成者和使用者：

1. 从 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下载示例。 对于生成者/使用者示例，使用 `Producer-Consumer` 目录中的项目。 请务必仔细查看代码，了解此示例的工作原理。 本主题包含以下类：
   
    * **运行** - 基于命令行参数启动使用者或生成者。

    * **生成者** - 将 1,000,000 个记录存储到主题中。

    * **使用者** - 从主题中读取记录。

2. 从开发环境中的命令行，将目录更改为该示例的 `Producer-Consumer` 目录位置，然后使用以下命令创建 jar 包：
   
        mvn clean package
   
    此命令创建一个名为 `target` 的新目录，包含名为 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 的文件。

3. 使用以下命令将 `kafka-producer-consumer-1.0-SNAPSHOT.jar` 文件复制到 HDInsight 群集：
   
        scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
   
    将 **SSHUSER** 替换为群集的 SSH 用户，并将 **CLUSTERNAME** 替换为群集的名称。 出现提示时，请输入 SSH 用户密码。

4. `scp` 命令完成复制文件后，使用 SSH 连接到群集，然后使用以下步骤将记录写入之前创建的测试主题。
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS
   
    这将启动生成者，并写入记录。 将显示计数器，因此可查看写入的记录数量。

    > [!NOTE]
    > 如果收到“权限被拒”错误，使用以下命令，使该文件成为可执行文件：```chmod +x kafka-producer-consumer.jar```

5. 完成该过程后，使用以下命令从主题中读取：
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS
   
    将显示读取的记录以及记录计数。 使用之前步骤中的脚本将多个记录发送到主题时，可能会看到超过 1,000,000 个记录。

6. 使用 __Ctrl + C__ 让使用者退出。

### <a name="multiple-consumers"></a>多个使用者

Kafka 的一个重要概念是使用者在读取记录时使用使用者组（由组 ID 定义）。 使用同一组负载平衡的多个使用者从主题读取；每个使用者都会收到一部分记录。 若要在操作中查看，请使用以下步骤：

1. 向群集打开新 SSH 会话，从而具有两个会话。 在每个会话中，使用以下步骤通过同一使用者组 ID 启用使用者：
   
        ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup

    > [!NOTE]
    > 由于这是一个新 SSH 会话，因此需要使用[获取 Zookeeper 和代理主机信息](#getkafkainfo)部分中的命令设置 `$KAFKABROKERS`。

2. 观察每个会话对从主题中收到的记录进行计数。 两个会话的总记录数应与之前从一个使用者中收到的记录数相同。

同一组中客户端的使用情况通过主题的分区进行处理。 之前创建的 `test` 主题具有 8 个分区。 若打开 8 个 SSH 会话，并在所有会话中启动一个使用者，每个使用者都将从主题的单个分区中读取记录。

> [!IMPORTANT]
> 使用者组中存在的使用者实例不能比分区多。 此示例中，一个使用者组最多可包含 8 个使用者，因为这是本主题中分区的数量。 或者可拥有多个使用者组，每个组的使用者不能超过 8 个。

存储在 Kafka 中的记录都按在分区中接收的顺序进行存储。 若要在分区内实现记录的有序交付，请创建一个使用者组，其中的使用者实例数与分区数匹配。 若要在主题内实现记录的有序交付，请创建仅含一个使用者实例的使用者组。

## <a name="streaming-api"></a>流式处理 API

已将流式处理 API 添加到 Kafka 版本 0.10.0 中；早期版本依赖于 Apache Spark 或 Storm 进行流式处理。

1. 如果尚未执行此操作，请从 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 下载示例。 对于流式处理示例，使用 `streaming` 目录中的项目。 请务必仔细查看代码，了解此示例的工作原理。 
   
    此项目仅包含一个类 `Stream`，该类从之前创建的 `test` 主题读取记录。 它会统计读取的字数，然后发出每个字，计数到名为 `wordcounts` 的主题。 `wordcounts` 主题在本部分之后的步骤中进行创建。

2. 从开发环境中的命令行，将目录更改为 `Streaming` 目录的位置，然后使用以下命令创建 jar 包：
   
        mvn clean package
   
    此命令创建一个名为 `target` 的新目录，包含名为 `kafka-streaming-1.0-SNAPSHOT.jar` 的文件。

3. 使用以下命令将 `kafka-streaming-1.0-SNAPSHOT.jar` 文件复制到 HDInsight 群集：
   
        scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
   
    将 **SSHUSER** 替换为群集的 SSH 用户，并将 **CLUSTERNAME** 替换为群集的名称。 出现提示时，请输入 SSH 用户密码。

4. `scp` 命令完成复制文件后，使用 SSH 连接到群集，然后使用以下步骤启动流式处理：
   
        ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
   
    此命令在后台启动流式处理。

5. 使用以下步骤将消息发送到 `test` 主题。 它们由流式处理示例进行处理：
   
        ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &

6. 使用以下步骤查看写入 `wordcounts` 主题的输出：
   
        /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
   
    > [!NOTE]
    > 我们必须让使用者打印密钥（包括字值）和反序列化程序使用密钥和值查看数据。
   
    输出与下面类似：
   
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
        a       13805
        snow    13637
   
    注意，每遇到一个字，计数就会增加。

7. 使用 __Ctrl + C__ 让使用者退出，然后使用 `fg` 命令将流式处理后台任务恢复到前台。 同样，使用 __Ctrl + C__ 使它退出。

## <a name="next-steps"></a>后续步骤

在本文档中，已了解在 HDInsight 上使用 Apache Kafka 的基本知识。 使用以下内容，详细了解如何使用 Kafka：

* kafka.apache.org 处的 [Apache Kafka 文档](http://kafka.apache.org/documentation.html)。
* [使用 MirrorMaker 创建 Kafka on HDInsight 的副本](hdinsight-apache-kafka-mirroring.md)
* [将 Apache Storm 与 Kafka on HDInsight 结合使用](hdinsight-apache-storm-with-kafka.md)
* [将 Apache Spark 与 Kafka on HDInsight 结合使用](hdinsight-apache-spark-with-kafka.md)




<!--HONumber=Jan17_HO2-->


