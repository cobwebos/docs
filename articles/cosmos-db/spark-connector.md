---
title: 将 Apache Spark 连接到 Azure Cosmos DB
description: 了解可将 Apache Spark 连接到 Azure Cosmos DB 的 Azure Cosmos DB Spark 连接器。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bc0f2044f70c674177f9c9786f56f0441db2e282
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978902"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>使用 Apache Spark 到 Azure Cosmos DB 的连接器加速大数据分析

可以使用 Cosmos DB Spark 连接器针对 Azure Cosmos DB 中存储的数据运行 [Spark](https://spark.apache.org/) 作业。 Cosmos 可用于批处理和流式处理，并可充当服务层来降低访问延迟。

可以使用的连接器，并[Azure Databricks](https://azure.microsoft.com/services/databricks)或[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)，提供在 Azure 上托管的 Spark 群集。 下表显示了支持的 Spark 版本。

| 组件 | Version |
|---------|-------|
| Apache Spark | 2.4.x、2.3.x、2.2.x 和 2.1.x |
| Scala | 2.11 |
| Azure Databricks 运行时版本 | > 3.4 |

> [!WARNING]
> 此连接器支持 Azure Cosmos DB 的核心 (SQL) API。
> 对于 Cosmos DB for MongoDB API，请使用 [MongoDB Spark 连接器](https://docs.mongodb.com/spark-connector/master/)。
> 对于 Cosmos DB Cassandra API，请使用 [Cassandra Spark 连接器](https://github.com/datastax/spark-cassandra-connector)。
>

## <a name="quickstart"></a>快速入门

* 遵循 [Java SDK 入门](sql-api-async-java-get-started.md)中的步骤设置 Cosmos DB 帐户，并填充一些数据。
* 遵循的步骤[Azure Databricks 入门](https://docs.azuredatabricks.net/getting-started/index.html)设置 Azure Databricks 工作区和群集。
* 现在可以创建新的 Notebook，并导入 Cosmos DB 连接器库。 有关如何设置工作区的详细信息，请跳转到[使用 Cosmos DB 连接器](#bk_working_with_connector)。
* 以下部分提供了有关如何使用连接器读取和写入数据的代码片段。

### <a name="reading-from-cosmos-db"></a>从 Cosmos DB 读取数据

以下代码片段演示如何在 PySpark 中创建一个 Spark 数据帧以从 Cosmos DB 读取数据。

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

Scala 中的相同代码片段：

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="writing-to-cosmos-db"></a>写入 Cosmos DB

以下代码片段演示如何在 PySpark 中将数据帧写入 Cosmos DB。

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

Scala 中的相同代码片段：

```scala
// Configure connection to the sink collection
val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Upsert the dataframe to Cosmos DB
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

有关更多代码片段和端到端的示例，请参阅 [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks)。

## <a name="bk_working_with_connector"></a> 使用连接器

可以基于 Github 中的源代码生成连接器，或通过以下链接从 Maven 下载 uber jar。

| Spark | Scala | 最新版本 |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.3.5](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>使用 Databricks notebook

创建 Databricks 工作区使用 Azure Databricks 指南中的指南库 >[使用 Azure Cosmos DB Spark 连接器](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> 请注意，**使用 Azure Cosmos DB Spark 连接器**页当前不是最新。 而不是到六个不同的库下载的六个单独的 jar，您可以从 maven 在下载 uber jar https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.5/jar) 和安装此一个 jar/库。
> 

### <a name="using-spark-cli"></a>使用 spark-cli

若要通过 spark-cli 使用连接器（即 `spark-shell`、`pyspark`、`spark-submit`），可以结合连接器的 [maven 坐标](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)使用 `--packages` 参数。

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5"

```

### <a name="using-jupyter-notebooks"></a>使用 Jupyter Notebook

如果在 HDInsight 中使用 Jupyter Notebook，可以使用 spark-magic `%%configure` 单元指定连接器的 maven 坐标。

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.3.5",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> 请注意，包含 `spark.jars.excludes` 旨在消除连接器、Apache Spark 和 Livy 之间的潜在冲突。

### <a name="build-the-connector"></a>生成连接器

目前，此连接器项目使用 `maven`，因此，若要生成不带依赖项的项目，可以运行：

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>使用我们的示例

[Cosmos DB Spark GitHub 存储库](https://github.com/Azure/azure-cosmosdb-spark)包含以下可供你尝试的示例 Notebook 和脚本。

* **使用 Spark 和 Cosmos DB 确保航班准时（西雅图）** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)：使用 HDInsight Jupyter Notebook 服务将 Spark 连接到 Cosmos DB，以展示 Spark SQL 和图形帧，并使用机器学习管道预测航班延误。
* **[将 Spark 连接到 Cosmos DB 更改源](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark%2Band%2BCosmos%2BDB%2BChange%2BFeed.ipynb)** ：有关如何将 Spark 连接到 Cosmos DB 更改源的快速展示。
* **Twitter 源与 Apache Spark 和 Azure Cosmos DB 更改源**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **使用 Apache Spark 到 Cosmos DB 图形查询**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[连接到 Azure Cosmos DB 的 Azure Databricks](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** 使用`azure-cosmosdb-spark`。  在此处链接也是 Azure Databricks 版本[航班正常率 notebook](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)。
* **[使用 Azure Cosmos DB 和 HDInsight (Apache Spark) 的 Lambda 体系结构](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** ：可以使用 Cosmos DB 和 Spark 减少维护大数据管道所造成的操作开销。

## <a name="more-information"></a>更多信息

我们在 `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) 中提供了更多信息，包括：

* [Azure Cosmos DB Spark 连接器用户指南](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [聚合示例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>配置和设置

* [Spark 连接器配置](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark 到 Cosmos DB 的连接器设置](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)（编写中）
* [通过 Apache Spark (HDI) 配置 Power BI 以直接查询 Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>故障排除

* [使用 Cosmos DB 聚合](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [已知问题](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>性能

* [性能提示](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [查询测试运行](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [编写测试运行](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>更改源

* [使用 Azure Cosmos DB 更改源和 Apache Spark 流处理更改](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [更改源演示](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [结构化流演示](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>监视

* [使用 Application Insights 监视 Spark 作业](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>后续步骤

从 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存储库下载 Spark 到 Azure Cosmos DB 连接器（如果尚未下载）。 浏览存储库中的其他资源：

* [聚合示例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [示例脚本和 Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

此外，还可以查看 [Apache Spark SQL、数据帧和数据集指南](https://spark.apache.org/docs/latest/sql-programming-guide.html)以及 [Azure HDInsight 上的 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 一文。
