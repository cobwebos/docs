---
title: 优化性能： MapReduce、HDInsight & Azure Data Lake Storage Gen2 |Microsoft Docs
description: Data Lake Storage Gen2 MapReduce 性能优化指南
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327930"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>优化性能： MapReduce、HDInsight & Azure Data Lake Storage Gen2

了解在优化 Map Reduce 作业的性能时应考虑的因素。 本文介绍了一系列性能优化指南。

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* Azure Data Lake Storage Gen2 帐户。 有关如何创建的说明，请参阅[快速入门：创建 Azure Data Lake Storage Gen2 的存储帐户](data-lake-storage-quickstart-create-account.md)。
* 具有 Data Lake Storage Gen2 帐户访问权限的 Azure HDInsight 群集。 请参阅[配合使用 Azure Data Lake Storage Gen2 和 Azure HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **在 HDInsight 上使用 MapReduce**。  有关详细信息，请参阅[在 HDInsight 上的 Hadoop 中使用 MapReduce](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Data Lake Storage Gen2 的性能优化指南**。  有关一般的性能概念，请参阅 [Data Lake Storage Gen2 性能优化指南](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>参数

运行 MapReduce 作业时，可以配置以下参数来提高 Data Lake Storage Gen2 的性能：

* **Mapreduce.map.memory.mb** - 要分配给每个映射器的内存量
* **Mapreduce.job.maps** - 每个作业的映射任务数
* **Mapreduce.reduce.memory.mb** - 要分配给每个化简器的内存量
* **Mapreduce.job.reduces** - 每个作业的化简任务数

**Mapreduce.map.memory/Mapreduce.reduce.memory** 应根据映射和/或化简任务所需的内存量调整此数字。  可以在 Ambari 中通过 Yarn 配置查看 mapreduce.map.memory 和 mapreduce.reduce.memory 的默认值。  在 Ambari 中，导航到 "YARN" 并查看 "配置" 选项卡。 将显示 YARN 内存。  

**Mapreduce.job.maps/Mapreduce.job.reduces** 此项确定要创建的映射器或化简器的最大数量。  拆分数将确定要为 MapReduce 作业创建多少个映射器。  因此，如果拆分数少于所请求的映射器数，则得到的映射器数可能会少于所请求的数量。       

## <a name="guidance"></a>指南

> [!NOTE]
> 本文档中的指南假定应用程序是在群集上运行的唯一应用程序。

**步骤1：确定正在运行的作业数**

默认情况下，MapReduce 会将整个群集用于作业。  可以通过让使用的映射器数少于可用的容器数，来使用更少的群集。        

**步骤2：设置 mapreduce. .map/mapreduce. 降低内存**

用于映射和化简任务的内存大小将取决于特定作业。  如果要提高并发性，可以减少内存大小。  并发运行的任务数取决于容器数。  减少每个映射器或化简器的内存量，可以创建多个容器，从而使更多映射器或化简器可以并发运行。  减少过多的内存量可能会导致某些进程内存不足。  如果在运行作业时收到堆错误，应增加每个映射器或化简器的内存。  应考虑到添加更多容器会添加每个附加容器的额外开销，这可能会降低性能。  另一种替代方法是通过使用具有更高内存量的群集或增加群集中的节点数来获得更多内存。  有更多内存将可以使用更多容器，这意味着可实现更高并发性。  

**步骤 3：确定总 YARN 内存量**

要优化 mapreduce.job.maps/mapreduce.job.reduces，应考虑可供使用的总 YARN 内存量。  该信息在 Ambari 中提供。  导航到 YARN 并查看 "配置" 选项卡。 YARN 内存显示在此窗口中。  应将 YARN 内存量与群集中的节点数相乘，获得总 YARN 内存量。

    Total YARN memory = nodes * YARN memory per node

如果使用的是空群集，则内存量可能会是群集的总 YARN 内存量。  如果其他应用程序正在使用内存，则可以通过将映射器或化简器的数目减少到要使用的容器数来选择仅使用群集的一部分内存。  

**步骤4：计算 YARN 容器数**

YARN 容器数决定作业可用的并发数量。  获取总 YARN 内存量并将其除以 mapreduce.map.memory。  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**步骤 5：设置 mapreduce.job.maps/mapreduce.job.reduces**

将 mapreduce.job.maps/mapreduce.job.reduces 设置为至少可用容器的数量。  可以通过增加映射器和化简器的数量来进一步试验，看是否可获得更佳性能。  请记住，映射器越多开销越大，因此使用太多映射器可能会降低性能。  

CPU 计划和 CPU 隔离在默认情况下关闭，因此 YARN 容器数受内存量约束。

## <a name="example-calculation"></a>示例计算

假设我们有一个由 8 个 D14 节点组成的群集，并且要运行 I/O 密集型作业。  下面是你应执行的计算：

**步骤1：确定正在运行的作业数**

在此示例中，假定我们的作业是唯一正在运行的作业。  

**步骤2：设置 mapreduce. .map/mapreduce. 降低内存**

在此示例中，我们要运行 I/O 密集型作业，并确定将 3GB 的内存用于映射任务完全足够。

    mapreduce.map.memory = 3GB

**步骤 3：确定总 YARN 内存量**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**步骤 4：计算 YARN 容器数**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**步骤 5：设置 mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>要运行的示例

为了演示 MapReduce 在 Data Lake Storage Gen2 上的运行方式，下面提供了一些在具有下列设置的群集上运行的示例代码：

* 16 个节点 D14v2
* 运行 HDI 3.6 的 Hadoop 群集

作为第一步，下面是一些示例命令，用于运行 MapReduce Teragen、Terasort 和 Teravalidate。  可以根据资源调整这些命令。

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
