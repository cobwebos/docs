---
title: 与 Apache Kafka Connect 集成 - Azure 事件中心 | Microsoft Docs
description: 本文介绍如何将 Apache Spark 与适用于 Kafka 的 Azure 事件中心配合使用。
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 84220d5dda26c25f40138629e2be1f10d57fe3c4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555127"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>在 Azure 事件中心集成 Apache Kafka Connect 支持（预览版）
随着业务需求的引入增加，对各种外部源和接收器的引入需求也增加。 [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) 提供的框架可以通过 Kafka 群集连接到任何外部系统（例如 MySQL、HDFS）和文件系统并与之进行数据的导入/导出。 本教程详细介绍如何将 Kafka Connect 框架与支持 Kafka 的事件中心配合使用。

本教程详细介绍如何将 Kafka Connect 与支持 Kafka 的 Azure 事件中心集成，以及如何部署基本的 FileStreamSource 和 FileStreamSink 连接器。 此功能目前处于预览状态。 虽然这些连接器不是用于生产的，但它们可以用于演示端到端 Kafka Connect 方案，让 Azure 事件中心充当 Kafka 中转站。

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) 上提供了此示例。

在本教程中，我们将执行以下步骤：

> [!div class="checklist"]
> * 创建事件中心命名空间
> * 克隆示例项目
> * 为事件中心配置 Kafka Connect
> * 运行 Kafka Connect
> * 创建连接器

## <a name="prerequisites"></a>先决条件
若要完成本演练，请确保具备以下先决条件：

- Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka 发行版（版本为 1.1.1，Scala 版本为 2.11），通过 [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) 提供
- 通读[用于 Apache Kafka 的事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)简介文章

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
要从事件中心服务进行发送和接收，需要使用事件中心命名空间。 请参阅[创建已启用 Kafka 的事件中心](event-hubs-create.md)，了解如何获取事件中心 Kafka 终结点。 获取事件中心连接字符串和完全限定域名 (FQDN) 供以后使用。 有关说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 

## <a name="clone-the-example-project"></a>克隆示例项目
克隆 Azure 事件中心存储库并导航到 tutorials/connect 子文件夹： 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>为事件中心配置 Kafka Connect
将 Kafka Connect 吞吐量从 Kafka 重定向到事件中心时，必须进行最低限定的重新配置。  以下 `connect-distributed.properties` 示例演示了如何配置 Connect，以便进行身份验证并与事件中心的 Kafka 终结点通信：

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>运行 Kafka Connect

此步骤在本地以分布式模式启动了一个 Kafka Connect 辅助角色，使用事件中心来保留群集状态。

1. 在本地保存上述 `connect-distributed.properties` 文件。  请务必替换大括号中的所有值。
2. 在本地计算机上导航到 Kafka 发行版的位置。
4. 运行 `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`。  看到 `'INFO Finished starting connectors and tasks'` 时，Connect 辅助角色 REST API 就可以进行交互了。 

> [!NOTE]
> 事件中心支持 Kafka 客户端自动创建主题。 在 Azure 门户中快速查看命名空间就可以发现，Connect 辅助角色的内部主题已自动创建。

### <a name="create-connectors"></a>创建连接器
此部分详细介绍如何启动 FileStreamSource 和 FileStreamSink 连接器。 

1. 为输入和输出数据文件创建目录。
    ```bash
    mkdir ~/connect-quickstart
    ```

2. 创建两个文件：一个文件包含种子数据，供 FileStreamSource 连接器读取，另一个文件供 FileStreamSink 连接器写入。
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. 创建 FileStreamSource 连接器。  确保将大括号替换为主目录路径。
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    运行以上命令以后，应该会在事件中心实例上看到事件中心 `connect-quickstart`。
4. 检查源连接器的状态。
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    可以选择使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/releases)来验证事件是否已到达 `connect-quickstart` 主题。

5. 创建 FileStreamSink 连接器。  再次请确保将大括号替换为主目录路径。
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. 检查接收器连接器的状态。
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. 验证是否已在文件之间复制数据，以及两个文件之间的数据是否相同。
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>清理
Kafka Connect 创建的事件中心主题用于存储配置、偏移量和状态，这些存储的内容在 Connect 群集关闭后也会保留。 除非需要这样进行保存，否则建议将这些主题删除。 可能还需删除在本演练过程中创建的 `connect-quickstart` 事件中心。

## <a name="next-steps"></a>后续步骤

若要详细了解事件中心和适用于 Kafka 的事件中心，请参阅以下主题：  

- [了解事件中心](event-hubs-what-is-event-hubs.md)
- [用于 Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)
- [如何创建启用 Kafka 的事件中心](event-hubs-create-kafka-enabled.md)
- [从 Kafka 应用程序流式传输到事件中心](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [将 Kafka 代理镜像到已启用 Kafka 的事件中心](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Spark 连接到已启用 Kafka 的事件中心](event-hubs-kafka-spark-tutorial.md)
- [将 Apache Flink 连接到已启用 Kafka 的事件中心](event-hubs-kafka-flink-tutorial.md)
- [将 Akka Streams 连接到已启用 Kafka 的事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)
