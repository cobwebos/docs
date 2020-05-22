---
title: 使用 Apache Spark for Azure Synapse Analytics 查询 Azure Cosmos DB 分析存储
description: 如何使用 Apache Spark for Azure Synapse Analytics 查询 Azure Cosmos DB 分析存储
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 02d4b6a636bff5ef11686abba6efb52f45f04779
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599248"
---
# <a name="query-azure-cosmos-db-analytical-store-with-apache-spark-for-azure-synapse-analytics"></a>使用 Apache Spark for Azure Synapse Analytics 查询 Azure Cosmos DB 分析存储

本文提供了一些示例，说明如何通过 Synapse 笔势与分析存储交互。 右键单击某个容器时，即可看到笔势。 借助笔势，可以快速生成代码，并根据需要对其进行定制。 笔势还适用于只需单击一下即可发现数据的功能。

## <a name="load-to-dataframe"></a>加载到数据帧

在此步骤中，你将从 Spark 数据帧的 Azure Cosmos DB 分析存储中读取数据。 它将显示名为 df 的数据帧中的 10 行。 将数据加载到数据帧后，可以执行其他分析。

此操作不会影响事务存储。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

Scala 中的等效代码笔势将是以下代码：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>创建 Spark 表

在此笔势中，你将创建一个指向所选容器的 Spark 表。 此操作不会导致任何数据移动。 如果决定删除该表，基础容器（以及相应的分析存储）不会受到影响。 

此方案非常方便，可以通过第三方工具重复使用表，并提供对运行时数据的访问。

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>将数据帧写入容器

在此笔势中，你会将数据帧写入到容器中。 此操作会影响事务性能并使用请求单位。 使用 Azure Cosmos DB 事务性能适用于写入事务。 请确保使用要写回到的数据帧替换 YOURDATAFRAME。

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

Scala 中的等效代码笔势将是以下代码：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>从容器加载流式处理数据帧
在此笔势中，你将使用 Spark 流式处理功能将容器中的数据加载到数据帧中。 数据将存储在连接到工作区的主数据湖帐户（和文件系统）中。 

如果未创建文件夹 /localReadCheckpointFolder，则将自动创建该文件夹。 此操作会影响 Azure Cosmos DB 的事务性能。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

Scala 中的等效代码笔势将是以下代码：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>将流式处理数据帧写入容器
在此笔势中，你会将流式处理数据帧写入到所选的 Azure Cosmos DB 容器中。 如果未创建文件夹 /localReadCheckpointFolder，则将自动创建该文件夹。 此操作会影响 Azure Cosmos DB 的事务性能。

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

Scala 中的等效代码笔势将是以下代码：
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>后续步骤

* [了解 Synapse 和 Azure Cosmos DB 均支持的功能](./concept-synapse-link-cosmos-db-support.md)
* [连接到 Synapse Link for Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
