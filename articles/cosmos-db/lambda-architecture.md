---
title: 使用 Azure Cosmos DB 和 HDInsight (Apache Spark) 的 Lambda 体系结构 | Microsoft Docs
description: 本文介绍如何实现使用 Azure Cosmos DB、HDInsight 和 Spark 的 lambda 体系结构
keywords: lambda-architecture
services: cosmos-db
documentationcenter: ''
author: dennyglee
manager: kfile
editor: ''
ms.assetid: 273aeae9-e31c-4a43-b216-5751c46f212e
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: 9c1a8c212fbacde9fbf69fdf9bf72ed2393786db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB：在 Azure 平台上实现 lambda 体系结构 

使用 Lambda 体系结构可对大型数据集进行高效的数据处理。 Lambda 体系结构使用批处理、流式处理和服务层，将查询大数据时存在的延迟降到最低。 

若要在 Azure 上实现 lambda 体系结构，可以结合以下技术来加快实时大数据分析：
* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)：本行业第一款全球分布式多模型数据库服务。 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/)：用于运行大规模数据分析应用程序的处理框架。
* Azure Cosmos DB [更改源](change-feed.md)：将新数据流式传输到批处理层供 HDInsight 处理
* [Spark 到 Azure Cosmos DB 的连接器](spark-connector.md)

本文介绍基于原始多层设计的 lambda 体系结构的基础知识，以及可以简化操作的、“经过重建”的 lambda 体系结构的优势。  

有关 lambda 体系结构的概述以及可在 lambda 体系结构示例中使用的资源，请观看以下视频：

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>什么是 lambda 体系结构？
lambda 体系结构是一种通用、可缩放且容错的数据处理体系结构，可以解决 [Nathan Marz](https://twitter.com/nathanmarz) 所述的批处理和速度延迟方案。

![显示 lambda 体系结构的示意图](./media/lambda-architecture/lambda-architecture-intro.png)

源：http://lambda-architecture.net/

上图根据 [https://lambda-architecture.net](http://lambda-architecture.net/) 中的内容描绘了 lambda 体系结构的基本原理。

 1. 所有**数据**同时推送到批处理层和速度层。
 2. **批处理层**包含主数据集（不可变、仅限追加的原始数据集），并预先计算批处理视图。
 3. **服务层**包含快速查询的批处理视图。 
 4. **速度层**补偿处理时间（针对服务层），只处理最新的数据。
 5. 通过合并批处理视图和实时视图中的结果或者单独 ping 每个结果，可以应答所有查询。

阅读后续内容后，只需使用以下内容即可实现此体系结构：

* Azure Cosmos DB 集合
* HDInsight (Apache Spark 2.1) 群集
* Spark 连接器 [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>速度层

从操作的角度看，既要维护两个数据流，又要确保数据状态正确，可能是一项复杂的任务。 为了简化操作，可以利用 [Azure Cosmos DB 更改源支持](change-feed.md)来保留批处理层的状态，同时通过速度层的更改源 API 来展示 Azure Cosmos DB 更改日志。  
![突出显示 lambda 体系结构的新数据、速度层和主数据集部分的示意图](./media/lambda-architecture/lambda-architecture-change-feed.png)

这些层中的要点包括：

 1. 所有**数据**只会推送到 Azure Cosmos DB，因此可以避免多重强制转换问题。
 2. **批处理层**包含主数据集（不可变、仅限追加的原始数据集），并预先计算批处理视图。
 3. 下一部分介绍**服务层**。
 4. **速度层**利用 HDInsight (Apache Spark) 读取 Azure Cosmos DB 更改源。 这样，便可以持久保存数据，同时可并行查询和处理数据。
 5. 通过合并批处理视图和实时视图中的结果或者单独 ping 每个结果，可以应答所有查询。
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>代码示例：流式传输到 Azure Cosmos DB 更改源的 Spark 结构化数据
若要运行**速度层**中包含的 Azure Cosmos DB 更改源的快速原型，可以使用[使用 Azure Cosmos DB 更改源和 Apache Spark 流处理更改](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)示例中的 Twitter 数据来测试该原型。 若要立即开始生成 Twitter 输出，请参阅中[将源从 Twitter 流式传输到 Cosmos DB](https://github.com/tknandu/TwitterCosmosDBFeed) 中的代码示例。 沿用前面的示例，可将 Twitter 数据载入 Azure Cosmos DB，然后将 HDInsight (Apache Spark) 群集设置为连接到更改源。 有关如何设置此配置的详细信息，请参阅 [Apache Spark 到 Azure Cosmos DB 连接器的设置](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)。  

以下代码片段演示如何配置 `spark-shell` 来运行一个结构化流作业，以连接到 Azure Cosmos DB 更改源。该项设置会检查实时 Twitter 数据流，以执行运行间隔计数。

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

有关完整代码示例，请参阅 [azure-cosmosdb-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)，其中包括：
* [Cosmos DB 更改源中的流查询 (Scala)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Cosmos DB 更改源中的流标记查询 (Scala)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

此代码的输出是一个 `spark-shell` 控制台，它会不断地运行一个结构化流作业，用于针对 Azure Cosmos DB 更改源中的 Twitter 数据执行间隔计数。 下图显示了流作业的输出和间隔计数。

![流的输出显示了针对 Azure Cosmos DB 更改源中的 Twitter 数据执行的间隔计数](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

有关 Azure Cosmos DB 更改源的详细信息，请参阅：

* [使用 Azure Cosmos DB 中的更改源支持](change-feed.md)
* [Azure CosmosDB 更改源处理程序库简介](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [流处理更改：Azure CosmosDB 更改源 + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>批处理层和服务层
由于新数据将载入 Azure Cosmos DB（其中的更改源用于速度层），因此，Azure Cosmos DB 是**主数据集**（不可变、仅限追加的原始数据集）所在的位置。 从载入后开始，可以使用 HDInsight (Apache Spark) 执行从**批处理层**到**服务层**的预先计算功能，如下图所示：

![突出显示 lambda 体系结构的批处理层和服务层的示意图](./media/lambda-architecture/lambda-architecture-batch-serve.png)

这些层中的要点包括：

 1. 所有**数据**只会推送到 Azure Cosmos DB（以避免多重强制转换问题）。
 2. **批处理层**包含 Azure Cosmos DB 中存储的主数据集（不可变、仅限追加的原始数据集）。 使用 HDI Spark 可以预先计算要存储在计算的批处理视图中的聚合。
 3. **服务层**是一个 Azure Cosmos DB 数据库，其中包含主数据集的集合以及计算的批处理视图。
 4. 本文稍后将介绍**速度层**。
 5. 通过合并批处理视图和实时视图中的结果或者单独 ping 每个结果，可以应答所有查询。

### <a name="code-example-pre-computing-batch-views"></a>代码示例：预先计算批处理视图
为了展示如何针对**主数据集**执行从 Apache Spark 到 Azure Cosmos DB 的预先计算视图，请使用[重建的 Lambda 体系结构 - 批处理层](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)和[重建的 Lambda 体系结构 - 批处理层到服务层](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) Notebook 中的以下代码片段。 此方案使用 Azure Cosmos DB 中存储的 Twitter 数据。

让我们先使用以下 PySpark 代码与 Azure Cosmos DB 中的 Twitter 数据建立配置连接。

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

接下来，运行以下 Spark SQL 语句，确定推文集的前 10 个井号标签。 对于此 Spark SQL 查询，我们将在 Jupyter Notebook 中运行此代码，且不会紧接在此代码片段的后面提供输出条形图。

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![按井号标签显示推文数量的图表](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

创建查询后，让我们使用 Spark 连接器将它保存回到某个集合，以便将输出数据保存到不同的集合中。  此示例使用 Scala 来展示连接。 与在前面的示例中一样，创建配置连接，以将 Apache Spark 数据帧保存到不同的 Azure Cosmos DB 集合。

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

指定 `SaveMode`（指示是要 `Overwrite`还是 `Append` 文档）后，创建与前面示例中的 Spark SQL 查询类似的 `tweets_bytags` 数据帧。  创建 `tweets_bytags` 数据帧后，可以使用 `write` 方法和前面指定的 `writeConfig` 将其保存。

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

现在，这最后一条语句已将 Spark 数据帧保存到新的 Azure Cosmos DB 集合；从 lambda 体系结构的角度来看，这就是**服务层**中的**批处理视图**。
 
#### <a name="resources"></a>资源

有关完整代码示例，请参阅 [azure-cosmosdb-spark/lambda/samples](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)，其中包括：
* 重建的 Lambda 体系结构 - 批处理层[HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* 重建的 Lambda 体系结构 - 批处理层到服务层[HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>速度层
如前所述，使用 Azure Cosmos DB 更改源库可以简化批处理层与速度层之间的操作。 在此体系结构中，使用 Apache Spark（通过 HDInsight）可以针对数据执行结构化流查询。 此外，还可以暂时保存结构化流查询的结果，使其他系统可以访问此数据。

![突出显示 lambda 体系结构的速度层的示意图](./media/lambda-architecture/lambda-architecture-speed.png)

为此，请创建一个独立的 Azure Cosmos DB 集合，用于保存结构化流查询的结果。  这样，就可以让其他系统（而不只是 Apache Spark）访问此信息。 另外，使用 Cosmos DB 生存时间 (TTL) 功能，可以配置为在设置的期限后自动删除文档。  有关 Azure Cosmos DB TTL 功能的详细信息，请参阅[利用生存时间使 Azure Cosmos DB 集合中的数据自动过期](time-to-live.md)

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Lambda 体系结构：重建
如前面的部分中所述，可以使用以下组件来简化原始的 lambda 体系结构：
* Azure Cosmos DB
* 使用 Azure Cosmos DB 更改源库来避免对批处理层与速度层之间的数据执行多重强制转换
* HDInsight 上的 Apache Spark
* Azure Cosmos DB 的 Spark 连接器

![显示使用 Azure Cosmos DB、Spark 和 Azure Cosmos DB 更改源 API 重建 lambda 体系结构的示意图](./media/lambda-architecture/lambda-architecture-re-architected.png)

使用这种设计只需两个托管服务：Azure Cosmos DB 和 HDInsight。 这两个服务共同解决了 lambda 体系结构的批处理层、服务层和速度层需求。 这不仅简化了操作，而且也简化了数据流。 
 1. 所有数据将推送到 Azure Cosmos DB 进行处理
 2. 批处理层包含主数据集（不可变、仅限追加的原始数据集），并预先计算批处理视图。
 3. 服务层包含快速查询的数据批处理视图。
 4. 速度层补偿处理时间（针对服务层），只处理最新的数据。
 5. 通过合并批处理视图和实时视图中的结果，可以应答所有查询。

### <a name="resources"></a>资源

 * **新数据**：[将源从 Twitter 流式传输到 CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed)，这是将新数据推送到 Azure Cosmos DB 的机制。
 * **批处理层：**批处理层由主数据集（不可变、仅限追加的原始数据集）组成，可以预先计算已推送到**服务层**的数据的批处理视图。
    * **重建的 Lambda 体系结构 - 批处理层** Notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) 查询批处理视图的主数据集。
 * **服务层：****服务层**由预先计算的数据组成，这些数据生成用于快速查询的批处理视图（例如聚合、特定的切片器，等等）。
    * **重建的 Lambda 体系结构 - 批处理层到服务层** Notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) 将批处理数据推送到服务层；即，Spark 将查询推文的批处理集合、对其进行处理，然后将其存储到另一个集合（计算的批处理）中。
* **速度层：****速度层**由利用 Azure Cosmos DB 更改源读取并立即处理数据的 Spark 组成。 还可以将数据保存到计算的 RT 中，使其他系统可以查询已处理的实时数据，而无需自行运行实时查询。
    * [Cosmos DB 更改源中的流查询](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala 脚本执行 Azure Cosmos DB 更改源中的流查询，通过 spark-shell 计算间隔计数。
    * [Cosmos DB 更改源中的流标记查询](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala 脚本执行 Azure Cosmos DB 更改源中的流查询，通过 spark-shell 计算标记的间隔计数。
  
## <a name="next-steps"></a>后续步骤
从 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存储库下载 Spark 到 Azure Cosmos DB 的连接器（如果尚未下载），并浏览该存储库中的其他资源：
* [Lambda 体系结构](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [分布式聚合示例](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [示例脚本和 Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [结构化流示例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [更改源演示](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [使用 Azure Cosmos DB 更改源和 Apache Spark 流处理更改](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

此外，还可以查看文章 [Apache Spark SQL、数据框架和数据集指南](http://spark.apache.org/docs/latest/sql-programming-guide.html)以及 [Azure HDInsight 上的 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)。
