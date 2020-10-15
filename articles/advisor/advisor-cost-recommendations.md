---
title: 使用 Azure 顾问降低服务成本
description: 使用 Azure 顾问优化 Azure 部署的成本。
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 454ed02c06511b55f9f592bbe6fe5ab08605d752
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075903"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>使用 Azure 顾问降低服务成本

通过识别闲置和未充分利用的资源，Azure 顾问可帮助优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>通过调整或关闭未充分利用的实例来优化虚拟机花费 

虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 

建议的操作是关机或重设大小，具体取决于要评估的资源。

当所有这些语句都为 true 时，顾问中的高级评估模型考虑关闭虚拟机： 
- CPU 利用率最大值的第 95 个百分位数小于 3%。 
- 7 天内的网络利用率低于 2%。
- 内存压力低于阈值

如果可以在较小 SKU（同一 SKU 系列内）或较少数量的实例中容纳当前负载，则顾问会考虑重设虚拟机大小，以使：
- 不面向用户的工作负荷的当前负载利用率不超过 80%。 
- 面向用户的工作负荷的负载利用率不超过 40%。 

在这里，顾问通过分析工作负荷的 CPU 利用率特征来确定工作负荷的类型。

顾问会显示与建议的操作（重设大小或关机）相对应的成本节省估算值。 对于重设大小，顾问提供当前 SKU 信息和目标 SKU 信息。

若要加强对低利用率虚拟机的识别，可在每个订阅的基础上调整 CPU 利用率规则。

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>通过适当调整大小优化 MariaDB、MySQL 和 PostgreSQL 服务器的支出 
顾问会分析使用情况并评估 MariaDB、MySQL 或 PostgreSQL 数据库服务器资源在过去 7 天内是否长时间未被充分利用。 资源利用率低会导致不必要的支出，可在不显著影响性能的情况下解决这一问题。 若要降低成本并高效管理资源，我们建议将计算大小 (vCore) 减少一半。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>通过消除未设置的 ExpressRoute 线路来降低成本

顾问会识别处于“未预配”提供程序状态超过一个月的 Azure ExpressRoute 线路。 如果你不打算通过连接提供程序预配线路，则建议删除该线路。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>通过删除或重新配置空闲虚拟网络网关来降低成本

顾问会识别已闲置超过 90 天的虚拟网关。 因为这些网关按小时计费，所以如果不打算再使用它们，则应考虑重新配置或删除它们。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>购买虚拟机预留实例可节省即用即付成本

顾问会查看过去 30 天的虚拟机使用情况，以确定是否可以通过购买 Azure 预留来节省成本。 顾问会显示节省潜力最高的区域和大小，以及可通过购买预留实现的成本节省估算值。 通过 Azure 预留，可以预先购买虚拟机的基本成本。 折扣会自动应用于新的或现有的 VM，这些 VM 具有与预留相同的大小和区域。 [深入了解 Azure 虚拟机预留实例。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

顾问还会向你通知在接下来的 30 天内将过期的预留实例。 它会建议你购买新的预留实例来避免即用即付定价。

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>购买多个资源类型的预留实例，以节省即用即付成本

顾问会分析以下资源在过去 30 天内的使用模式，并建议可优化成本的预留容量购买量。

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 预留容量
顾问会分析 Azure Cosmos DB 在过去 30 天内的使用模式，并建议可优化成本的预留容量购买量。 通过使用预留容量，可以预购买按小时计的 Azure Cosmos DB 使用量，从而节省即用即付成本。 预留容量是一项计费权益，它会自动应用于新部署和现有部署。 顾问基于 3 年期预留定价并根据过去 30 天内观察到的使用模式进行推测，从而计算各订阅的成本节省估算值。 共享范围建议适用于预留容量购买，并可提高成本节省。

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SQL 数据库和 SQL 托管实例保留容量
顾问在过去30天内分析 SQL 数据库和 SQL 托管实例使用模式。 然后建议可优化成本的预留容量购买量。 通过使用预留容量，可以预购买按小时计的 SQL DB 使用量，从而节省 SQL 计算成本。 SQL 许可证单独收费，不会因预留而打折。 预留容量是一项计费权益，它会自动应用于新部署和现有部署。 顾问基于 3 年期预留定价并根据过去 30 天内观察到的使用模式进行推测，从而计算各订阅的成本节省估算值。 共享范围建议适用于预留容量购买，并可提高成本节省。 有关详细信息，请参阅 [AZURE Sql 数据库 & SQL 托管实例保留容量](../azure-sql/database/reserved-capacity-overview.md)。

### <a name="app-service-stamp-fee-reserved-capacity"></a>应用服务印花费预留容量
顾问会分析 Azure 应用服务隔离环境在过去 30 天内的印花费使用模式，并建议可优化成本的预留容量购买量。 通过使用预留容量，可以为隔离环境印花费预购买按小时计的使用量，从而节省即用即付成本。 请注意，预留容量仅适用于印花费，而不适用于应用服务实例。 预留容量是一项计费权益，它会自动应用于新部署和现有部署。 顾问基于 3 年期预留定价并根据过去 30 天内的使用模式计算各订阅的成本节省估算值。

### <a name="blob-storage-reserved-capacity"></a>Blob 存储预留容量
顾问会分析 Azure Blob 存储和 Azure Data Lake Storage 在过去 30 天内的使用情况， 然后计算可优化成本的预留容量购买量。 通过预留容量，可以预购买按小时计的使用量，从而节省按需成本。 Blob 存储预留容量仅适用于存储在 Azure Blob 常规用途 v2 和 Azure Data Lake Storage Gen2 帐户上的数据。 预留容量是一项计费权益，它会自动应用于新部署和现有部署。 顾问基于 3 年期预留定价以及过去 30 天内观察到的使用模式计算各订阅的成本节省估算值。 共享范围建议适用于预留容量购买，并可提高成本节省。

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB、MySQL 和 PostgreSQL 预留容量
顾问会分析 Azure Database for MariaDB、Azure Database for MySQL 和 Azure Database for PostgreSQL 在过去 30 天内的使用模式， 然后建议可优化成本的预留容量购买量。 通过使用预留容量，可以预购买按小时计的 MariaDB、MySQL 和 PostgreSQL 使用量，从而节省当前成本。 预留容量是一项计费权益，它会自动应用于新部署和现有部署。 顾问基于 3 年期预留定价以及过去 30 天内观察到的使用模式计算各订阅的成本节省估算值。 共享范围建议适用于预留容量购买，并可提高成本节省。

### <a name="azure-synapse-analytics-formerly-sql-data-warehouse-reserved-capacity"></a>Azure Synapse Analytics（以前称为 SQL 数据仓库）预留容量
顾问会分析 Azure Synapse Analytics 在过去 30 天内的使用模式，并建议可优化成本的预留容量购买量。 通过使用预留容量，可以预购买按小时计的 Synapse Analytics 使用量，从而节省按需成本。 预留容量是一项计费权益，它会自动应用于新部署和现有部署。 顾问基于 3 年期预留定价以及过去 30 天内观察到的使用模式计算各订阅的成本节省估算值。 共享范围建议适用于预留容量购买，并可提高成本节省。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>删除未关联的公共 IP 地址可节省资金

顾问可识别与负载均衡器和 VM 等 Azure 资源无关的公共 IP 地址。 这些公共 IP 地址会产生少许费用。 如果不打算使用它们，则可以通过删除它们来节省成本。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>删除将要发生故障的 Azure 数据工厂管道

顾问会检测重复失败的 Azure 数据工厂管道， 并建议解决问题或在不需要时删除管道。 即使这些管道在发生故障时没有为你提供服务，我们也会向你收取相关费用。

## <a name="use-standard-snapshots-for-managed-disks"></a>使用托管磁盘的标准快照
为了节省60% 的成本，我们建议将快照存储在标准存储中，而不考虑父磁盘的存储类型。 此选项是托管磁盘快照的默认选项。 顾问标识存储在高级存储中的快照，并建议从高级存储迁移到标准存储。 [详细了解托管磁盘定价。](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>使用生命周期管理
通过使用有关 Azure Blob 存储对象计数、总大小和事务的情报，顾问可检测是否应启用生命周期管理来对一个或多个存储帐户上的数据执行分层。 它会提示你创建生命周期管理规则，以将数据自动分层到冷存储或存档存储，从而优化存储成本，并将数据保留在 Azure Blob 存储中来确保应用程序兼容性。

## <a name="create-an-ephemeral-os-disk-recommendation"></a>创建临时 OS 磁盘的建议
借助[临时 OS 磁盘](../virtual-machines/ephemeral-os-disks.md)，你可以： 
- 节省 OS 磁盘的存储成本。 
- 降低 OS 磁盘的读取/写入延迟。 
- 通过将 OS（和临时磁盘）重置为其原始状态，加速 VM 重置映像操作。

对于生存期较短的 IaaS VM 或具有无状态工作负荷的 VM，最好使用临时 OS 磁盘。 顾问提供有关可从临时 OS 磁盘中受益的资源的建议。

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>缩短 Azure 数据资源管理器表缓存期（策略），实现群集成本优化（预览版）
顾问会识别符合以下要求的资源：缩短表缓存策略将释放具有低 CPU 利用率、内存和高缓存大小配置的 Azure 数据资源管理器群集节点。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的成本建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在任意页面中搜索并选择[顾问](https://aka.ms/azureadvisordashboard)。

1. 在顾问仪表板中，选择“成本”选项卡 。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [顾问分数](azure-advisor-score.md)
* [顾问入门](advisor-get-started.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)