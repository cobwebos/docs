---
title: 有关如何在 Azure Synapse Analytics 的 Apache Spark 中使用 Linux Foundation Delta Lake 的概述
description: 了解如何在 Azure Synapse Analytics 的 Apache Spark 中使用 Delta Lake 来创建和使用具有 ACID 属性的表。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: euang
ms.topic: overview
ms.subservice: spark
ms.date: 07/28/2020
ms.author: euang
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 655daeb0149228d78d5288b0e5d0d705a5743d28
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008635"
---
# <a name="linux-foundation-delta-lake-overview"></a>Linux Foundation Delta Lake 概述

为了使内容更加清晰，本文根据[此处](https://docs.delta.io/latest/quick-start.html)的初始版本进行了改编。 本文将帮助你快速了解 [Delta Lake](https://delta.io) 的主要功能。 本文提供的代码片段演示了何从交互式查询、批处理查询和流式处理查询中读取和写入 Delta Lake 表。 代码片段也在一组笔记本中提供：[此处的 PySpark](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Python.ipynb)、[此处的 Scala](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Scala/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20Scala.ipynb) 和[此处的 C#](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/Spark.NET%20C%23/Hitchikers%20Guide%20to%20Delta%20Lake%20-%20CSharp.ipynb)

下面是我们将介绍的内容：

* 创建表
* 读取数据
* 更新表数据
* 覆盖表数据
* 无覆盖的条件更新
* 使用“按时间顺序查看”读取较早版本的数据
* 将数据流写入表
* 读取表中的更改流
* SQL 支持

## <a name="configuration"></a>配置

请确保修改以下内容，以适合你的环境。

:::zone pivot = "programming-language-python"

```python
import random

session_id = random.randint(0,1000000)
delta_table_path = "/delta/delta-table-{0}".format(session_id)

delta_table_path
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var sessionId = (new Random()).Next(10000000);
var deltaTablePath = $"/delta/delta-table-{sessionId}";

deltaTablePath
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val sessionId = scala.util.Random.nextInt(1000000)
val deltaTablePath = s"/delta/delta-table-$sessionId";
```

::: zone-end

结果：

“/delta/delta-table-335323”

## <a name="create-a-table"></a>创建表

若要创建 Delta Lake 表，请以增量格式编写数据帧。 可将格式从 Parquet、CSV 和 JSON 等更改为增量格式。

下面的代码演示了如何使用从数据帧推断的架构创建新的 Delta Lake 表。

:::zone pivot = "programming-language-python"

```python
data = spark.range(0,5)
data.show()
data.write.format("delta").save(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(0,5);
data.Show();
data.Write().Format("delta").Save(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(0, 5)
data.show
data.write.format("delta").save(deltaTablePath)
```

::: zone-end

结果：

| ID|
|---|
|  0|
|  1|
|  2|
|  3|
|  4|

## <a name="read-data"></a>读取数据

通过指定文件路径和增量格式来读取 Delta Lake 表中的数据。

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Load(deltaTablePath);
df.Show()
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").load(deltaTablePath)
df.show()
```

::: zone-end

结果：

| ID|
|---|
|  1|
|  3|
|  4|
|  0|
|  2|

此结果的顺序与上面的结果顺序不同，因为在输出结果之前未显式指定顺序。

## <a name="update-table-data"></a>更新表数据

Delta Lake 支持使用标准数据帧 API 修改表的多种操作，这是增量格式添加的重要增强功能之一。 下面的示例运行一个批处理作业来覆盖表中的数据。

:::zone pivot = "programming-language-python"

```python
data = spark.range(5,10)
data.write.format("delta").mode("overwrite").save(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var data = spark.Range(5,10);
data.Write().Format("delta").Mode("overwrite").Save(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val data = spark.range(5, 10)
data.write.format("delta").mode("overwrite").save(deltaTablePath)
df.show()
```

::: zone-end

结果：

| ID|
|---|
|  7|
|  8|
|  5|
|  9|
|  6|

在这里，你可看到所有 5 个记录均已更新为包含新值。

## <a name="save-as-catalog-tables"></a>另存为目录表

Delta Lake 可写入托管的或外部目录表。

:::zone pivot = "programming-language-python"

```python
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql("CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{0}'".format(delta_table_path))
spark.sql("SHOW TABLES").show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
data.Write().Format("delta").SaveAsTable("ManagedDeltaTable");
spark.Sql($"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '{deltaTablePath}'");
spark.Sql("SHOW TABLES").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
data.write.format("delta").saveAsTable("ManagedDeltaTable")
spark.sql(s"CREATE TABLE ExternalDeltaTable USING DELTA LOCATION '$deltaTablePath'")
spark.sql("SHOW TABLES").show
```

::: zone-end

结果：

|database|         tableName|isTemporary|
|--------|------------------|-----------|
| 默认值|externaldeltatable|      false|
| 默认值| manageddeltatable|      false|

通过此代码，你在目录中根据现有数据帧创建了一个新表，称为托管表。 然后，你在目录中定义了一个使用现有位置的新的外部表，称为外部表。 在输出中，你可看到无论这两个表是如何创建的，目录中都列出了它们。

现在可查看这两个表的扩展属性

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ManagedDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ManagedDeltaTable").show(truncate=false)
```

::: zone-end

结果：

|col_name                    |data_type                                                                                                    |comment|
|----------------------------|-------------------------------------------------------------------------------------------------------------|-------|
|id                          |bigint                                                                                                       |Null   |
|                            |                                                                                                             |       |
|表详细信息  |                                                                                                             |       |
|数据库                    |默认值                                                                                                      |       |
|表                       |manageddeltatable                                                                                            |       |
|所有者                       |trusted-service-user                                                                                         |       |
|创建时间                |2020 年 4 月 25 日星期六 00:35:34 UTC                                                                                 |       |
|上次访问                 |1970 年 1 月 01 日星期四 00:00:00 UTC                                                                                 |       |
|创建者                  |Spark 2.4.4.2.6.99.201-11401300                                                                              |       |
|类型                        |托管                                                                                                      |       |
|提供程序                    |delta                                                                                                        |       |
|表属性            |[transient_lastDdlTime=1587774934]                                                                           |       |
|统计信息                  |2407 个字节                                                                                                   |       |
|位置                    |abfss://data@<data lake>.dfs.core.windows.net/synapse/workspaces/<workspace name>/warehouse/manageddeltatable|       |
|Serde 库               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                                                           |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                                                             |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat                                                    |       |
|存储属性          |[serialization.format=1]                                                                                     |       |

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql("DESCRIBE EXTENDED ExternalDeltaTable").Show(truncate: 0);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql("DESCRIBE EXTENDED ExternalDeltaTable").show(truncate=false)
```

::: zone-end

结果：

|col_name                    |data_type                                                             |comment|
|----------------------------|----------------------------------------------------------------------|-------|
|id                          |bigint                                                                |Null   |
|                            |                                                                      |       |
|表详细信息  |                                                                      |       |
|数据库                    |默认值                                                               |       |
|表                       |externaldeltatable                                                    |       |
|所有者                       |trusted-service-user                                                  |       |
|创建时间                |2020 年 4 月 25 日星期六 00:35:38 UTC                                          |       |
|上次访问                 |1970 年 1 月 01 日星期四 00:00:00 UTC                                          |       |
|创建者                  |Spark 2.4.4.2.6.99.201-11401300                                       |       |
|类型                        |EXTERNAL                                                              |       |
|提供程序                    |DELTA                                                                 |       |
|表属性            |[transient_lastDdlTime=1587774938]                                    |       |
|位置                    |abfss://data@<data lake>.dfs.core.windows.net/delta/delta-table-587152|       |
|Serde 库               |org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe                    |       |
|InputFormat                 |org.apache.hadoop.mapred.SequenceFileInputFormat                      |       |
|OutputFormat                |org.apache.hadoop.hive.ql.io.HiveSequenceFileOutputFormat             |       |
|存储属性          |[serialization.format=1]                                              |       |

## <a name="conditional-update-without-overwrite"></a>无覆盖的条件更新

Delta Lake 提供编程 API 来进行条件更新、删除数据并将数据合并（通常称为更新插入）到表中。

:::zone pivot = "programming-language-python"

```python
from delta.tables import *
from pyspark.sql.functions import *

delta_table = DeltaTable.forPath(spark, delta_table_path)

delta_table.update(
  condition = expr("id % 2 == 0"),
  set = { "id": expr("id + 100") })
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Delta;
using Microsoft.Spark.Extensions.Delta.Tables;
using Microsoft.Spark.Sql;
using static Microsoft.Spark.Sql.Functions;

var deltaTable = DeltaTable.ForPath(deltaTablePath);

deltaTable.Update(
  condition: Expr("id % 2 == 0"),
  set: new Dictionary<string, Column>(){{ "id", Expr("id + 100") }});
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
import io.delta.tables._
import org.apache.spark.sql.functions._

val deltaTable = DeltaTable.forPath(deltaTablePath)

// Update every even value by adding 100 to it
deltaTable.update(
  condition = expr("id % 2 == 0"),
  set = Map("id" -> expr("id + 100")))
deltaTable.toDF.show
```

::: zone-end

结果：

| ID|
|---|
|106|
|108|
|  5|
|  7|
|  9|

在这里，你只为每个偶数 ID 增加了 100。

:::zone pivot = "programming-language-python"

```python
delta_table.delete("id % 2 == 0")
delta_table.toDF().show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.Delete(condition: Expr("id % 2 == 0"));
deltaTable.ToDF().Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.delete(condition = expr("id % 2 == 0"))
deltaTable.toDF.show
```

::: zone-end

结果：

| ID|
|---|
|  5|
|  7|
|  9|

请注意，删除了每个偶数行。

:::zone pivot = "programming-language-python"

```python
new_data = spark.range(0,20).alias("newData")

delta_table.alias("oldData")\
    .merge(new_data.alias("newData"), "oldData.id = newData.id")\
    .whenMatchedUpdate(set = { "id": lit("-1")})\
    .whenNotMatchedInsert(values = { "id": col("newData.id") })\
    .execute()

delta_table.toDF().show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var newData = spark.Range(20).As("newData");

deltaTable
    .As("oldData")
    .Merge(newData, "oldData.id = newData.id")
    .WhenMatched()
        .Update(new Dictionary<string, Column>() {{"id", Lit("-1")}})
    .WhenNotMatched()
        .Insert(new Dictionary<string, Column>() {{"id", Col("newData.id")}})
    .Execute();

deltaTable.ToDF().Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val newData = spark.range(0, 20).toDF

deltaTable.as("oldData").
  merge(
    newData.as("newData"),
    "oldData.id = newData.id").
  whenMatched.
  update(Map("id" -> lit(-1))).
  whenNotMatched.
  insert(Map("id" -> col("newData.id"))).
  execute()

deltaTable.toDF.show()
```

::: zone-end

结果：

| ID|
|---|
| 18|
| 15|
| 19|
|  2|
|  1|
|  6|
|  8|
|  3|
| -1|
| 10|
| 13|
|  0|
| 16|
|  4|
| -1|
| 12|
| 11|
| 14|
| -1|
| 17|

在此你合并了现有数据。 已在 update(WhenMatched) 代码路径中为现有数据分配了值 -1。 还添加了在代码片段顶部创建且已通过插入代码路径 (WhenNotMatched) 添加的新数据。

### <a name="history"></a>历史记录

通过 Delta Lake 可查看表的历史记录， 也就是查看对基础 Delta Table 所做的更改。 下面的单元展示了查看历史记录是多么的简单。

:::zone pivot = "programming-language-python"

```python
delta_table.history().show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show(false)
```

::: zone-end

结果：

|版本|          timestamp|userId|userName|operation|                                                operationParameters| 作业 (job)|笔记本|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|---------|-------------------------------------------------------------------|----|--------|---------|-----------|--------------|-------------|
|      4|2020-04-25 00:36:27|  null|    null|    MERGE|                       [predicate -> (oldData.`ID` = newData.`ID`)]|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|   DELETE|[predicate -> ["((`ID` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|   UPDATE| [predicate -> ((ID#744L % cast(2 as bigint)) = cast(0 as bigint))]|null|    null|     Null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|    WRITE|                             [mode -> Overwrite, partitionBy -> []]|null|    null|     null|          0|          Null|        false|
|      0|2020-04-25 00:34:34|  null|    null|    WRITE|                         [mode -> ErrorIfExists, partitionBy -> []]|null|    null|     null|       null|          null|         true|

在这里，你可看到对上述代码片段所作的全部修改。

## <a name="read-older-versions-of-data-using-time-travel"></a>使用“按时间顺序查看”读取较早版本的数据

可使用一项称为“按时间顺序查看”的功能查询 Delta Lake 表的历史快照。 如果要访问已覆盖的数据，可使用 versionAsOf 选项查询表的快照，然后再覆盖第一组数据。

运行下面的单元之后，应会看到覆盖前的第一组数据。 “按时间顺序查看”功能非常强大，它利用 Delta Lake 事务日志的强大功能来访问表中已不存在的数据。 如果删除版本 0 选项（或指定版本 1），则可再次查看更新的数据。 有关详细信息，请参阅[查询表的旧快照](https://docs.delta.io/latest/delta-batch.html#deltatimetravel)。

:::zone pivot = "programming-language-python"

```python
df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
df.show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var df = spark.Read().Format("delta").Option("versionAsOf", 0).Load(deltaTablePath);
df.Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val df = spark.read.format("delta").option("versionAsOf", 0).load(deltaTablePath)
df.show()
```

::: zone-end

结果：

| ID|
|---|
|  0|
|  1|
|  4|
|  3|
|  2|

在这里，你可看到已返回到最早版本的数据。

## <a name="write-a-stream-of-data-to-a-table"></a>将数据流写入表

还可使用 Spark 结构化流式处理来写入 Delta Lake 表。 即使有针对表并行运行的其他流或批处理查询，Delta Lake 事务日志也可确保仅处理一次。 默认情况下，流在追加模式下运行，这会将新记录添加到表中。

要详细了解 Delta Lake 与结构化流式处理的集成，请参阅[表流读取和写入](https://docs.delta.io/latest/delta-streaming.html)。

下面的单元中是我们要进行的操作：

* 单元 30：显示新追加的数据
* 单元 31：查看历史记录
* 单元 32：停止结构化流式处理作业
* 单元 33：查看历史记录 <-- 你会发现追加已停止

首先要设置一个简单的 Spark 流式处理作业以生成序列，然后将该作业写入到 Delta Table。

:::zone pivot = "programming-language-python"

```python
streaming_df = spark.readStream.format("rate").load()
stream = streaming_df\
    .selectExpr("value as id")\
    .writeStream\
    .format("delta")\
    .option("checkpointLocation", "/tmp/checkpoint-{0}".format(session_id))\
    .start(delta_table_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var streamingDf = spark.ReadStream().Format("rate").Load();
var stream = streamingDf.SelectExpr("value as id").WriteStream().Format("delta").Option("checkpointLocation", $"/tmp/checkpoint-{sessionId}").Start(deltaTablePath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val streamingDf = spark.readStream.format("rate").load()
val stream = streamingDf.select($"value" as "id").writeStream.format("delta").option("checkpointLocation", s"/tmp/checkpoint-$sessionId").start(deltaTablePath)
```

::: zone-end

## <a name="read-a-stream-of-changes-from-a-table"></a>读取表中的更改流

当流写入 Delta Lake 表后，你还可将该表作为流式处理源进行读取。 例如，可再启动一个流式处理查询，该查询打印对 Delta Lake 表所作的全部更改。

:::zone pivot = "programming-language-python"

```python
delta_table.toDF().sort(col("id").desc()).show(100)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.ToDF().Sort(Col("id").Desc()).Show(100);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.toDF.sort($"id".desc).show
```

::: zone-end

结果：

| ID|
|---|
| 19|
| 18|
| 17|
| 16|
| 15|
| 14|
| 13|
| 12|
| 11|
| 10|
|  8|
|  6|
|  4|
|  3|
|  2|
|  1|
|  0|
| -1|
| -1|
| -1|

:::zone pivot = "programming-language-python"

```python
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(20, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(20, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
deltaTable.history.show
```

::: zone-end

结果：

|版本|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       Null|

在此，你需要删除一些不太感兴趣的列，简化历史记录视图的查看体验。

:::zone pivot = "programming-language-python"

```python
stream.stop()
delta_table.history().drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").show(100, 1000, False)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
stream.Stop();
deltaTable.History().Drop("userId", "userName", "job", "notebook", "clusterId", "isolationLevel", "isBlindAppend").Show(100, 1000, false);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
stream.stop
deltaTable.history.show
```

结果：

|版本|          timestamp|       operation|                                                                  operationParameters|readVersion|
|-------|-------------------|----------------|-------------------------------------------------------------------------------------|-----------|
|      5|2020-04-25 00:37:09|STREAMING UPDATE|[outputMode -> Append, queryId -> d26b4f8a-7e5a-44f2-a5fb-23a7bd02aef7, epochId -> 0]|          4|
|      4|2020-04-25 00:36:27|           MERGE|                                         [predicate -> (oldData.`id` = newData.`id`)]|          3|
|      3|2020-04-25 00:36:08|          DELETE|                  [predicate -> ["((`id` % CAST(2 AS BIGINT)) = CAST(0 AS BIGINT))"]]|          2|
|      2|2020-04-25 00:35:51|          UPDATE|                   [predicate -> ((id#744L % cast(2 as bigint)) = cast(0 as bigint))]|          1|
|      1|2020-04-25 00:35:05|           WRITE|                                               [mode -> Overwrite, partitionBy -> []]|          0|
|      0|2020-04-25 00:34:34|           WRITE|                                           [mode -> ErrorIfExists, partitionBy -> []]|       Null|

::: zone-end

## <a name="convert-parquet-to-delta"></a>将 Parquet 转换为 Delta

可执行从 Parquet 格式到 Delta 的就地转换。

在此，你需要测试现有表是否采用增量格式。
:::zone pivot = "programming-language-python"

```python
parquet_path = "/parquet/parquet-table-{0}".format(session_id)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetPath = $"/parquet/parquet-table-{sessionId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetPath = s"/parquet/parquet-table-$sessionId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

结果：

错误

你现在需要将数据转换为增量格式并验证其是否有效。

:::zone pivot = "programming-language-python"

```python
DeltaTable.convertToDelta(spark, "parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
DeltaTable.ConvertToDelta(spark, $"parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
DeltaTable.convertToDelta(spark, s"parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

结果：

正确

## <a name="sql-support"></a>SQL 支持

Delta 通过 SQL 支持表实用工具命令。 可使用 SQL 执行以下操作：

* 获取 DeltaTable 的历史记录
* 清空 DeltaTable
* 将 Parquet 文件转换为 Delta

:::zone pivot = "programming-language-python"

```python
spark.sql("DESCRIBE HISTORY delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"DESCRIBE HISTORY delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"DESCRIBE HISTORY delta.`$deltaTablePath`").show()
```

::: zone-end

结果：

|版本|          timestamp|userId|userName|       operation| operationParameters| 作业 (job)|笔记本|clusterId|readVersion|isolationLevel|isBlindAppend|
|-------|-------------------|------|--------|----------------|--------------------|----|--------|---------|-----------|--------------|-------------|
|      5|2020-04-25 00:37:09|  null|    null|STREAMING UPDATE|[outputMode -> Ap...|null|    null|     null|          4|          null|         true|
|      4|2020-04-25 00:36:27|  null|    null|           MERGE|[predicate -> (ol...|null|    null|     null|          3|          null|        false|
|      3|2020-04-25 00:36:08|  null|    null|          DELETE|[predicate -> ["(...|null|    null|     null|          2|          null|        false|
|      2|2020-04-25 00:35:51|  null|    null|          UPDATE|[predicate -> ((i...|null|    null|     Null|          1|          null|        false|
|      1|2020-04-25 00:35:05|  null|    null|           WRITE|[mode -> Overwrit...|null|    null|     null|          0|          Null|        false|
|      0|2020-04-25 00:34:34|  null|    null|           WRITE|[mode -> ErrorIfE...|null|    null|     null|       null|          null|         true|

:::zone pivot = "programming-language-python"

```python
spark.sql("VACUUM delta.`{0}`".format(delta_table_path)).show()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
spark.Sql($"VACUUM delta.`{deltaTablePath}`").Show();
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
spark.sql(s"VACUUM delta.`$deltaTablePath`").show()
```

::: zone-end

结果：

|                path|
|--------------------|
|abfss://data@arca...|

你现在需要验证表是否不是增量格式表，然后使用 Spark SQL 将其转换为增量格式，并确认它已正确转换。

:::zone pivot = "programming-language-python"

```python
parquet_id = random.randint(0,1000)
parquet_path = "/parquet/parquet-table-{0}-{1}".format(session_id, parquet_path)
data = spark.range(0,5)
data.write.parquet(parquet_path)
DeltaTable.isDeltaTable(spark, parquet_path)
spark.sql("CONVERT TO DELTA parquet.`{0}`".format(parquet_path))
DeltaTable.isDeltaTable(spark, parquet_path)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var parquetId =  (new Random()).Next(10000000);
var parquetPath = $"/parquet/parquet-table-{sessionId}-{parquetId}";
var data = spark.Range(0,5);
data.Write().Parquet(parquetPath);
DeltaTable.IsDeltaTable(parquetPath);
spark.Sql($"CONVERT TO DELTA parquet.`{parquetPath}`");
DeltaTable.IsDeltaTable(parquetPath);
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val parquetId = scala.util.Random.nextInt(1000)
val parquetPath = s"/parquet/parquet-table-$sessionId-$parquetId"
val data = spark.range(0,5)
data.write.parquet(parquetPath)
DeltaTable.isDeltaTable(parquetPath)
spark.sql(s"CONVERT TO DELTA parquet.`$parquetPath`")
DeltaTable.isDeltaTable(parquetPath)
```

::: zone-end

结果：

正确

有关完整文档，请参阅 [Delta Lake 文档页](https://docs.delta.io/latest/delta-intro.html)

有关详细信息，请参阅 [Delta Lake 项目](https://github.com/delta-io/delta)。

## <a name="next-steps"></a>后续步骤

* [.NET for Apache Spark 文档](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
