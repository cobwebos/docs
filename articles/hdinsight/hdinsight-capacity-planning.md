---
title: Azure HDInsight 中的群集容量规划
description: 确定 Azure HDInsight 群集的容量和性能规划的关键问题。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/07/2020
ms.openlocfilehash: 4ede8833fdbdbd57654e6c02147f53e58a17b1de
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886987"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 群集的容量规划

在部署 HDInsight 群集之前，通过确定所需的性能和规模来规划预期的群集容量。 这种规划有助于优化可用性与成本。 部署后无法更改某些群集容量决策。 如果性能参数发生更改，可以拆除群集，然后重新创建，而不会丢失存储的数据。

容量规划期间要提出的重要问题包括：

* 应在哪个地理区域中部署群集？
* 需要多少存储？
* 应部署哪种群集类型？
* 群集节点应使用的虚拟机 (VM) 大小和类型是什么？
* 群集应包含多少个工作节点？

## <a name="choose-an-azure-region"></a>选择 Azure 区域

Azure 区域确定群集的物理预配位置。 为了将读写延迟最小化，群集应靠近数据所在的位置。

许多 Azure 区域提供 HDInsight。 要查找最近的区域，请参阅[按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)。

## <a name="choose-storage-location-and-size"></a>选择存储位置和大小

### <a name="location-of-default-storage"></a>默认存储的位置

默认存储（Azure 存储帐户或 Azure Data Lake Storage）必须与群集位于同一位置。 所有位置都提供 Azure 存储。 数据存储湖存储 Gen1 在某些地区可用 - 请参阅当前[数据存储可用性](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

### <a name="location-of-existing-data"></a>现有数据的位置

如果要使用现有存储帐户或数据湖存储作为群集的默认存储，则必须将群集部署在同一位置。

### <a name="storage-size"></a>存储大小

在已部署的群集上，可以附加其他 Azure 存储帐户或访问其他数据湖存储。 所有存储帐户必须与群集位于同一位置。 数据湖存储可能位于其他位置，但距离遥远可能会带来一些延迟。

Azure 存储有一些[容量限制](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)，而数据存储第 1 代几乎是无限的。

群集可以访问不同存储帐户的组合。 典型示例包括：

* 当数据量可能会超过单个 Blob 存储容器的存储容量时。
* 当对 Blob 容器的访问速率可能会超过阈值，从而发生限制时。
* 如果要创建数据，已上载到群集可用的 Blob 容器。
* 出于安全原因想要隔离存储的不同部分，或要简化管理时。

为提高性能，请对每个存储帐户仅使用一个容器。

## <a name="choose-a-cluster-type"></a>选择群集类型

群集类型确定 HDInsight 群集配置为运行的工作负载。 类型包括[阿帕奇哈多普](./hadoop/apache-hadoop-introduction.md)，[阿帕奇风暴](./storm/apache-storm-overview.md)，[阿帕奇卡夫卡](./kafka/apache-kafka-introduction.md)，或[阿帕奇火花](./spark/apache-spark-overview.md)。 有关可用群集类型的详细说明，请参阅 [Azure HDInsight 简介](hdinsight-overview.md#cluster-types-in-hdinsight)。 每个群集类型具有一个特定的部署拓扑，该拓扑附带大小和节点数方面的要求。

## <a name="choose-the-vm-size-and-type"></a>选择 VM 大小和类型

每个群集类型具有一组节点类型，每个节点类型在 VM 大小和类型方面提供特定的选项。

若要确定应用程序的最佳群集大小，可以建立群集容量基准，并根据指示增加大小。 例如，可以使用模拟工作负荷或“canary 查询”。** 在不同的大小群集上运行模拟工作负载。 逐渐增加大小，直到达到预期性能。 可在其他生产查询中定期插入 canary 查询，以显示群集是否具有足够的资源。

有关如何为工作负荷选择正确的 VM 系列的详细信息，请参阅[为群集选择正确的 VM 大小](hdinsight-selecting-vm-size.md)。

## <a name="choose-the-cluster-scale"></a>选择群集规模

群集的规模由其 VM 节点数量决定。 对于所有群集类型，有具有特定比例的节点类型，以及支持横向扩展的节点类型。例如，群集可能只需要三个[Apache ZooKeeper](https://zookeeper.apache.org/)节点或两个头节点。 以分布式方式进行数据处理的工作节点受益于其他辅助角色节点。

根据群集类型，增加辅助节点的数量会增加额外的计算容量（如更多内核）。 更多的节点将增加整个群集所需的总内存，以支持正在处理的数据的内存中存储。 与选择 VM 大小和类型一样，通常根据经验选择正确的群集比例。 使用模拟工作负载或金丝雀查询。

您可以横向扩展群集以满足峰值负载需求。 然后，当不再需要这些额外的节点时，将其缩减为缩减。 [自动缩放功能](hdinsight-autoscale-clusters.md)允许您根据预先确定的指标和时间自动缩放群集。 有关手动扩展群集的详细信息，请参阅[缩放 HDInsight 群集](hdinsight-scaling-best-practices.md)。

### <a name="cluster-lifecycle"></a>群集生命周期

群集的生存期需要付费。 如果只有特定时间需要群集，[请使用 Azure 数据工厂创建按需群集](hdinsight-hadoop-create-linux-clusters-adf.md)。 还可以创建 PowerShell 脚本用于预配和删除群集，然后使用 [Azure 自动化](https://azure.microsoft.com/services/automation/)计划这些脚本。

> [!NOTE]  
> 删除某个群集时，也会一并删除其默认 Hive 元存储。 若要保留元存储供下一次重新创建群集时使用，可以使用 Azure 数据库或 [Apache Oozie](https://oozie.apache.org/) 等外部元数据存储。
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>查明群集作业错误

有时，由于并行执行多个映射并减少多节点群集上的组件，可能会发生错误。 为了帮助隔离问题，请尝试分布式测试。 在单个辅助节点群集上运行并发多个作业。 然后展开此方法，在包含多个节点的群集上同时运行多个作业。 要在 Azure 中创建单节点 HDInsight 群集*`Custom(size, settings, apps)`*，在门户中预配新群集时，请使用 选项，并在**群集大小**部分中对*辅助节点数*使用值 1。

## <a name="quotas"></a>配额

确定目标群集 VM 大小、规模和类型之后，请检查订阅的当前配额容量限制。 达到配额限制时，无法部署新群集。 或者通过添加更多辅助节点来扩展现有群集。 唯一存在配额限制的是每个订阅的区域级别的 CPU 核心配额。 例如，订阅可能会在美国东部区域有 30 个核心的限制。

要检查可用的内核，请执行以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到 HDInsight 群集的 **"概述"** 页面。
3. 在左侧菜单上，选择 **"配额限制**"。

   该页显示正在使用的内核数、可用内核数和总内核数。

如果需要请求增加配额，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 选择页面左下方的“帮助 + 支持”****。
1. 选择 **"新建支持请求**"。
1. 在“新建支持请求”页面的“基本信息”选项卡下，选择以下选项********：

   - **问题类型**：**服务和订阅限制（配额）**
   - **订阅**：要修改的订阅
   - **配额类型**： **HDInsight**

     ![创建支持请求来增加 HDInsight 核心配额](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. 选择 **"下一步"：解决方案 >>**。
1. 在“详细信息”页上，输入问题的说明，选择问题的严重性、首选联系方法和其他必需字段****。
1. 选择 **"下一步"：查看 = 创建 >>**。
1. 在“查看 + 创建”选项卡上，选择“创建”。********

> [!NOTE]  
> 如果需要增加专用区域中的 HDInsight 核心配额，请[提交允许列表请求](https://aka.ms/canaryintwhitelist)。

可以[联系支持部门来请求提高配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

有一些固定的配额限制。 例如，单个 Azure 订阅最多只能有 10，000 个内核。 有关这些限制的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何在 HDInsight 中设置和配置群集。
* [监视群集性能](hdinsight-key-scenarios-to-monitor.md)：了解要在 HDInsight 群集中监视的、可能会影响群集容量的关键情况。
