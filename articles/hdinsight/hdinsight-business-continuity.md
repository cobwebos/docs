---
title: Azure HDInsight 业务连续性
description: 本文概述了 Azure HDInsight 业务连续性规划的最佳实践、单一区域可用性和优化选项。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: beb3c54a0ab7f6f063232a1ad49744d99746c589
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893639"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight 业务连续性

Azure HDInsight 群集依赖于许多 Azure 服务，例如存储、数据库、Active Directory、Active Directory 域服务、网络和 Key Vault。 设计良好的、高度可用且容错的容错分析应用程序时，应设计出足够的冗余，以应对其中一项或多项服务的区域或本地中断。 本文概述了用于业务连续性规划的最佳实践、单一区域可用性和优化选项。

## <a name="general-best-practices"></a>一般最佳实践

本部分讨论在业务连续性规划期间要考虑的一些最佳实践。

* 确定发生灾难和原因时所需的最小业务功能。 例如，评估是否需要数据转换层的故障转移功能 (以 *黄色) 显示， (* 蓝色) 显示数据服务层，或者只需要数据服务层的故障转移。

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="数据转换和数据服务层":::

* 根据工作负荷、开发生命周期和部门划分群集。 具有更多群集可降低影响多个不同业务流程的单个大型故障的几率。

* 使您的辅助区域成为只读的。 具有读取和写入功能的故障转移区域可能会导致复杂的体系结构。

* 发生灾难时，暂时性群集更易于管理。 以可以循环的方式设计工作负荷，并且不会在群集中维护状态。

* 如果发生灾难并且需要在新区域中重新启动，则工作负荷通常不会完成。 在本质上将工作负荷设计为幂等。

* 在群集部署过程中使用自动化，并确保尽可能多地编写群集配置设置的脚本，以确保在发生灾难时快速、完全自动化的部署。

* 使用 HDInsight 上的 Azure 监视工具检测群集中的异常行为并设置相应的警报通知。 你可以部署预配置的、特定于 HDInsight 群集的管理解决方案，这些解决方案收集特定群集类型的重要性能指标。 有关详细信息，请参阅 [适用于 HDInsight 的 Azure 监视](./hdinsight-hadoop-oms-log-analytics-tutorial.md)。  

* 订阅 Azure 运行状况警报，以获得有关订阅、服务或区域的服务问题、计划内维护、运行状况和安全建议的通知。 包含问题原因和 resolute ETA 的运行状况通知可帮助你更好地执行故障转移和故障回复。 有关详细信息，请参阅 [Azure 服务运行状况文档](/azure/service-health/)。

## <a name="single-region-availability"></a>单区域可用性

基本 HDInsight 系统具有以下组件。 所有组件都有其自己的单区域容错机制。

* 虚拟机) 计算 (： Azure HDInsight 群集
* 元存储 (s) ： Azure SQL 数据库
* 存储： Azure Data Lake Gen2 或 Blob 存储
* 身份验证： Azure Active Directory，Azure Active Directory 域服务，企业安全性套餐
* 域名解析： Azure DNS

还可以使用其他可选服务，例如 Azure Key Vault 和 Azure 数据工厂。

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="数据转换和数据服务层":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight 群集 (计算) 

HDInsight 提供99.9% 的可用性 SLA。 为了在单个部署中提供高可用性，默认情况下，HDInsight 附带了许多处于高可用性模式的服务。 HDInsight 中的容错机制由 Microsoft 和 Apache OSS 生态系统高可用性服务提供。

以下服务设计为具有高可用性：

#### <a name="infrastructure"></a>基础结构

* 活动和备用头节点
* 多个网关节点
* 三个 Zookeeper 仲裁节点
* 容错域和更新域分布的辅助节点

#### <a name="service"></a>服务

* Apache Ambari 服务器
* YARN 的应用程序时间线
* 适用于 Hadoop MapReduce 的作业历史记录服务器
* Apache Livy
* HDFS
* YARN 资源管理器
* HBase Master

有关详细信息，请参阅 [Azure HDInsight 支持的高可用性服务](./hdinsight-high-availability-components.md) 的相关文档。

它不会总是产生灾难性的事件来影响业务功能。 单个区域中一个或多个以下服务中的服务事件也可能导致预期的业务功能丢失。

### <a name="hdinsight-metastore"></a>HDInsight 元存储

HDInsight 使用 [AZURE SQL 数据库](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) 作为元存储，它提供99.99% 的 SLA。 数据中心内的三个数据副本使用同步复制来保存。 如果副本丢失，则可无缝地提供备用副本。 支持[活动异地复制](../azure-sql/database/active-geo-replication-overview.md)，最多可使用四个数据中心。 如果存在故障转移（手动或数据中心），则层次结构中的第一个副本将自动变为可读写。 有关详细信息，请参阅 [AZURE SQL 数据库业务连续性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)。

### <a name="hdinsight-storage"></a>HDInsight 存储

HDInsight 建议 Azure Data Lake Storage Gen2 作为基础存储层。 [Azure 存储](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)（包括 Azure Data Lake Storage Gen2）提供99.9% 的 SLA。 HDInsight 使用 LRS 服务，其中有三个数据副本保留在一个数据中心内，复制是同步的。 当副本丢失时，将无缝地提供副本。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) 提供99.9% 的 SLA。 Active Directory 是全局服务，具有多个内部冗余级别和自动可恢复性。 有关详细信息，请参阅 [Microsoft 如何持续提高 Azure Active Directory 的可靠性](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)。

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory 域服务 (AD DS) 

[Azure Active Directory 域服务](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) 提供99.9% 的 SLA。 Azure AD DS 是在全球分布的数据中心托管的高度可用的服务。 副本集是 Azure AD DS 中的一种预览功能，在 Azure 区域处于脱机状态的情况下，它可以实现地理灾难恢复。 有关详细信息，请参阅 [副本集概念和功能 Azure Active Directory 域服务](../active-directory-domain-services/concepts-replica-sets.md) 以了解详细信息。  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) 提供100% 的 SLA。 HDInsight 在不同位置使用 Azure DNS 进行域名解析。

## <a name="multi-region-cost-and-complexity-optimizations"></a>多区域成本和复杂性优化

使用跨区域高可用性改善业务连续性灾难恢复要求体系结构设计的复杂性更高，成本更高。 下表详细说明了一些可能会增加总拥有成本的技术领域。

### <a name="cost-optimizations"></a>成本优化

|领域|成本升级的原因|优化策略|
|----|------------------------|-----------------------|
|数据存储|复制次要区域中的主数据/表|仅复制特选的数据|
|数据出口|出站跨区域数据传输以价格提供。 查看带宽定价指导原则|仅复制特选数据以减少区域出口量|
|群集计算|辅助区域中的其他 HDInsight 群集|在主要故障后使用自动脚本部署辅助计算。 使用自动缩放将辅助群集大小保持在最小值。 使用更便宜的 VM Sku。 在 VM Sku 可能会打折的区域中创建辅助数据库。|
|身份验证 |次要区域中的多用户方案将产生其他 Azure AD DS 设置|避免次要区域中的多用户设置。|

### <a name="complexity-optimizations"></a>复杂性优化

|领域|复杂性升级的原因|优化策略|
|----|------------------------|-----------------------|
|读取写入模式 |需要对主数据库和辅助副本启用读取和写入 |将辅助副本设计为只读|
|0 RPO & RTO |不需要零数据丢失 (RPO = 0) ，而不能停机 (RTO = 0)  |设计 RPO 和 RTO 以减少需要故障转移的组件数量。|
|业务功能 |需要辅助数据库中主要的业务功能 |评估是否可以在辅助数据库中以业务功能的最低关键部分运行。|
|连接性 |需要从主系统中的所有上游和下游系统也连接到辅助系统|将辅助连接限制为最低关键的关键部分。|

## <a name="next-steps"></a>后续步骤

若要详细了解本文中所述的项，请参阅：

* [Azure HDInsight 业务连续性体系结构](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 高度可用的解决方案体系结构案例研究](./hdinsight-high-availability-case-study.md)
* [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](./hadoop/hdinsight-use-hive.md)
