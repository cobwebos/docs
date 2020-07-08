---
title: 优化 Apache Spark 群集配置 - Azure HDInsight
description: 了解如何配置 Apache Spark 群集以最大化 Azure HDInsight 的吞吐量。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 464f0dcab3debf92605d2f13be9b25ece63f4bf2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737676"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Apache Spark 的群集配置优化

本文介绍如何在 Azure HDInsight 上优化 Apache Spark 群集的配置，以获得最佳性能。

## <a name="overview"></a>概述

根据 Spark 群集工作负荷，用户可能认为某个非默认 Spark 配置更能优化 Spark 作业执行。  使用示例工作负载执行基准测试，以验证任何非默认群集配置。

下面是一些可调整的常见参数：

|参数 |说明 |
|---|---|
|--num-executors|设置适当的执行程序数量。|
|--executor-cores|设置每个执行程序的内核数。 通常应使用中等大小的执行程序，因为其他进程会占用部分可用内存。|
|--executor-memory|设置每个执行程序的内存大小，用于控制 YARN 上的堆大小。 留一些内存用于执行开销。|

## <a name="select-the-correct-executor-size"></a>选择正确的执行程序大小

在决定执行程序配置时，请考虑 Java 垃圾回收 (GC) 开销。

* 通过以下方式减小执行程序大小：
    1. 将堆大小减至 32 GB 以下，使 GC 开销 < 10%。
    2. 减少内核数，使 GC 开销 < 10%。

* 通过以下方式增加执行程序大小：
    1. 减少执行程序之间的通信开销。
    2. 在较大的群集（超过 100 个执行程序）上减少执行程序 (N2) 之间已打开的连接数。
    3. 增加堆大小，以容纳占用大量内存的任务。
    4. 可选：减少每个执行程序的内存开销。
    5. 可选：通过超额订阅 CPU 来增加使用率和并发。

一般情况下，选择执行程序大小时：

1. 最开始，每个执行程序 30 GB，并分发可用的计算机内核。
2. 对于较大的群集（超过 100 个执行程序），增加执行程序内核数。
3. 基于试运行和上述因素（比如 GC 开销）修改大小。

运行并发查询时，请考虑：

1. 最开始，每个执行程序 30 GB，并分发所有计算机内核。
2. 通过超额订阅 CPU，创建多个并行 Spark 应用程序（延迟缩短大约 30%）。
3. 跨并行应用程序分布查询。
4. 基于试运行和上述因素（比如 GC 开销）修改大小。

有关使用 Ambari 配置执行程序的更多信息，请参阅 [Apache Spark 设置 - Spark 执行程序](apache-spark-settings.md#configuring-spark-executors)。

通过查看时间线视图，监视查询性能中的离群值或其他性能问题。 还可以查看 SQL 图、作业统计信息等。 有关使用 YARN 和 Spark History Server 调试 Spark 作业的信息，请参阅[调试 Azure HDInsight 中运行的 Apache Spark 作业](apache-spark-job-debugging.md)。 有关使用 YARN Timeline Server 的提示，请参阅[访问 Apache Hadoop YARN 应用程序日志](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

有时，一个或几个执行程序的速度比其他执行程序要慢，执行任务时花费的时间也长得多。 这种缓慢通常发生在较大的群集（超过 30 个节点）上。 在这种情况下，应将工作划分成更多任务，以便计划程序可以补偿速度较慢的任务。 例如，任务数量应至少为应用程序中执行程序内核数的两倍。 也可以使用 `conf: spark.speculation = true` 对任务启用推理执行。

## <a name="next-steps"></a>后续步骤

* [优化 Apache Spark 的数据处理](optimize-cluster-configuration.md)
* [优化 Apache Spark 的数据存储](optimize-data-storage.md)
* [优化 Apache Spark 的内存使用率](optimize-memory-usage.md)
