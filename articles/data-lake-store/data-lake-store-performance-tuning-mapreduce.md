---
title: "Azure Data Lake Store MapReduce 性能优化指南 | Microsoft 文档"
description: "Azure Data Lake Store MapReduce 性能优化指南"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 522e03769a8f09acd88d92d72c4658407b86bd0b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>MapReduce on HDInsight 和 Azure Data Lake Store 性能优化指南


## <a name="prerequisites"></a>先决条件

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure Data Lake Store 帐户**。 有关如何创建帐户的说明，请参阅 [Azure Data Lake Store 入门](data-lake-store-get-started-portal.md)
* 具有 Data Lake Store 帐户访问权限的**Azure HDInsight 群集**。 请参阅[创建包含 Data Lake Store 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保对该群集启用远程桌面。
* **在 HDInsight 上使用 MapReduce**。  有关详细信息，请参阅[在 HDInsight 上的 Hadoop 中使用 MapReduce](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)  
* **ADLS 性能优化指南**。  有关一般的性能概念，请参阅 [Data Lake Store 性能优化指南](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="parameters"></a>parameters

运行 MapReduce 作业时，以下是可配置以提高 ADLS 性能的最重要参数：

* **Mapreduce.map.memory.mb** - 要分配给每个映射器的内存量
* **Mapreduce.job.maps** - 每个作业的映射任务数
* **Mapreduce.reduce.memory.mb** - 要分配给每个化简器的内存量
* **Mapreduce.job.reduces** - 每个作业的化简任务数

**Mapreduce.map.memory/Mapreduce.reduce.memory** 应根据映射和/或化简任务所需的内存量调整此数字。  可以在 Ambari 中通过 Yarn 配置查看 mapreduce.map.memory 和 mapreduce.reduce.memory 的默认值。  在 Ambari 中，导航到 YARN 并查看“配置”选项卡。随即显示 YARN 内存。  

**Mapreduce.job.maps/Mapreduce.job.reduces** 此项确定要创建的映射器或化简器的最大数量。  拆分数将确定要为 MapReduce 作业创建多少个映射器。  因此，如果拆分数少于所请求的映射器数，则得到的映射器数可能会少于所请求的数量。       

## <a name="guidance"></a>指南

**步骤 1：确定正在运行的作业数** - 默认情况下，MapReduce 会将整个群集用于作业。  可以通过让使用的映射器数少于可用的容器数，来使用更少的群集。  本文档中的指南假定应用程序是在群集上运行的唯一应用程序。      

**步骤 2：设置 mapreduce.map.memory/mapreduce.reduce.memory** - 用于映射和化简任务的内存大小将取决于特定作业。  如果要提高并发性，可以减少内存大小。  并发运行的任务数取决于容器数。  减少每个映射器或化简器的内存量，可以创建多个容器，从而使更多映射器或化简器可以并发运行。  减少过多的内存量可能会导致某些进程内存不足。  如果在运行作业时收到堆错误，应增加每个映射器或化简器的内存。  应考虑到添加更多容器会添加每个附加容器的额外开销，这可能会降低性能。  另一种替代方法是通过使用具有更高内存量的群集或增加群集中的节点数来获得更多内存。  有更多内存将可以使用更多容器，这意味着可实现更高并发性。  

**步骤 3：确定总 YARN 内存量** - 若要优化 mapreduce.job.maps/mapreduce.job.reduces，应考虑可供使用的总 YARN 内存量。  该信息在 Ambari 中提供。  导航到 YARN 并查看“配置”选项卡。YARN 内存量会显示在此窗口中。  应将 YARN 内存量与群集中的节点数相乘，获得总 YARN 内存量。

    Total YARN memory = nodes * YARN memory per node
如果使用的是空群集，则内存量可能会是群集的总 YARN 内存量。  如果其他应用程序正在使用内存，则可以通过将映射器或化简器的数目减少到要使用的容器数来选择仅使用群集的一部分内存。  

**步骤 4：计算 YARN 容器数** - YARN 容器数决定可并发运行的作业数量。  获取总 YARN 内存量并将其除以 mapreduce.map.memory。  

    # of YARN containers = total YARN memory / mapreduce.map.memory

步骤 5： 设置 mapreduce.job.maps/mapreduce.job.reduces 将 mapreduce.job.maps/mapreduce.job.reduces 设置为最少可用容器数。  可以通过增加映射器和化简器的数量来进一步试验，看是否可获得更佳性能。  请记住，映射器越多开销越大，因此使用太多映射器可能会降低性能。  

CPU 计划和 CPU 隔离在默认情况下关闭，因此 YARN 容器数受内存量约束。

## <a name="example-calculation"></a>示例计算

让我们假设你当前有一个由 8 个 D14 节点组成的群集，并且你想要运行一个 I/O 密集型作业。  下面是你应执行的计算：

**步骤 1：确定正在运行的作业数** - 对于本示例，假定我们的作业是唯一正在运行的作业。  

**步骤 2：设置 mapreduce.map.memory/mapreduce.reduce.memory** - 在本示例中，正在运行一个 I/O 密集型作业，并确定将 3GB 的内存用于映射任务已足够。

    mapreduce.map.memory = 3GB
**步骤 3：确定总 YARN 内存量**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**步骤 4：计算 YARN 容器数**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**步骤 5：设置 mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>限制

**ADLS 限制**

作为一种多租户服务，ADLS 设置帐户级带宽限制。  如果达到这些限制，将开始看到任务失败。 这可以通过观察任务日志中的限制错误来确定。  如果作业需要更多带宽，请与我们联系。   

若要查看是否受到限制，需要在客户端上启用调试日志记录。 下面介绍执行该操作的方法：

1. 将 log4j 属性中的以下属性放到 Ambari > YARN >“配置”>“高级 yarn-log4j”中：log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. 重新启动所有节点/服务使配置生效。

3. 如果受到限制，会在 YARN 日志文件中看到 HTTP 429 错误代码。 YARN 日志文件位于 /tmp/&lt;用户&gt;/yarn.log 中

## <a name="examples-to-run"></a>要运行的示例

为了演示 MapReduce 在 Azure Data Lake Store 上的运行方式，下面提供一些示例代码，这些代码已在具有下列设置的群集上运行：

* 16 个节点 D14v2
* 运行 HDI 3.6 的 Hadoop 群集

作为第一步，下面是一些示例命令，用于运行 MapReduce Teragen、Terasort 和 Teravalidate。  可以根据资源调整这些命令。

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
