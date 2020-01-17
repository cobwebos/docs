---
title: 将 Azure HDInsight 3.6 Apache Storm 迁移到 HDInsight 4.0 Apache Spark
description: 将 Apache Storm 工作负荷迁移到 Spark 流式处理或 Spark 结构化流式处理的差异和迁移流程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157787"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>将 Azure HDInsight 3.6 Apache Storm 迁移到 HDInsight 4.0 Apache Spark

本文档介绍如何将 HDInsight 3.6 上的 Apache Storm 工作负荷迁移到 HDInsight 4.0。 HDInsight 4.0 不支持 Apache Storm 群集类型，你将需要迁移到另一流数据平台。 两个合适的选项是 Apache Spark 流式处理和 Spark 结构化流式处理。 本文档介绍了这些平台之间的差异，还建议使用工作流迁移 Apache Storm 工作负荷。

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight 中的风暴迁移路径

如果要从 HDInsight 3.6 上的 Apache Storm 迁移，可以使用多个选项：

* HDInsight 4.0 上的 Spark 流式处理
* HDInsight 4.0 上的 Spark 结构化流式处理
* Azure 流分析

本文档提供有关从 Apache Storm 迁移到 Spark 流式处理和 Spark 结构化流式处理的指南。

> [!div class="mx-imgBorder"]
> ![HDInsight 风暴迁移路径](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Apache Storm 和 Spark 流式处理、Spark 结构化流式处理之间的比较

Apache Storm 可以提供不同级别的有保证的消息处理。 例如，基本的 Storm 应用程序至少可以保证一次处理，而 [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) 仅可以保证一次处理。 Spark 流式处理和 Spark 结构化流式处理可保证只要发生节点故障，就能准确处理任何输入事件。 风暴具有处理每个单个事件的模型，还可以将微批处理模型与 Trident 一起使用。 Spark 流式处理和 Spark 结构化流式处理提供了微批处理处理模型。

|  |Storm |Spark 流式处理 | Spark 结构化流式处理|
|---|---|---|---|
|**事件处理保障**|至少一次 <br> 恰好一次（Trident） |[恰好一次](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[恰好一次](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**处理模型**|实时 <br> 微批（Trident） |微批处理 |微批处理 |
|**事件时间支持**|[是](https://storm.apache.org/releases/2.0.0/Windowing.html)|否|[是](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**语言**|Java 等。|Scala、Java、Python|Python、R、Scala、Java、SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark 流式处理与 Spark 结构化流式处理

Spark 结构化流式处理正在替换 Spark 流式处理（DStreams）。 结构化流式处理将继续接收增强和维护，而 DStreams 只会处于维护模式。 **注意：需要一些链接来强调这一点**。 结构化流的功能与 DStreams 所支持的源和接收器的功能并不相同，因此请评估你的要求，以便选择适当的 Spark 流处理选项。

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>流式处理（单一事件）处理与微批处理

风暴提供处理每个单个事件的模型。 这意味着所有传入的记录将在其到达时立即处理。 Spark 流式处理应用程序必须等待一小部分，收集事件的每个微批处理，才能发送该批处理进行处理。 与此相反，事件驱动应用程序会立即处理每个事件。 Spark 流式处理延迟一般为几秒钟。 微批处理方法的优点是数据处理效率更高和聚合计算更简单。

> [!div class="mx-imgBorder"]
> ![流式处理和微批处理](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>风暴体系结构和组件

Storm 拓扑由多个以有向无环图 (DAG) 形式排列的组件构成。 数据在该图中的组件之间流动。 每个组件使用一个或多个数据流，并可选择性地发出一个或多个流。

|组件 |Description |
|---|---|
|Spout|将数据引入拓扑。 它们将一个或多个流发出到拓扑中。|
|闪电|使用从 spout 或其他螺栓发出的流。 Bolt 可以选择性地将流发出到拓扑中。 Bolt 还负责将数据写入 HDFS、Kafka 或 HBase 等外部服务或存储。|

> [!div class="mx-imgBorder"]
> ![风暴组件的交互](./media/migrate-storm-to-spark/apache-storm-components.png)

风暴包含以下三个守护程序，使风暴群集正常运行。

|守护程序 |Description |
|---|---|
|Nimbus|与 Hadoop JobTracker 相似，它负责在群集周围分发代码，并将任务分配给计算机并监视失败。|
|Zookeeper|用于群集协调。|
|Supervisor|侦听分配给其计算机的工作，根据 Nimbus 中的指令启动和停止工作进程。 每个工作进程都执行拓扑的一个子集。 用户的应用程序逻辑（Spout 和螺栓）在此处运行。|

> [!div class="mx-imgBorder"]
> ![nimbus、zookeeper 和监察员守护程序](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark 流式处理体系结构和组件

以下步骤总结了组件在 Spark 流式处理（DStreams）和 Spark 结构化流式处理中的协同工作方式：

* 启动 Spark 流式处理时，驱动程序会在执行器中启动任务。
* 执行器从流式处理数据源接收流。
* 当执行器接收数据流时，它会将流拆分为块，并将其保留在内存中。
* 数据块将复制到其他执行器。
* 然后，处理后的数据将存储在目标数据存储区中。

> [!div class="mx-imgBorder"]
> ![spark 流式传输路径到输出](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Spark 流式处理（DStream）工作流

每个批处理间隔后，将生成新的 RDD，其中包含该间隔的所有数据。 将连续的 Rdd 集收集到 DStream 中。 例如，如果批处理间隔为 1 秒，则 DStream 将每秒发出一个批处理，其中包含一个 RDD（包含该秒期间引入的所有数据）。 处理 DStream 时，温度事件将出现在其中一个批处理中。 Spark 流式处理应用程序处理包含事件的批处理并最终作用于每个 RDD 中存储的数据。

> [!div class="mx-imgBorder"]
> ![spark 流式处理批处理](./media/migrate-storm-to-spark/spark-streaming-batches.png)

有关 Spark 流式处理可用的不同转换的详细信息，请参阅[DStreams 上的转换](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)。

## <a name="spark-structured-streaming"></a>Spark 结构化流

Spark 结构化流式处理以表的形式表示数据流，这种情况并不深入。 随着新数据的到达，表继续增长。 此输入表由长时间运行的查询持续处理，结果将发送到输出表。

在结构化流中，数据抵达系统后立即被引入输入表中。 可以编写针对此输入表执行操作的查询（使用数据帧和数据集 API）。

查询输出将生成一个*结果表*，其中包含查询的结果。 您可以为外部数据存储（例如关系数据库）的结果表中绘制数据。

从输入表中处理数据的时间由触发器间隔控制。 默认情况下，触发器间隔为零，因此，结构化流会在数据抵达时尽快处理数据。 在实践中，这意味着结构化流在处理完前一查询的运行之后，会立即针对所有新收到的数据启动另一个处理运行。 可将触发器配置为根据某个间隔运行，以便在基于时间的批中处理流数据。

> [!div class="mx-imgBorder"]
> ![处理结构化流式处理中的数据](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> 结构化流式处理的 ![编程模型](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>一般迁移流程

从风暴到 Spark 的建议迁移流程采用以下初始体系结构：

* Kafka 用作流式处理数据源
* Kafka 和风暴部署在同一虚拟网络上
* 风暴处理的数据会写入数据接收器，如 Azure 存储或 Azure Data Lake Storage Gen2。

    > [!div class="mx-imgBorder"]
    > 假设的当前环境 ![关系图](./media/migrate-storm-to-spark/presumed-current-environment.png)

若要将应用程序从风暴迁移到其中一个 Spark 流式处理 Api，请执行以下操作：

1. **部署新群集。** 在同一虚拟网络中部署新的 HDInsight 4.0 Spark 群集，并在其上部署 Spark 流式处理或 Spark 结构化流式处理应用程序并对其进行彻底的测试。

    > [!div class="mx-imgBorder"]
    > ![HDInsight 中的新 spark 部署](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **停止在旧风暴群集上使用。** 在现有风暴中，停止使用流式处理数据源中的数据，并等待数据完成写入目标接收器。

    > [!div class="mx-imgBorder"]
    > ![停止在当前群集上使用](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **开始在新 Spark 群集上使用。** 从新部署的 HDInsight 4.0 Spark 群集开始流式传输数据。 此时，将通过使用最新的 Kafka 偏移量来接管进程。

    > [!div class="mx-imgBorder"]
    > ![开始使用新群集](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **根据需要删除旧群集。** 开关完成并正常工作后，根据需要删除旧的 HDInsight 3.6 风暴群集。

    > [!div class="mx-imgBorder"]
    > ![根据需要删除旧的 HDInsight 群集](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>后续步骤

有关风暴、Spark 流式处理和 Spark 结构化流式处理的详细信息，请参阅以下文档：

* [Apache Spark 流式处理概述](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 结构化流式处理概述](../spark/apache-spark-structured-streaming-overview.md)