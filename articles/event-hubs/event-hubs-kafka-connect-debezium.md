---
title: 在 Azure 事件中心集成 Apache Kafka 连接 (预览版) with Debezium for Change Data Capture
description: 本文介绍如何将 Apache Spark 与适用于 Kafka 的 Azure 事件中心配合使用。
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: a11ec882a50d051a34758562ac84dcef5b799f5f
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136749"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>在 Azure 事件中心集成 Apache Kafka 连接支持 (预览版) with Debezium for Change Data Capture

**变更数据捕获 (CDC) **是一项技术，用于在响应创建、更新和删除操作时跟踪数据库表中的行级更改。 [Debezium](https://debezium.io/)是一个分布式平台，它在不同数据库中可用的变更数据捕获功能的基础上构建 (例如， [PostgreSQL) 中的逻辑解码](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)。 它提供一组[Kafka 连接连接器](https://debezium.io/documentation/reference/1.2/connectors/index.html)，这些连接器可用于在数据库表 () 中点击行级更改，然后将它们转换为事件流，然后将其发送到[Apache Kafka](https://kafka.apache.org/)。

本教程介绍如何在 azure 中使用[Azure 事件 (中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-about?WT.mc_id=devto-blog-abhishgu)（Kafka) 、 [Azure DB For PostgreSQL](../postgresql/overview.md)和 Debezium）在 azure 上设置变更数据捕获系统。 它将使用[Debezium PostgreSQL 连接器](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)从 Azure 事件中心中的 PostgreSQL 对 Kafka 主题流式处理数据库修改

在本教程中，我们将执行以下步骤：

> [!div class="checklist"]
> * 创建事件中心命名空间
> * 安装和配置 Azure Database for PostgreSQL
> * 配置和运行 Kafka Connect with Debezium PostgreSQL 连接器
> * 测试变更数据捕获
> *  (可选) 使用连接器的更改数据事件 `FileStreamSink`

## <a name="pre-requisites"></a>先决条件
若要完成本演练，你将需要：

- Azure 订阅。 如果没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- Linux/MacOS
- Kafka 发行版（版本为 1.1.1，Scala 版本为 2.11），通过 [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) 提供
- 通读[用于 Apache Kafka 的事件中心](./event-hubs-for-kafka-ecosystem-overview.md)简介文章

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间
要从事件中心服务进行发送和接收，需要使用事件中心命名空间。 有关创建命名空间和事件中心的说明，请参阅[创建事件中心](event-hubs-create.md)。 获取事件中心连接字符串和完全限定域名 (FQDN) 供以后使用。 有关说明，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md)。 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>安装和配置 Azure Database for PostgreSQL
[Azure Database for PostgreSQL](../postgresql/overview.md)是基于开源 PostgreSQL 数据库引擎的社区版本的关系数据库服务，提供两个部署选项：单一服务器和超大规模 (Citus) 。 [按照这些说明](../postgresql/quickstart-create-server-database-portal.md)使用 Azure 门户创建 Azure Database for PostgreSQL 服务器。 

## <a name="setup-and-run-kafka-connect"></a>设置并运行 Kafka Connect
本部分将介绍以下主题：

- Debezium 连接器安装
- 为事件中心配置 Kafka Connect
- 启动 Kafka 连接群集与 Debezium 连接器

### <a name="download-and-setup-debezium-connector"></a>下载并设置 Debezium 连接器
请按照[Debezium 文档](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector)中的最新说明下载并设置连接器。

- 下载连接器的插件存档。 例如，若要下载连接器的版本 `1.2.0` ，请使用此链接-https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- 提取 JAR 文件并将其复制到[Kafka Connect 插件。路径](https://kafka.apache.org/documentation/#connectconfigs)。


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

为 PostgreSQL 源连接器创建配置文件 (`pg-source-connector.json`) -根据 Azure PostgreSQL 实例替换这些值。

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
> `database.server.name`属性是一个逻辑名称，用于标识并提供所监视的特定 PostgreSQL 数据库服务器/群集的命名空间。 有关详细信息，请参阅[Debezium 文档](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

若要创建连接器的实例，请使用 Kafka Connect REST API 终结点：

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

检查连接器的状态：

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>测试变更数据捕获
若要查看操作中的变更数据捕获，你将需要在 Azure PostgreSQL 数据库中创建/更新/删除记录。

首先，连接到 Azure PostgreSQL 数据库， (下面的示例使用[psql](https://www.postgresql.org/docs/12/app-psql.html)) 

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**创建表和插入记录**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

现在，连接器应为 "操作"，并将 "更改数据事件" 发送到事件中心主题，其中包含以下 na，e `my-server.public.todos` ，假设你已将 `my-server` 作为的值， `database.server.name` 并且 `public.todos` 是要跟踪其更改的表 (按 `table.whitelist` 配置) 

**检查事件中心主题**

让我们 introspect 该主题的内容，确保一切按预期运行。 下面的示例使用 [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) ，但你也可以[使用此处列出的任何选项创建使用者](apache-kafka-developer-guide.md)

`kafkacat.conf`使用以下内容创建名为的文件：

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> 中的更新 `metadata.broker.list` 和属性，按 `sasl.password` `kafkacat.conf` 事件中心信息。 

在不同的终端中，启动使用者：

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

应该会看到表示 PostgreSQL 中生成的更改数据事件的 JSON 有效负载，以响应刚添加到表中的行 `todos` 。 下面是有效负载的代码段：


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

`payload`对于简洁) ，事件包含和其 `schema` (省略。 在 `payload` 部分中，请注意 create operation (`"op": "c"`) 是如何表示的 `"before": null` ，这意味着它是新的 `INSERT` ed 行， `after` 为该行中的列提供值，并 `source` 提供 PostgreSQL 的实例元数据。

同样，也可以尝试更新或删除操作，并 introspect 更改数据事件。 例如，若要更新 (的任务状态， `configure and install connector` 假设其 `id` 为 `3`) ：

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a> (可选) 安装 FileStreamSink 连接器
在 `todos` 事件中心主题中捕获所有表更改后，我们将使用 FileStreamSink 连接器 (默认情况下，Kafka Connect) 中提供使用这些事件。

为连接器创建配置文件 (`file-sink-connector.json`) -将 `file` 属性替换为每个文件系统

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

若要创建连接器并检查其状态：

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

插入/更新/删除数据库记录，并在配置的输出接收器文件中监视记录：

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>清理
Kafka Connect 创建的事件中心主题用于存储配置、偏移量和状态，这些存储的内容在 Connect 群集关闭后也会保留。 除非需要这样进行保存，否则建议将这些主题删除。 你可能还需要删除在 `my-server.public.todos` 本演练过程中创建的事件中心。

## <a name="next-steps"></a>后续步骤

若要详细了解适用于 Kafka 的事件中心，请参阅以下文章：  

- [在事件中心镜像 Kafka 中转站](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Spark 连接到事件中心](event-hubs-kafka-spark-tutorial.md)
- [将 Apache Flink 连接到事件中心](event-hubs-kafka-flink-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)
- [将 Akka Streams 连接到事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [针对 Azure 事件中心的 Apache Kafka 开发人员指南](apache-kafka-developer-guide.md)
