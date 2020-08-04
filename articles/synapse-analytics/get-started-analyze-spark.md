---
title: 教程：开始使用 Spark 进行分析
description: 本教程将介绍设置和使用 Azure Synapse Analytics 的基本步骤。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 5c6b35c1d9f00cae8fc688569e3a491679900995
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093223"
---
# <a name="analyze-with-apache-spark"></a>使用 Apache Spark 进行分析

本教程介绍使用 Apache Spark for Azure Synapse 加载和分析数据的基本步骤。

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>将纽约市出租车数据加载到 Spark nyctaxi 数据库

SQLDB1 的表中有可用数据。 将其加载到名为 nyctaxi 的 Spark 数据库。

1. 在 Synapse Studio 中，转到“开发”中心。
1. 选择 + > “笔记本” 。
1. 在笔记本顶部，将“附加到”值设置为 Spark1 。
1. 选择“添加代码”以添加笔记本代码单元，并粘贴以下文本：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 转到“数据”中心，右键单击“数据库”，然后选择“刷新”  。 应看到以下数据库：

    - SQLDB1（SQL 池）
    - nyctaxi (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和笔记本分析纽约市出租车数据

1. 返回到笔记本。
1. 创建新代码单元并输入以下文本。 然后运行该单元以显示我们加载到 nyctaxi Spark 数据库中的纽约市出租车数据。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 运行以下代码，执行之前在 SQL 池 SQLDB1 中所做的相同分析。 此代码将分析结果另存到名为 nyctaxi.passengercountstats 的表，然后可视化结果。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 在单元结果中，选择“图表”以查看直观呈现出来的数据。

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>使用 Spark 和笔记本自定义数据可视化效果

使用笔记本可控制图表的呈现方式。 下面的代码显示了一个简单的示例。 它使用常用库 matplotlib 和 seaborn 。 该代码将呈现在之前运行 SQL 查询时所显示的同类型折线图。

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>将 Spark 表中的数据加载到 SQL 池表

之前我们将数据从 SQL 池表 SQLDB1.dbo.Trip 复制到 Spark 表 nyctaxi.trip 中 。 然后，我们使用 Spark 将数据聚合到了 Spark 表 nyctaxi.passengercountstats。 现在，我们将数据从 nyctaxi.passengercountstats 复制到名为 SQLDB1.dbo.PassengerCountStats 的 SQL 池表中 。

在笔记本中运行以下单元。 它将聚合的 Spark 表复制回 SQL 池表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分析存储中的数据](get-started-analyze-storage.md)


