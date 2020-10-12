---
title: Azure HDInsight 中的群集容量规划
description: 确定 Azure HDInsight 群集的容量和性能规划的关键问题。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 8e76f767470b9052b25cd2b2958f3f9e9780881b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83714740"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 群集的容量规划

在部署 HDInsight 群集之前，应确定需要的性能和规模，从而为所需的群集容量做好规划。 这种规划有助于优化可用性与成本。 部署之后，某些群集容量决策不可更改。 如果性能参数发生更改，可以拆除群集，然后重新创建，而不会丢失存储的数据。

容量规划期间要提出的重要问题包括：

* 应在哪个地理区域中部署群集？
* 需要多少存储？
* 应部署哪种群集类型？
* 群集节点应使用的虚拟机 (VM) 大小和类型是什么？
* 群集应包含多少个工作节点？

## <a name="choose-an-azure-region"></a>选择 Azure 区域

Azure 区域确定群集的物理预配位置。 为了将读写延迟最小化，群集应靠近数据所在的位置。

许多 Azure 区域提供 HDInsight。 若要查找最近的区域，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)。

## <a name="choose-storage-location-and-size"></a>选择存储位置和大小

### <a name="location-of-default-storage"></a>默认存储的位置

默认存储（Azure 存储帐户或 Azure Data Lake Storage）必须与群集位于同一位置。 所有位置都提供 Azure 存储。 Data Lake Storage Gen1 已在某些区域中提供，请参阅当前 [Data Lake Storage 的可用性](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

### <a name="location-of-existing-data"></a>现有数据的位置

如果想将现有的存储帐户或 Data Lake Storage 用作群集的默认存储，你必须在与其相同的位置部署群集。

### <a name="storage-size"></a>存储大小

你可以在部署的群集上附加更多 Azure 存储帐户，或访问其他 Data Lake Storage。 所有存储帐户均必须与群集位于同一位置。 Data Lake Storage 可以位于不同的位置，不过，距离较远可能会造成某种程度的延迟。

Azure 存储具有某些[容量限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)，而 Data Lake Storage Gen1 几乎没有限制。

群集可以访问不同存储帐户的组合。 典型示例包括：

* 当数据量可能会超过单个 Blob 存储容器的存储容量时。
* 当对 Blob 容器的访问速率可能会超过阈值，从而发生限制时。
* 想要将已上传到 Blob 容器的数据提供给群集使用时。
* 出于安全原因想要隔离存储的不同部分，或要简化管理时。

为提高性能，请对每个存储帐户仅使用一个容器。

## <a name="choose-a-cluster-type"></a>选择群集类型

群集类型决定 HDInsight 群集被配置运行的工作负载。 类型包括 [Apache Hadoop](./hadoop/apache-hadoop-introduction.md)、[Apache Storm](./storm/apache-storm-overview.md)、[Apache Kafka](./kafka/apache-kafka-introduction.md) 或 [Apache Spark](./spark/apache-spark-overview.md)。 有关可用群集类型的详细说明，请参阅 [Azure HDInsight 简介](hdinsight-overview.md#cluster-types-in-hdinsight)。 每个群集类型具有一个特定的部署拓扑，该拓扑附带大小和节点数方面的要求。

## <a name="choose-the-vm-size-and-type"></a>选择 VM 大小和类型

每个群集类型具有一组节点类型，每个节点类型在 VM 大小和类型方面提供特定的选项。

若要确定应用程序的最佳群集大小，可以建立群集容量基准，并根据指示增加大小。 例如，可以使用模拟工作负荷或“canary 查询”。** 在不同大小的群集上运行模拟工作负载。 逐渐增加大小，直到达到预期性能。 可在其他生产查询之间定期插入 canary 查询，以显示群集是否有足够的资源。

有关如何为工作负荷选择正确的 VM 系列的详细信息，请参阅[为群集选择适当的 VM 大小](hdinsight-selecting-vm-size.md)。

## <a name="choose-the-cluster-scale"></a>选择群集规模

群集的规模由其 VM 节点数量决定。 所有群集类型都存在一些具有特定缩放的节点类型，以及一些支持横向扩展的节点类型。例如，某个群集可能恰好需要三个 [Apache ZooKeeper](https://zookeeper.apache.org/) 节点或两个头节点。 更多的工作器节点对以分布方式执行数据处理的工作器节点有益。

增加工作器节点数会增加额外的计算能力（例如更多核心），这具体取决于群集的类型。 更多节点将增加整个群集支持正在处理的数据的内存中存储所需的总内存。 就像选择 VM 大小和类型一样，适当的群集规模通常是凭经验选择出来的。 使用的是模拟工作负载或 canary 查询。

你可以横向扩展群集以满足峰值负载需求。 然后在不再需要这些额外的节点时，进行纵向缩减。 通过[自动缩放功能](hdinsight-autoscale-clusters.md)，你可以根据预先确定的指标和时间安排自动缩放群集。 有关手动缩放群集的详细信息，请参阅[缩放 HDInsight 群集](hdinsight-scaling-best-practices.md)。

### <a name="cluster-lifecycle"></a>群集生命周期

在群集的生存期内会产生费用。 如果只是需要在特定的时间使用群集，请[使用 Azure 数据工厂创建按需群集](hdinsight-hadoop-create-linux-clusters-adf.md)。 还可以创建 PowerShell 脚本用于预配和删除群集，然后使用 [Azure 自动化](https://azure.microsoft.com/services/automation/)计划这些脚本。

> [!NOTE]  
> 删除某个群集时，也会一并删除其默认 Hive 元存储。 若要保留元存储供下一次重新创建群集时使用，可以使用 Azure 数据库或 [Apache Oozie](https://oozie.apache.org/) 等外部元数据存储。

### <a name="isolate-cluster-job-errors"></a>查明群集作业错误

有时，多节点群集上多个映射和化简组件的并行执行可能导致出错。 若要帮助隔离此问题，请尝试分布式测试。 在单工作器节点群集上并发运行多个作业。 然后将这种方法扩展为在包含多个节点的群集上并发运行多个作业。 若要在 Azure 中创建单节点 HDInsight 群集，请使用 `Custom(size, settings, apps)` 选项，并于在门户中预配新群集时，将值 1 用作“群集大小”部分中的工作器节点数** ******。

## <a name="quotas"></a>配额

有关管理订阅配额的详细信息，请参阅[要求增加配额](quota-increase-request.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中设置和配置群集。
* [监视群集性能](hdinsight-key-scenarios-to-monitor.md)：了解要在 HDInsight 群集中监视的、可能会影响群集容量的关键情况。
