---
title: Azure SQL 数据库服务 - vCore | Microsoft 文档
description: 使用基于 vCore 的购买模型，可以单独缩放计算和存储资源，匹配本地性能，以及优化价格。
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/30/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 8266d9e3530969154ac9c8c877badda9f8b4fed3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307266"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>选择 vCore 服务层、计算、内存、存储和 IO 资源

使用基于 vCore 的购买模型，可以单独缩放计算和存储资源，匹配本地性能，以及优化价格。 它还允许你选择硬件世代：
- 第 4 代 - 最多 24 个基于 Intel E5-2673 v3 (Haswell) 2.4 GHz 处理器的逻辑 CPU，vCore = 1 PP（物理核心），每核心 7 GB，附加了 SSD
- 第 5 代 - 最多 80 个基于 Intel E5-2673 v4 (Broadwell) 2.3 GHz 处理器的逻辑 CPU，vCore=1 LP（超线程），每个核心 5.1。 GB，快速 eNVM SSD

vCore 模式还允许使用[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)来节省成本。

## <a name="service-tier-characteristics"></a>服务层特征

vCore 模型提供了两个服务层：常规用途和业务关键。 服务层根据一系列性能级别、高可用性设计、故障隔离、存储类型和 IO 范围进行区分。 客户必须单独配置所需的存储和备份保留期。

下表可帮助你了解这两个层之间的差别：

||**常规用途**|**业务关键**|
|---|---|---|
|最适用于|大多数业务工作负荷。 提供预算导向的、均衡且可缩放的计算和存储选项。|IO 要求高的业务应用程序。 使用多个独立副本，提供最高级别的故障恢复能力。|
|计算|第 4 代：1 到 24 个 vCore<br/>第 5 代：1 到 80 个 vCore|第 4 代：1 到 24 个 vCore<br/>第 5 代：1 到 80 个 vCore|
|内存|Gen4：每个核心 7 GB<br>第 5 代：每个核心 5.1 GB | Gen4：每个核心 7 GB<br>第 5 代：每个核心 5.1 GB |
|存储|[高级远程存储](../virtual-machines/windows/premium-storage.md)，<br/>单一数据库：5 GB – 4 TB<br/>托管实例：32 GB - 8 TB |本地 SSD 存储，<br/>单一数据库：5 GB - 1 TB<br/>托管实例：32 GB - 4 TB |
|IO 吞吐量（近似）|单一数据库：每个 vCore 提供 500 IOPS，最大 7000 IOPS</br>托管实例：取决于[文件大小](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|每个 vCore 提供 5000 IOPS，最大 200000 IOPS|
|可用性|1 个副本，无读取缩放组|3 个副本，1 个[读取缩放副本](sql-database-read-scale-out.md)，<br/>区域冗余 HA|
|备份|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（默认为 7 天）|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（默认为 7 天）|
|内存中|不适用|支持|
|||

有关详细信息，请参阅[单一实例数据库中的 vCore 资源限制](sql-database-vcore-resource-limits-single-databases.md)和[托管实例中的 vCore 资源限制](sql-database-managed-instance.md#vcore-based-purchasing-model)。 

> [!IMPORTANT]
> 如果所需的计算容量 vCore 数不超过一个，请使用基于 DTU 的购买模型。

有关常见问题的解答，请参阅 [SQL 数据库常见问题解答](sql-database-faq.md)。 

## <a name="storage-considerations"></a>存储注意事项

请注意以下几点：
- 分配的存储由数据文件 (MDF) 和日志文件 (LDF) 使用。
- 每个单一实例数据库性能级别支持一个最大数据库大小，默认最大大小为 32 GB。
- 配置所需的单一数据库大小（MDF 大小）时，系统会自动额外添加 30% 的存储来支持 LDF
- 托管实例中的存储大小必须指定为 32 GB 的倍数。
- 可以选择介于 10 GB 与受支持最大值之间的任何单一实例数据库大小
 - 对于标准存储，可以按 10 GB 的增量增大或减小大小
 - 对于高级存储，可以按 250 GB 的增量增大或减小大小
- 在“常规用途”服务层中，`tempdb` 使用附加的 SSD，此存储成本包含在 vCore 价格中。
- 在“业务关键”服务层中，`tempdb` 与 MDF 和 LDF 文件共享附加的 SSD，tempDB 存储成本已包含在 vCore 价格中。

> [!IMPORTANT]
> 需要支付分配给 MDF 和 LDF 的总存储费用。

若要监视 MDF 和 LDF 的当前总大小，请使用 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要监视单个 MDF 和 LDF 文件的当前大小，请使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>备份和存储

为数据库备份分配存储，以支持 SQL 数据库的时间点还原 (PITR) 和[长期保留 (LTR)](sql-database-long-term-retention.md) 功能。 此存储空间针对每个数据库单独分配，并根据数据库费用分开计费。 

- **PITR**：自动将各个数据库备份复制到 [RA-GRS 存储](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 创建新备份时，存储大小动态递增。  存储由每周完整备份、每日差异备份和 5 分钟复制一次的事务日志备份使用。 存储消耗量取决于数据库变化率和保留期。 可单独为每个数据库配置 7 到 35 天的保留期。 提供与 1 倍数据库大小相等的最小存储量，不收取额外费用。 对于大多数数据库而言，此容量足以将备份存储 7 天。
- **LTR**：SQL 数据库提供相应的选项用于将完整备份的长期保留期配置为最多 10 年。 如果启用了 LTR 策略，则这些备份将自动存储在 RA-GRS 存储中，但你可以控制复制备份的频率。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 此配置将定义要为 LTR 备份使用多少存储。 可以使用 LTR 定价计算器来估算 LTR 存储成本。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

## <a name="azure-hybrid-use-benefit"></a>Azure 混合使用权益

在基于 vCore 的购买模型中，可以使用[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)交换现有许可证，以获得 SQL 数据库的折扣价格。 借助这项 Azure 权益，可以使用附带软件保障的本地 SQL Server 许可证，将 Azure SQL 数据库的成本最多节省 30%。

![定价](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>从 DTU 模型迁移到 vCore 模型

### <a name="migration-of-single-databases-with-geo-replication-links"></a>使用异地复制链接迁移单一数据库

从基于 DTU 的模型迁移到基于 vCore 的模型类似于在标准和高级数据库之间升级或降级异地复制关系。 这不需要终止异地复制，但用户必须遵守顺序规则。 升级时，必须先升级辅助数据库，再升级主数据库。 降级时，必须反转顺序：先降级主数据库，再降级辅助数据库。 

在两个弹性池之间使用异地复制时，建议将一个池指定为主池，将另一个池指定为辅助池。 在这种情况下，迁移弹性池应遵循相同的指导原则。  但是，从技术上讲，弹性池可能包含主数据库和辅助数据库。 在这种情况下，若要正确迁移，应将利用率较高的池视为主池，并相应地遵循顺序规则。  

下表提供具体迁移场景的指导： 

|当前服务层|目标服务层|迁移类型|用户操作|
|---|---|---|---|
|标准|常规用途|横向|可按任意顺序迁移，但需确保 vCore 大小适当*|
|高级|业务关键|横向|可按任意顺序迁移，但需确保 vCore 大小适当*|
|标准|业务关键|升级|必须先迁移辅助数据库|
|业务关键|标准|降级|必须先迁移主数据库|
|高级|常规用途|降级|必须先迁移主数据库|
|常规用途|高级|升级|必须先迁移辅助数据库|
|业务关键|常规用途|降级|必须先迁移主数据库|
|常规用途|业务关键|升级|必须先迁移辅助数据库|
||||

\* 标准层中的每 100 个 DTU 至少需要 1 个 vCore，高级层中的每 125 个 DTU 至少需要 1 个 vCore

### <a name="migration-of-failover-groups"></a>迁移故障转移组 

迁移包含多个数据库的故障转移组需要单独迁移主数据库和辅助数据库。 在此过程中，请遵循相同的注意事项和顺序规则。 将数据库转换到基于 vCore 的模型后，故障转移组将保持有效并使用相同的策略设置。 

### <a name="creation-of-a-geo-replication-secondary"></a>创建异地复制辅助数据库

只能使用与主数据库相同的服务层来创建异地辅助数据库。 对于日志生成速率较高的数据库，建议使用与主数据库相同的性能级别来创建辅助数据库。 如果在弹性池中为单个主数据库创建异地辅助数据库，建议对该池使用与主数据库性能级别匹配的 `maxVCore` 设置。 如果在弹性池中为另一个弹性池的主数据库创建异地辅助数据库，建议对该池使用相同的 `maxVCore` 设置

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>使用数据库复制将基于 DTU 的数据库转换到基于 vCore 的数据库。

可将采用基于 DTU 的性能级别的任何数据库复制到采用基于 vCore 的性能级别的数据库，且无需遵守上述限制或特殊的顺序，前提是目标性能级别支持源数据库的最大数据库大小。 这是因为，数据库复制操作在启动时会创建数据快照，而不会在源与目标之间执行数据同步。 

## <a name="next-steps"></a>后续步骤

- 有关适用于单一数据库的特定性能级别和存储大小选项的详细信息，请参阅[适用于单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- 有关适用于弹性池的特定性能级别和存储大小选项的详细信息，请参阅[适用于弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels)。
