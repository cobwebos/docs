---
title: Spark 上 Azure Cosmos DB Cassandra API 中的 DDL 操作
description: 本文详细介绍了针对 Spark 上 Azure Cosmos DB Cassandra API 的密钥空间和表 DDL 操作。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 5c12787cd6e0df19fd842dd44da49aa5ea97aa05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60898876"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Spark 上 Azure Cosmos DB Cassandra API 中的 DDL 操作

本文详细介绍了针对 Spark 上 Azure Cosmos DB Cassandra API 的密钥空间和表 DDL 操作。

## <a name="cassandra-api-related-configuration"></a>与 Cassandra API 相关的配置 

```scala
import org.apache.spark.sql.cassandra._

//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="keyspace-ddl-operations"></a>密钥空间 DDL 操作

### <a name="create-a-keyspace"></a>创建密钥空间

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

在 cqlsh 中运行以下命令，可看到先前创建的密钥空间。

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>删除密钥空间

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>表 DDL 操作

**注意事项：**  

- 可使用 create table 语句在表级别分配吞吐量。  
- 一个分区键可存储 10 GB 的数据。  
- 一条记录最多可存储 2 MB 的数据。  
- 一个分区键范围可存储多个分区键。

### <a name="create-a-table"></a>创建表

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

在 cqlsh 中运行以下命令，可看到名为“books”的表 

```bash
USE books_ks;
DESCRIBE books;
```

预配的吞吐量和默认 TTL 值未显示在上一个命令的输出中，可从门户获取这些值。

### <a name="alter-table"></a>更改表

可使用 alter table 命令更改以下值：

* 预配的吞吐量 
* 生存时间值
<br>目前不支持更改列。

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>删除表

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>在 cqlsh 中验证

在 cqlsh 中运行以下命令，可看到“books”表不再可用：

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>后续步骤

创建密钥空间和表后，请继续阅读以下有关 CRUD 操作的文章：
 
* [创建/插入操作](cassandra-spark-create-ops.md)  
* [读取操作](cassandra-spark-read-ops.md)  
* [upsert 操作](cassandra-spark-upsert-ops.md)  
* [删除操作](cassandra-spark-delete-ops.md)  
* [聚合操作](cassandra-spark-aggregation-ops.md)  
* [表复制操作](cassandra-spark-table-copy-ops.md)  
