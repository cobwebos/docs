---
title: 优化 Spark 作业的性能 - Azure HDInsight
description: 在 Azure HDInsight 中显示通用策略，以获得最佳性能的 Apache Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/17/2020
ms.openlocfilehash: 5012b5abf12beadbcb18f21fe2fe6ebfb076598a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617966"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>在 HDInsight 中优化 Apache Spark 作业

了解如何为特定工作负荷优化 Apache Spark 群集配置。  最常见的挑战是内存压力，因为配置不正确（如大小错误的执行器）。 此外，长时间运行的操作，以及导致笛卡尔操作的任务。 可通过以下方式为作业提速：使用适当的缓存，并允许[数据倾斜](#optimize-joins-and-shuffles)。 为了获得最佳性能，监视和查看长时间运行且耗费资源的 Spark 作业执行。 有关在 HDInsight 上开始使用 Apache Spark 的信息，请参阅[使用 Azure 门户创建 Apache Spark 群集](apache-spark-jupyter-spark-sql-use-portal.md)。

以下部分介绍常用的 Spark 作业优化方法和建议。

## <a name="choose-the-data-abstraction"></a>选择数据抽象

早期的 Spark 版本使用 RDD 来抽象数据，Spark 1.3 和 1.6 分别引入了 DataFrame 和数据集集。 请仔细衡量下列优缺点：

* **数据帧**
    * 大多数情况下的最佳选择。
    * 通过 Catalyst 提供查询优化。
    * 全阶段代码生成。
    * 直接内存访问。
    * 垃圾回收 (GC) 开销低。
    * 不像数据集那样易于开发者使用，因为没有编译时检查或域对象编程。
* **数据**
    * 适合可容忍性能受影响的复杂 ETL 管道。
    * 不适合需要考虑性能受影响的聚合。
    * 通过 Catalyst 提供查询优化。
    * 提供域对象编程和编译时检查，适合开发。
    * 增加序列化/反序列化开销。
    * GC 开销高。
    * 中断全阶段代码生成。
* **RDD**
    * 除非需要构建新的自定义 RDD，否则无需使用 RDD。
    * 不能通过 Catalyst 提供查询优化。
    * 不提供全阶段代码生成。
    * GC 开销高。
    * 必须使用 Spark 1.x 旧版 API。

## <a name="use-optimal-data-format"></a>使用最佳数据格式

Spark 支持多种格式，比如 csv、json、xml、parquet、orc 和 avro。 Spark 可以借助外部数据源进行扩展，以支持更多格式 — 有关详细信息，请参阅 [Apache Spark 包](https://spark-packages.org)。

最能提高性能的格式是采用 *Snappy 压缩*的 Parquet，这是 Spark 2.x 中的默认格式。 Parquet 以分列格式存储数据，并在 Spark 中得到了高度优化。

## <a name="select-default-storage"></a>选择默认存储

创建新的 Spark 群集时，可以选择将 Azure Blob 存储或 Azure Data Lake Storage 用作群集的默认存储。 这两个选项都使您能够为瞬态群集提供长期存储。 因此，当您删除群集时，您的数据不会自动删除。 用户可以重新创建暂时性群集，并且依然能访问数据。

| 存储类型 | 文件系统 | Speed | 暂时性 | 用例 |
| --- | --- | --- | --- | --- |
| Azure Blob 存储 | **wasb:**//url/ | **标准** | 是 | 暂时性群集 |
| Azure Blob 存储（安全） | **参数：**//url/ | **标准** | 是 | 暂时性群集 |
| Azure Data Lake Storage Gen 2| **abfs：**//url/ | **较快** | 是 | 暂时性群集 |
| Azure 数据存储第 1 代| **adl:**//url/ | **较快** | 是 | 暂时性群集 |
| 本地 HDFS | **hdfs:**//url/ | **最快** | 否 | 全天候交互型群集 |

有关存储选项的完整说明，请参阅[比较存储选项以与 Azure HDInsight 群集一起使用](../hdinsight-hadoop-compare-storage-options.md)。

## <a name="use-the-cache"></a>使用缓存

Spark 提供自己的本机缓存机制，可通过各种方法（比如 `.persist()`、`.cache()` 和 `CACHE TABLE`）使用。 此本机缓存对于小型数据集和 ETL 管道中有效，您需要缓存中间结果。 但是，Spark 本机缓存目前不可用于分区，因为缓存表不保留分区数据。 *存储层缓存*是一种更通用且更可靠的缓存技术。

* 本机 Spark 缓存（不推荐）
    * 适用于小型数据集。
    * 不与分区配合使用，这可能会在将来的 Spark 版本中更改。

* 存储级缓存（推荐）
    * 可以使用[IO 缓存](apache-spark-improve-performance-iocache.md)功能在 HDInsight 上实现。
    * 使用内存中和 SSD 缓存。

* 本地 HDFS（推荐）
    * `hdfs://mycluster` 路径。
    * 使用 SSD 缓存。
    * 删除群集时，缓存数据将丢失，需要重新生成缓存。

## <a name="use-memory-efficiently"></a>有效利用内存

Spark 通过将数据放在内存中来运行。 因此，管理内存资源是优化 Spark 作业执行的一个关键方面。  可通过以下几种方法来有效地利用群集内存。

* 为分区策略中的数据大小、类型和分布优先选择较小的数据分区和帐户。
* 考虑更新、更高效[`Kryo data serialization`](https://github.com/EsotericSoftware/kryo)，而不是默认的 Java 序列化。
* 首选 YARN，因为它分批进行 `spark-submit`。
* 监视和优化 Spark 配置设置。

下图展示了 Spark 内存结构和一些键执行程序内存参数供用户参考。

### <a name="spark-memory-considerations"></a>Spark 内存注意事项

如果使用 Apache Hadoop YARN，则 YARN 控制每个 Spark 节点上所有容器使用的内存。  下图展示了一些键对象及其关系。

![YARN Spark 内存管理](./media/apache-spark-perf/apache-yarn-spark-memory.png)

若要解决显示“内存不足”消息的问题，请尝试：

* 查看 DAG 管理数据重组。 通过映射端化简减少内存使用，对源数据进行预分区（或 Bucket 存储化），最大化单个数据重组，以及减少发送的数据量。
* 首选具有固定内存限制的 `ReduceByKey`，而不是 `GroupByKey`，后者提供聚合、窗口化和其他功能，但具有无限内存限制。
* 首选在执行程序或分区上执行更多工作的 `TreeReduce`，而不是在驱动程序上执行所有工作的 `Reduce`。
* 使用数据帧而不是较低级别的 RDD 对象。
* 创建用于封装操作（比如“Top N”、各种聚合或窗口化操作）的 ComplexType。

有关其他故障排除步骤，请参阅[Azure HDInsight 中 Apache Spark 的内存错误异常](apache-spark-troubleshoot-outofmemory.md)。

## <a name="optimize-data-serialization"></a>优化数据序列化

Spark 作业是分布式作业，因此，适当的数据序列化对实现最佳性能很重要。  Spark 有两个序列化选项：

* Java 序列化是默认选项。
* `Kryo`序列化是一种较新的格式，可以比 Java 更快、更紧凑的序列化。  `Kryo`要求您在程序中注册类，并且它尚未支持所有可序列化类型。

## <a name="use-bucketing"></a>使用 Bucket 存储

存储桶类似于数据分区。 但是，每个存储桶可以保存一组列值，而不仅仅是一个列值。 此方法非常适合对值（如数百万或更多）的值（如产品标识符）进行分区。 通过哈希行的 Bucket 键可以确定 Bucket。 由 Bucket 存储的表可提供独一无二的优化，因为它们存储了有关其 Bucket 存储方式和排序方式的元数据。

下面是一些高级 Bucket 存储功能：

* 基于 Bucket 存储元信息的查询优化。
* 优化的聚合。
* 优化的联接。

可以同时使用分区和 Bucket 存储。

## <a name="optimize-joins-and-shuffles"></a>优化联接和数据重组

如果在联接或随机播放上作业速度较慢，则原因可能是*数据偏斜*。 数据偏斜是作业数据中的不对称。 例如，地图作业可能需要 20 秒。 但是，运行数据联接或随机排列的作业需要数小时。 若要解决数据倾斜问题，应对整个键进行加盐加密，或对仅仅一部分键使用*独立的加密盐*。 如果使用隔离盐，则应进一步筛选以隔离地图联接中的盐键子集。 另一种做法是引入 Bucket 列，先在 Bucket 中进行预聚合。

导致联接变慢的另一个因素可能是联接类型。 默认情况下，Spark 使用 `SortMerge` 联接类型。 这种类型的联接最适合大型数据集。 但是，在计算上成本很高，因为它必须先对数据的左右两侧进行排序，然后才能合并它们。

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

如果使用存储桶表，则有第三个联接类型，即`Merge`联接。 已进行正确预分区和预排序的数据集将跳过 `SortMerge` 联接中成本高昂的排序阶段。

联接的顺序至关重要，尤其是在较为复杂的查询中。 应先从最严格的联接开始。 此外，尽可能移动在聚合后增加行数的联接。

若要管理笛卡尔联接的并行度，可以添加嵌套结构，进行窗口化，以及在可能的情况下跳过 Spark 作业中的一个或多个步骤。

## <a name="customize-cluster-configuration"></a>自定义群集配置

根据您的 Spark 群集工作负载，您可以确定非默认 Spark 配置将导致更优化的 Spark 作业执行。  使用示例工作负载进行基准测试，以验证任何非默认群集配置。

下面是一些可调整的常见参数：

|参数 |说明 |
|---|---|
|--执行者|设置适当的执行器数量。|
|--执行者核心|设置每个执行器的内核数。 通常应使用中等大小的执行程序，因为其他进程会占用部分可用内存。|
|--执行器内存|设置每个执行器的内存大小，该执行器控制 YARN 上的堆大小。 留一些内存以进行执行开销。|

### <a name="select-the-correct-executor-size"></a>选择正确的执行程序大小

在决定执行程序配置时，请考虑 Java 垃圾回收 (GC) 开销。

* 通过以下方式减小执行程序大小：
    1. 将堆大小减至 32 GB 以下，使 GC 开销 < 10%。
    2. 减少内核数，使 GC 开销 < 10%。

* 通过以下方式增加执行程序大小：
    1. 减少执行程序之间的通信开销。
    2. 在较大的群集（超过 100 个执行程序）上减少执行程序 (N2) 之间已打开的连接数。
    3. 增加堆大小，以容纳占用大量内存的任务。
    4. 可选：减少每个执行程序的内存开销。
    5. 可选：通过过度订阅 CPU 来增加使用率和并发性。

通常，在选择执行器大小时：

1. 最开始，每个执行程序 30 GB，并分发可用的计算机内核。
2. 对于较大的群集（超过 100 个执行程序），增加执行程序内核数。
3. 基于试运行和上述因素（比如 GC 开销）修改大小。

运行并发查询时，请考虑：

1. 最开始，每个执行程序 30 GB，并分发所有计算机内核。
2. 通过超额订阅 CPU，创建多个并行 Spark 应用程序（延迟缩短大约 30%）。
3. 跨并行应用程序分布查询。
4. 基于试运行和上述因素（比如 GC 开销）修改大小。

有关使用 Ambari 配置执行器的详细信息，请参阅[Apache Spark 设置 - Spark 执行器](apache-spark-settings.md#configuring-spark-executors)。

通过查看时间线视图，监视异常值或其他性能问题的查询性能。 还有 SQL 图形、作业统计信息等。 有关使用 YARN 和 Spark 历史记录服务器调试 Spark 作业的信息，请参阅[在 Azure HDInsight 上运行的调试 Apache Spark 作业](apache-spark-job-debugging.md)。 有关使用 YARN 时间线服务器的提示，请参阅[访问 Apache Hadoop YARN 应用程序日志](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

有时，一个或几个执行程序的速度比其他执行程序要慢，执行任务时花费的时间也长得多。 这种缓慢经常发生在较大的群集（> 30 个节点）。 在这种情况下，应将工作划分成更多任务，以便计划程序可以补偿速度较慢的任务。 例如，任务数量应至少为应用程序中执行程序内核数的两倍。 也可以使用 `conf: spark.speculation = true` 对任务启用推理执行。

## <a name="optimize-job-execution"></a>优化作业执行

* 根据需要进行缓存，例如，如果数据要使用两次，则缓存它。
* 将变量广播到所有执行程序。 只对变量执行一次序列化，以便加快查找速度。
* 使用驱动程序上的线程池，这会加快许多任务的操作速度。

定期监视正在运行的作业，看是否有性能问题。 如果需要更深入地了解某些问题，请考虑使用以下性能分析工具之一：

* [英特尔 PAL 工具](https://github.com/intel-hadoop/PAT)监控 CPU、存储和网络带宽使用情况。
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) 分析 Spark 和执行程序代码。

Spark 2.x 查询性能的关键在于 Tungsten 引擎，这取决于全程代码生成。 在某些情况下，可能会禁用全程代码生成。 例如，如果在聚合表达式中使用非可变类型 (`string`)，则会显示 `SortAggregate`，而不是 `HashAggregate`。 例如，为了提高性能，可尝试运行以下命令，然后重新启用代码生成：

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>后续步骤

* [调试 Azure HDInsight 中运行的 Apache Spark 作业](apache-spark-job-debugging.md)
* [管理 HDInsight 上 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [配置 Apache Spark 设置](apache-spark-settings.md)
* [优化 Apache Spark](https://spark.apache.org/docs/latest/tuning.html)
* [How to Actually Tune Your Apache Spark Jobs So They Work](https://www.slideshare.net/ilganeli/how-to-actually-tune-your-spark-jobs-so-they-work)（如何真正优化 Apache Spark 作业以使其正常运行）
* [`Kryo Serialization`](https://github.com/EsotericSoftware/kryo)
