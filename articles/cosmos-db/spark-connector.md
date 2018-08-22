---
title: 将 Apache Spark 连接到 Azure Cosmos DB | Microsoft Docs
description: 了解可将 Apache Spark 连接到 Azure Cosmos DB 的 Azure Cosmos DB Spark 连接器。 可以在 Microsoft 提供的多租户全局分布式数据库系统上执行分布式聚合。
keywords: apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 57ad621006a79148aaa6e9435d6ede446539c648
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714747"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>使用 Apache Spark 到 Azure Cosmos DB 的连接器加速大数据分析
 
通过 Apache Spark 到 Azure Cosmos DB 的连接器，Azure Cosmos DB 可充当 Apache Spark 作业的输入源或输出接收器。 将 [Spark](http://spark.apache.org/) 连接到 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 后，可以解决瞬息万变的数据科学问题。 可以使用 Azure Cosmos DB 快速保存和查询数据。 该连接器有效利用本机 Azure Cosmos DB 托管的索引。 针对快速变化的多区域分布式数据执行分析和向下推送谓词筛选时，这些索引可以实现可更新的列。 此类数据的范围包括物联网 (IoT)、数据科学和分析方案，等等。

通过以下视频详细了解该连接器：

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>连接器组件

Spark 到 Azure Cosmos DB 的连接器具有以下组件：

* 使用 [Azure Cosmos DB](http://documentdb.com) 可跨数目不限的地理区域预配和弹性缩放吞吐量与存储。  

* [Apache Spark](http://spark.apache.org/) 是专为速度、易用性和复杂分析打造的强大开源处理引擎。  

* 使用 [Azure Databricks 上的 Apache Spark 群集](https://docs.azuredatabricks.net/getting-started/index.html)可在 Spark 群集上运行 Spark 作业。

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>将 Apache Spark 连接到 Azure Cosmos DB

可使用两种方法连接 Apache Spark 和 Azure Cosmos DB：

- [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python)：基于 Python 的连接器，也称为 *pyDocumentDB*。  

- [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java)：基于 Java 的连接器。


**支持的版本**

| 组件 | 版本 |
|---------|-------|
|Apache Spark| 2.1.x、2.2.x、2.3.x |
| Scala|2.11|
| Azure Databricks 运行时版本 | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>使用 Python 或 pyDocumentDB SDK 进行连接

下图显示了 pyDocumentDB SDK 实现的体系结构：

![通过 pyDocumentDB DB 从 Spark 流到 Azure Cosmos DB 的数据流示意图](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>数据流

pyDocumentDB 实现的数据流如下所示：

* Spark 主节点通过 pyDocumentDB 连接到 Azure Cosmos DB 网关节点。 只需指定 Spark 和 Azure Cosmos DB 连接即可。 连接到相应主节点和网关节点的过程是透明的。  

* 网关节点向 Azure Cosmos DB 发出查询，并针对数据节点中的集合分区运行查询。 这些查询的响应发回到网关节点，结果集返回到 Spark 主节点。  

* 将后续查询（例如，针对 Spark 数据框架执行的查询）发送到 Spark 辅助角色节点进行处理。  

Spark 与 Azure Cosmos DB 之间的通信仅限于 Spark 主节点和 Azure Cosmos DB 网关节点。 查询的速度与这两个节点之间的传输层允许的速度相同。

运行以下步骤，使用 pyDocumentDB SDK 将 Spark 连接到 Azure Cosmos DB：

1. 创建 [Azure Databricks 工作区](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace)和 [Spark 群集](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)。 Azure Databricks 运行时版本 4.0 在该工作区中包含 Apache Spark 2.3.0 和 Scala 2.11。  

2. 创建并运行群集后，转到“工作区” > “创建” > “库”。  
3. 在“新建库”对话框中，选择“上传 Python Egg 或 PyPi”作为源。 提供 **pydocumentdb** 作为名称，然后选择“安装库”。 pyDocumentdb SDK 已发布到 pip 包，可以找到它并安装。 

   ![“新建库”对话框的屏幕截图](./media/spark-connector/create-library.png)

4. 安装库后，将其附加到早前创建的群集。  

5. 转到“工作区” > “创建” > “Notebook”。  

6. 在“创建 Notebook”对话框中输入用户友好名称，然后选择“Python”作为语言。 从下拉列表中选择前面创建的群集，然后选择“创建”。  

7. 使用“doctorwho”Azure Cosmos DB 帐户中托管的航班示例数据运行几个 Spark 查询。 （本帐户可公开访问。）[azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 存储库中托管了 Notebook 的 HTML 版本。 下载存储库文件，并转到 `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`。 可将 Notebook 导入 Azure Databricks 帐户并运行。 以下部分详细介绍了代码块的功能。

以下代码片段显示了如何导入 pyDocumentDB SDK 并在 Spark 上下文中运行查询。 如代码片段中所述，pyDocumentDB SDK 包含连接到 Azure Cosmos DB 帐户所需的连接参数。 导入所需的库，并配置主密钥和主机以创建 Azure Cosmos DB 客户端 (pydocumentdb.document_client)。


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

接下来，可以运行查询。 以下代码片段连接到 doctorwho 帐户中的 airports.codes 集合，并运行查询来提取华盛顿州的机场城市信息。 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

运行查询后，结果将是已转换为 Python 列表的“query_iterable.QueryIterable”。 此列表随后会转换为 Spark 数据帧。 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>使用 pyDocumentDB SDK 时的注意事项
对于以下情况，我们建议使用 pyDocumentDB SDK 将 Spark 连接到 Azure Cosmos DB：

* 要使用 Python。  

* 要将 Azure Cosmos DB 中相对较小的结果集返回到 Spark。 请注意，Azure Cosmos DB 中的基础数据集可能非常大，要应用筛选器或针对 Azure Cosmos DB 源运行谓词筛选器。

## <a name="connect-by-using-the-java-sdk"></a>使用 Java SDK 进行连接

下图显示了 Azure Cosmos DB SQL Java SDK 实现的体系结构以及 Spark 辅助角色节点之间的数据移动：

![Spark 到 Azure Cosmos DB 的连接器中的数据流示意图](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>数据流

Java SDK 实现的数据流如下所示：

* Spark 主节点连接到 Azure Cosmos DB 网关节点，以获取分区映射。 只需指定 Spark 和 Azure Cosmos DB 连接即可。 连接到相应主节点和网关节点的过程是透明的。  

* 此信息将返回给 Spark 主节点。 此时，应该能够分析查询，确定需要访问 Azure Cosmos DB 中的哪些分区及其位置。  

* 将此信息传输到 Spark 工作节点。  

* Spark 工作节点直接连接到 Azure Cosmos DB 分区以提取数据，并将数据返回到工作节点中的 Spark 分区。  

Spark 与 Azure Cosmos DB 之间的通信速度会大幅提高，因为数据在 Spark 工作节点与 Azure Cosmos DB 数据节点（分区）之间移动。 本示例向 Azure Cosmos DB 帐户发送一些 Twitter 数据示例，并使用该数据运行 Spark 查询。 使用以下步骤将示例 Twitter 数据写入 Azure Cosmos DB：

1. 创建 [Azure Cosmos DB SQL API 帐户](create-sql-api-dotnet.md#create-a-database-account)并[将集合添加](create-sql-api-dotnet.md#add-a-collection)到帐户。  

2. 从 GitHub 下载 [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) 示例。 使用此源将示例 Twitter 数据写入 Azure Cosmos DB。  

3. 打开命令提示符并运行以下命令安装 Tweepy 和 pyDocumentdb 模块：

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. 提取 Twitter 源示例的内容并打开 config.py 文件。 更新 masterKey、host、databaseId、collectionId 和 preferredLocations 值。  

5. 转到 `http://apps.twitter.com/` 并注册 Twitter 源脚本作为新应用程序。 为应用选择名称和应用程序后，将提供“用户密钥”、“用户机密”、“访问令牌”和“访问令牌机密”。 复制这些值并在 config.py 文件中将其更新以提供对 Twitter 的应用程序编程访问。   

6. 保存 config.py 文件。 打开命令提示符并使用以下命令运行 Python 应用程序：

   ```bash
   Python driver.py
   ```

7. 转到门户中的 Azure Cosmos DB 集合，并验证 Twitter 数据是否写入集合。

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>查找 Java SDK 并将其附加到 Spark 群集

1. 创建 [Azure Databricks 工作区](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace)和 [Spark 群集](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)。 Azure Databricks 运行时版本 4.0 在该工作区中包含 Apache Spark 2.3.0 和 Scala 2.11。  

2. 创建并运行群集后，转到“工作区” > “创建” > “库”。  

3. 在“新建库”对话框中，选择“Maven 坐标”作为源。 提供坐标值 **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**，并选择“创建库”。 已解析 Maven 依赖项，并将包添加到工作区。 在上述 Maven 坐标格式中，2.3.0 表示 Spark 版本，2.11 表示 Scala 版本，1.2.0 表示 Azure Cosmos DB 连接器版本。 

4. 安装库后，将其附加到早前创建的群集。 

本文演示如何在以下场景中使用 Spark 连接器 Java SDK：

* 从 Azure Cosmos DB 读取 Twitter 数据。  

* 读取流式传输到 Azure Cosmos DB 的 Twitter 数据。  

* 将 Twitter 数据写入 Azure Cosmos DB。 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>从 Azure Cosmos DB 读取 Twitter 数据
 
在本部分，我们将运行 Spark 查询以从 Azure Cosmos DB 中读取一批 Twitter 数据。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 存储库中托管了 Notebook 的 HTML 版本。 下载存储库文件，并转到 `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html`。 可将 Notebook 导入 Azure Databricks 帐户，并更新帐户 URI、主密钥、数据库和集合名称。 可以运行或按如下所示创建 Notebook：

1. 转到 Azure Databricks 帐户并选择“工作区” > “创建” > “Notebook”。 

2. 在“创建 Notebook”对话框中输入用户友好名称，然后选择“Python”作为语言。 从下拉列表中选择前面创建的群集，然后选择“创建”。  

3. 更新终结点、主密钥、数据库和集合值以连接到帐户。 使用 spark.read.format() 命令读取推文。

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. 运行查询以通过缓存数据中不同井号标签获取推文计数。 

   ```python
   %sql
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

Java SDK 支持以下配置映射的值： 

|设置  |Description  |
|---------|---------|
|query_maxdegreeofparallelism  | 设置并行执行查询期间客户端运行的并发操作数。 如果将其设置为大于 0 的值，则会将并发操作的数量限制为分配的值。 如果它设置为小于 0，则系统会自动决定要运行的并发操作数。 由于连接器使用执行器映射每个集合分区，因此该值对读取操作没有任何影响。        |
|query_maxbuffereditemcount     |    设置并行执行查询期间可在客户端缓冲的最大项数。 如果将其设置为大于 0 的值，则会将缓冲项的数量限制为分配的值。 如果将其设置为小于 0，系统将自动决定要缓冲的项数。     |
|query_enablescan    |   设置选项以对因为在请求的路径上选择退出索引而无法提供的查询进行扫描。       |
|query_disableruperminuteusage  |  如果常规预配的请求单位 (RU)/秒已耗尽，请禁用 RU/分钟容量以提供查询。       |
|query_emitverbosetraces   |   设置选项以允许查询发出调查的详细跟踪。      |
|query_pagesize  |   设置每个查询请求的查询结果页大小。 若要优化吞吐量，请使用大型页面大小来减少提取结果的往返次数。      |
|query_custom  |  设置 Azure Cosmos DB 查询以在从 Azure Cosmos DB 中获取数据时替代默认查询。 请注意，提供此值时，它也将用于取代包含向下推送谓词的查询。     |

根据具体情况，应使用不同的配置值来优化性能和吞吐量。 请注意，配置键当前不区分大小写，配置值始终为字符串。

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>读取流式传输到 Azure Cosmos DB 的 Twitter 数据

在本部分，我们将运行 Spark 查询以读取流式处理 Twitter 数据的更改源。 在此部分运行查询时，请确保 Twitter 源应用正在运行并将数据发送到 Azure Cosmos DB。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 存储库中托管了 Notebook 的 HTML 版本。 下载存储库文件，并转到 `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`。 可将 Notebook 导入 Azure Databricks 帐户，并更新帐户 URI、主密钥、数据库和集合名称。 可以运行或按如下所示创建 Notebook：

1. 转到 Azure Databricks 帐户并选择“工作区” > “创建” > “Notebook”。  

2. 在“创建 Notebook”对话框中输入用户友好名称，然后选择“Scala”作为语言。 从下拉列表中选择前面创建的群集，然后选择“创建”。  

3. 更新终结点、主密钥、数据库和集合值以连接到帐户。

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. 使用 spark.readStream.format() 命令开始将更改源读取为流：

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. 开始将查询流式传输到控制台：

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK 支持以下配置映射的值：

|设置  |Description  |
|---------|---------|
|readchangefeed   |  表示从 CosmosDB 更改源获取集合内容。 默认值为 false。       |
|changefeedqueryname |   用于标识查询的自定义字符串。 连接器分别跟踪不同更改源查询的集合继续标记。 如果 readchangefeed 为 true，则为不能取空值的必需配置。      |
|changefeedcheckpointlocation  |   如果节点发生故障，本地文件存储的路径可以持续继续标记。      |
|changefeedstartfromthebeginning  |  设置更改源是从头开始 (true) 还是从当前点开始 (false)。 默认情况下，从当前 (false) 开始。       |
|rollingchangefeed  |   表示更改源是否来自上一个查询的布尔值。 默认值为 false，这意味着更改将从首次读取集合开始计算。      |
|changefeedusenexttoken  |   用于支持处理故障情况的布尔值。 它表示已正常处理当前更改源批。 弹性分布式数据集应使用下一个继续标记来获取后续的更改批。      |
| InferStreamSchema | 表示是否应在流式传输开始时对流式处理数据的架构进行采样的布尔值。 默认情况下，此值设置为 true。 如果此参数设置为 true 且数据采样后流式处理数据的架构发生更改，则新添加的属性将在流式处理数据帧中删除。 <br/><br/> 如果希望流式传输数据帧与架构无关，请将此参数设置为 false。 在此模式下，从 Azure Cosmos DB 更改源读取的文档正文将包装在正文属性中。 此属性位于除系统属性值以外的结果流数据帧中。
 |

### <a name="connection-settings"></a>连接设置

Java SDK 支持以下连接设置：

|设置  |Description  |
|---------|---------|
|connectionmode   |  设置内部 DocumentClient 应使用与 Azure Cosmos DB 进行通信的连接模式。 允许的值为 DirectHttps（默认值）和 Gateway。 DirectHttps 连接模式将请求直接路由到 CosmosDB 分区，并提供一些延迟优势。       |
|connectionmaxpoolsize   |  设置内部 DocumentClient 使用的连接池大小的值。 默认值为 100。       |
|connectionidletimeout  |  以秒为单位设置空闲连接的超时值。 默认值为 60。       |
|query_maxretryattemptsonthrottledrequests    |  设置最大重试次数。 客户端速率限制导致请求失败时可以使用此值。 如果未指定，重试次数默认值为 1000。       |
|query_maxretrywaittimeinseconds   |  设置最大重试时间（以秒为单位）。 默认情况下为 1000 秒。       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>将 Twitter 数据写入 Azure Cosmos DB 

本部分运行 Spark 查询，以将一批 Twitter 数据写入同一数据库中的新集合。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 存储库中托管了 Notebook 的 HTML 版本。 下载存储库文件，并转到 `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`。 可将 Notebook 导入 Azure Databricks 帐户，并更新帐户 URI、主密钥、数据库和集合名称。 可以运行或按如下所示创建 Notebook：

1. 转到 Azure Databricks 帐户并选择“工作区” > “创建” > “Notebook”。  

2. 在“创建 Notebook”对话框中输入用户友好名称，然后选择“Scala”作为语言。 从下拉列表中选择前面创建的群集，然后选择“创建”。  

3. 更新终结点、主密钥、数据库和集合值以连接到数据库集合来读写 Twitter 数据。

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. 运行查询以通过缓存数据中不同井号标签获取推文计数。 

   ```scala
   %sql
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

5. 创建推文标记的新数据帧并将数据保存到新集合中。 运行以下代码后，可返回到门户并验证数据是否已写入集合。 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Java SDK 支持以下配置映射的值：

|设置  |Description  |
|---------|---------|
| BulkImport | 表示是否应使用 BulkExecutor 库导入数据的布尔值。 默认情况下，此值设置为 true。 |
|WritingBatchSize  |   表示将数据写入 Azure Cosmos DB 集合时要使用的批大小。 <br/><br/> 如果 BulkImport 参数设置为 true，则 WritingBatchSize 参数表示作为 BulkExecutor 库 importAll API 的提供输入的文档的批大小。 默认情况下，此值设置为 100K。 <br/><br/> 如果 BulkImport 参数设置为 false，则 WritingBatchSize 参数表示写入 Azure Cosmos DB 集合时要使用的批大小。 连接器以批方式异步发送 createDocument 和 upsertDocument 请求。 只要群集资源可用，批大小越大，可实现的吞吐量就越大。 另一方面，指定较小的批大小以限制速率和 RU 消耗。 默认情况下，写入批大小设置为 500。  |
|Upsert   |  表示写入 Azure Cosmos DB 集合时是否使用 upsertDocument 而不是 CreateDocument 的布尔值字符串。   |
| WriteThroughputBudget |  表示分配给集合的总吞吐量中想要分配给批量引入 Spark 作业的 RU/秒数值的整数字符串。 |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>写入流式传输到 Azure Cosmos DB 的 Twitter 数据 

本部分运行 Spark 查询，以将流式处理 Twitter 数据的更改源写入同一数据库中的新集合。 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub 存储库中托管了 Notebook 的 HTML 版本。 下载存储库文件，并转到 `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`。 可将 Notebook 导入 Azure Databricks 帐户，并更新帐户 URI、主密钥、数据库和集合名称。 可以运行或按如下所示创建 Notebook：

1. 转到 Azure Databricks 帐户并选择“工作区” > “创建” > “Notebook”。  

2. 在“创建 Notebook”对话框中输入用户友好名称，然后选择“Scala”作为语言。 从下拉列表中选择前面创建的群集，然后选择“创建”。  

3. 更新终结点、主密钥、数据库和集合值以连接到数据库集合来读写 Twitter 数据。

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. 使用 spark.readStream.format() 命令开始将更改源读取为流：
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. 使用 writeStream.format() 方法定义目标集合的配置并启动流式处理作业：

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Java SDK 支持以下配置映射的值：

|设置  |Description  |
|---------|---------|
|Upsert   |  表示写入 Azure Cosmos DB 集合时是否使用 upsertDocument 而不是 CreateDocument 的布尔值字符串。   |
|checkpointlocation  |   如果节点发生故障，本地文件存储的路径可以持续继续标记。   |
|WritingBatchSize  |  表示将数据写入 Azure Cosmos DB 集合时要使用的批大小。 连接器以批方式异步发送 createDocument 和 upsertDocument 请求。 只要群集资源可用，批大小越大，可实现的吞吐量就越大。 另一方面，指定较小的批大小以限制速率和 RU 消耗。 默认情况下，写入批大小设置为 500。  |


### <a name="considerations-when-using-java-sdk"></a>使用 Java SDK 时的注意事项

对于以下情况，我们建议使用 Java SDK 将 Spark 连接到 Azure Cosmos DB：

* 要使用 Python 或 Scala。  

* 要在 Apache Spark 与 Azure Cosmos DB 之间传输大量的数据。 与 pyDocumentDB 相比，Java SDK 的性能更高。 有关查询性能差异的详细信息，请参阅[查询测试运行 wiki 文章](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs)。

## <a name="next-steps"></a>后续步骤

从 [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub 存储库下载 Spark 到 Azure Cosmos DB 连接器（如果尚未下载）。 浏览存储库中的其他资源：

* [聚合示例](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [示例脚本和 Notebook](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

此外，还可以查看 [Apache Spark SQL、数据帧和数据集指南](http://spark.apache.org/docs/latest/sql-programming-guide.html)以及 [Azure HDInsight 上的 Apache Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) 一文。
