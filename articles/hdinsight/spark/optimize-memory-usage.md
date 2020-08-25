---
title: 优化 Apache Spark - Azure HDInsight 中的内存使用情况
description: 了解如何在 Azure HDInsight 上优化 Apache Spark 中的内存使用情况。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 056060f8b94747651c78c757150d5e5a5982c7af
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757772"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Apache Spark 的内存使用情况优化

本文介绍如何优化 Apache Spark 群集的内存管理，以在 Azure HDInsight 上获得最佳性能。

## <a name="overview"></a>概述

Spark 在运行时会将数据放在内存中。 因此，管理内存资源是优化 Spark 作业执行的一个重要方面。  可通过以下几种方法来有效地利用群集内存。

* 为分区策略中的数据大小、类型和分布优先选择较小的数据分区和帐户。
* 考虑使用更新、更有效的 [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo)，而不是使用默认的 Java 序列化。
* 首选 YARN，因为它分批进行 `spark-submit`。
* 监视和优化 Spark 配置设置。

下图展示了 Spark 内存结构和一些键执行程序内存参数供用户参考。

## <a name="spark-memory-considerations"></a>Spark 内存注意事项

如果使用的是 Apache Hadoop YARN，则 YARN 会控制每个 Spark 节点上的所有容器使用的内存。  下图展示了一些键对象及其关系。

![YARN Spark 内存管理](./media/apache-spark-perf/apache-yarn-spark-memory.png)

若要解决显示“内存不足”消息的问题，请尝试：

* 查看 DAG 管理数据重组。 通过映射端化简减少内存使用，对源数据进行预分区（或 Bucket 存储化），最大化单个数据重组，以及减少发送的数据量。
* 首选具有固定内存限制的 `ReduceByKey`，而不是 `GroupByKey`，后者提供聚合、窗口化和其他功能，但具有无限内存限制。
* 首选在执行程序或分区上执行更多工作的 `TreeReduce`，而不是在驱动程序上执行所有工作的 `Reduce`。
* 使用 DataFrame，而不是级别较低的 RDD 对象。
* 创建用于封装操作（比如“Top N”、各种聚合或窗口化操作）的 ComplexType。

有关其他故障排除步骤，请参阅 [Azure HDInsight 中 Apache Spark 的 OutOfMemoryError 异常](apache-spark-troubleshoot-outofmemory.md)。

## <a name="next-steps"></a>后续步骤

* [优化 Apache Spark 的数据处理](optimize-cluster-configuration.md)
* [优化 Apache Spark 的数据存储](optimize-data-storage.md)
* [优化 Apache Spark 的群集配置](optimize-cluster-configuration.md)
