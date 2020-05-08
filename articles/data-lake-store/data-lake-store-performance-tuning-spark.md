---
title: 性能优化-Spark 与 Azure Data Lake Storage Gen1
description: 了解 Azure HDInsight 上的 Spark 和 Azure Data Lake Storage Gen1 的性能优化指南。
author: stewu
ms.service: data-lake-store
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 665fd3bf29f0ec4d2196bd29be300ee909364e31
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691106"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Spark on HDInsight 和 Azure Data Lake Storage Gen1 性能优化指南

在优化 Spark 的性能时，需要考虑到群集中将要运行的应用数目。 默认情况下，你可以在 HDI 群集上并发运行四个应用（注意：默认设置可能会更改）。 可以使用更少的应用，这样便可以覆盖默认设置，将群集中的更多资源用于这些应用。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建一个的说明，请参阅[Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集****。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。
* **在 Data Lake Storage Gen1 中运行 Spark 群集**。 有关详细信息，请参阅[使用 HDInsight Spark 群集分析中的数据 Data Lake Storage Gen1](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Data Lake Storage Gen1 的性能优化指南**。 有关一般的性能概念，请参阅[Data Lake Storage Gen1 性能优化指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>参数

运行 Spark 作业时，可以优化下面这些最重要的设置，提高 Data Lake Storage Gen1 的性能：

* **执行器数目** - 可执行的并发任务数。

* **执行器内存** - 分配给每个执行器的内存量。

* **执行器核心数** - 分配给每个执行器的核心数。

**执行器数目**：执行器数目设置可并行运行的任务数上限。 可并行运行的实际任务数目受到群集中可用的内存和 CPU 资源的约束。

**执行器内存**：指分配给每个执行器的内存量。 每个执行器所需的内存量取决于作业。 复杂的操作所需的内存量较大。 简单的操作（如读取和写入）对内存的要求较低。 可在 Ambari 中查看每个执行器的内存量。 在 Ambari 中，导航到 Spark 并查看 **"配置" 选项卡**。

**执行器核心数**：设置每个执行器使用的核心数量，决定了每个执行器可以运行的并行线程数。 例如，如果执行器核心数 = 2，则每个执行器可以运行 2 个并行任务。 所需的执行器核心数取决于作业。 I/O 密集型作业在执行每个任务时不需要大量的内存，因此，每个执行器可以处理更多的并行任务。

默认情况下，在 HDInsight 上运行 Spark 时，将为每个物理核心定义两个虚拟 YARN 核心。 此数字能够在并发性与多个线程的上下文切换次数之间提供适当的平衡。

## <a name="guidance"></a>指南

运行 Spark 分析工作负载处理 Data Lake Storage Gen1 中的数据时，建议使用最新的 HDInsight 版本来获得 Data Lake Storage Gen1 的最佳性能。 如果作业消耗较多的 I/O 资源，可以配置某些参数来提高性能。 Data Lake Storage Gen1 是一种高度可缩放的存储平台，能够应对较高的吞吐量。 如果作业主要包括读取或写入，则增大传入和传出 Data Lake Storage Gen1 的 I/O 的并发性可以提高性能。

可通过几种常规方法来提高 I/O 密集型作业的并发性。

**步骤 1：确定群集中运行的应用数目** – 应该知道群集中运行了多少个应用，包括当前正在使用的应用。 每项 Spark 设置的默认值假设同时运行了 4 个应用。 因此，每个应用只能利用群集 25% 的资源。 若要提高性能，可以通过更改执行器数目来覆盖默认值。

**步骤 2：设置执行器内存** – 要设置的第一个参数是执行器内存。 内存取决于要运行的作业。 减少每个执行器的内存分配可以提高并发性。 如果在运行作业时看到内存不足异常，应增大此参数的值。 一种替代方法是使用具有更高内存量的群集或增大群集的大小来获得更多内存。 增加内存便可以使用更多的执行器，意味着并发性得到提高。

**步骤 3：设置执行器核心数** – 对于不执行复杂操作的 I/O 密集型工作负荷而言，一开始最好是指定一个较大数值的执行器核心数，以增加每个执行器的并行任务数。 将执行器核心数设置为 4 是个不错的起点。

    executor-cores = 4
增加执行器核心数可以提高并行度，这样可以体验不同执行器核心数带来的效果。 对于执行较复杂操作的作业，应减少每个执行器的核心数。 如果执行器核心数设置为 4 以上，则垃圾回收可能会变得低效，并且性能会下降。

**步骤 4：确定群集中的 YARN 内存量** – Ambari 中提供了此信息。 导航到 YARN 并查看 "Contigs" 选项卡。YARN 内存显示在此窗口中。
请注意，在该窗口中操作时，还可以查看默认的 YARN 容器大小。 YARN 容器大小与每个执行器的内存量参数相同。

    Total YARN memory = nodes * YARN memory per node
**步骤 5：计算执行器数目**

**计算内存约束** - 执行器数目参数受内存或 CPU 的约束。 内存约束由应用程序的可用 YARN 内存量决定。 获取 YARN 内存总量，并将其除以执行器内存。 需要根据应用数目重新换算约束，以便能够将它与应用数目相除。

    Memory constraint = (total YARN memory / executor memory) / # of apps
**计算 CPU 约束** - 将虚拟核心总数除以每个执行器的核心数可以计算出 CPU 约束。 每个物理核心有 2 个虚拟核心。 与内存约束类似，可以除以应用数目来计算结果。

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**设置执行器数目** – 使用内存约束和 CPU 约束的最小值可以得出执行器数目参数。 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)
设置较大的执行器数目不一定会提高性能。 应考虑到添加更多执行器会增加每个附加执行器的额外开销，这可能会降低性能。 执行器数目受群集资源的约束。

## <a name="example-calculation"></a>示例计算

假设你当前有一个由8个 D4v2 节点组成的群集，其中运行了两个应用，其中包括要运行的应用。

**步骤1：确定群集上正在运行的应用数**-你知道群集上有两个应用，包括要运行的应用。

**步骤 2：设置执行器内存** – 对于本示例，我们确定 6GB 执行器内存对于 I/O 密集型作业已足够。

    executor-memory = 6GB
**步骤3：设置执行器核心**–由于这是一个 i/o 密集型作业，因此我们可以将每个执行器的核心数设置为4。 将每个执行器的核心数设置为大于4可能会导致垃圾回收问题。

    executor-cores = 4
**步骤 4：确定群集中的 YARN 内存量** – 导航到 Ambari，可以发现每个 D4v2 具有 25GB YARN 内存。 由于有 8 个节点，因此内存总量为每个节点的可用 YARN 内存量乘以 8。

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25 GB = 200 GB
**步骤 5：计算执行器数目** – 将内存约束和 CPU 约束的最小值除以 Spark 中运行的应用数目可以得出执行器数目参数。

**计算内存约束** – 将 YARN 内存总量除以每个执行器的内存量可以计算出内存约束。

    Memory constraint = (total YARN memory / executor memory) / # of apps 
    Memory constraint = (200 GB / 6 GB) / 2
    Memory constraint = 16 (rounded)
**计算 CPU 约束** - 将 YARN 核心总数除以每个执行器的核心数可以计算出 CPU 约束。
    
    YARN cores = nodes in cluster * # of cores per node * 2
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**设置执行器数目**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16
