---
title: "Azure 数据湖存储 Spark 性能优化准则 |Microsoft 文档"
description: "Azure 数据湖存储 Spark 性能优化准则"
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
ms.openlocfilehash: 2109744fb7ffdfafb7a86bbea355e119718af099
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>性能优化在 HDInsight 和 Azure Data Lake 存储上的 Spark 的指南

优化时在 Spark 中的性能，你需要考虑的应用程序将在你的群集上运行的数量。  默认情况下，你可以运行 4 HDI 群集上的并发的应用 (注意： 默认设置进行更改)。  你可能决定使用更少的应用，因此可以重写默认设置，还可以使用这些应用的多个群集。  

## <a name="prerequisites"></a>必备条件

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 数据湖存储帐户**。 有关如何创建一个说明，请参阅[开始使用 Azure 数据湖存储](data-lake-store-get-started-portal.md)
* **Azure HDInsight 群集**有权访问数据湖存储帐户。 请参阅[使用 Data Lake 存储创建 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。 请确保为群集启用远程桌面。
* **Azure Data Lake 存储上运行 Spark 群集**。  有关详细信息，请参阅[使用 HDInsight Spark 群集，来分析数据湖存储区中的数据](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **性能优化指导说明了 ADLS**。  有关常规性能概念，请参阅[数据湖存储性能优化指南](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>参数

当运行 Spark 作业时，以下是可进行调整以增加性能 ADLS 最重要设置：

* **Num 执行器**-并发可执行的任务数。

* **执行器内存**-分配给每个执行程序的内存量。

* **执行程序内核**-分配给每个执行程序的内核数。                     

**Num 执行器**Num 执行器将设置可以并行运行的任务的最大数目。  可以并行运行的任务的实际数受约束的内存和你的群集中的可用 CPU 资源。

**执行器内存**这是要分配给每个执行程序的内存量。  每个执行程序所需的内存是依赖于该作业。  对于复杂的操作，内存需要更高版本使用。  对于简单的操作，如读取和写入，内存要求将较低。  可以在 Ambari 中查看的每个执行程序的内存量。  在 Ambari，导航到 Spark，并查看配置选项卡。  

**执行程序内核**这设置每执行程序，确定每执行程序可以运行的并行线程数已用的内核的数量。  例如，如果执行程序内核 = 2，则每个执行程序可以运行两个并行任务中执行程序。  执行程序内核需要会依赖于该作业。  I/O 大量作业不需要大量的每个任务的内存，因此每个执行程序可以处理更多的并行任务。

默认情况下，两个虚拟 YARN 内核在 HDInsight 上运行 Spark 时为每个物理核心定义。  此数字提供良好的平衡的 concurrecy 以及上下文切换从多个线程的数量。  

## <a name="guidance"></a>指南

运行时 Spark 分析工作负荷使用数据湖存储区中的数据，我们建议你使用的最新的 HDInsight 版本以获取最佳性能与数据湖存储。 你的作业时更耗费 I/O 资源，则可以配置某些参数以提高性能。  Azure 数据湖存储是一个高度可扩展的存储平台，可以处理较高的吞吐量。  如果作业主要包含读取或写入，然后针对 I/O 增加并发，与其他 Azure 数据湖存储可以提高性能。

有几种常规方法可以提高 I/O 密集型作业并发性。

**步骤 1： 确定你的群集上运行多少应用**– 你应知道多少应用程序上群集包括当前正在运行。  有 4 的默认值对于设置假定每个 Spark 同时运行的应用。  因此，你将只能有 25%的群集可用于每个应用程序。  若要获取更好的性能，可以通过更改执行器数覆盖默认值。  

**步骤 2： 设置执行器内存**– 若要设置的第一件事是执行器内存。  内存会依赖于想要运行的作业。  可以通过将每个执行程序较少内存分配来提高并发性。  如果在运行你的作业时，看到内存不足异常，则应增加此参数的值。  一种替代方法是群集的内存的通过使用具有更高版本量的群集或增加你大小获取更多内存。  更多的内存将启用详细执行器要使用，这意味着更多并发。

**步骤 3： 设置执行程序内核**– 为 I/O 密集型工作负荷不具有复杂的操作，最好是启动具有很多的执行程序的内核增加的每个执行程序的并行任务数。  将执行程序内核设置为 4 是一个不错的起点。   

    executor-cores = 4
增加的执行程序内核数将为你提供并行度使您可以具有不同的执行程序内核进行试验。  对于具有更复杂的操作的作业，你应该将每个执行程序的内核数目减少。  执行程序内核是否设置为高于 4，然后垃圾回收可能会效率低下，降低性能。

**步骤 4： 确定 YARN 群集中的内存量**– Ambari 中提供了此信息。  导航到 YARN 并查看配置选项卡。  在此窗口显示 YARN 内存。  
注意： 当你在窗口中，你还可以查看默认 YARN 容器大小。  YARN 容器大小等同于每个执行程序参数的内存。

    Total YARN memory = nodes * YARN memory per node
**步骤 5： 计算 num 执行器**

**计算内存约束**-num 执行器参数受内存或 CPU 限制。  内存约束由你的应用程序的可用 YARN 内存量确定。  应采取的 YARN 内存总量和除以执行器内存。  约束必须是取消缩放的应用程序数，因此我们除以的应用数。

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**计算 CPU 约束**-CPU 约束的计算方法为每个执行程序的内核数除以总虚拟内核数。  没有为每个物理核心 2 虚拟内核数。  我们与内存约束类似，可以通过应用数来除。

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**设置 num 执行器**– 通过获取的内存约束和 CPU 约束最小值可确定 num 执行器参数。 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
设置更多的 num 执行器并不一定增加性能。  你应考虑添加更多的执行器将添加额外的开销为每个其他执行程序，这可能会降低性能。  Num 执行器受群集资源。    

## <a name="example-calculation"></a>示例计算

假设你当前有运行的 2 的群集组成 8 个 D4v2 节点包括想要运行的应用。  

**步骤 1： 确定你的群集上运行多少应用**– 你知道你具有 2 上你的群集，包括你要运行的应用。  

**步骤 2： 设置执行器内存**– 对于此示例中，我们确定 6 GB 的执行器内存足以满足 I/O 密集型作业。  

    executor-memory = 6GB
**步骤 3： 设置执行程序内核**– 由于这是 I/O 密集型作业，我们可以设置为 4 到每个执行程序的内核数。  将每个执行程序内核数设置为大于 4 可能会导致垃圾回收集合问题。  

    executor-cores = 4
**步骤 4： 确定 YARN 群集中的内存量**– 我们导航到，Ambari 就可以找出每个 D4v2 具有 25 GB 的 YARN 内存。  由于有 8 个节点，则可用的 YARN 内存乘以 8。

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**步骤 5： 计算 num 执行器**– 通过获取的内存约束最小值可确定 num 执行器参数和 CPU 约束除以 # of Spark 上运行的应用。    

**计算内存约束**– 内存约束的计算方法为除以每执行程序的内存的 YARN 内存总量。

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**计算 CPU 约束**-CPU 约束的计算方法为每个执行程序的内核数除以总 yarn 内核。
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**集执行器 num**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

