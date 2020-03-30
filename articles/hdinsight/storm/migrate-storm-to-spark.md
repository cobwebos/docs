---
title: 将 Azure HDInsight 3.6 阿帕奇风暴迁移到 HDInsight 4.0 阿帕奇火花
description: 用于将 Apache Storm 工作负载迁移到 Spark 流式处理或 Spark 结构化流的差异和迁移流。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157787"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>将 Azure HDInsight 3.6 阿帕奇风暴迁移到 HDInsight 4.0 阿帕奇火花

本文档介绍如何在 HDInsight 3.6 上将 Apache 风暴工作负载迁移到 HDInsight 4.0。 HDInsight 4.0 不支持 Apache 风暴群集类型，您需要迁移到另一个流数据平台。 两个合适的选项是 Apache Spark 流式处理和 Spark 结构化流式处理。 本文档介绍这些平台之间的差异，并推荐用于迁移 Apache Storm 工作负载的工作流。

## <a name="storm-migration-paths-in-hdinsight"></a>HDInsight 中的风暴迁移路径

如果要在 HDInsight 3.6 上从 Apache 风暴迁移，您有多种选项：

* HDInsight 4.0 上的火花流
* HDInsight 4.0 上的火花结构化流式处理
* Azure 流分析

本文档提供了从 Apache 风暴迁移到 Spark 流和火花结构化流的指南。

> [!div class="mx-imgBorder"]
> ![HDInsight 风暴迁移路径](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>阿帕奇风暴和火花流的比较，火花结构化流

Apache Storm 可以提供不同级别的有保证的消息处理。 例如，基本的 Storm 应用程序可以保证至少一次处理，[而 Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html)可以保证一次处理。 Spark 流式处理和 Spark 结构化流可确保任何输入事件完全处理一次，即使发生节点故障也是如此。 Storm 具有处理每个单个事件的模型，您还可以将微型批处理模型与三叉星一起使用。 Spark 流式处理和火花结构化流提供微批处理模型。

|  |Storm |Spark 流式处理 | 火花结构化流|
|---|---|---|---|
|**事件处理保证**|至少一次 <br> 正好一次（三叉） |[正好一次](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[正好一次](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**处理模型**|实时 <br> 微型批次（三叉） |微型批次 |微型批次 |
|**事件时间支持**|[是](https://storm.apache.org/releases/2.0.0/Windowing.html)|否|[是](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**语言**|Java 等|斯卡拉， 爪哇， Python|Python， R， Scala， Java， SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>火花流与 Spark 结构化流

火花结构化流正在取代火花流 （DStreams）。 结构化流将继续接收增强和维护，而 DStreams 将仅处于维护模式。 **注意：需要链接来强调这一点**。 结构化流栈的功能不如它支持的源和接收器的 DStreams 多，因此请评估您的要求，以选择适当的 Spark 流处理选项。

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>流式处理（单事件）处理与微批处理处理

Storm 提供了处理每个单个事件的模型。 这意味着所有传入的记录一到达即会立即处理。 Spark 流式处理应用程序必须先等待一会，以收集事件的每个微批处理，才能发送该批处理进行处理。 与此相反，事件驱动应用程序会立即处理每个事件。 Spark 流式处理延迟一般为几秒钟。 微批处理方法的优点是数据处理效率更高和聚合计算更简单。

> [!div class="mx-imgBorder"]
> ![流式处理和微批次处理](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>风暴架构和组件

Storm 拓扑由多个以有向无环图 (DAG) 形式排列的组件构成。 数据在该图中的组件之间流动。 每个组件使用一个或多个数据流，并可选择性地发出一个或多个流。

|组件 |描述 |
|---|---|
|壶 嘴|将数据引入拓扑。 它们将一个或多个流发出到拓扑中。|
|螺栓|消耗从喷口或其他螺栓发出的流。 Bolt 可以选择性地将流发出到拓扑中。 Bolt 还负责将数据写入 HDFS、Kafka 或 HBase 等外部服务或存储。|

> [!div class="mx-imgBorder"]
> ![风暴成分的相互作用](./media/migrate-storm-to-spark/apache-storm-components.png)

风暴由以下三个守护进程组成，它们使风暴群集保持正常运行。

|守护程序 |描述 |
|---|---|
|Nimbus|与 Hadoop JobTracker 类似，它负责在群集周围分发代码，并将任务分配给计算机并监视故障。|
|Zookeeper|用于群集协调。|
|监督器|侦听分配给其机器的工作，并根据 Nimbus 的指令启动和停止工作流程。 每个辅助角色进程执行拓扑的子集。 用户的应用程序逻辑（Souts 和螺栓）在此处运行。|

> [!div class="mx-imgBorder"]
> ![尼姆布斯，动物园管理员和主管守护神](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark 流式处理架构和组件

以下步骤总结了组件在 Spark 流 （DStreams） 和 Spark 结构化流中如何协同工作：

* 启动 Spark 流式处理时，驱动程序将启动执行器中的任务。
* 执行器从流数据源接收流。
* 当执行器接收数据流时，它会将流拆分为块，并将它们保存在内存中。
* 数据块将复制到其他执行器。
* 然后，处理后的数据存储在目标数据存储中。

> [!div class="mx-imgBorder"]
> ![火花流路径输出](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>火花流 （DStream） 工作流

每个批处理间隔后，将生成新的 RDD，其中包含该间隔的所有数据。 连续的 RDD 集被收集到 DStream 中。 例如，如果批处理间隔为 1 秒，则 DStream 将每秒发出一个批处理，其中包含一个 RDD（包含该秒期间引入的所有数据）。 处理 DStream 时，温度事件将出现在其中一个批处理中。 Spark 流式处理应用程序处理包含事件的批处理并最终作用于每个 RDD 中存储的数据。

> [!div class="mx-imgBorder"]
> ![火花流处理批次](./media/migrate-storm-to-spark/spark-streaming-batches.png)

有关 Spark 流式处理可用的不同转换的详细信息，请参阅[DStreams 上的转换](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)。

## <a name="spark-structured-streaming"></a>Spark 结构化流

Spark 结构化流表示数据流作为未绑定深度的表。 随着新数据的到来，该表将继续增长。 此输入表由长时间运行的查询持续处理，并将结果发送到输出表。

在结构化流中，数据抵达系统后立即被引入输入表中。 可以编写针对此输入表执行操作的查询（使用数据帧和数据集 API）。

查询输出生成*结果表*，其中包含查询的结果。 可以从外部数据存储（此类关系数据库）的结果表中绘制数据。

处理输入表中数据的时间由触发器间隔控制。 默认情况下，触发器间隔为零，因此，结构化流会在数据抵达时尽快处理数据。 在实践中，这意味着结构化流在处理完前一查询的运行之后，会立即针对所有新收到的数据启动另一个处理运行。 可将触发器配置为根据某个间隔运行，以便在基于时间的批中处理流数据。

> [!div class="mx-imgBorder"]
> ![结构化流中数据处理](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![结构化流编程模型](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>一般迁移流

建议的从 Storm 到 Spark 的迁移流假定以下初始体系结构：

* 卡夫卡用作流数据源
* 卡夫卡和风暴部署在同一虚拟网络上
* Storm 处理的数据将写入数据接收器，例如 Azure 存储或 Azure 数据湖存储 Gen2。

    > [!div class="mx-imgBorder"]
    > ![假定当前环境图](./media/migrate-storm-to-spark/presumed-current-environment.png)

要将应用程序从 Storm 迁移到 Spark 流 API 之一，请执行以下操作：

1. **部署新群集。** 在同一虚拟网络中部署新的 HDInsight 4.0 Spark 群集，并在其中部署 Spark 流式处理或 Spark 结构化流式处理应用程序并对其进行彻底测试。

    > [!div class="mx-imgBorder"]
    > ![HDInsight 中的新火花部署](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **停止在旧的风暴群集上使用。** 在现有 Storm 中，停止使用来自流数据源的数据，然后等待数据完成写入目标接收器。

    > [!div class="mx-imgBorder"]
    > ![停止在当前群集上使用](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **开始在新的 Spark 群集上使用。** 从新部署的 HDInsight 4.0 Spark 群集开始流式传输数据。 此时，该过程通过从最新的 Kafka 偏移量中消耗来接管。

    > [!div class="mx-imgBorder"]
    > ![开始在新群集上使用](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **根据需要删除旧群集。** 交换机完成并正常工作后，根据需要删除旧的 HDInsight 3.6 风暴群集。

    > [!div class="mx-imgBorder"]
    > ![根据需要删除旧的 HDInsight 群集](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>后续步骤

有关风暴、火花流和 Spark 结构化流的详细信息，请参阅以下文档：

* [Apache Spark 流式处理概述](../spark/apache-spark-streaming-overview.md)
* [Apache Spark 结构化流的概述](../spark/apache-spark-structured-streaming-overview.md)