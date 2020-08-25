---
title: 优化 Apache Spark 的数据存储 - Azure HDInsight
description: 了解如何优化数据存储，以用于 Azure HDInsight 上的 Apache Spark。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperfq1
ms.openlocfilehash: 092757728e791f60616d9dceca43e109e7f0019e
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757806"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Apache Spark 的数据存储优化

本文介绍了用于优化数据存储以在 Azure HDInsight 上高效执行 Apache Spark 作业的策略。

## <a name="overview"></a>概述

Spark 支持多种格式，比如 csv、json、xml、parquet、orc 和 avro。 Spark 可以借助外部数据源进行扩展，以支持更多格式 — 有关详细信息，请参阅 [Apache Spark 包](https://spark-packages.org)。

最能提高性能的格式是采用 *Snappy 压缩*的 Parquet，这是 Spark 2.x 中的默认格式。 Parquet 以分列格式存储数据，并在 Spark 中得到了高度优化。

## <a name="choose-data-abstraction"></a>选择数据抽象

早期的 Spark 版本使用 RDD 提取数据，Spark 1.3 和 1.6 分别引入了 DataFrame 和 DataSet。 请仔细衡量下列优缺点：

* **DataFrame**
    * 大多数情况下的最佳选择。
    * 通过 Catalyst 提供查询优化。
    * 全阶段代码生成。
    * 直接内存访问。
    * 垃圾回收 (GC) 开销低。
    * 不像数据集那样易于开发者使用，因为没有编译时检查或域对象编程。
* **DataSet**
    * 适合可容忍性能受影响的复杂 ETL 管道。
    * 不适合需要考虑性能受影响的聚合。
    * 通过 Catalyst 提供查询优化。
    * 提供域对象编程和编译时检查，适合开发。
    * 增加序列化/反序列化开销。
    * GC 开销高。
    * 中断全阶段代码生成。
* **RDD**
    * 不必使用 RDD，除非需要生成新的自定义 RDD。
    * 不能通过 Catalyst 提供查询优化。
    * 不提供全阶段代码生成。
    * GC 开销高。
    * 必须使用 Spark 1.x 旧版 API。

## <a name="select-default-storage"></a>选择默认存储

创建新的 Spark 群集时，可以将 Azure Blob 存储或 Azure Data Lake Storage 用作群集的默认存储。 这两个选项都具有可长期存储暂时性群集的优势。 这样，在删除群集时，就不会自动删除数据。 用户可以重新创建暂时性群集，并且依然能访问数据。

| 存储类型 | 文件系统 | Speed | 暂时性 | 用例 |
| --- | --- | --- | --- | --- |
| Azure Blob 存储 | **wasb:** //url/ | **标准** | 是 | 暂时性群集 |
| Azure Blob 存储（安全） | **wasbs:** //url/ | **标准** | 是 | 暂时性群集 |
| Azure Data Lake Storage Gen 2| **abfs:** //url/ | **较快** | 是 | 暂时性群集 |
| Azure Data Lake Storage Gen 1| **adl:** //url/ | **较快** | 是 | 暂时性群集 |
| 本地 HDFS | **hdfs:** //url/ | **最快** | 否 | 全天候交互型群集 |

有关存储选项的完整说明，请参阅[比较用于 Azure HDInsight 群集的存储选项](../hdinsight-hadoop-compare-storage-options.md)。

## <a name="use-the-cache"></a>使用缓存

Spark 提供自己的本机缓存机制，可通过各种方法（比如 `.persist()`、`.cache()` 和 `CACHE TABLE`）使用。 这种本机缓存适用于小型数据集以及需要缓存中间结果的 ETL 管道。 但是，Spark 本机缓存目前并非很适用于分区，因为缓存表不保留分区数据。 *存储层缓存*是一种更通用且更可靠的缓存技术。

* 本机 Spark 缓存（不推荐）
    * 适用于小型数据集。
    * 不可用于分区，但将来的 Spark 版本可能会实现这一点。

* 存储级缓存（推荐）
    * 可以在 HDInsight 上使用 [IO 缓存](apache-spark-improve-performance-iocache.md)功能实现。
    * 使用内存中和 SSD 缓存。

* 本地 HDFS（推荐）
    * `hdfs://mycluster` 路径。
    * 使用 SSD 缓存。
    * 删除群集时，缓存数据将丢失，需要重新生成缓存。

## <a name="optimize-data-serialization"></a>优化数据序列化

Spark 作业是分布式作业，因此，适当的数据序列化对实现最佳性能很重要。  Spark 有两个序列化选项：

* Java 序列化是默认选项。
* `Kryo` 序列化是一种较新的格式，可带来比 Java 更快、更紧凑的序列化。  `Kryo` 要求在程序中注册类，并且尚不支持所有的可序列化类型。

## <a name="use-bucketing"></a>使用 Bucket 存储

Bucket 存储类似于数据分区。 但每个 Bucket 都可以保存一组列值，而不只是一个列值。 此方法非常适合对大量（数以百万计或更多）值（比如产品标识符）分区。 通过哈希行的 Bucket 键可以确定 Bucket。 由 Bucket 存储的表可提供独一无二的优化，因为它们存储了有关其 Bucket 存储方式和排序方式的元数据。

下面是一些高级 Bucket 存储功能：

* 基于 Bucket 存储元信息的查询优化。
* 优化的聚合。
* 优化的联接。

可以同时使用分区和 Bucket 存储。

## <a name="next-steps"></a>后续步骤

* [优化 Apache Spark 的数据处理](optimize-cluster-configuration.md)
* [优化 Apache Spark 的内存使用率](optimize-memory-usage.md)
* [优化 Apache Spark 的群集配置](optimize-cluster-configuration.md)
