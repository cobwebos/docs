---
title: 将 Azure HDInsight 3.6 Apache Storm 迁移到 HDInsight 4.0 Apache Spark
description: Spark 流或 Spark 结构化流的差异，以及将 Apache Storm 工作负荷迁移到这两个平台的迁移流程。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: e1262a4699bc42cb5b9a4398be2254854c5d5ff2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86081190"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>将 Azure HDInsight 3.6 Apache Storm 迁移到 HDInsight 4.0 Apache Spark

本文档介绍如何将 HDInsight 3.6 上的 Apache Storm 工作负荷迁移到 HDInsight 4.0。 HDInsight 4.0 不支持 Apache Storm 群集类型，你需要迁移到另一流数据平台。 两个适当的选项是 Apache Spark 流和 Spark 结构化流。 本文档将介绍这些平台的差异，并提供迁移 Apache Storm 工作负荷的建议工作流。

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight 中的 Storm 迁移路径

若要从 HDInsight 3.6 上的 Apache Storm 进行迁移，可以使用多个选项：

* HDInsight 4.0 上的 Spark 流
* HDInsight 4.0 上的 Spark 结构化流
* Azure 流分析

本文档提供有关从 Apache Storm 迁移到 Spark 流和 Spark 结构化流的指导。

> [!div class="mx-imgBorder"]
> ![HDInsight Storm 迁移路径](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Apache Storm、Spark 流与 Spark 结构化流之间的比较

Apache Storm 可以提供不同级别的有保证的消息处理。 例如，基本的 Storm 应用程序至少可以保证一次处理，而 [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) 仅可以保证一次处理。 Spark 流和 Spark 结构化流保证恰好处理任意输入事件一次，即使某个节点发生故障。 Storm 提供一个用于处理每个事件的模型，你也可以将微批模型与 Trident 配合使用。 Spark 流和 Spark 结构化流提供微批处理模型。

|  |Storm |Spark 流式处理 | Spark 结构化流|
|---|---|---|---|
|**事件处理保证**|至少一次 <br> 恰好一次 (Trident) |[恰好一次](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[恰好一次](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**处理模型**|实时 <br> 微批 (Trident) |微批 |微批 |
|**事件时间支持**|[是](https://storm.apache.org/releases/2.0.0/Windowing.html)|否|[是](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**语言**|Java 等。|Scala、Java、Python|Python、R、Scala、Java、SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark 流与 Spark 结构化流

Spark 结构化流即将取代 Spark 流 (DStreams)。 结构化流会不断得到增强和维护，而 DStreams 只会保留维护模式。 **注释：需要提供链接来强调这一点**。 在现成支持的源和接收器方面，结构化流的功能目前不如 DStreams 那么多，因此，在选择适当的 Spark 流处理选项之前，请先评估要求。

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>流（单一事件）处理与微批处理

Storm 提供用于处理每个事件的模型。 这意味着，所有传入的记录在抵达后即会得到处理。 Spark 流应用程序必须先等待一会，以收集事件的每个微批，然后发送该批进行处理。 与此相反，事件驱动应用程序会立即处理每个事件。 Spark 流式处理延迟一般为几秒钟。 微批处理方法的优点是数据处理效率更高和聚合计算更简单。

> [!div class="mx-imgBorder"]
> ![流处理和微批处理](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Storm 体系结构和组件

Storm 拓扑由多个以有向无环图 (DAG) 形式排列的组件构成。 数据在该图中的组件之间流动。 每个组件使用一个或多个数据流，并可选择性地发出一个或多个流。

|组件 |说明 |
|---|---|
|Spout|将数据引入拓扑。 它们将一个或多个流发出到拓扑中。|
|Bolt|使用 Spout 或其他 Bolt 发出的流。 Bolt 可以选择性地将流发出到拓扑中。 Bolt 还负责将数据写入 HDFS、Kafka 或 HBase 等外部服务或存储。|

> [!div class="mx-imgBorder"]
> ![Storm 组件的交互](./media/migrate-storm-to-spark/apache-storm-components.png)

Storm 包括使 Storm 群集保持正常运行的以下三个守护程序。

|守护程序 |说明 |
|---|---|
|Nimbus|类似于 Hadoop JobTracker，负责在整个群集中分发代码、将任务分配给计算机以及监视故障情况。|
|Zookeeper|用于群集协调。|
|监督器|侦听分配给其计算机的工作，并根据 Nimbus 的指令启动和停止工作进程。 每个工作进程执行一部分拓扑。 用户的应用程序逻辑（Spout 和 Bolt）在此处运行。|

> [!div class="mx-imgBorder"]
> ![nimbus、zookeeper 和 supervisor 守护程序](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark 流体系结构和组件

以下步骤汇总了组件如何在 Spark 流 (DStreams) 和 Spark 结构化流中配合工作：

* 启动 Spark 流时，驱动程序将在执行程序中启动任务。
* 执行程序从流数据源接收流。
* 当执行程序收到数据流时，会将流拆分为块，并将其保留在内存中。
* 数据块将复制到其他执行程序。
* 然后，处理的数据将存储在目标数据存储中。

> [!div class="mx-imgBorder"]
> ![要输出的 Spark 流路径](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Spark 流 (DStream) 工作流

每个批处理间隔后，将生成新的 RDD，其中包含该间隔的所有数据。 连续的 RDD 集将被收集到 DStream 中。 例如，如果批处理间隔为 1 秒，则 DStream 将每秒发出一个批处理，其中包含一个 RDD（包含该秒期间引入的所有数据）。 处理 DStream 时，温度事件将出现在其中一个批处理中。 Spark 流式处理应用程序处理包含事件的批处理并最终作用于每个 RDD 中存储的数据。

> [!div class="mx-imgBorder"]
> ![Spark 流处理批](./media/migrate-storm-to-spark/spark-streaming-batches.png)

有关适用于 Spark 流的不同转换的详细信息，请参阅 [DStreams 中的转换](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)。

## <a name="spark-structured-streaming"></a>Spark 结构化流

Spark 结构化流以表的形式表示数据流，该表的深度不受限。 随着新数据的抵达，该表会不断增大。 此输入表由一个长时间运行的查询持续处理，结果将发送到输出表。

在结构化流中，数据抵达系统后立即被引入输入表中。 可以编写针对此输入表执行操作的查询（使用数据帧和数据集 API）。

查询输出生成一个结果表，其中包含查询的结果。  可以基于外部数据存储（例如关系数据库）的结果表绘制数据。

处理输入表中数据的时间由触发器间隔控制。 默认情况下，触发器间隔为零，因此，结构化流会在数据抵达时尽快处理数据。 在实践中，这意味着结构化流在处理完前一查询的运行之后，会立即针对所有新收到的数据启动另一个处理运行。 可将触发器配置为根据某个间隔运行，以便在基于时间的批中处理流数据。

> [!div class="mx-imgBorder"]
> ![结构化流中数据的处理](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![结构化流的编程模型](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>一般迁移流程

从 Storm 迁移到 Spark 的建议迁移流程假设采用以下初始体系结构：

* 将 Kafka 用作流数据源
* Kafka 和 Storm 部署在同一虚拟网络中
* Storm 处理的数据写入 Azure 存储或 Azure Data Lake Storage Gen2 等数据接收器。

    > [!div class="mx-imgBorder"]
    > ![设想的当前环境示意图](./media/migrate-storm-to-spark/presumed-current-environment.png)

若要将应用程序从 Storm 迁移到某个 Spark 流 API，请执行以下操作：

1. **部署新群集。** 在同一虚拟网络中部署新的 HDInsight 4.0 Spark 群集，在该群集上部署 Spark 流或 Spark 结构化流应用程序，并对其进行全面的测试。

    > [!div class="mx-imgBorder"]
    > ![HDInsight 中的新 Spark 部署](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **停止旧 Storm 群集上的数据使用。** 在现有 Storm 中，停止使用流数据源中的数据，并等待完成将数据写入目标接收器。

    > [!div class="mx-imgBorder"]
    > ![停止当前群集上的数据使用](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **开始在新 Spark 群集上使用数据。** 开始从新部署的 HDInsight 4.0 Spark 群集流式传输数据。 此时，将通过从最新的 Kafka 偏移量使用数据来接管进程。

    > [!div class="mx-imgBorder"]
    > ![开始在新群集上使用数据](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **根据需要删除旧群集。** 切换完成并正常工作后，根据需要删除旧的 HDInsight 3.6 Storm 群集。

    > [!div class="mx-imgBorder"]
    > ![根据需要删除旧的 HDInsight 群集](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>后续步骤

有关 Storm、Spark 流和 Spark 结构化流的详细信息，请参阅以下文档：

* [Apache Spark 流式处理概述](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 结构化流的概述](../spark/apache-spark-structured-streaming-overview.md)
