---
title: 将 Azure 事件中心（预览版）上的 Apache Kafka Connect 与 Debezium 集成进行变更数据捕获
description: 本文提供了有关如何将 Debezium 与 Azure 事件中心一起用于 Kafka 的信息。
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: cac04bed797bb9956125bc1a38fdfa5c8285050e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061676"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>将 Azure 事件中心（预览版）上的 Apache Kafka Connect 支持与 Debezium 集成进行变更数据捕获

**变更数据捕获 (CDC)** 是一项技术，用来跟踪为响应创建、更新和删除操作而在数据库表中进行的行级更改。 [Debezium](https://debezium.io/) 是一个基于不同数据库中提供的变更数据捕获功能（例如，[PostgreSQL 中的逻辑解码](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)）构建的分布式平台。 它提供了一组 [Kafka Connect 连接器](https://debezium.io/documentation/reference/1.2/connectors/index.html)，这些连接器会深入探索数据库表中的行级更改，然后将它们转换为事件流，这些事件流随后会发送到 [Apache Kafka](https://kafka.apache.org/)。

本教程介绍了如何使用 [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-about?WT.mc_id=devto-blog-abhishgu)（适用于 Kafka）、[Azure DB for PostgreSQL](../postgresql/overview.md) 和 Debezium 在 Azure 上设置基于变更数据捕获的系统。 它将使用 [Debezium PostgreSQL 连接器](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)将数据库修改从 PostgreSQL 流式传输到 Azure 事件中心内的 Kafka 主题。

在本教程中，我们将执行以下步骤：

> [!div class="checklist"]
> * 创建事件中心命名空间
> * 设置和配置 Azure Database for PostgreSQL
> * 使用 Debezium PostgreSQL 连接器配置并运行 Kafka Connect
> * 测试变更数据捕获
> * （可选）通过 `FileStreamSink` 连接器使用变更数据事件

## <a name="pre-requisites"></a>先决条件
若要完成本演练，你将需要：

- Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- Linux/MacOS
- Kafka 发行版（版本为 1.1.1，Scala 版本为 2.11），通过 [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) 提供
- 通读[用于 Apache Kafka 的事件中心](./event-hubs-for-kafka-ecosystem-overview.md)简介文章

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
要从事件中心服务进行发送和接收，需要使用事件中心命名空间。 有关创建命名空间和事件中心的说明，请参阅[创建事件中心](event-hubs-create.md)。 获取事件中心连接字符串和完全限定域名 (FQDN) 供以后使用。 有关说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>设置和配置 Azure Database for PostgreSQL
[Azure Database for PostgreSQL](../postgresql/overview.md) 是一项关系数据库服务，基于开源 PostgreSQL 数据库引擎的社区版本，并且以两种部署选项提供：单一服务器和超大规模 (Citus)。 请[按照这些说明](../postgresql/quickstart-create-server-database-portal.md)使用 Azure 门户创建 Azure Database for PostgreSQL 服务器。 

## <a name="setup-and-run-kafka-connect"></a>设置并运行 Kafka Connect
本部分包含以下主题：

- Debezium 连接器安装
- 为事件中心配置 Kafka Connect
- 使用 Debezium 连接器启动 Kafka Connect 群集

### <a name="download-and-setup-debezium-connector"></a>下载并设置 Debezium 连接器
请按照 [Debezium 文档](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector)中的最新说明下载并设置连接器。

- 下载连接器的插件存档。 例如，若要下载连接器的 `1.2.0` 版本，请使用此链接 - https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- 提取 JAR 文件并将其复制到 [Kafka Connect plugin.path](https://kafka.apache.org/documentation/#connectconfigs)。


### <a name="configure-kafka-connect-for-event-hubs"></a>为事件中心配置 Kafka Connect
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

### <a name="run-kafka-connect"></a>运行 Kafka Connect
此步骤在本地以分布式模式启动了一个 Kafka Connect 辅助角色，使用事件中心来保留群集状态。

1. 在本地保存上述 `connect-distributed.properties` 文件。  请务必替换大括号中的所有值。
2. 在本地计算机上导航到 Kafka 发行版的位置。
3. 运行 `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` 并等待群集启动。

> [!NOTE]
> Kafka Connect 使用 Kafka AdminClient API 自动创建具有建议配置（包括压缩）的主题。 在 Azure 门户中快速查看命名空间就可以发现，Connect 辅助角色的内部主题已自动创建。
>
> Kafka Connect 内部主题**必须使用压缩**。  如果未正确配置内部连接主题，事件中心团队不负责修复不正确的配置。

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>配置并启动 Debezium PostgreSQL 源连接器

为 PostgreSQL 源连接器创建配置文件 (`pg-source-connector.json`) - 根据你的 Azure PostgreSQL 实例替换这些值。

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` 属性是一个逻辑名称，用于标识并提供所监视的特定 PostgreSQL 数据库服务器/群集的命名空间。 有关详细信息，请查看 [Debezium 文档](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

若要创建连接器的实例，请使用 Kafka Connect REST API 终结点：

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

若要检查连接器的状态，请执行以下命令：

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>测试变更数据捕获
若要查看操作中的变更数据捕获，你需要在 Azure PostgreSQL 数据库中创建/更新/删除记录。

首先连接到 Azure PostgreSQL 数据库（以下示例使用 [psql](https://www.postgresql.org/docs/12/app-psql.html)）

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**创建一个表并插入记录**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

连接器现在应当发挥作用，并将变更数据事件发送到一个事件中心主题，其中包含以下 na,e `my-server.public.todos`（假设你已将 `my-server` 用作 `database.server.name` 的值，并且 `public.todos` 是要跟踪其变更的表（根据 `table.whitelist` 配置））

**检查事件中心主题**

让我们对该主题的内容自检一下，确保一切符合预期。 下面的示例使用 [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat)，但你也可以[使用此处列出的任何选项来创建使用者](apache-kafka-developer-guide.md)

创建包含以下内容的名为 `kafkacat.conf` 的文件：

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> 根据事件中心信息，更新 `kafkacat.conf` 中的 `metadata.broker.list` 和 `sasl.password` 属性。 

在另一个终端中，启动一个使用者：

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

你应该会看到 JSON 有效负载，它们表示为响应你刚才添加到 `todos` 表中的行而在 PostgreSQL 中生成的变更数据事件。 下面是有效负载的代码片段：


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

该事件包含 `payload` 及其 `schema`（为简洁起见，此处进行了省略）。 在 `payload` 部分中，请注意 create 操作 (`"op": "c"`) 是如何表示的 - `"before": null` 表示它是一个新 `INSERT` 的行，`after` 提供了行中各个列的值，`source` 提供了从中选取此事件的 PostgreSQL 实例元数据等信息。

你可以对 update 或 delete 操作进行同样的尝试，并对变更数据事件进行自检。 例如，若要更新 `configure and install connector` 的任务状态（假设其 `id` 为 `3`），可以执行以下命令：

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>（可选）安装 FileStreamSink 连接器
现在，我们已在事件中心主题中捕获了所有 `todos` 表变更，因此将通过 FileStreamSink 连接器（在 Kafka Connect 中默认提供）来使用这些事件。

为连接器创建配置文件 (`file-sink-connector.json`) - 根据你的文件系统替换 `file` 属性

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

若要创建连接器并检查其状态，请执行以下命令：

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

插入/更新/删除数据库记录，并在配置的输出接收器文件中监视记录：

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>清理
Kafka Connect 创建的事件中心主题用于存储配置、偏移量和状态，这些存储的内容在 Connect 群集关闭后也会保留。 除非需要这样进行保存，否则建议将这些主题删除。 可能还需要删除在本演练过程中创建的 `my-server.public.todos` 事件中心。

## <a name="next-steps"></a>后续步骤

若要详细了解适用于 Kafka 的事件中心，请参阅以下文章：  

- [在事件中心镜像 Kafka 中转站](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Spark 连接到事件中心](event-hubs-kafka-spark-tutorial.md)
- [将 Apache Flink 连接到事件中心](event-hubs-kafka-flink-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)
- [将 Akka Streams 连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
