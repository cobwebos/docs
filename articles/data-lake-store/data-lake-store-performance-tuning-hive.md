---
title: 性能优化-Azure Data Lake Storage Gen1 上的 Hive
description: 适用于 Hive on HdInsight 和 Azure Data Lake Storage Gen1 的性能优化指南。
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 2e44332ddab9387c05a45d15101ccd2bdec3ada4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690521"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Hive on HDInsight 和 Azure Data Lake Storage Gen1 性能优化指南

已设置默认设置，以便针对许多不同用例提供良好性能。  对于 I/O 密集型查询，可以优化 Hive 以获取更好的 Azure Data Lake Storage Gen1 性能。  

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen1 帐户**。 有关如何创建一个的说明，请参阅[Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集****。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。
* **在 HDInsight 上运行 Hive**。  若要了解有关在 HDInsight 上运行 Hive 作业的信息，请参阅[在 HDInsight 上使用 Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Data Lake Storage Gen1 的性能优化指南**。  有关一般的性能概念，请参阅[Data Lake Storage Gen1 性能优化指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>参数

下面是为提高 Data Lake Storage Gen1 性能要优化的最重要设置：

* **hive.tez.container.size** - 每个任务使用的内存量

* **tez.grouping.min-size** - 每个映射器的最小大小

* **tez.grouping.max-size** - 每个映射器的最大大小

* **hive.exec.reducer.bytes.per.reducer** - 每个化简器的大小

**hive.tez.container.size** - 容器大小确定可供每个任务使用的内存量。  这是用于控制 Hive 中的并发性的主要输入。  

**tez.grouping.min-size** - 使用此参数可以设置每个映射器的最小大小。  如果 Tez 选择的映射器数小于此参数的值，则 Tez 将使用此处设置的值。

**tez.grouping.max-size** - 使用此参数可以设置每个映射器的最大大小。  如果 Tez 选择的映射器数大于此参数的值，则 Tez 将使用此处设置的值。

**hive.exec.reducer.bytes.per.reducer** - 此参数设置每个化简器的大小。  默认情况下，每个化简器为 256 MB。  

## <a name="guidance"></a>指南

**设置 hive.exec.reducer.bytes.per.reducer** - 默认值适用于数据未压缩时。  对于已压缩的数据，应减小化简器。  

**设置 hive.tez.container.size** - 在每个节点中，内存由 yarn.nodemanager.resource.memory-mb 指定，并且默认情况下应在 HDI 群集上正确设置。  有关在 YARN 中设置相应内存的其他信息，请参阅此[文章](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)。

通过减小 Tez 容器可增加并行度，I/O 密集型工作负荷可以从中受益。 这样可为用户提供更多容器，从而提高并发性。  但是，某些 Hive 查询（例如 MapJoin）需要占用大量内存。  如果任务没有足够的内存，则在运行时会出现“内存不足”异常。  如果收到“内存不足”异常，则应增加内存。   

正在运行的并发任务数或平行度将受到总 YARN 内存量的限制。  YARN 容器数将决定可运行多少个并发任务。  若要查找每个节点的 YARN 内存量，可以转到 Ambari。  导航到 YARN 并查看 "配置" 选项卡。 YARN 内存显示在此窗口中。  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
提高使用 Data Lake Storage Gen1 的性能的关键是尽可能多地增加并发性。  Tez 会自动计算应创建的任务数，因此无需设置它。   

## <a name="example-calculation"></a>示例计算

让我们假设你有一个由 8 个节点组成的 D14 群集。  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>限制

**Data Lake Storage Gen1 限制** 

如果达到 Data Lake Storage Gen1 提供的带宽限制，将开始出现任务失败。 这可以通过观察任务日志中的限制错误来确定。  可以通过增加 Tez 容器大小来减少并行度。  如果作业需要更多并发性，请与我们联系。

若要查看是否受到限制，需要在客户端上启用调试日志记录。 下面介绍执行该操作的方法：

1. 将以下属性放入 Hive config 中的 log4j 属性。这可以从 Ambari view： log4j 中完成。 datalake = DEBUG 重新启动所有节点/服务，以使配置生效。

2. 如果受到限制，会在 Hive 日志文件中看到 HTTP 429 错误代码。 Hive 日志文件位于 /tmp/&lt;用户&gt;/hive.log 中

## <a name="further-information-on-hive-tuning"></a>有关 Hive 优化的详细信息

下面是将帮助优化 Hive 查询的几个博客：
* [在 Hdinsight 中优化 Hadoop 的 Hive 查询](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Hive 查询性能故障排除](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [有关优化 Hive on HDInsight 的 Ignite 讨论](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
