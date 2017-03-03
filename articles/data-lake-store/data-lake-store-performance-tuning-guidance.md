---
title: "Azure Data Lake Store 性能优化指南 | Microsoft Docs"
description: "Azure Data Lake Store 性能优化指南"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7
ms.lasthandoff: 01/18/2017


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Azure Data Lake Store 性能优化指南

本文提供有关在将数据写入高性能 Azure Data Lake Store 或从中读取数据时，如何获得最佳优化的指导。 本文旨在帮助用户了解可为常用数据上载/下载工具和数据分析工作负荷配置的参数。 本指南中的优化措施专门针对要将大量数据写入 Data Lake Store 或从中读取大量数据的资源密集型工作负荷。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Store 帐户访问权限的**Azure HDInsight 群集**。 请参阅[创建包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。


## <a name="guidelines-for-data-ingestion-tools"></a>数据引入工具指导原则

本部分提供有关在将数据复制到或移到 Data Lake Store 时如何提高性能的指导。 本部分讲解限制性能的因素，以及如何克服这些限制。 下面是要注意的几个事项。

* **源数据** - 源数据的来源位置可能存在多种约束。 如果源数据位于慢速机械硬盘中或者吞吐功能较低的远程存储中，则吞吐量可能成为瓶颈。 SSD（最好是本地磁盘）的磁盘吞吐量较高，可提供最佳性能。

* **网络** - 如果源数据在 VM 上，则 VM 与 Data Lake Store 之间的网络连接就很重要。 在 VM 中配备容量最大的 NIC 可以获得更大的网络带宽。

* **跨区域复制** - 跨区域数据 I/O 固有地产生较高的网络费用，例如，在美国东部 2 区的 VM 上运行数据引入工具，将数据写入美国中部的 Data Lake Store 帐户。 如果跨区域复制数据，性能可能会下降。 建议在与目标 Data Lake Store 帐户所在的同一区域中的 VM 上使用数据引入作业，以最大程度地提高网络吞吐量。                                                                                                                                        

* **群集** - 如果要通过 HDInsight 群集运行数据引入作业（例如，针对 DistCp），我们建议在群集中使用 D 系列 VM，因为它们包含更多的内存。 增加核心也有助于提高吞吐量。                                                                                                                                                                                                                                                                                                            

* **线程并发性** - 如果使用 HDInsight 群集从存储容器中复制数据，请注意，可使用的并发线程数会根据群集大小、容器大小和线程设置而受到限制。 提高 Data Lake Store 性能的最重要方法之一增大并发性。 应该优化设置来最大程度地提高并发度，从而获得更高的吞吐量。 下表列出了为实现更大并发性而可以配置的每种引入方法的设置。 单击表中的链接可转到相应的文章，其中介绍了如何使用工具将数据引入 Data Lake Store，以及如何优化工具的性能来获得最大吞吐量。

    | 工具               | 并发性设置                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [Powershell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount、ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Azure Data Lake Analytics 单元         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (mapper)                             |
    | [Azure 数据工厂](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size, -m (mapper)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>有关处理 HDInsight 工作负荷的指导原则

运行分析工作负荷处理 Data Lake Store 中的数据时，我们建议使用 HDInsight 3.5 群集版本来获得 Data Lake Store 的最佳性能。 如果作业消耗较多的 I/O 资源，可出于性能原因配置某些参数。 例如，如果作业主要包括读取或写入，则增大传入和传出 Azure Data Lake Store 的 I/O 的并发性可以提高性能。

如果并发性较高，Azure Data Lake Store 的性能会得到最大的优化。 可通过几种常规方法来提高 I/O 密集型作业的并发性。

1. **运行大量的计算 YARN 容器，而不要运行少量的大型 YARN 容器** – 增加容器可提高输入和输出操作的并发性，因而可以利用 Data Lake Store 的能力。

    例如，假设 HDInsight 群集中有 2 个 D3v2 节点。 每个 D3v2 节点包含 12GB 的 YARN 内存，因此，2 台 D3v2 计算机包含 24GB YARN 内存。 另外，假设已将 YARN 容器大小设置为 6GB。 这意味着，可以创建 4 个 6GB 大小的容器。 因此，可以并行运行 4 个并发任务。 若要提高并发性，可将容器大小减少到 3GB，这样，便可以创建 8 个 3GB 大小的容器。 因此，可以并行运行 8 个并发任务。 下面是示意图。

    ![Data Lake Store 性能](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    一个常见问题是为何不将容器大小减至 1GB 内存，这样就可以创建 24 个容器，进一步提高并发性。 这取决于任务是否需要 3GB 内存，1GB 是否足够。  在容器中执行的简单操作可能只需要 1GB 内存，而复杂的操作却需要 3GB 内存。  如果过度减小容器的大小，可能会发生内存不足异常。  如果发生这种情况，应增大容器的大小。  除了内存以外，虚拟核心数也会影响并行度。

    ![Data Lake Store 性能](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **提高群集中的内存，获得更大的并发性** – 可以通过增大群集大小或选择内存更大的 VM 类型，来提高群集中的内存。 这样可以增大 YARN 的可用内存量，从而可以创建多个容器，提高并发性。  

    例如，假设 HDInsight 群集包含单个 D3v2 节点，该节点具有 12GB 的 YARN 内存和 3GB 的容器。  可将群集扩展为 2 个 D3v2 节点，这样就会将 YARN 内存提高到 24GB，  将并发性从 4 提高到 8。

    ![Data Lake Store 性能](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **首先将任务数设置为现有的并发性数** – 现已适当地设置了容器大小来获得最大并发量。 接下来，应设置任务数，以使用所有这些容器。 每个工作负荷中的任务具有不同的名称。

    可能还需要考虑作业的大小。 如果作业较大，则每个任务可能要处理大量的数据。 可能需要使用更多的任务，以便每个任务不用处理过多的数据。

    例如，假设有 6 个容器。 我们一开始将任务数设置为 6。 可以尝试使用更多的任务，看看性能是否得到提升。 设置更多的任务不会增大并发性。 如果将任务数设置为大于 6，则在下一阶段之前，任务不会执行。 如果将任务数设置为小于 6，并发性不会充分利用。

    每个工作负荷提供不同的参数用于设置任务数。 下表列出了其中一些参数。

    | 工作负载               | 用于设置任务数的参数                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [Spark on HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [Hive on HDInsight](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [MapReduce on HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [Storm on HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>工作进程数</li><li>Spout 执行器实例数</li><li>Bolt 执行器实例数 </li><li>Spout 任务数</li><li>Bolt 任务数</li></ul>|

## <a name="see-also"></a>另请参阅
* [Overview of Azure Data Lake Store](data-lake-store-overview.md)
* [Get Started with Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

