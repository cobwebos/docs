---
title: 在 Spark 中使用 Azure Cosmos DB Cassandra API
description: 本文是 Spark 中 Cosmos DB Cassandra API 集成的主页。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75d2930363b6ad1aeace22d7529df04f31deefe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60893616"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>从 Spark 连接到 Azure Cosmos DB Cassandra API

本文是有关 Spark 中 Azure Cosmos DB Cassandra API 集成的系列文章中的一篇。 这些文章介绍了连接、数据定义语言 (DDL) 操作、基本数据操作语言 (DML) 操作，以及 Spark 中的高级 Azure Cosmos DB Cassandra API 集成。 

## <a name="prerequisites"></a>必备组件
* [预配一个 Azure Cosmos DB Cassandra API 帐户。](create-cassandra-dotnet.md#create-a-database-account)

* 预配所选的 Spark 环境 [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | 其他]。

## <a name="dependencies-for-connectivity"></a>连接的依赖项
* **Cassandra 的 Spark 连接器：** Spark 连接器用于连接到 Azure Cosmos DB Cassandra API。  请识别并使用 [Maven 中心]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector)内与 Spark 环境的 Spark 和 Scala 版本兼容的连接器版本。

* **Cassandra API 的 Azure Cosmos DB 帮助器库：** 除了 Spark 连接器以外，还需要 Azure Cosmos DB 中名为 [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) 的另一个库。 此库包含自定义连接工厂和重试策略类。

  Azure Cosmos DB 中的重试策略配置为处理 HTTP 状态代码 429（“请求速率太大”）异常。 Azure Cosmos DB Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误，你可以结合退让进行重试。 由于 Azure Cosmos DB 使用预配的吞吐量模型，当传入/传出速率增大时，会发生请求速率限制异常。 重试策略可以防范 Spark 作业出现数据高峰（短暂性地超过针对集合分配的吞吐量）。

  > [!NOTE] 
  > 重试策略只能防范 Spark 作业出现短暂的高峰。 如果尚未配置用于运行工作负荷的足够 RU，则重试策略不适用，并且重试策略类会再次引发异常。

* **Azure Cosmos DB 帐户连接详细信息：** Azure Cassandra API 帐户名称、帐户终结点和密钥。
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark 连接器吞吐量配置参数

下表列出了连接器提供的特定于 Azure Cosmos DB Cassandra API 的吞吐量配置参数。 有关所有配置参数的详细列表，请参阅 Spark Cassandra 连接器 GitHub 存储库的[配置参考](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md)页。

| **属性名称** | **默认值** | **说明** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  第 |每个批的行数。 请将此参数设置为 1。 此参数用于提高重型工作负荷的吞吐量。 |
| spark.cassandra.connection.connections_per_executor_max  | 无 | 每个执行器的每个节点的最大连接数。 10*n 相当于 n 节点 Cassandra 群集中每个节点可以建立 10 个连接。 因此，如果需要为 5 节点 Cassandra 群集的每个执行器的每个节点建立 5 个连接，则应将此配置设置为 25。 请根据为 Spark 作业配置的并行度或执行器数目修改此值。   |
| spark.cassandra.output.concurrent.writes  |  100 | 定义每个执行器可以执行的并行写入数。 由于“batch.size.rows”设置为 1，因此请务必相应地增大此值。 请根据工作负荷要实现的并行度或吞吐量修改此值。 |
| spark.cassandra.concurrent.reads |  512 | 定义每个执行器可以执行的并行读取数。 请根据工作负荷要实现的并行度或吞吐量修改此值  |
| spark.cassandra.output.throughput_mb_per_sec  | 无 | 定义每个执行器的总写入吞吐量。 可将此参数用作 Spark 作业吞吐量的上限，并根据 Cosmos DB 集合的预配吞吐量修改此参数。   |
| spark.cassandra.input.reads_per_sec| 无   | 定义每个执行器的总读取吞吐量。 可将此参数用作 Spark 作业吞吐量的上限，并根据 Cosmos DB 集合的预配吞吐量修改此参数。  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | 定义每个 Spark 任务的、在发送到 Cassandra API 之前可以存储在内存中的批数 |
| spark.cassandra.connection.keep_alive_ms | 60000 | 定义在经过多长的时间之后未使用的连接可供使用。 | 

请根据 Spark 作业的预期工作负荷，以及为 Cosmos DB 帐户预配的吞吐量，来调整这些参数的吞吐量和并行度。

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>从 Spark 连接到 Azure Cosmos DB Cassandra API

### <a name="cqlsh"></a>cqlsh
以下命令详细说明如何从 cqlsh 连接到 Azure CosmosDB Cassandra API。  在 Spark 中运行示例时，可以使用此命令进行验证。<br>
**从 Linux/Unix/Mac：**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.Azure Databricks
以下文章介绍了 Azure Databricks 群集预配、连接到 Azure Cosmos DB Cassandra API 时所需的群集配置，以及用于演示 DDL 操作、DML 操作等的几个示例 Notebook。<BR>
[在 Azure Databricks 中使用 Azure Cosmos DB Cassandra API](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.Azure HDInsight-Spark
以下文章介绍了 HDinsight-Spark 服务、预配、连接到 Azure Cosmos DB Cassandra API 时所需的群集配置，以及用于演示 DDL 操作、DML 操作等的几个示例 Notebook。<BR>
[在 HDInsight-Spark 中使用 Azure Cosmos DB Cassandra API](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.常规 Spark 环境
前面的部分与基于 Azure Spark 的 PaaS 服务相关，本部分介绍任何常规 Spark 环境。  下面详细介绍了连接器依赖项、导入和 Spark 会话配置。 “后续步骤”部分提供了 DDL 操作、DML 操作等的代码示例。  

#### <a name="connector-dependencies"></a>连接器依赖项：

1. 添加 Maven 坐标以获取 [Spark 的 Cassandra 连接器](cassandra-spark-generic.md#dependencies-for-connectivity)
2. 添加 Maven 坐标以获取 Cassandra API 的 [Azure Cosmos DB 帮助器库](cassandra-spark-generic.md#dependencies-for-connectivity)

#### <a name="imports"></a>导入：

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark 会话配置：

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>后续步骤

以下文章演示了 Spark 与 Azure Cosmos DB Cassandra API 的集成。 
 
* [DDL 操作](cassandra-spark-ddl-ops.md)
* [创建/插入操作](cassandra-spark-create-ops.md)
* [读取操作](cassandra-spark-read-ops.md)
* [upsert 操作](cassandra-spark-upsert-ops.md)
* [删除操作](cassandra-spark-delete-ops.md)
* [聚合操作](cassandra-spark-aggregation-ops.md)
* [表复制操作](cassandra-spark-table-copy-ops.md)
