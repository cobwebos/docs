---
title: 将 Apache Spark 与 Azure 事件中心集成 | Microsoft Docs
description: 与 Apache Spark 集成可对事件中心启用结构化流
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: sethm
ms.openlocfilehash: 9f1cf75fdea1dd7f5842c2efdaeca663d611065c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626915"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>将 Apache Spark 与 Azure 事件中心集成

Azure 事件中心与 [Apache Spark](https://spark.apache.org/) 无缝集成，可构建分布式流式处理应用程序。 此集成支持 [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html)、[Spark 流式处理](http://spark.apache.org/docs/latest/streaming-programming-guide.html)和[结构化流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)。 Apache Spark 事件中心连接器可在 [GitHub](https://github.com/Azure/azure-event-hubs-spark) 上使用。 此库还可用于 [Maven 中央存储库](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)中的 Marven 项目。

本文介绍如何在 [Azure Databricks](https://azure.microsoft.com/services/databricks/) 中创建持续应用程序。 虽然本文使用 Azure Databricks，但 [HDInsight](../hdinsight/spark/apache-spark-overview.md) 还提供了 Spark 群集。

本文中的示例使用两个 Scala notebook：一个用于流式处理来自事件中心的事件，另一个用于将事件发送回事件中心。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有，请[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* 事件中心实例。 如果没有，请[创建一个](event-hubs-create.md)。
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) 实例。 如果没有，请[创建一个](../azure-databricks/quickstart-create-databricks-workspace-portal.md)。
* [使用 maven 坐标创建库](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package)：`com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`。

使用以下代码流式处理来自事件中心的事件：

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
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
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
以下代码使用 Spark 批处理 API 将事件发送到事件中心。 还可以编写流式处理查询将事件发送到事件中心：

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>后续步骤

现在已介绍如何使用 Apache Spark 事件中心连接器设置可缩放的容错流。 访问以下链接，深入了解如何配合使用事件中心以及结构化流和 Spark 流式处理：

* [结构化流 + Azure 事件中心集成指南](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark 流式处理 + 事件中心集成指南](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
