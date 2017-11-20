---
title: "将 Apache Kafka 与 Storm on HDInsight 配合使用 - Azure | Microsoft Docs"
description: "同时安装 Apache Kafka 和 Apache Storm on HDInsight。 了解如何使用 Storm 附带的 KafkaBolt 和 KafkaSpout 组件写入到 Kafka，并从中进行读取。 此外，还了解如何使用 Flux 框架来定义和提交 Storm 拓扑。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 50a22877241c77ccb1a7df24ab7df006094a439f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>将 Apache Kafka 与 HDInsight 中的 Storm 配合使用

了解如何使用 Apache Storm 执行对 Apache Kafka 的读写操作。 本示例还演示如何将数据从 Storm 拓扑保存到由 HDInsight 使用的 HDFS 兼容文件系统。

> [!NOTE]
> 本文档中的步骤创建了一个 Azure 资源组，其中同时包含了 Storm on HDInsight 群集以及 Kafka on HDInsight 群集。 这些群集都位于 Azure 虚拟网络中，允许 Storm 群集直接与 Kafka 群集进行通信。
> 
> 完成本文档中的步骤后，请记得删除这些群集，避免支付额外费用。

## <a name="get-the-code"></a>获取代码

本文档中使用的示例代码位于 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka)。

若要编译此项目，需要对开发环境进行以下配置：

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更高版本。 HDInsight 3.5 或更高版本需要 Java 8。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH 客户端（需要 `ssh` 和 `scp` 命令）- 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

* 文本编辑器或 IDE。

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，应该检查它们是否存在并且包含系统的正确值。

* `JAVA_HOME` - 应该指向 JDK 的安装目录。
* `PATH` - 应该包含以下路径：
  
    * `JAVA_HOME`（或等效路径）。
    * `JAVA_HOME\bin`（或等效路径）。
    * Maven 的安装目录。

## <a name="create-the-clusters"></a>创建群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 中转站的权限。 与 Kafka 对话的任何内容都必须与 Kafka 群集中的节点位于同一 Azure 虚拟网络中。 对于此示例，Kafka 和 Storm 群集都位于 Azure 虚拟网络中。 下图显示通信在群集之间的流动方式：

![Azure 虚拟网络中的 Storm 和 Kafka 群集图示](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> 通过 Internet 可访问群集上的其他服务，例如 SSH 和 Ambari。 有关可用于 HDInsight 的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

虽然可手动创建 Azure 虚拟网络、Kafka 和 Storm 群集，但使用 Azure 资源管理器模板会更简单。 使用以下步骤将 Azure 虚拟网络、Kafka 和 Storm 群集部署到 Azure 订阅。

1. 使用以下按钮登录到 Azure，并在 Azure 门户中打开模板。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet-v2.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure 资源管理器模板位于 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet-v2.json**。 它创建以下资源：
    
    * Azure 资源组
    * Azure 虚拟网络
    * Azure 存储帐户
    * HDInsight 版本 3.6 上的 Kafka（三个辅助角色节点）
    * HDInsight 版本 3.6 上的 Storm（三个辅助角色节点）

  > [!WARNING]
  > 若要确保 Kafka on HDInsight 的可用性，群集必须至少包含 3 个辅助节点。 此模板创建的 Kafka 群集包含三个辅助角色节点。

2. 使用以下指南填充“自定义部署”部分中的条目：
   
    ![HDInsight 自定义部署](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **资源组**：创建一个资源组或选择现有的资源组。 此组包含 HDInsight 群集。
   
    * **位置**：选择在地理上邻近的位置。

    * **基群集名称**：此值将用作 Storm 和 Kafka 群集的基名称。 例如，输入 **hdi** 创建名为 **storm-hdi** 的 Storm 群集和名为 **kafka-hdi** 的 Kafka 群集。
   
    * **群集登录用户名**：Storm 和 Kafka 群集的管理员用户名。
   
    * **群集登录密码**：Storm 和 Kafka 群集的管理员用户密码。
    
    * **SSH 用户名**：创建 Storm 和 Kafka 群集的 SSH 用户。
    
    * **SSH 密码**：Storm 和 Kafka 群集的 SSH 用户的密码。

3. 阅读“条款和条件”，并选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，并选择“购买”。 创建群集大约需要 20 分钟时间。

创建资源后，会显示“资源组”部分。

![VNet 和群集的资源组部分](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> 请注意，HDInsight 群集的名称为 **storm-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。

## <a name="understanding-the-code"></a>了解数据

此项目包含两种拓扑：

* **KafkaWriter**：由 **writer.yaml** 文件定义，此拓扑使用 Apache Storm 提供的 KafkaBolt 向 Kafka 写入随机的句子。

    此拓扑使用自定义 **SentenceSpout** 组件生成随机的句子。

* **KafkaReader**：由 **reader.yaml** 文件定义，此拓扑使用 Apache Storm 提供的 KafkaSpout 从 Kafka 读取数据，然后将数据记录到 stdout。

    此拓扑使用 Storm HdfsBolt 将数据写入 Storm 群集的默认存储。
### <a name="flux"></a>Flux

此拓扑使用 [Flux](https://storm.apache.org/releases/1.1.0/flux.html) 定义。 Storm 0.10.x 中引入了 Flux，允许从代码分离拓扑配置。 对于使用 Flux 框架的拓扑，该拓扑在 YAML 文件中进行定义。 YAML 文件可以作为拓扑的一部分包括在内。 它也可以是提交拓扑时使用的独立文件。 Flux 还支持在运行时进行变量替换，本示例中使用了该变量替换。

在运行时为这些拓扑设置以下参数：

* `${kafka.topic}`：拓扑读取/写入的 Kafka 主题的名称。

* `${kafka.broker.hosts}`：Kafka 中转站运行时所在的主机。 写入 Kafka 时，KafkaBolt 将使用中转站信息。

* `${kafka.zookeeper.hosts}`：Kafka 群集中 Zookeeper 运行时所在的主机。

有关 Flux 拓扑的详细信息，请参阅 [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html)。

## <a name="download-and-compile-the-project"></a>下载并编译项目

1. 在开发环境中，从 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 下载项目，打开一个命令行，并将目录更改为下载该项目的位置。

2. 从 **hdinsight-storm-java-kafka** 目录，使用以下命令来编译该项目并创建用于部署的包：

  ```bash
  mvn clean package
  ```

    包过程会在 `target` 目录中创建名为 `KafkaTopology-1.0-SNAPSHOT.jar` 的文件。

3. 使用以下命令将包复制到 Storm on HDInsight 群集。 使用群集的 SSH 用户名替换 **USERNAME**。 用创建群集时使用的基名称替换 **BASENAME**。

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    出现提示时，请输入在创建群集时使用的密码。

## <a name="configure-the-topology"></a>配置拓扑

1. 使用以下方法之一发现 Kafka 中转站主机：

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash 示例假定 `$CLUSTERNAME` 包含 HDInsight 群集的名称。 还假定 [jq](https://stedolan.github.io/jq/) 已安装。 出现提示时，输入群集登录帐户的密码。

    返回的值类似于下文：

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > 虽然群集可能有两个以上的代理主机，但无需向客户端提供所有主机的完整列表。 只需提供一两个就足够了。

2. 使用以下方法之一发现 Kafka Zookeeper 主机：

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    > [!IMPORTANT]
    > Bash 示例假定 `$CLUSTERNAME` 包含 HDInsight 群集的名称。 还假定 [jq](https://stedolan.github.io/jq/) 已安装。 出现提示时，输入群集登录帐户的密码。

    返回的值类似于下文：

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > 虽然有两个以上的 Zookeeper 节点，但无需向客户端提供所有主机的完整列表。 只需提供一两个就足够了。

    请保存该值，因为稍后会使用它。

3. 编辑项目根目录中的 `dev.properties` 文件。 将中转站和 Zookeeper 主机信息添加到此文件中的匹配行。 下面的示例使用前面步骤中的示例值进行配置：

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. 保存 `dev.properties` 文件，然后使用以下命令将其上传到 Storm 群集：

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
    ```

    使用群集的 SSH 用户名替换 **USERNAME**。 用创建群集时使用的基名称替换 **BASENAME**。

## <a name="start-the-writer"></a>启动编写器

1. 使用以下命令通过 SSH 连接到 Storm 群集。 用创建群集时使用的 SSH 用户名替换 **USERNAME**。 用创建群集时使用的基名称替换 **BASENAME**。

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    出现提示时，请输入在创建群集时使用的密码。
   
    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 从 SSH 连接使用以下命令，创建拓扑所使用的 Kafka 主题：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    将 `$KAFKAZKHOSTS` 替换为在上一部分中检索到的 Zookeeper 主机信息。

2. 与 Storm 群集建立 SSH 连接后，使用以下命令启动编写器拓扑：

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    此命令使用的参数如下：

    * `org.apache.storm.flux.Flux`：使用 Flux 配置和运行此拓扑。

    * `--remote`：将拓扑提交到 Nimbus。 拓扑分布在群集中的辅助角色节点。

    * `-R /writer.yaml`：使用 `writer.yaml` 文件配置拓扑。 `-R` 指示此资源包含在 jar 文件中。 它位于 jar 的根目录中，因此 `/writer.yaml` 是它的路径。

    * `--filter`：使用 `dev.properties` 文件中的值填充 `writer.yaml` 拓扑中的条目。 例如，文件中 `kafka.topic` 条目的值用于替换拓扑定义中的 `${kafka.topic}` 条目。

5. 启动拓扑后，使用以下命令验证它是否正将数据写入到 Kafka 主题：

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    将 `$KAFKAZKHOSTS` 替换为在上一部分中检索到的 Zookeeper 主机信息。

    此命令使用 Kafka 附带的脚本来监视主题。 片刻之后，它应开始返回已写入到主题的随机句子。 输出类似于以下示例：

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    使用 Ctrl+c 来停止脚本。

## <a name="start-the-reader"></a>启动读取器

1. 与 Storm 群集建立 SSH 会话后，使用以下命令启动读取器拓扑：

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. 启动拓扑后，打开 Storm UI。 此 Web UI 位于 `https://storm-BASENAME.azurehdinsight.net/stormui`。 使用创建群集时使用的基名称替换 __BASENAME__。 

    出现提示时，使用创建群集时使用的管理员登录名（默认情况下为 `admin`）和密码。 此时会显示与下图类似的网页：

    ![Storm UI](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. 从 Storm UI 中，选择“拓扑摘要”部分中的 __kafka-reader__ 链接，以显示有关 __kafka-reader__ 拓扑的信息。

    ![Storm Web UI 的拓扑摘要部分](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. 要显示有关 logger-bolt 组件的实例的信息，请选择“Bolt (全部时间)”部分中的 logger-bolt 链接。

    ![Bolt 部分中的 logger-bolt 链接](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. 在“执行器”部分中，选择“端口”列中的链接，以显示有关此组件实例的日志记录信息。

    ![执行器链接](./media/hdinsight-apache-storm-with-kafka/executors.png)

    该日志包含从 Kafka 主题读取的数据的日志。 日志中的信息类似于以下文本：

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>停止拓扑

与 Storm 群集建立 SSH 会话后，使用以下命令停止 Storm 拓扑：

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

由于本文档中的步骤在相同的 Azure 资源组中创建两个群集，因此可在 Azure 门户中删除资源组。 删除资源组会移除按照本文档创建的所有资源。

## <a name="next-steps"></a>后续步骤

有关更多可用于 Storm on HDInsight 的示例拓扑，请参阅[示例 Storm 拓扑和组件](storm/apache-storm-example-topology.md)。

有关在基于 Linux 的 HDInsight 上部署和监视拓扑的详细信息，请参阅[在基于 Linux 的 HDInsight 上部署和管理 Apache Storm 拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)