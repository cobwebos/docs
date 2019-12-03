---
title: 将 Apache Kafka on HDInsight 与 Azure IoT 中心配合使用
description: 了解如何将 Apache Kafka on HDInsight 与 Azure IoT 中心配合使用。 Kafka Connect Azure IoT 中心项目为 Kafka 提供源和接收器连接器。 源连接器可从 IoT 中心读取数据，接收器连接器将数据写入 IoT 中心。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 884d10ce1bc5e6b710c849d0be1cb9165caac4c5
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706095"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>将 Apache Kafka on HDInsight 与 Azure IoT 中心配合使用

了解如何使用 [Apache Kafka Connect Azure IoT 中心](https://github.com/Azure/toketi-kafka-connect-iothub)连接器在 Apache Kafka on HDInsight 与 Azure IoT 中心之间移动数据。 本文档介绍如何通过群集中的边缘节点运行 IoT 中心连接器。

使用 Kafka Connect API 可以实施所需的连接器，用于将数据连续提取到 Kafka，或者将数据从 Kafka 推送到另一个系统。 [Apache Kafka Connect Azure IoT 中心](https://github.com/Azure/toketi-kafka-connect-iothub)是可将数据从 Azure IoT 中心提取到 Kafka 的连接器。 该连接器还能将数据从 Kafka 推送到 IoT 中心。

从 IoT 中心提取数据时，可以使用__源__连接器。 将数据推送到 IoT 中心时，可以使用__接收器__连接器。 IoT 中心连接器同时提供源连接器和接收器连接器。

下图显示了在使用连接器时，Azure IoT 中心与 Kafka on HDInsight 之间的数据流。

![显示通过连接器将数据从 IoT 中心传送到 Kafka 的图像](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

有关 Connect API 的详细信息，请参阅 [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)。

## <a name="prerequisites"></a>必备组件

* HDInsight 上的 Apache Kafka 群集。 有关详细信息，请参阅 [Kafka on HDInsight 快速入门](apache-kafka-get-started.md)文档。

* Kafka 群集中的边缘节点。 有关详细信息，请参阅[将边缘节点与 HDInsight 配合使用](../hdinsight-apps-use-edge-node.md)文档。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* Azure IoT 中心和设备。 本文介绍[如何使用 Connect Raspberry Pi online 模拟器连接到 Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started)。

* [Scala 生成工具](https://www.scala-sbt.org/)。

## <a name="build-the-connector"></a>构建连接器

1. 将连接器的源从[https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/)下载到本地环境。

2. 在命令提示符下，导航到 `toketi-kafka-connect-iothub-master` 目录。 然后使用以下命令生成和打包项目：

    ```cmd
    sbt assembly
    ```

    生成需要几分钟才能完成。 命令在项目的 `toketi-kafka-connect-iothub-master\target\scala-2.11` 目录中创建一个名为 `kafka-connect-iothub-assembly_2.11-0.7.0.jar` 的文件。

## <a name="install-the-connector"></a>安装连接器

1. 将 .jar 文件上传到 Kafka on HDInsight 群集的边缘节点。 将 `CLUSTERNAME` 替换为群集的实际名称，以编辑以下命令。 下面使用 SSH 用户帐户和[边缘节点](../hdinsight-apps-use-edge-node.md#access-an-edge-node)名称的默认值，并根据需要进行修改。

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 文件复制完成后，使用 SSH 连接到边缘节点：

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 若要在 Kafka `libs` 目录中安装连接器，请使用以下命令：

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

使 SSH 连接保持活动状态，以执行剩余的步骤。

## <a name="configure-apache-kafka"></a>配置 Apache Kafka

在 SSH 连接到边缘节点后，使用以下步骤将 Kafka 配置为在独立模式下运行连接器：

1. 设置密码变量。 将 PASSWORD 替换为群集登录密码，然后输入以下命令：

    ```bash
    export password='PASSWORD'
    ```

1. 安装[jq](https://stedolan.github.io/jq/)实用程序。 jq 可以更轻松地处理从 Ambari 查询返回的 JSON 文档。 输入以下命令：

    ```bash
    sudo apt -y install jq
    ```

1. 获取 Kafka 代理的地址。 群集中可能有许多的代理，但只需引用其中的一到两个。 若要获取两个代理主机的地址，请使用以下命令：

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    复制值供以后使用。 返回的值类似于下文：

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. 获取 Apache Zookeeper 节点的地址。 群集中有多个 Zookeeper 节点，但只需引用其中的一到两个。 使用以下命令将地址存储在变量 `KAFKAZKHOSTS`中：

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. 在独立模式下运行连接器时，将使用 `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` 文件来与 Kafka 代理通信。 若要编辑 `connect-standalone.properties` 文件，请使用以下命令：

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. 进行以下编辑：

    |当前值 |新值 | 注释 |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|将 `localhost:9092` 值替换为上一步中的代理主机|配置边缘节点的独立配置以查找 Kafka 代理。|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|做出此项更改后，可以使用 Kafka 随附的控制台生成方执行测试。 对于其他生产方和使用方，可能需要不同的转换器。 有关使用其他转换器值的信息，请参阅 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|同上。|
    |N/A|`consumer.max.poll.records=10`|添加到文件末尾。 此项更改会将接收器连接器限制为每次处理 10 条记录，防止该连接器发生超时。 有关详细信息，请参阅[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。|

1. 要保存文件，请使用 __Ctrl + X__、__Y__，并按 __Enter__。

1. 若要创建连接器使用的主题，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    若要验证 `iotin` 和 `iotout` 主题是否存在，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` 主题用于从 IoT 中心接收消息。 `iotout` 主题用于向 IoT 中心发送消息。

## <a name="get-iot-hub-connection-information"></a>获取 IoT 中心连接信息

若要检索连接器使用的 IoT 中心信息，请使用以下步骤：

1. 获取事件中心兼容的终结点，以及 IoT 中心的事件中心兼容终结点名称。 若要获取此信息，请使用以下方法之一：

   * __在 [Azure 门户](https://portal.azure.com/)中__使用以下步骤：

     1. 导航到 IoT 中心并选择“终结点”。
     2. 在“内置终结点”中，选择“事件”。
     3. 在“属性”中，复制以下字段的值：

         * __与事件中心兼容的名称__
         * __与事件中心兼容的终结点__
         * __分区__

        > [!IMPORTANT]  
        > 门户中的终结点值可能包含本示例不需要的额外文本。 提取与模式 `sb://<randomnamespace>.servicebus.windows.net/` 匹配的文本。

   * __在 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ 中使用以下命令：

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       将 `myhubname` 替换为 IoT 中心的名称。 响应类似于以下文本：

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. 获取__共享访问策略__和__密钥__。 本示例使用__服务__密钥。 若要获取此信息，请使用以下方法之一：

    * __在 [Azure 门户](https://portal.azure.com/)中__使用以下步骤：

        1. 依次选择“共享访问策略”、“服务”。
        2. 复制“主密钥”值。
        3. 复制“连接字符串 - 主键”值。

    * __在 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ 中使用以下命令：

        1. 若要获取主密钥值，请使用以下命令：

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            将 `myhubname` 替换为 IoT 中心的名称。 响应是发送到此中心的 `service` 策略的主密钥。

        2. 若要获取 `service` 策略的连接字符串，请使用以下命令：

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            将 `myhubname` 替换为 IoT 中心的名称。 响应是 `service` 策略的连接字符串。

## <a name="configure-the-source-connection"></a>配置源连接

若要将源配置为使用 IoT 中心，请在与边缘节点建立 SSH 连接后执行以下操作：

1. 在 `/usr/hdp/current/kafka-broker/config/` 目录中创建 `connect-iot-source.properties` 文件的副本。 若要从 toketi-kafka-connect-iothub 项目下载文件，请使用以下命令：

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. 若要编辑 `connect-iot-source.properties` 文件并添加 IoT 中心信息，请使用以下命令：

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. 在编辑器中，找到并更改以下条目：

    |当前值 |编辑|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|将 `PLACEHOLDER` 替换为 `iotin`。 从 IoT 中心收到的消息将放入 `iotin` 主题中。|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|将 `PLACEHOLDER` 替换为与事件中心兼容的名称。|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|将 `PLACEHOLDER` 替换为与事件中心兼容的终结点。|
    |`IotHub.AccessKeyName=PLACEHOLDER`|将 `PLACEHOLDER` 替换为 `service`。|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|将 `PLACEHOLDER` 替换为 `service` 策略的主密钥。|
    |`IotHub.Partitions=PLACEHOLDER`|将 `PLACEHOLDER` 替换为前面步骤中的分区数。|
    |`IotHub.StartTime=PLACEHOLDER`|将 `PLACEHOLDER` 替换为 UTC 日期。 此日期是连接器开始检查消息的时间。 日期格式为 `yyyy-mm-ddThh:mm:ssZ`。|
    |`BatchSize=100`|将 `100` 替换为 `5`。 做出此项更改后，如果 IoT 中心出现五条新消息，则连接器会将消息读入 Kafka。|

    有关示例配置，请参阅[Azure IoT 中心的 Kafka 连接源连接器](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)。

1. 若要保存更改，请依次按 __Ctrl + X__、__Y__、__Enter__。

有关配置连接器源的详细信息，请参阅 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)。

## <a name="configure-the-sink-connection"></a>配置接收器连接

若要将接收器连接配置为使用 IoT 中心，请在与边缘节点建立 SSH 连接后执行以下操作：

1. 在 `/usr/hdp/current/kafka-broker/config/` 目录中创建 `connect-iothub-sink.properties` 文件的副本。 若要从 toketi-kafka-connect-iothub 项目下载文件，请使用以下命令：

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. 若要编辑 `connect-iothub-sink.properties` 文件并添加 IoT 中心信息，请使用以下命令：

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. 在编辑器中，找到并更改以下条目：

    |当前值 |编辑|
    |---|---|
    |`topics=PLACEHOLDER`|将 `PLACEHOLDER` 替换为 `iotout`。 写入 `iotout` 主题的消息将转发到 IoT 中心。|
    |`IotHub.ConnectionString=PLACEHOLDER`|将 `PLACEHOLDER` 替换为 `service` 策略的连接字符串。|

    有关示例配置，请参阅适用[于 Azure IoT 中心的 Kafka 连接接收器连接器](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。

1. 若要保存更改，请依次按 __Ctrl + X__、__Y__、__Enter__。

有关配置连接器接收器的详细信息，请参阅 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。

## <a name="start-the-source-connector"></a>启动源连接器

1. 若要启动源连接器，请在与边缘节点建立 SSH 连接后使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    启动连接器后，将消息从设备发送到 IoT 中心。 当连接器从 IoT 中心读取消息并将其存储在 Kafka 主题时，会将信息记录到控制台：

    ```text
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > 连接器启动时，可能会出现几条警告。 这些警告不会影响从 IoT 中心接收消息。

1. 几分钟后，请使用**Ctrl + C**停止连接器。 连接器停止需要几分钟时间。

## <a name="start-the-sink-connector"></a>启动接收器连接器

与边缘节点建立 SSH 连接后，使用以下命令在独立模式下启动接收器连接器：

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

连接器运行时，会显示类似于以下文本的信息：

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> 连接器启动时，可能会出现几条警告。 可以放心地忽略这些警告。

## <a name="send-messages"></a>发送消息

若要通过连接器发送消息，请使用以下步骤：

1. 打开到 Kafka 群集的*第二个*SSH 会话：

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 获取新 ssh 会话的 Kafka 代理的地址。 将 PASSWORD 替换为群集登录密码，然后输入以下命令：

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. 若要将消息发送到 `iotout` 主题，请使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    此命令不会将你返回到正常的 Bash 提示符。 而是将键盘输入发送到 `iotout` 主题。

1. 若要将消息发送到设备，请将一个 JSON 文档粘贴到 `kafka-console-producer` 的 SSH 会话中。

    > [!IMPORTANT]  
    > 必须将 `"deviceId"` 条目的值设置为设备 ID。 在以下示例中，设备名为 `myDeviceId`：

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) 中更详细地介绍了此 JSON 文档的架构。

    如果使用模拟的 Raspberry Pi 设备，并且该设备正在运行，则设备会记录以下消息：

    ```text
    Receive message: Turn On
    ```

    重新发送 JSON 文档，但这次请更改 `"message"` 条目的值。 设备会记录新值。

有关使用接收器连接器的详细信息，请参阅 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。

## <a name="next-steps"></a>后续步骤

本文档已介绍如何使用 Apache Kafka Connect API 在 HDInsight 上启动 IoT Kafka 连接器。 使用以下链接来发现与 Kafka 配合使用的其他方式：

* [将 Apache Spark 与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-spark-with-kafka.md)
* [将 Apache Storm 与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-storm-with-kafka.md)
