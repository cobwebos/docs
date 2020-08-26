---
title: 使用 Azure 顾问降低服务成本
description: 使用 Azure 顾问优化 Azure 部署的成本。
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d234e89d0d042999805fae73d3df24c03d1027c9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654032"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>使用 Azure 顾问降低服务成本

Azure 顾问通过识别空闲和未充分利用的资源，帮助优化和减少总体 Azure 支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>通过调整或关闭未充分利用的实例来优化虚拟机花费 

虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 

建议的操作是关闭或重设大小，特定于正在评估的资源。

如果这两个语句都为 true，则 Advisor 中的高级评估模型考虑关闭虚拟机： 
- 最大 CPU 使用率最大值的 P95th 小于3%。 
- 在七天内，网络使用率低于2%。
- 内存压力低于阈值

如果可以在同一 SKU) 系列内的较小 SKU (中调整虚拟机的大小，则顾问会考虑调整虚拟机的大小，例如：
- 对于不是面向用户的工作负荷，当前负载不超过80% 的利用率。 
- 对于面向用户的工作负荷，负载不超过40%。 

此处，顾问通过分析工作负荷的 CPU 利用率特征来确定工作负荷的类型。

顾问显示了建议的操作的估算成本节约：重设大小或关闭。 对于大小调整，顾问提供当前和目标 SKU 信息。

如果你想要更积极地识别使用不足的虚拟机，可以根据每个订阅调整 CPU 使用率规则。

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>通过适当调整大小优化 MariaDB、MySQL 和 PostgreSQL 服务器的支出 
顾问会分析你的使用情况，并评估你的 MariaDB、MySQL 或 PostgreSQL 数据库服务器资源在过去7天内是否被广泛利用。 资源利用率较低会导致不需要的支出，而不会对性能产生重大影响。 为了降低成本并有效管理资源，建议将计算大小 (Vcore) 减一半。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>通过消除未设置的 ExpressRoute 线路来降低成本

顾问标识已处于 " **未预配** " 状态的 Azure ExpressRoute 线路超过一个月。 如果你不打算通过连接服务提供商来预配线路，则建议删除线路。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>通过删除或重新配置空闲虚拟网络网关来降低成本

顾问标识空闲时间超过90天的虚拟网络网关。 由于这些网关按小时计费，因此，如果不想再使用它们，则应考虑重新配置或删除它们。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>购买虚拟机预留实例可节省即用即付成本

顾问会在过去30天内检查虚拟机的使用情况，以确定是否可以通过购买 Azure 保留来节省资金。 顾问会向你显示最大可能节省的区域和大小，以及采购预订的预计节省量。 通过 Azure 预留，可以预先购买虚拟机的基本成本。 折扣会自动应用到与预留大小和区域相同的新的或现有的 Vm。 [深入了解 Azure 虚拟机预留实例。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

顾问还会通知你将在接下来的30天内到期的保留实例。 建议购买新的预订实例，以避免即用即付定价。

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>购买多个资源类型的保留实例以节省即用即付成本

顾问会在过去30天内分析以下资源的使用模式，并为优化成本提供预留的容量购买建议。

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB 保留的容量
顾问会在过去30天内分析 Azure Cosmos DB 使用模式，并建议预留的容量购买以优化成本。 通过使用预留容量，你可以预购买 Azure Cosmos DB 小时的使用情况，并通过即用即付成本保存。 预留容量是一项计费权益，并自动应用于新的和现有的部署。 顾问通过使用3年预订定价并推断过去30天内观测到的使用模式，来计算各个订阅的节省估计。 共享范围的建议适用于预留容量购买，并可提高节省费用。

### <a name="sql-paas-reserved-capacity"></a>SQL PaaS 预留容量
顾问在过去30天内分析 SQL PaaS 弹性数据库池和 SQL 托管实例使用模式。 然后，它建议保留的容量购买，以优化成本。 通过使用预留容量，你可以预购买 SQL DB 每小时使用情况并保存你的 SQL 计算成本。 你的 SQL 许可证将单独收费，且不会按预订折扣。 预留容量是一项计费权益，并自动应用于新的和现有的部署。 顾问通过使用3年预订定价并推断过去30天内观测到的使用模式，来计算各个订阅的节省估计。 共享范围的建议适用于预留容量购买，并可提高节省费用。

### <a name="app-service-stamp-fee-reserved-capacity"></a>应用服务戳记保留容量
顾问会在过去30天内分析 Azure App Service 隔离环境的戳记费使用模式，并建议预留容量购买，以优化成本。 通过使用预留容量，你可以为隔离的环境戳记收费预先购买每小时的使用量，并通过即用即付成本节省费用。 请注意，保留容量仅适用于邮票费，不适用于应用服务实例。 预留容量是一项计费权益，并自动应用于新的和现有的部署。 顾问会根据过去30天内的使用情况模式，使用3年保留价格计算各个订阅的节省估计值。

### <a name="blob-storage-reserved-capacity"></a>Blob 存储预留容量
顾问会分析你的 Azure Blob 存储，并 Azure Data Lake 过去30天内的存储使用情况。 然后计算用于优化成本的保留容量购买。 利用预留容量，你可以预购买每小时的使用情况，并按你当前按需的成本保存。 Blob 存储保留容量仅适用于 Azure Blob 常规用途 v2 和 Azure Data Lake Storage Gen2 帐户上存储的数据。 预留容量是一项计费权益，并自动应用于新的和现有的部署。 顾问使用3年保留价格和过去30天内观察到的使用模式来计算各个订阅的节省估计值。 共享范围的建议适用于预留容量购买，并可提高节省费用。

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB、MySQL 和 PostgreSQL 保留容量
顾问会分析过去30天内 Azure Database for MariaDB、Azure Database for MySQL 和 Azure Database for PostgreSQL 的使用模式。 然后，它建议保留的容量购买，以优化成本。 通过使用预留容量，你可以预购买 MariaDB、MySQL 和 PostgreSQL 每小时的使用情况，并节省当前成本。 预留容量是一项计费权益，并自动应用于新的和现有的部署。 顾问使用3年保留价格和过去30天内观察到的使用模式来计算各个订阅的节省估计值。 共享范围的建议适用于预留容量购买，并可提高节省费用。

### <a name="azure-synapse-analytics-formerly-sql-data-warehouse-reserved-capacity"></a>Azure Synapse Analytics (以前的 SQL 数据仓库) 保留容量
顾问会在过去30天内分析 Azure Synapse Analytics 使用模式，并建议使用保留容量购买来优化成本。 通过使用预留容量，你可以预购买 Synapse Analytics 每小时的使用情况，并按需成本保存。 预留容量是一项计费权益，并自动应用于新的和现有的部署。 顾问使用3年保留价格和过去30天内观察到的使用模式来计算各个订阅的节省估计值。 共享范围的建议适用于预留容量购买，并可提高节省费用。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>删除未关联的公共 IP 地址可节省资金

顾问标识与 Azure 资源（例如负载均衡器和 Vm）无关的公共 IP 地址。 名义费用与这些公共 IP 地址相关联。 如果你不打算使用它们，则可以通过删除它们来节省费用。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>删除将要发生故障的 Azure 数据工厂管道

顾问检测到重复失败的 Azure 数据工厂管道。 如果不需要，建议解决问题或删除管道。 即使这些管道发生故障，也会对这些管道进行计费。

## <a name="use-standard-snapshots-for-managed-disks"></a>使用托管磁盘的标准快照
为了节省60% 的成本，我们建议将快照存储在标准存储中，而不考虑父磁盘的存储类型。 此选项是托管磁盘快照的默认选项。 顾问标识存储在高级存储中的快照，并建议从高级存储迁移到标准存储。 [详细了解托管磁盘定价。](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>使用生命周期管理
通过使用有关 Azure Blob 存储对象计数、总大小和事务的智能，顾问将检测是否应启用生命周期管理，以便在一个或多个存储帐户上对数据进行分层。 它会提示创建生命周期管理规则，以自动将数据分层到超酷或存档存储，以优化存储成本，同时保留 Azure Blob 存储中的数据以实现应用程序兼容性。

## <a name="create-an-ephemeral-os-disk-recommendation"></a>创建临时 OS 磁盘的建议
[临时 OS 磁盘](../virtual-machines/ephemeral-os-disks.md) 允许执行以下操作： 
- 节省操作系统磁盘的存储成本。 
- 获取更低的操作系统磁盘读/写延迟。 
- 通过将 OS (和临时磁盘) 重置为其原始状态，获得更快的 VM 重置操作。

最好为生存期较短的 IaaS Vm 或具有无状态工作负荷的 Vm 使用临时 OS 磁盘。 顾问为可从暂时 OS 磁盘获益的资源提供建议。

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>减少 Azure 数据资源管理器表缓存- (策略) 用于群集成本优化 (预览) 
顾问标识了这样的资源：减少表缓存策略会释放 Azure 数据资源管理器群集节点的 CPU 使用率较低、内存和高速缓存大小配置较高。

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的成本建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在任意页面中搜索并选择[顾问](https://aka.ms/azureadvisordashboard)。

1. 在顾问仪表板中，选择“成本”选项卡 。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问高可用性建议](advisor-high-availability-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
