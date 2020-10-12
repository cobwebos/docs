---
title: 使用 Apache Spark 将 Synapse Link for Azure Cosmos DB 数据复制到 SQL 池
description: 将数据加载到 Spark 数据帧中、策展数据并将其加载到 SQL 池表中
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 409f1ecee5ccf42a0168d500b40337366e07bfc0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91287844"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-sql-pool-using-apache-spark"></a>使用 Apache Spark 将数据从 Azure Cosmos DB 复制到 SQL 池

Azure Synapse Link for Azure Cosmos DB 使用户能够对 Azure Cosmos DB 中的操作数据运行准实时分析。 但是，有时需要聚合和丰富某些数据，以便为数据仓库用户提供服务。 只需通过笔记本中的几个单元格即可策展和导出 Synapse Link 数据。

## <a name="prerequisites"></a>必备知识
* [为 Synapse 工作区预配](../quickstart-create-workspace.md)以下内容：
    * [Spark 池](../quickstart-create-apache-spark-pool-studio.md)
    * [SQL 池](../quickstart-create-sql-pool-studio.md)
* [为 Cosmos DB 帐户预配包含数据的 HTAP 容器](../../cosmos-db/configure-synapse-link.md)
* [将 Azure Cosmos DB HTAP 容器连接到工作区](./how-to-connect-synapse-link-cosmos-db.md)
* [使用正确的设置将数据从 Spark 导入 SQL 池](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>步骤
在本教程中，你将连接到分析存储，因此不会对事务存储产生任何影响（它不会消耗任何请求单位）。 我们将执行以下步骤：
1. 将 Cosmos DB HTAP 容器读入 Spark 数据帧
2. 在新数据帧中聚合结果
3. 将数据引入 SQL 池中

[![从 Spark 到 SQL 的步骤 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>数据
在该示例中，我们使用名为“RetailSales”的 HTAP 容器。 它属于名为“ConnectedData”的链接服务，具有以下架构：
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

为了进行报告，我们将按“productCode”和“weekStarting”聚合销售额（数量、收入 [价格 x 数量]）   。 最后，我们会将该数据导入名为“dbo.productsales”的 SQL 池表。

## <a name="configure-a-spark-notebook"></a>配置 Spark 笔记本
创建一个 Spark 笔记本，以 Scala as Spark (Scala) 作为主要语言。 使用笔记本的默认会话设置。

## <a name="read-the-data-in-spark"></a>读取 Spark 中的数据
在第一个单元格中，使用 Spark 将 Cosmos DB HTAP 容器读取到数据帧中。

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>在新数据帧中聚合结果

在第二个单元格中，在将新数据帧加载到 SQL 池数据库之前，先运行它所需的转换和聚合。

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-sql-pool"></a>将结果加载到 SQL 池中

在第三个单元格中，将数据加载到 SQL 池中。 它将自动创建一个临时外部表、外部数据源和外部文件格式，作业完成后会删除这些内容。

```java
df_olap_aggr.write.sqlanalytics("arnaudpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>通过 SQL 查询结果

可以使用简单的 SQL 查询（例如以下 SQL 脚本）查询结果：
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

查询会以图表模式显示以下结果：[![从 Spark 到 SQL 的步骤 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>后续步骤
* [使用 Apache Spark 查询 Azure Cosmos DB 分析存储](./how-to-query-analytical-store-spark.md)