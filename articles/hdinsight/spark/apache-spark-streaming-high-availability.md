---
title: YARN 中高可用性的火花流作业 - Azure HDInsight
description: 如何在 Azure HDInsight 中设置 Apache Spark 流式处理以实现高可用性方案
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807147"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>使用 YARN 创建高可用性 Apache Spark 流式处理作业

使用 [Apache Spark](https://spark.apache.org/) 流式处理可以实现可缩放、高吞吐量、容错的应用程序进行数据流处理。 可将 HDInsight Spark 群集上的 Spark 流式处理应用程序连接到各种数据源，例如 Azure 事件中心、Azure IoT 中心、[Apache Kafka](https://kafka.apache.org/)、[Apache Flume](https://flume.apache.org/)、Twitter、[ZeroMQ](http://zeromq.org/)、原始 TCP 套接字，或者通过这些应用程序监视 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 文件系统中发生的更改。 Spark 流支持容错，可保证只会处理任意给定的事件一次，即使某个节点发生故障。

Spark Streaming 创建长时间运行的作业，在此期间，您可以将转换应用于数据，然后将结果推送到文件系统、数据库、仪表板和控制台。 Spark 流处理微批数据，它首先收集定义的时间间隔内的一批事件。 接下来，发送该批进行处理和输出。 批时间间隔通常以几分之一秒定义。

![Spark Streaming](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark 流使用离散流 (DStream) 表示连续的数据流。** 可以从事件中心或 Kafka 等输入源，或通过将转换应用到另一个 DStream，来创建此 DStream。 事件抵达 Spark 流应用程序后，将以可靠的方式存储该事件。 也就是说，会复制事件数据，使多个节点获取它的副本。 这可确保任何单个节点的故障不会导致事件丢失。

Spark 核心使用弹性分布式数据集 (RDD)。** RDD 将数据分布到群集中的多个节点，其中每个节点通常完全在内存中维护其自身的数据，以实现最佳性能。 每个 RDD 表示在某个批间隔内收集的事件。 批间隔时间过后，Spark 流会生成新的 RDD，其中包含该间隔内的所有数据。 此连续 RDD 集将收集到 DStream 中。 Spark 流应用程序处理每个批的 RDD 中存储的数据。

![Spark DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark 结构化流作业

Spark 结构化流在 Spark 2.0 中引入，在流式处理结构化数据时用作分析引擎。 Spark 结构化流使用 SparkSQL 批处理引擎 API。 与 Spark 流式处理一样，Spark 结构化流在不断到达的微批次数据上运行其计算。 Spark 结构化流以包含无限行的输入表形式表示数据流。 也就是说，随着新数据的抵达，输入表会不断增大。 此输入表由一个长时间运行的查询持续处理，结果将写出到输出表。

![Spark 结构化流](./media/apache-spark-streaming-high-availability/structured-streaming.png)

在结构化流中，数据抵达系统后立即被引入输入表中。 可以编写针对此输入表执行操作的查询。 查询输出将生成名为“结果表”的另一个表。 结果表包含查询的结果，从中可以抽取要发送到外部数据存储（例如关系数据库）的数据。 触发器间隔用于设置处理输入表中的数据的时间。** 默认情况下，结构化流会在数据抵达时尽快处理数据。 但是，也可以将触发器配置为根据更长的间隔运行，以便在基于时间的批中处理流数据。 每次出现新数据时，都可以刷新结果表中的数据，以便它包含自流式查询开始以来的所有输出数据（*完整模式*），或者它可能只包含自上次处理查询以来的新数据（*追加模式*）。

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>创建容错的 Spark 流作业

要为 Spark 流式处理作业创建一个高度可用的环境，请首先对单个作业进行编码，以便在发生故障时恢复。 此类自我恢复作业是容错的。

RDD 具有多个属性，可帮助高可用和容错的 Spark 流式处理作业：

* 在 RDD 中作为 DStream 存储的输入数据批将自动复制到内存中以实现容错。
* 工作节点故障导致数据丢失后，可以基于其他工作节点中复制的输入数据重新计算这些数据（前提是这些工作器节点可用）。
* 在一秒钟即可实现快速故障恢复，因为故障/延迟后的恢复是通过内存中计算发生的。

### <a name="exactly-once-semantics-with-spark-streaming"></a>使用 Spark 流式处理正好一次语义

若要创建一个处理每个事件一次（且仅一次）的应用程序，请考虑所有系统故障点在出现问题后如何重启，以及如何避免数据丢失。 正好一次语义要求在任何时候不丢失任何数据，并且无论发生故障的位置如何，消息处理都是可重新启动的。 请参阅[创建具有一次事件处理的 Spark 流式处理作业](apache-spark-streaming-exactly-once.md)。

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark 流式处理和 Apache Hadoop YARN

在 HDInsight 中，群集工作由 *Yet Another Resource Negotiator* (YARN) 协调。 设计 Spark 流的高可用性涉及到 Spark 流和 YARN 组件方面的技术。  下面显示了使用 YARN 的示例配置。

![YARN 体系结构](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

以下部分描述此配置的设计注意事项。

### <a name="plan-for-failures"></a>故障规划

若要创建高可用性的 YARN 配置，应该针对可能发生的执行器或驱动程序故障做好规划。 某些 Spark 流作业还包括数据保障要求，需要对此进行额外的配置和设置。 例如，流应用程序可能存在零数据丢失业务保障要求，且不能出现宿主流系统或 HDInsight 群集中发生的任何错误。

如果**执行器**发生故障，Spark 会自动重新启动其任务和接收器，因此无需更改配置。

但是，如果**驱动程序**发生故障，则其所有关联的执行器都会发生故障，并且所有已收到的块和计算结果都会丢失。 要从驱动程序故障中恢复，请使用*DStream 检查点*，如[创建 Spark 流作业中所述，正好处理一次事件](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)。 DStream 检查点会定期将 DStreams 的有向无环图 (DAG) 保存到 Azure 存储等容错存储中。**  检查点可让 Spark 结构化流根据检查点信息重启有故障的驱动程序。  重启此驱动程序会启动新的执行器，同时重启接收器。

使用 DStream 检查点恢复驱动程序：

* 使用配置设置 `yarn.resourcemanager.am.max-attempts` 在 YARN 上配置自动驱动程序重启。
* 使用 `streamingContext.checkpoint(hdfsDirectory)` 在 HDFS 兼容的文件系统中设置检查点目录。
* 重构源代码以使用检查点进行恢复，例如：

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* 通过使用 `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` 启用预写日志 (WAL) 来配置丢失数据的恢复，并使用 `StorageLevel.MEMORY_AND_DISK_SER` 针对输入 DStreams 禁用内存中复制。

总之，使用检查点 + WAL + 可靠的接收器，您将能够提供"至少一次"数据恢复：

* 正好一次，只要接收的数据不会丢失，并且输出要么是幂等的，要么是事务性的。
* 正好有一次，使用新的 Kafka 直接方法，该方法使用 Kafka 作为复制日志，而不是使用接收器或 WAL。

### <a name="typical-concerns-for-high-availability"></a>高可用性的一般考虑因素

* 监视流作业比批处理作业更难。 Spark 流作业通常要运行很长时间，而 YARN 只会在作业完成后才聚合日志。  在应用程序或 Spark 升级期间，Spark 检查点会丢失，因此，在升级期间需要清除检查点目录。

* 将 YARN 群集模式配置为即使客户端发生故障也要运行驱动程序。 设置驱动程序自动重启：

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark 和 Spark 流 UI 提供一个可配置的指标系统。 您还可以使用其他库（如 Graphite/Grafana）下载仪表板指标，例如"已处理的编号记录"、驱动程序&执行器上的内存/GC 使用情况、"总延迟"、"群集利用率"等。 在结构化流 2.1 或更高版本中，可以使用 `StreamingQueryListener` 来收集其他指标。

* 应将长时间运行的作业分段。  将 Spark 流应用程序提交到群集后，必须定义运行作业的 YARN 队列。 可以使用 [YARN 容量计划程序](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)将长时间运行的作业提交到单独的队列。

* 正常关闭流应用程序。 如果偏移量是已知的，并且所有应用程序状态存储在外部，则可以在适当的位置以编程方式停止流应用程序。 一种方法是通过每隔 *n* 秒检查一次外部标志，来使用 Spark 中的“线程挂钩”。 也可以在启动应用程序时使用 HDFS 中创建的标记文件，然后在想要停止应用程序时删除该文件。** 对于标记文件方法，可以在 Spark 应用程序中使用一个单独的线程，用于调用如下所示的代码：

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>后续步骤

* [Apache Spark 流式处理概述](apache-spark-streaming-overview.md)
* [使用一次事件处理创建 Apache Spark 流式处理作业](apache-spark-streaming-exactly-once.md)
* [YARN 中长时间运行的 Apache Spark 流式处理作业](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [结构化流：容错语义](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [离散流：可缩放流处理的容错模型](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
