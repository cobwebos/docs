---
title: Azure HDInsight 中的 Spark 结构化流 | Microsoft Docs
description: 如何在 HDInsight Spark 群集上使用 Spark 结构化流应用程序。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: f3eccfb63b127f3cbb8f9d95ec2fa802861d8c9d
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="overview-of-spark-structured-streaming"></a>Spark 结构化流的概述

使用 Spark 结构化流可以实施可缩放、高吞吐量、容错的应用程序用于处理数据流。 结构化流构建在 Spark SQL 引擎的基础之上，从 Spark SQL 数据帧和数据集方面改进了构造，因此，我们可以像编写批处理查询一样编写流查询。  

结构化流应用程序在 HDInsight Spark 群集上运行，可从 Kafka、TCP 套接字（调试时）、Azure 存储或 Azure Data Lake Store 连接到流数据。 后两个选项依赖于外部存储服务，可用于观察添加到存储中的新文件，以及处理其内容，如同这些文件经过流式处理一样。 

结构化流创建长时间运行的查询，在此期间，可对输入数据应用操作，例如选择、投影、聚合、开窗，以及将流数据帧与引用数据帧相联接。 接下来，可以使用自定义代码（例如 SQL 数据库或 Power BI）将结果输出到文件存储（Azure 存储 Blob 或 Data Lake Store）或任何数据存储。 结构化流可向控制台提供输出以用于本地调试，还可以向内存中表提供输出，以便可以在 HDInsight 中查看用于调试的生成数据。 

![使用 HDInsight 和 Spark 结构化流进行流处理 ](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png)

> [!NOTE]
> Spark 结构化流即将取代 Spark 流 (DStreams)。 今后，结构化流会不断得到增强和维护，而 DStreams 只会保留维护模式。 在现成支持的源和接收器方面，结构化流的功能目前不如 DStreams 那样全面，因此，在选择适当的 Spark 流处理选项之前，请先评估要求。 

## <a name="streams-as-tables"></a>流即表

Spark 结构化流以表的形式表示数据流，该表的深度受限，即，随着新数据的抵达，该表会不断增大。 此输入表由一个长时间运行的查询持续处理，结果将发送到输出表：

![结构化流的概念](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png)

在结构化流中，数据抵达系统后立即被引入输入表中。 可以编写针对此输入表执行操作的查询（使用数据帧和数据集 API）。 查询输出将生成另一个表（结果表）。 结果表包含查询的结果，从中可以抽取外部数据存储（例如关系数据库）的数据。 处理输入表中数据的时间由触发器间隔控制。 默认情况下，触发器间隔为零，因此，结构化流会在数据抵达时尽快处理数据。 在实践中，这意味着结构化流在处理完前一查询的运行之后，会立即针对所有新收到的数据启动另一个处理运行。 可将触发器配置为根据某个间隔运行，以便在基于时间的批中处理流数据。 

结果表中的数据可以只包含自上次处理查询以来生成的新数据（追加模式），或者，每当生成新数据时，可以完全刷新结果表中的数据，使表中包含自开始执行流查询以来生成的所有输出数据（完整模式）。

### <a name="append-mode"></a>追加模式

在追加模式下，只有自上次运行查询以来添加到结果表的行才出现在结果表中，并写入外部存储。 例如，最简单的查询只是将输入表中的所有数据按原样复制到结果表。 在触发器间隔过去之后，将会处理新数据，代表这些新数据的行会显示在结果表中。 

假设你要处理来自温度传感器（例如恒温器）的遥测数据。 假设第一个触发器在时间 00:01 处理了设备 1 的一个事件，该设备的温度读数为 95 度。 在查询的第一个触发器中，只有包含时间 00:01 的行会出现在结果表中。 当另一个事件在时间 00:02 抵达时，唯一的新行是包含时间 00:02 的行，因此，结果表只包含该行。

![结构化流追加模式](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png)

使用追加模式时，查询将应用投影（选择它关注的列）、筛选（只选择与特定条件匹配的行）或联接（使用静态查找表中的数据来扩充数据）。 使用追加模式可以轻松做到只将相关的新数据推送到外部存储。

### <a name="complete-mode"></a>完整模式

沿用上述情景，但这一次使用的是完整模式。 在完整模式下，整个输出表根据触发器间隔刷新，因此，该表不仅包含来自最新触发器运行的数据，而且还包含来自所有运行的数据。 可以使用完整模式将输入表中的数据按原样复制到结果表。 在每个触发的运行中，新结果行会连同前面的所有行一起显示。 输出结果表最终会存储自查询开始以来收集的所有数据，并且最终将会耗尽内存。 完整模式适合用于以某种方式汇总传入数据的聚合查询，因此，在每个触发器间隔，将使用新的摘要更新结果表。 

假设到目前为止已经处理了五秒钟的数据，接下来需要处理第六秒钟的数据。 输入表包含时间 00:01 和时间 00:03 的事件。 此示例查询的目的是给出设备每隔五秒的平均温度。 此查询的实现将应用一个聚合，以便提取处于每个 5 秒时间范围内的所有值，计算温度平均值，然后生成一行来显示该间隔内的平均温度。 在第一个 5 秒时间范围结束时，会生成两个元组：(00:01, 1, 95) 和 (00:03, 1, 98)。 因此，对于时间范围 00:00-00:05，该聚合会生成包含平均温度 96.5 度的元组。 在下一个 5 秒时间范围内，时间 00:06 处只有一个数据点，因此，生成的平均温度为 98 度。 在时间 00:10 处使用完整模式时，结果表包含时间范围 00:00-00:05 和 00:05 00:10 的行，因为查询会输出所有聚合行，而不仅仅是新行。 因此，随着新时间范围的添加，结果表会不断增大。    

![结构化流完整模式](./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png)

使用所有使用完整模式的查询都会导致表无限增大。  沿用前面的示例，假设查询不是按时间范围求温度的平均值，而是按设备 ID 求温度的平均值。 结果表包含固定数量的行（每个设备一个），这些行包含根据设备发送的所有数据点计算得出的平均设备温度。 收到新温度后，结果表将会更新，因此，表中的平均值始终是最新的。 

## <a name="components-of-a-spark-structured-streaming-application"></a>Spark 结构化流应用程序的组件

这个简单的示例查询可以根据一小时时间范围汇总温度读数。 在本例中，数据存储在 Azure 存储（附加为 HDInsight 群集的默认存储）中的 JSON 文件内：

    {"time":1469501107,"temp":"95"}
    {"time":1469501147,"temp":"95"}
    {"time":1469501202,"temp":"95"}
    {"time":1469501219,"temp":"95"}
    {"time":1469501225,"temp":"95"}

这些 JSON 文件存储在 HDInsight 群集容器下的 `temps` 子文件夹中。 

### <a name="define-the-input-source"></a>定义输入源

首先配置一个数据帧，用于描述数据源，以及该源所需的任何设置。 此示例从 Azure 存储中的 JSON 文件抽取数据，并在读取时向这些数据应用一个架构。

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    //Cluster-local path to the folder containing the JSON files
    val inputPath = "/temps/" 

    //Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
    val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

    //Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
    val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath) 

#### <a name="apply-the-query"></a>应用查询

接下来，针对流数据帧应用包含所需操作的查询。 在本例中，某个聚合会将所有行分组到 1 小时时间范围，然后计算该 1 小时时间范围内的最小、平均和最大温度。

    val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))

### <a name="define-the-output-sink"></a>定义输出接收器

接下来，针对在每个触发器间隔内添加到结果表中的行定义目标。 此示例只是将所有行输出到稍后可以使用 SparkSQL 查询的内存中表 `temps`。 完整输出模式可以确保每次都会输出所有时间范围的所有行。

    val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete") 

### <a name="start-the-query"></a>启动查询

启动流查询，并一直运行到收到终止信号为止。 

    val query = streamingOutDF.start()  

### <a name="view-the-results"></a>查看结果

运行查询时，在同一个 SparkSession 中，可以针对存储查询结果的 `temps` 表运行 SparkSQL 查询。 

    select * from temps

此查询生成如下所示的结果：


| window |  min(temp) | avg(temp) | max(temp) |
| --- | --- | --- | --- |
|{u'start': u'2016-07-26T02:00:00.000Z', u'end'... |    95 |    95.231579 | 99 |
|{u'start': u'2016-07-26T03:00:00.000Z', u'end'...  |95 |   96.023048 | 99 |
|{u'start': u'2016-07-26T04:00:00.000Z', u'end'...  |95 |   96.797133 | 99 |
|{u'start': u'2016-07-26T05:00:00.000Z', u'end'...  |95 |   96.984639 | 99 |
|{u'start': u'2016-07-26T06:00:00.000Z', u'end'...  |95 |   97.014749 | 99 |
|{u'start': u'2016-07-26T07:00:00.000Z', u'end'...  |95 |   96.980971 | 99 |
|{u'start': u'2016-07-26T08:00:00.000Z', u'end'...  |95 |   96.965997 | 99 |  

有关 Spark 结构化流 API 及其支持的输入数据源、操作和输出接收器的详细信息，请参阅 [Spark 结构化流编程指南](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)。

## <a name="checkpointing-and-write-ahead-logs"></a>检查点和预写日志

为提供复原和容错功能，结构化流依赖检查点，确保即使出现节点故障，流处理仍可以不间断地继续。 在 HDInsight 中，Spark 会创建持久存储（Azure 存储或 Data Lake Store）的检查点。 这些检查点存储有关流查询的进度信息。 此外，结构化流使用预写日志 (WAL)。 WAL 捕获已收到的、但尚未由查询处理的引入数据。 如果发生故障并从 WAL 重新开始处理，从源收到的任何事件不会丢失。

## <a name="deploying-spark-streaming-applications"></a>部署 Spark 流式处理应用程序

通常在本地将 Spark 流应用程序生成为 JAR 文件，然后通过将此 JAR 文件复制到 HDInsight 群集上附加的默认存储，在 Spark on HDInsight 中部署此应用程序。 可以使用 POST 操作，通过群集上可用的 LIVY REST API 启动该应用程序。 POST 的正文包括提供 JAR 路径的 JSON 文档、其 main 方法定义并运行流应用程序的类的名称，可选的作业资源要求（例如执行器、内存和核心的数量）以及应用程序代码所需的任何配置设置。

![部署 Spark 流式处理应用程序](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

此外，可以使用 GET 请求针对 LIVY 终结点检查所有应用程序的状态。 最后，可以通过针对 LIVY 终结点发出 DELETE 请求终止正在运行的应用程序。 有关 LIVY API 的详细信息，请参阅[通过 LIVY 远程作业](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 中创建 Apache Spark 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark 结构化流编程指南](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [通过 LIVY 以远程方式启动 Spark 作业](apache-spark-livy-rest-interface.md)
