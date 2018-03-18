---
title: "将 Apache Spark 与 Azure 事件中心集成 | Microsoft Docs"
description: "与 Apache Spark 集成可对事件中心启用结构化流"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>将 Apache Spark 与 Azure 事件中心集成

Azure 事件中心与 [Apache Spark](https://spark.apache.org/) 无缝集成可轻松构建_端到端_分布式流式处理应用程序。 此集成支持 [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html)、[Spark 流式处理](http://spark.apache.org/docs/latest/streaming-programming-guide.html)、[结构化流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)。 Apache Spark 事件中心连接器可在 [GitHub](https://github.com/Azure/azure-event-hubs-spark) 上使用。 此库还可用于 [Maven 中央存储库](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)中的 Marven 项目

本文将演示如何在 [Azure Databrick](https://azure.microsoft.com/services/databricks/) 中生成持续应用程序。 虽然本文使用 [Azure Databricks](https://azure.microsoft.com/services/databricks/)，但 [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview) 还提供了 Spark 群集。

以下示例使用两个 Scala notebook，一个用于流式处理事件中心中的事件，另一个用于将事件发送回事件中心。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有，请[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* 事件中心实例。 如果没有，请[创建一个](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) 实例。 如果没有，请[创建一个](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)
* [使用 maven 坐标创建库](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package)：`com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

在 notebook 中使用以下代码片段流式处理事件中心中的事件。

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
以下代码片段用于通过 Spark 的批处理 API 将事件发送到事件中心。 还可以编写流查询将事件发送到事件中心。

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

本文可让你快速了解事件中心连接器在构建实时容错流式处理解决方案时如何起作用。 按照后续步骤操作，了解有关结构化流和事件中心集成的连接器的详细信息。

## <a name="next-steps"></a>后续步骤

* [结构化流 + Azure 事件中心集成指南](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark 流式处理 + 事件中心集成指南](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
