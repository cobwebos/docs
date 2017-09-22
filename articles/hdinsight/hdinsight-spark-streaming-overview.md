---
title: "Azure HDInsight 中的 Spark 流式处理是什么 | Microsoft Docs"
description: "如何在 HDInsight Spark 群集上使用 Spark 流式处理应用程序。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 7608951ffb79d945ce3271792e7cf1a0d0b254b3
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="overview-of-spark-streaming"></a>Spark 流式处理概述

Spark 流式处理提供对 HDInsight Spark 群集的数据流处理，同时保证即便发生节点故障，任何输入事件也仅处理一次。 Spark 流是一个长时间运行的作业，接收各种来源（包括 Azure 事件中心、Azure IoT 中心、Kafka、Flume、Twitter、ZeroMQ、原始 TCP 套接字）的输入数据，或来自监视 HDFS 文件系统的输入数据。 与单独的事件驱动进程不同，Spark 流将输入数据批处理为时间范围，例如 2 秒的切片，然后使用映射、减少、联接和提取操作转化每批数据。 然后，它将转换后的数据写入文件系统、数据库、仪表板和控制台。

![使用 HDInsight 和 Spark 流式处理的流处理](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark 流式处理应用程序必须先等待几秒，以收集事件的每个微批处理，才能发送该批处理，用于处理。 与此相反，事件驱动应用程序会立即处理每个事件。 Spark 流式处理延迟一般为几秒钟。 微批处理方法的优点是数据处理效率更高和聚合计算更简单。

## <a name="introducing-the-dstream"></a>引入 DStream

Spark 流式处理使用称为 DStream 的离散流表示传入数据的连续流。 可以从事件中心或 Kafka 等输入源，或通过将转换应用于另一个 DStream，创建 DStream。

DStream 可提供基于原始事件数据的抽象层。 

从单一事件开始，例如已连接调温器的温度读数。 此事件到达 Spark 流式处理应用程序后，系统将以可靠方式存储事件，即在多个节点上进行复制。 此容错功能可确保任何单个节点的故障都不会导致事件丢失。 Spark 核心使用将数据分布到群集中的多个节点的数据结构，其中每个节点通常维护其自己的内存中数据，以实现最佳性能。 此数据结构称为弹性分布式数据集 (RDD)。

每个 RDD 表示在用户定义的时间范围（称为批处理间隔）内收集的事件。 每个批处理间隔后，将生成新的 RDD，其中包含该间隔的所有数据。 连续的 RDD 集将被收集到 DStream。 例如，如果批处理间隔为 1 秒，则 DStream 将每秒发出一个批处理，其中包含一个 RDD（包含该秒期间引入的所有数据）。 处理 DStream 时，温度事件将出现在其中一个批处理中。 Spark 流式处理应用程序处理包含事件的批处理并最终作用于每个 RDD 中存储的数据。

![温度事件的示例 DStream ](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark 流式处理应用程序的结构
Spark 流式处理应用程序是一个长时间运行的应用程序，从引入源接收数据，应用转换以处理数据，然后将数据推送到一个或多个目标。 Spark 流式处理应用程序的结构主要包含两个部分。 首先，定义数据的来源，要对数据执行哪些处理以及结果应发送到何处。 其次，无限期运行应用程序，等待停止信号。

例如，下面是一个简单的应用程序，通过 TCP 套接字接收一行文本并对每个单词出现的次数进行计数。

### <a name="define-the-application"></a>定义应用程序

应用程序逻辑定义分为以下四步：

1. 创建 StreamingContext。
2. 从 StreamingContext 创建 DStream。
3. 将转换应用于 DStream。
4. 输出结果。

此定义是静态的，且在运行该应用程序前没有处理任何数据。

#### <a name="create-a-streamingcontext"></a>创建 StreamingContext
从指向群集的 SparkContext 创建 StreamingContext。 创建 StreamingContext 时，指定批处理的大小（以秒为单位），例如：

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>创建 DStream
使用创建的 StreamingContext 实例，为输入源创建输入 DStream。 在这种情况下，我们将监视附加到 HDInsight 群集的默认存储中的新文件的外观。

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>应用转换
通过对 DStream 应用转换来实现处理。 应用程序将从文件中一次接收一行文本，将每行拆分成单词。然后使用 map-reduce 模式对每个单词出现的次数进行计数。

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>输出结果
通过应用输出操作，将转换结果推送到目标系统。 在这种情况下，我们将通过计算在控制台输出中显示每个运行的结果。

    wordCounts.print()

### <a name="run-the-application"></a>运行应用程序
启动流式处理应用程序并运行，直到收到终止信号。

    ssc.start()            
    ssc.awaitTermination()

有关 Spark 流 API 及其支持的事件源、转换和输出操作的详细信息，请参阅 [Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)（Spark 流式处理编程指南）。

下面的示例应用程序是自包含的，因此可以在 [Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-kernels.md) 内运行它。 在示例中，我们将在类 DummySource 中创建模拟数据源，该源每五秒输出计数器的值以及当前时间（以毫秒为单位）。 将创建新的 StreamingContext 对象，其批处理间隔为 30 秒。 每次创建批处理时，它都将检查生成的 RDD，将其转换为 Spark DataFrame 并通过 DataFrame 创建一个临时表。

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

然后，我们便可以定期查询 DataFrame，查看批处理中存在的当前值集。 在这种情况下，使用以下 SQL 查询：

    %%sql
    SELECT * FROM demo_numbers

生成的输出如下所示：

| value | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

我们会看到六个值，因为 DummySource 每隔 5 秒创建一个值，且我们每隔 30 秒发出一个批处理。

## <a name="sliding-windows"></a>滑动窗口
如果想在某个时间段对 DStream 执行聚合计算，例如获取最后 2 秒钟内的平均温度，可以使用 Spark 流式处理随附的滑动窗口操作。 滑动窗口定义为具有一个持续时间（窗口长度）和在期间计算窗口内容的时间间隔（即滑动间隔）。

这些滑动窗口可以重叠，例如，可以定义长度为 2 秒的窗口，每 1 秒滑动一次。 这意味着每次执行聚合计算时，窗口将包括上一个窗口最后 1 秒的数据以及下 1 秒的任何新数据。

![温度事件的示例初始窗口](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![滑动后的温度事件的示例窗口](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

在下面的示例中，我们将更新使用 DummySource 的代码，将批处理收集到持续时间 1 分钟和滑动 1 分钟的窗口中。

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

第一分钟后，这会产生 12 个条目 - 窗口中收集到的两个批处理中各有 6 个条目。

| value | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

Spark 流式传输 API 中可用的滑动窗口函数包括 window、countByWindow、reduceByWindow 和 countByValueAndWindow。 有关这些函数的详细信息，请参阅 [Transformations on DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams)（DStreams 的转换）。

## <a name="checkpointing"></a>检查点
为提供复原和容错功能，Spark 流式处理依赖检查点，确保即使出现节点故障，流处理仍可以不间断地继续。 在 HDInsight 中，Spark 创建持久存储（Azure 存储或 Data Lake Store）的检查点。 这些检查点存储关于流式处理应用程序的元数据，例如应用程序定义的配置和操作，以及已排队但尚未处理的所有批处理。 在某些情况下，检查点还包括将数据保存在 RDD 中以缩短从 Spark 托管的 RDD 中存在的内容重新生成数据状态所需的时间。

## <a name="deploying-spark-streaming-applications"></a>部署 Spark 流式处理应用程序
通常在本地生成 Spark 流式处理应用程序，然后将其部署到 Spark on HDInsight，方法是通过将包含应用程序的 JAR 文件复制到附加到 HDInsight 群集的默认存储中。 然后，可以使用群集中可用的 LIVY REST API 启动应用程序。 这属于 POST 操作，其中 POST 的正文包括提供 JAR 路径的 JSON 文档、其 main 方法定义并运行流式处理应用程序的类的名称，可选的作业资源要求（如执行器、内存和内核的数量）以及应用程序代码所需的任何配置设置。

![滑动后的温度事件的示例窗口](./media/hdinsight-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

此外，可以使用 GET 请求针对 LIVY 终结点检查所有应用程序的状态。 最后，可以通过针对 LIVE 终结点发出 DELETE 请求终止正在运行的应用程序。 有关 LIVY API 的详细信息，请参阅[通过 LIVY 远程作业](hdinsight-apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>后续步骤

* [在 HDInsight 中创建 Apache Spark 群集](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)（Spark 流式处理编程指南）
* [通过 LIVY 以远程方式启动 Spark 作业](hdinsight-apache-spark-livy-rest-interface.md)
