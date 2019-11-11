---
title: Azure Data Lake Storage Gen1 性能优化-MapReduce
description: Data Lake Storage Gen1 MapReduce 性能优化指南
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904593"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>MapReduce on HDInsight 和 Azure Data Lake Storage Gen1 性能优化指南

## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Storage Gen1 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Storage Gen1 帐户访问权限的 Azure HDInsight 群集。 请参阅[创建包含 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。
* **在 HDInsight 上使用 MapReduce**。 有关详细信息，请参阅[在 HDInsight 上的 Hadoop 中使用 MapReduce](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **查看 Data Lake Storage Gen1 的性能优化指南**。 有关一般的性能概念，请参阅 [Data Lake Storage Gen1 性能优化指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parameters

运行 MapReduce 作业时，以下是可配置以提高 Azure Data Lake Storage Gen1 性能的最重要参数：

|参数      | 说明  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  要分配给每个映射器的内存量。  |
|`Mapreduce.job.maps`     |  每个作业的映射任务数。  |
|`Mapreduce.reduce.memory.mb`     |  要分配给每个化简器的内存量。  |
|`Mapreduce.job.reduces`    |   每个作业的减少任务数。  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce .map。内存/Mapreduce. 减少内存

根据映射和/或缩减任务所需的内存量调整此数字。 可以通过 Yarn 配置查看 Ambari 中 `mapreduce.map.memory` 和 `mapreduce.reduce.memory` 的默认值。 在 Ambari 中，导航到 "YARN" 并查看 "配置 **" 选项卡**。将显示 YARN 内存。

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce. maps/Mapreduce。

这会确定要创建的映射器或化简器的最大数目。 拆分数确定为 MapReduce 作业创建了多少个映射器。 因此，如果拆分次数少于请求的映射器数，则可能会获得比所请求的映射器更少的值。

## <a name="guidance"></a>指南

### <a name="step-1-determine-number-of-jobs-running"></a>步骤1：确定正在运行的作业数

默认情况下，MapReduce 会将整个群集用于作业。 使用的映射器可以比可用容器更少。 本文档中的指南假定应用程序是在群集上运行的唯一应用程序。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>步骤2：设置 mapreduce. .map/mapreduce. 降低内存

用于映射和化简任务的内存大小将取决于特定作业。 如果要提高并发性，可以减少内存大小。 并发运行的任务数取决于容器数。 减少每个映射器或化简器的内存量，可以创建多个容器，从而使更多映射器或化简器可以并发运行。 减少过多的内存量可能会导致某些进程内存不足。 如果在运行作业时出现堆错误，请增加每个映射器或化简器的内存。 请考虑添加更多容器会增加每个额外容器的额外开销，这可能会降低性能。 另一种替代方法是通过使用具有更高内存量的群集或增加群集中的节点数来获得更多内存。 有更多内存将可以使用更多容器，这意味着可实现更高并发性。

### <a name="step-3-determine-total-yarn-memory"></a>步骤3：确定总 YARN 内存

若要优化 mapreduce/mapreduce，请考虑可供使用的总 YARN 内存量。 该信息在 Ambari 中提供。 导航到 YARN 并**查看 "配置" 选项卡**。YARN 内存显示在此窗口中。 将 YARN 内存与群集中的节点数相乘，以获取总 YARN 内存。

`Total YARN memory = nodes * YARN memory per node`

如果使用的是空群集，则内存可以是群集的总 YARN 内存。 如果其他应用程序正在使用内存，则可以通过将映射器或化简器的数目减少到要使用的容器数来选择仅使用群集的一部分内存。

### <a name="step-4-calculate-number-of-yarn-containers"></a>步骤4：计算 YARN 容器数

YARN 容器数决定作业可用的并发数量。 获取总 YARN 内存量并将其除以 mapreduce.map.memory。

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>步骤5：设置 mapreduce. maps/mapreduce。

将 mapreduce.job.maps/mapreduce.job.reduces 设置为至少可用容器的数量。 可以通过增加映射器和化简器的数量来进一步试验，看是否可获得更佳性能。 请记住，映射器越多开销越大，因此使用太多映射器可能会降低性能。

CPU 计划和 CPU 隔离在默认情况下关闭，因此 YARN 容器数受内存量约束。

## <a name="example-calculation"></a>示例计算

让我们假设你当前有一个由 8 个 D14 节点组成的群集，并且你想要运行一个 I/O 密集型作业。 下面是你应执行的计算：

### <a name="step-1-determine-number-of-jobs-running"></a>步骤1：确定正在运行的作业数

对于我们的示例，我们假设作业是唯一运行的作业。

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>步骤2：设置 mapreduce. .map/mapreduce. 降低内存

在我们的示例中，你运行的是一个 i/o 密集型作业，并决定为映射任务使用了 3 GB 内存。

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>步骤3：确定总 YARN 内存

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>步骤4：计算 YARN 容器数

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>步骤5：设置 mapreduce. maps/mapreduce。

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>限制

**Data Lake Storage Gen1 限制**

作为一种多租户服务，Azure Data Lake Storage Gen1 设有帐户级带宽限制。 如果达到这些限制，将开始看到任务失败。 这可以通过观察任务日志中的限制错误来确定。 如果作业需要更多带宽，请与我们联系。

若要查看是否受到限制，需要在客户端上启用调试日志记录。 下面是执行该操作的方法：

1. 将 log4j 属性中的以下属性放到 Ambari > YARN >“配置”>“高级 yarn-log4j”中：log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. 重新启动所有节点/服务使配置生效。

3. 如果受到限制，将在 YARN 日志文件中看到 HTTP 429 错误代码。 YARN 日志文件位于 /tmp/&lt;用户&gt;/yarn.log 中

## <a name="examples-to-run"></a>要运行的示例

为了演示 MapReduce 如何在 Data Lake Storage Gen1 上运行，以下是在具有以下设置的群集上运行的一些示例代码：

* 16 个节点 D14v2
* 运行 HDI 3.6 的 Hadoop 群集

作为第一步，下面是一些示例命令，用于运行 MapReduce Teragen、Terasort 和 Teravalidate。 可以根据资源调整这些命令。

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
