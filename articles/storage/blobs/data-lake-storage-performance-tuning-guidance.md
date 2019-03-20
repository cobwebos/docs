---
title: Azure Data Lake Storage Gen2 性能优化指南 | Microsoft Docs
description: Azure Data Lake Storage Gen2 性能优化指南
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: f175360586428b57d1ff10e3529ae9e3283399e8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117042"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>优化 Azure Data Lake Storage Gen2 性能

Azure Data Lake Storage Gen2 支持使用高吞吐量进行 I/O 密集型分析和数据移动。  在 Data Lake Storage Gen2 中，使用所有可用的吞吐量（每秒可读取或写入的数据量）对于获取最佳性能非常重要。  可通过尽可能多地执行并行读取和写入来实现这一点。

![Data Lake Storage Gen2 性能](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 可进行缩放，以便为所有分析方案提供必要的吞吐量。 默认情况下，Data Lake Storage Gen2 帐户自动提供足够的吞吐量来满足广泛用例的需求。 对于客户达到默认限制的情况，可联系 [Azure 支持部门](https://azure.microsoft.com/support/faq/)配置 Data Lake Storage Gen2 帐户，以获得更多吞吐量。

## <a name="data-ingestion"></a>数据引入

将数据从源系统引入 Data Lake Storage Gen2 时，源硬件、源网络硬件以及与 Data Lake Storage Gen2 的网络连接可能成为瓶颈，请务必考虑到这一点。  

![Data Lake Storage Gen2 性能](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

确保数据移动不受这些因素的影响至关重要。

### <a name="source-hardware"></a>源硬件

无论使用本地计算机还是 Azure 中的 VM，都应仔细选择合适的硬件。 对于源磁盘硬件，最好使用 SSD（而不是 HDD）并选取主轴转速更快的磁盘硬件。 对于源网络硬件，请尽可能使用最快的 NIC。  在 Azure 上，建议使用 Azure D14 VM，该虚拟机具有相当强大的磁盘和网络硬件。

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>到 Data Lake Storage Gen2 的网络连接

源数据和 Data Lake Storage Gen2 之间的网络连接有时可能成为瓶颈。 如果源数据位于本地，请考虑使用 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 的专用链接。 如果源数据在 Azure 中，当数据与 Data Lake Storage Gen2 帐户位于同一 Azure 区域时，可获得最佳性能。

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>配置数据引入工具，实现最大并行化

解决上述源硬件和网络连接瓶颈后，即可配置引入工具。 下表概述了几种常用引入工具的关键设置，并提供了关于这些工具的详尽性能优化文章。  若要深入了解方案应使用何种工具，请参阅这篇[文章](data-lake-storage-data-scenarios.md)。

| 工具               | 设置     | 更多详细信息                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [链接](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure 数据工厂| parallelCopies    | [链接](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [链接](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>调整数据集结构

数据存储在 Data Lake Storage Gen2 中时，文件大小、文件数和文件夹结构会对性能产生影响。  以下部分介绍了这些方面的最佳做法。  

### <a name="file-size"></a>文件大小

通常，HDInsight 和 Azure Data Lake Analytics 等分析引擎的每个文件都存在开销。 如果将数据存储为多个小文件，这可能会对性能产生负面影响。 通常可将数据组织到较大文件中，以获得更佳性能（大小为 256MB 到 100GB）。 某些引擎和应用程序可能会在高效处理大于 100GB 的文件方面遇到问题。

有时，数据管道对原始数据（含有多个小文件）的控制有限。 建议执行“烹调”过程来生成更大的文件，以供下游应用程序使用。

### <a name="organizing-time-series-data-in-folders"></a>将时序数据组织到文件夹中

对于 Hive 工作负荷，分区修剪时序数据可帮助某些查询只读取小部分数据，从而提高性能。    

引入时序数据的这些管道通常以非常结构化的方式对其文件和文件夹命名。 下面是一个极常见的示例，其中数据按日期进行了结构化：

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

请注意，日期/时间信息同时显示为文件夹和文件名。

下方是日期和时间的一种常见模式

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

同样，选择的文件夹和文件组织方式应针对更大的文件大小和每个文件夹中合理的文件数进行优化。

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>在 HDInsight 上优化 Hadoop 和 Spark 工作负荷的 I/O 密集型作业

作业属于以下三个类别之一：

* CPU 密集型。  这些作业的计算时间长，I/O 时间最短。  例如，机器学习作业和自然语言处理作业。  
* 内存密集型。  这些作业占用大量内存。  例如，PageRank 作业和实时分析作业。  
* I/O 密集型。  这些作业大部分时间都在执行 I/O。  常见示例为，仅执行读取和写入操作的复制作业。  其他示例包括：读取大量数据、执行某些数据转换，然后将数据写回存储区的数据准备作业。  

以下指南仅适用于 I/O 密集型作业。

## <a name="general-considerations"></a>一般注意事项

可以具有在单个操作中读取或写入高达 100MB 的作业，但是该大小的缓冲区可能会损害性能。
若要优化性能，请尝试使 I/O 操作的大小保持在 4MB 到 16MB 之间。

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight 群集的一般注意事项

* HDInsight 版本。 为获得最佳性能，请使用最新版 HDInsight。
* 区域。 将 Data Lake Storage Gen2 帐户和 HDInsight 群集放置在同一区域。  

HDInsight 群集由两个头节点和一些辅助角色节点组成。 每个辅助角色节点提供特定数量的核心和内存，具体取决于 VM 类型。  运行作业时，YARN 充当资源协商者，负责分配可用的内存和核心以创建容器。  每个容器运行完成作业所需的任务。  容器可并行运行以快速处理任务。 因此，通过并行运行尽可能多的容器可以提高性能。

可优化 HDInsight 群集中的以下 3 层，以增加容器数和使用所有可用的吞吐量。  

* 物理层
* YARN 层
* 工作负荷层

### <a name="physical-layer"></a>物理层

运行具有更多节点和/或更大 VM 的群集。  更大的群集可运行更多 YARN 容器，如下图所示。

![Data Lake Storage Gen2 性能](./media/data-lake-storage-performance-tuning-guidance/VM.png)

使用具有更多网络带宽的 VM。  如果网络带宽低于 Data Lake Storage Gen2 吞吐量，则网络带宽量可能成为瓶颈。  网络带宽大小因不同 VM 而异。  请选择具有可能的最大网络带宽的 VM 类型。

### <a name="yarn-layer"></a>YARN 层

使用较小的 YARN 容器。  缩减每个 YARN 容器的大小，创建更多包含相同数量资源的容器。

![Data Lake Storage Gen2 性能](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

始终需要最小的 YARN 容器，具体取决于工作负荷。 如果选取的容器太小，作业会出现内存不足的问题。 YARN 容器通常不应小于 1 GB。 YARN 容器一般为 3 GB。 对于某些工作负荷，可能需要更大的 YARN 容器。  

增加每个 YARN 容器的核心数。  增加分配给每个容器的核心数，以提高每个容器中运行的并行任务数。  这适用于每个容器运行多个任务的应用程序，例如 Spark。  对于在每个容器中运行单个线程的应用程序（如 Hive），最好分配多个容器，而不是为每个容器分配多个核心。

### <a name="workload-layer"></a>工作负荷层

使用所有可用的容器。  将任务数设置为等于或大于可用容器数，以便利用所有资源。

![Data Lake Storage Gen2 性能](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

失败的任务成本高昂。 如果每项任务都有大量数据需要处理，那么任务失败就会导致以高成本重试任务。  因此，最好创建多个任务，每个任务处理少量数据。

除上述常规准则外，每个应用程序都有不同的参数，可用于优化该特定应用程序。 下表列出了一些参数和链接，有助于开始对每个应用程序执行性能优化。

| 工作负荷 | 用于设置任务数的参数 |
|----------|------------------------|
| [Spark on HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [Hive on HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce on HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm on HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>工作进程数</li><li>Spout 执行器实例数</li><li>Bolt 执行器实例数 </li><li>Spout 任务数</li><li>Bolt 任务数</li></ul>|

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Storage Gen2 概述](data-lake-storage-introduction.md)
