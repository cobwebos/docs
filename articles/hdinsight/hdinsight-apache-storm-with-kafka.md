---
title: 教程：Apache Kafka 与 Storm on HDInsight - Azure | Microsoft Docs
description: 了解如何使用 Apache Storm 和 Apache Kafka on HDInsight 创建流式处理管道。 本教程使用 KafkaBolt 和 KafkaSpout 组件来流式传输 Kafka 的数据。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2018
ms.author: larryfr
ms.openlocfilehash: 8baafd69e45210b74db8b0bf41b765067b1251a8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-use-apache-storm-with-kafka-on-hdinsight"></a>教程：将 Apache Storm 与 Kafka on HDInsight 结合使用

本教程说明如何使用 Apache Storm 拓扑并通过 Apache Kafka on Azure HDInsight 来读取和写入数据。 本教程还说明如何将数据保存到 Storm 群集上的 HDFS 兼容存储。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * Storm 和 Kafka
> * 了解数据
> * 创建 Kafka 和 Spark 群集
> * 生成拓扑
> * 配置拓扑
> * 创建 Kafka 主题
> * 启动拓扑
> * 停止拓扑
> * 清理资源

## <a name="prerequisites"></a>先决条件

* 熟悉 Kafka 主题的创建。 有关详细信息，请参阅 [Kafka on HDInsight 快速入门](./kafka/apache-kafka-get-started.md)文档。

* 熟悉 Storm 解决方案（拓扑）的生成和部署。 具体而言，熟悉使用 Flux 框架的拓扑。 有关详细信息，请参阅[使用 Java 创建 Storm 拓扑](./storm/apache-storm-develop-java-topology.md)文档。

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) 或更高版本。 HDInsight 3.5 或更高版本需要 Java 8。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH 客户端（需要 `ssh` 和 `scp` 命令）- 有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，应该检查它们是否存在并且包含系统的正确值。

* `JAVA_HOME` - 应该指向 JDK 的安装目录。
* `PATH` - 应该包含以下路径：
  
    * `JAVA_HOME`（或等效路径）。
    * `JAVA_HOME\bin`（或等效路径）。
    * Maven 的安装目录。

> [!IMPORTANT]
> 本文档中的步骤需要一个 Azure 资源组并且该资源组同时包含 Storm on HDInsight 群集和 Kafka on HDInsight 群集。 这些群集都位于 Azure 虚拟网络中，允许 Spark 群集直接与 Kafka 群集进行通信。
> 
> 为方便起见，本文档链接到了一个模板，该模板可创建所有所需 Azure 资源。 
>
> 有关在虚拟网络中使用 HDInsight 的详细信息，请参阅[使用虚拟网络扩展 HDInsight](hdinsight-extend-hadoop-virtual-network.md) 文档。

## <a name="storm-and-kafka"></a>Storm 和 Kafka

Apache Storm 提供了多个组件以便与 Kafka 配合使用。 此教程中使用了以下组件：

* `org.apache.storm.kafka.KafkaSpout`：此组件用于读取 Kafka 中的数据。 此组件依赖于下列组件：

    * `org.apache.storm.kafka.SpoutConfig`：提供 Spout 组件的配置。

    * `org.apache.storm.spout.SchemeAsMultiScheme` 和 `org.apache.storm.kafka.StringScheme`：Kafka 中的数据转换成 Storm 元组的方式。

* `org.apache.storm.kafka.bolt.KafkaBolt`：此组件将数据写入 Kafka。 此组件依赖于下列组件：

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`：描述被写入的主题。

    * `org.apache.kafka.common.serialization.StringSerializer`：配置 Bolt 以将数据串行化为字符串值。

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`：将 Storm 拓扑内使用的元组数据结构映射到存储在 Kafka 中的字段。

`org.apache.storm : storm-kafka` 包提供了这些组件。 使用与 Storm 版本相匹配的包版本。 对于 HDInsight 3.6，Storm 版本为 1.1.0。
还需要 `org.apache.kafka : kafka_2.10` 包，其中包含其他 Kafka 组件。 使用与 Storm 版本相匹配的 Kafka 版本。 对于 HDInsight 3.6，Kafka 版本为 0.10.0.0。

以下 XML 是 `pom.xml` 中 Maven 项目的依赖项声明：

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>0.10.0.0</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>了解数据

[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 中提供了本文档所用代码。

本教程提供了两种拓扑：

* Kafka 编写器：生成随机句子并将其存储到 Kafka。

* Kafka 读取器：读取 Kafka 中的数据，然后将其存储到 Storm 群集的 HDFS 兼容文件存储。

    > [!WARNING] 
    > 若要使 Storm 与 HDInsight 使用的 HDFS 兼容存储配合使用，需执行脚本操作。 脚本会向 Storm 的 `extlib` 路径安装多个 jar 文件。 使用本教程中的模板可在群集创建过程中自动使用脚本。
    >
    > 如果不使用本文档中的模板创建 Storm 群集，则必须手动将脚本操作应用于群集。
    >
    > 脚本操作位于 `https://hdiconfigactions2.blob.core.windows.net/stormextlib/stormextlib.sh` 并应用于 Storm 群集的 supervisor 和 nimbus 节点。 有关使用脚本操作的详细信息，请参阅[使用脚本操作自定义 HDInsight](hdinsight-hadoop-customize-cluster-linux.md) 文档。

此拓扑使用 [Flux](https://storm.apache.org/releases/1.1.2/flux.html) 定义。 Storm 0.10.x 中引入了 Flux，允许从代码分离拓扑配置。 对于使用 Flux 框架的拓扑，该拓扑在 YAML 文件中进行定义。 YAML 文件可以作为拓扑的一部分包括在内。 它也可以是提交拓扑时使用的独立文件。 Flux 还支持在运行时进行变量替换，本示例中使用了该变量替换。

在运行时为这些拓扑设置以下参数：

* `${kafka.topic}`：拓扑读取/写入的 Kafka 主题的名称。

* `${kafka.broker.hosts}`：Kafka 中转站运行时所在的主机。 写入 Kafka 时，KafkaBolt 将使用中转站信息。

* `${kafka.zookeeper.hosts}`：Kafka 群集中 Zookeeper 运行时所在的主机。

* `${hdfs.url}`：HDFSBolt 组件的文件系统 URL。 指示是否已将数据写入 Azure 存储帐户或 Azure Data Lake Store。

* `${hdfs.write.dir}`：写入数据的目录。

有关 Flux 拓扑的详细信息，请参阅 [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html)。

### <a name="kafka-writer"></a>Kafka 编写器

在 Kafka 编写器拓扑中，Kafka bolt 组件将两个字符串值作为参数。 这些参数指示 bolt 将哪些元组字段发送到 Kafka 作为密钥值和消息值。 密钥用于对 Kafka 中的数据进行分区。 消息是正在存储的数据。

在此示例中，`com.microsoft.example.SentenceSpout` 组件会发出包含两个字段（`key` 和 `message`）的元组。 Kafka bolt 提取这些字段，并将其中的数据发送到 Kafka。

这两个字段不必使用名称 `key` 和 `message`。 此项目中使用这些名称是便于映射易于理解。

以下 YAML 对 Kafka 编写器组件进行了定义：

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka 读取器

在 Kafka 读取器拓扑中，Spout 组件从 Kafka 读取数据作为字符串值。 然后，数据通过日志记录组件写入 Storm 日志，并通过 HDFS bolt 组件写入 Storm 群集的 HDFS 兼容文件系统。

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>属性替换项

项目包含名为 `dev.properties` 的文件，用于传递拓扑使用的参数。 它定义以下属性：

| dev.properties 文件 | 说明 |
| --- | --- |
| `kafka.zookeeper.hosts` | Kafka 群集的 Zookeeper 主机。 |
| `kafka.broker.hosts` | Kafka 代理主机（辅助角色节点）。 |
| `kafka.topic` | 拓扑使用的 Kafka 主题。 |
| `hdfs.write.dir` | Kafka 读取器拓扑写入的目录。 |
| `hdfs.url` | Storm 群集使用的文件系统。 对于 Azure 存储帐户，使用值 `wasb:///`。 对于 Azure Data Lake Store，使用值 `adl:///`。 |

## <a name="create-the-clusters"></a>创建群集

Apache Kafka on HDInsight 不提供通过公共 Internet 访问 Kafka 中转站的权限。 使用 Kafka 的任何项都必须位于同一 Azure 虚拟网络中。 在本教程中，Kafka 和 Storm 群集位于同一 Azure 虚拟网络。 

下图显示通信在 Storm 和 Kafka 之间的流动方式：

![Azure 虚拟网络中的 Storm 和 Kafka 群集图示](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> 通过 Internet 可访问群集上的其他服务，例如 SSH 和 Ambari。 有关可用于 HDInsight 的公共端口的详细信息，请参阅 [HDInsight 使用的端口和 URI](hdinsight-hadoop-port-settings-for-services.md)。

若要创建 Azure 虚拟网络，然后在其中创建 Kafka 和 Storm 群集，请使用以下步骤：

1. 使用以下按钮登录到 Azure，并在 Azure 门户中打开模板。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    Azure 资源管理器模板位于 **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**。 它创建以下资源：
    
    * Azure 资源组
    * Azure 虚拟网络
    * Azure 存储帐户
    * HDInsight 版本 3.6 上的 Kafka（三个辅助角色节点）
    * HDInsight 版本 3.6 上的 Storm（三个辅助角色节点）

  > [!WARNING]
  > 若要确保 Kafka on HDInsight 的可用性，群集必须至少包含 3 个辅助节点。 此模板创建的 Kafka 群集包含三个辅助角色节点。

2. 使用以下指南填充“自定义部署”部分中的条目：

    2. 使用以下信息填充“自定义模板”部分的条目：

    | 设置 | 值 |
    | --- | --- |
    | 订阅 | Azure 订阅 |
    | 资源组 | 包含资源的资源组。 |
    | Location | 创建资源时所在的 Azure 区域。 |
    | Kafka 群集名称 | Kafka 群集的名称。 |
    | Storm 群集名称 | Storm 群集的名称。 |
    | 群集登录用户名 | 群集的管理员用户名。 |
    | 群集登录密码 | 群集的管理员用户密码。 |
    | SSH 用户名 | 要为群集创建的 SSH 用户。 |
    | SSH 密码 | 用于 SSH 用户的密码。 |
   
    ![模板参数图片](./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png)

3. 阅读“条款和条件”，并选择“我同意上述条款和条件”。

4. 最后，选中“固定到仪表板”，并选择“购买”。

> [!NOTE]
> 创建群集可能需要长达 20 分钟的时间。

## <a name="build-the-topology"></a>生成拓扑

1. 在开发环境中，从 [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) 下载项目，打开一个命令行，并将目录更改为下载该项目的位置。

2. 从 **hdinsight-storm-java-kafka** 目录，使用以下命令来编译该项目并创建用于部署的包：

  ```bash
  mvn clean package
  ```

    包过程会在 `target` 目录中创建名为 `KafkaTopology-1.0-SNAPSHOT.jar` 的文件。

3. 使用以下命令将包复制到 Storm on HDInsight 群集。 将 `sshuser` 替换为群集的 SSH 用户名。 将 `stormclustername` 替换为 Storm 群集的名称。

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    出现提示时，请输入在创建群集时使用的密码。

## <a name="configure-the-topology"></a>配置拓扑

1. 使用以下方法之一发现 HDInsight 群集上的 Kafka 的 Kafka 中转站主机：

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > 以下 Bash 示例假定 `$CLUSTERNAME` 包含 Kafka 群集名的名称。 它还假定安装了 [jq](https://stedolan.github.io/jq/) 1.5 或更高版本。 出现提示时，输入群集登录帐户的密码。

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    返回的值类似于下文：

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > 虽然群集可能有两个以上的代理主机，但无需向客户端提供所有主机的完整列表。 只需提供一两个就足够了。

2. 使用以下方法之一发现 HDInsight 群集上的 Kafka 的 Zookeeper 主机：

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > 以下 Bash 示例假定 `$CLUSTERNAME` 包含 Kafka 群集的名称。 还假定 [jq](https://stedolan.github.io/jq/) 已安装。 出现提示时，输入群集登录帐户的密码。

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    返回的值类似于下文：

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > 虽然有两个以上的 Zookeeper 节点，但无需向客户端提供所有主机的完整列表。 只需提供一两个就足够了。

    请保存该值，因为稍后会使用它。

3. 编辑项目根目录中的 `dev.properties` 文件。 将 Kafka 群集的中转站和 Zookeeper 主机信息添加到此文件中的匹配行。 下面的示例使用前面步骤中的示例值进行配置：

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

    > [!IMPORTANT]
    > `hdfs.url` 条目为使用 Azure 存储帐户的群集而进行配置。 若要将此拓扑与使用 Data Lake Store 的 Storm 群集结合使用，请将此值从 `wasb` 更改为 `adl`。

4. 保存 `dev.properties` 文件，然后使用以下命令将其上传到 Storm 群集：

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    使用群集的 SSH 用户名替换 **USERNAME**。 用创建群集时使用的基名称替换 **BASENAME**。

## <a name="create-the-kafka-topic"></a>创建 Kafka 主题

Kafka 将数据存储在主题中。 启动 Storm 拓扑之前，必须创建主题。 若要创建拓扑，请使用以下步骤：

1. 使用以下命令通过 SSH 连接到 Kafka 群集。 将 `sshuser` 替换为创建群集时所用的 SSH 用户名。 将 `kafkaclustername` 替换为 Kafka 群集的名称：

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    出现提示时，请输入在创建群集时使用的密码。
   
    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 若要创建 Kafka 主题，请使用以下命令。 将 `$KAFKAZKHOSTS` 替换为配置拓扑时使用的 Zookeeper 主机信息：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    此命令可连接到 Kafka 群集的 Zookeeper，并创建名为 `stormtopic` 的新主题。 Storm 拓扑会使用本主题。

## <a name="start-the-writer"></a>启动编写器

1. 使用以下命令通过 SSH 连接到 Storm 群集。 将 `sshuser` 替换为创建群集时所用的 SSH 用户名。 将 `stormclustername` 替换为 Storm 群集的名称：

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    出现提示时，请输入在创建群集时使用的密码。
   
    有关信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

2. 与 Storm 群集建立 SSH 连接后，使用以下命令启动编写器拓扑：

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    此命令使用的参数如下：

    * `org.apache.storm.flux.Flux`：使用 Flux 配置和运行此拓扑。

    * `--remote`：将拓扑提交到 Nimbus。 拓扑分布在群集中的辅助角色节点。

    * `-R /writer.yaml`：使用 `writer.yaml` 文件配置拓扑。 `-R` 指示此资源包含在 jar 文件中。 它位于 jar 的根目录中，因此 `/writer.yaml` 是它的路径。

    * `--filter`：使用 `dev.properties` 文件中的值填充 `writer.yaml` 拓扑中的条目。 例如，文件中 `kafka.topic` 条目的值用于替换拓扑定义中的 `${kafka.topic}` 条目。

## <a name="start-the-reader"></a>启动读取器

1. 与 Storm 群集建立 SSH 会话后，使用以下命令启动读取器拓扑：

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. 稍等片刻，然后使用以下命令来查看读取器拓扑创建的文件：

    ```bash
    hdfs dfs -ls /stormdata
    ```

    输出与以下文本类似：

        Found 173 items
        -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
        -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
        -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
        ...

3. 若要查看日志文件的内容，请使用以下命令。 将 `filename.txt` 替换为文件的名称：

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    以下文本是文件内容示例：

        four score and seven years ago
        snow white and the seven dwarfs
        i am at two with nature
        snow white and the seven dwarfs
        i am at two with nature
        four score and seven years ago
        an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>停止拓扑

与 Storm 群集建立 SSH 会话后，使用以下命令停止 Storm 拓扑：

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>清理资源

若要清理本教程创建的资源，可以删除资源组。 删除资源组也会删除相关联的 HDInsight 群集，以及与资源组相关联的任何其他资源。

若要使用 Azure 门户删除资源组，请执行以下操作：

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。
2. 找到要删除的资源组，然后右键单击列表右侧的“更多”按钮 (...)。
3. 选择“删除资源组”，然后进行确认。

> [!WARNING]
> 创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集以每分钟按比例收费，因此无需再使用群集时，应始终将其删除。
> 
> 删除 Kafka on HDInsight 群集会删除存储在 Kafka 中的任何数据。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Storm 拓扑来向 Kafka on HDInsight 写入数据以及从中读取数据。 同时还介绍了如何将数据存储到 HDInsight 使用的 HDFS 兼容存储。

若要了解有关使用 Kafka on HDInsight 的详细信息，请参阅 [Kafka 生成者和使用者 API](kafka/apache-kafka-producer-consumer-api.md) 文档。

有关在基于 Linux 的 HDInsight 上部署和监视拓扑的详细信息，请参阅[在基于 Linux 的 HDInsight 上部署和管理 Apache Storm 拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)
