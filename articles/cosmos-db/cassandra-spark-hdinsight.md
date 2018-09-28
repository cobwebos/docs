---
title: 使用 HDInsight 从 YARN 上的 Spark 访问 Azure Cosmos DB Cassandra API
description: 本文介绍如何使用 HDInsight 从 YARN 上的 Spark 使用 Azure Cosmos DB Cassandra API
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: f71f5fa71d685af103bd82b3ccd2a910ab81d90f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962907"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>使用 HDInsight 从 YARN 上的 Spark 访问 Azure Cosmos DB Cassandra API

本文介绍如何使用 spark-shell 的 HDInsight 从 YARN 上的 Spark 访问 Azure Cosmos DB Cassandra API。 HDInsight 是 Microsoft 在 Azure 上的 Hortonworks Hadoop PaaS，可利用 HDFS 的对象存储，包括 [Spark](../hdinsight/spark/apache-spark-overview.md) 在内，有多种类型。  虽然本文档中的内容参考了 HDInsight Spark，但可适用于所有 Hadoop 分发。  

## <a name="prerequisites"></a>先决条件

* [预配 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [回顾连接到 Azure Cosmos DB Cassandra API 的基础知识](cassandra-spark-generic.md)

* [预配 HDInsight-Spark 群集](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [查看使用 Cassandra API 的代码示例](cassandra-spark-generic.md#next-steps)

* [使用 cqlsh 进行验证（如需要）](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* Spark2 中的 Cassandra API 配置 - Cassandra 的 Datastax 连接器需要将 Cassandra 连接详细信息作为 Spark 上下文的一部分进行初始化。 启动 Jupyter 笔记本时，Spark 会话和上下文已初始化，因此，不建议停止然后重新初始化 Spark 上下文，除非它是完整的，将每项配置都作为 HDInsight 默认 Jupyter Notebook 启动的一部分进行设置。 解决办法是将 Cassandra 实例详细信息直接添加到 Ambari 中的 Spark2 服务配置。 这是针对每个需要 Spark2 服务重新启动的群集的一次性活动。
 
  1. 依次转到 Ambari 和 Spark2 服务，然后单击 configs

  2. 然后转到自定义 spark2-defaults 并添加具有以下内容的新属性，然后重新启动 Spark2 服务：

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>从 Spark shell 访问 Azure Cosmos DB Cassandra API

Spark shell 用于测试/探索目的。

* 启动 Spark shell，其中包含与群集的 Spark 版本兼容的所需 maven 依赖项。

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* 执行一些 DDL 和 DML 操作

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //datastax Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* 运行 CRUD 操作

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>从 Jupyter Notebook 访问 Azure Cosmos DB Cassandra API

HDInsight Spark 附带有 Zeppelin 和 Jupyter Notebook 服务。 二者均为支持 Scala 和 Python 的基于 Web 的笔记本环境。 Notebook 非常适用于交互式探索性分析和协作，但不适用于运营/生产性的流程。

以下 Jupyter Notebook 可以上传到 HDInsight Spark 群集，并提供使用 Azure Cosmos DB Cassandra API 的现成示例。 请务必查看第一个笔记本 `1.0-ReadMe.ipynb`，以查看用于连接到 Azure Cosmos DB Cassandra API 的 Spark 服务配置。

将 [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) 下的 Notebook 下载到计算机。
  
### <a name="how-to-upload"></a>如何上传：
启动 Jupyter 时，导航到 Scala。 先创建一个目录，然后将笔记本上传到该目录。 上传按钮位于顶部右侧。  

### <a name="how-to-run"></a>如何运行：
依次运行笔记本，按顺序运行每个笔记本单元。  单击每个笔记本顶部的“运行”按钮来执行所有单元，或按 shift+enter 来执行每个单元。

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>从 Spark Scala 程序访问 Azure Cosmos DB Cassandra API

对于生产中的自动化流程，Spark 程序通过 [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) 提交到群集。

## <a name="next-steps"></a>后续步骤

* [如何在 IDE 中生成 Spark Scala 程序并通过 Livy 将其提交 HDInsight Spark 群集以供执行](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [如何从 Spark Scala 程序连接到 Azure Cosmos DB Cassandra API](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [使用 Cassandra API 的代码示例完整列表](cassandra-spark-generic.md)
