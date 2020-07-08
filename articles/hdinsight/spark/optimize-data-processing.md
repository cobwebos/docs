---
title: 优化 Apache Spark 的数据处理 - Azure HDInsight
description: 了解如何选择通过 Azure HDInsight 在 Apache Spark 上处理数据的最有效操作。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 021999e1757993eea4bbfe3aec0bd68049a37e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737659"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Apache Spark 的数据处理优化

本文介绍如何在 Azure HDInsight 上优化 Apache Spark 群集的配置，以获得最佳性能。

## <a name="overview"></a>概述

如果某个联接和数据重组操作上有速度较慢的作业，可能是由数据倾斜引起的。 数据倾斜是指作业数据不对称。 例如，运行映射作业可能需要 20 秒。 但运行对数据进行联接或重组的作业则需数小时。 若要解决数据倾斜问题，应对整个键进行加盐加密，或对仅仅一部分键使用*独立的加密盐*。 如果使用独立的加密盐，应进一步进行筛选，将映射联接中已进行加盐加密的键的子集隔离出来。 另一种做法是引入 Bucket 列，先在 Bucket 中进行预聚合。

导致联接变慢的另一个因素可能是联接类型。 默认情况下，Spark 使用 `SortMerge` 联接类型。 这种类型的联接最适合大型数据集。 但另一方面又会占用大量计算资源，因为它必须先对数据的左右两侧进行排序，然后才进行合并。

`Broadcast` 联接最适合小型数据集，或者联接的一侧比另一侧小得多的情况。 这种联接会将一侧数据广播到所有执行程序，因此通常需要为广播提供更多内存。

可以通过设置 `spark.sql.autoBroadcastJoinThreshold` 来更改配置中的联接类型，也可以使用 DataFrame API (`dataframe.join(broadcast(df2))`) 来设置联接提示。

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

如果使用由 Bucket 存储的表，则有第三种联接类型，即 `Merge` 联接。 已进行正确预分区和预排序的数据集将跳过 `SortMerge` 联接中成本高昂的排序阶段。

联接的顺序至关重要，尤其是在较为复杂的查询中。 应先从最严格的联接开始。 此外，尽可能移动在聚合后增加行数的联接。

若要管理笛卡尔联接的并行度，可以添加嵌套结构，进行窗口化，以及在可能的情况下跳过 Spark 作业中的一个或多个步骤。

## <a name="optimize-job-execution"></a>优化作业执行

* 根据需要进行缓存，例如，如果数据要使用两次，则缓存它。
* 将变量广播到所有执行程序。 只对变量执行一次序列化，以便加快查找速度。
* 使用驱动程序上的线程池，这会加快许多任务的操作速度。

定期监视正在运行的作业，看是否有性能问题。 如果需要更深入地了解某些问题，请考虑使用以下性能分析工具之一：

* [Intel PAL 工具](https://github.com/intel-hadoop/PAT)监视 CPU、存储和网络带宽使用情况。
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) 分析 Spark 和执行程序代码。

Spark 2.x 查询性能的关键在于 Tungsten 引擎，这取决于全程代码生成。 在某些情况下，可能会禁用全程代码生成。 例如，如果在聚合表达式中使用非可变类型 (`string`)，则会显示 `SortAggregate`，而不是 `HashAggregate`。 例如，为了提高性能，可尝试运行以下命令，然后重新启用代码生成：

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>后续步骤

* [优化 Apache Spark 的数据存储](optimize-data-storage.md)
* [优化 Apache Spark 的内存使用率](optimize-memory-usage.md)
* [优化 Apache Spark 的群集配置](optimize-cluster-configuration.md)
