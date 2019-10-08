---
title: Azure SQL 数据库 - 常规用途和业务关键 | Microsoft Docs
description: 本文讨论基于 vCore 的购买模型中的“常规用途”和“业务关键”服务层级。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: 7b5fd9800fdd2ee3b46087308f81f506e3e09e03
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034956"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL 数据库服务层级

Azure SQL 数据库基于 SQL Server 数据库引擎体系结构，该体系结构已根据云环境做出调整，以确保即使在发生基础结构故障时，也仍能提供 99.99% 的可用性。 Azure SQL 数据库中使用了三个服务层级，每个服务层级具有不同的体系结构模型。 这些服务层级为：

- [常规用途](sql-database-service-tier-general-purpose.md)，适用于面向预算的工作负荷。
- [超大规模](sql-database-service-tier-hyperscale.md)，适用于大多数业务工作负荷，可提供高度可缩放的存储、读取和快速数据库还原功能。
- [业务关键](sql-database-service-tier-business-critical.md)，适用于具有高延迟的低延迟工作负荷，并可进行故障恢复和快速故障转移。

本文讨论了在基于 vCore 的购买模型中，对于常规用途和业务关键服务层，·补间的服务层、存储和备份注意事项。

## <a name="service-tier-comparison"></a>服务层级比较

下表介绍了最新一代（第 5 代）服务层级之间的主要差异。 请注意，单一数据库和托管实例中的服务层级特征可能会不同。

| | 资源类型 | 常规用途 |  超大规模 | 业务关键 |
|:---:|:---:|:---:|:---:|:---:|
| **最适用于** | |  提供以预算导向的、均衡的计算和存储选项。 | 大多数业务工作负荷。 自动缩放存储大小，最高可达 100 TB、流体垂直和水平计算缩放、快速数据库还原。 | 事务速率较高和 IO 延迟较低的 OLTP 应用程序。 使用多个同步更新的副本提供最高的故障恢复能力和快速故障转移。|
|  **在以下资源类型中可用：** ||单一数据库/弹性池/托管实例 | 单一数据库 | 单一数据库/弹性池/托管实例 |
| **计算大小**|单一数据库/弹性池 | 1 - 80 个 vCore | 1 - 80 个 vCore | 1 - 80 个 vCore |
| | 托管实例 | 4、8、16、24、32、40、64、80 个 vCore | 不可用 | 4、8、16、24、32、40、64、80 个 vCore |
| | 托管实例池 | 2，4，8，16，24，32，40，64，80 Vcore | 不可用 | 不可用 |
| **存储类型** | All | 高级远程存储（每个实例） | 具有本地 SSD 缓存的分离的存储（每个实例） | 超快的本地 SSD 存储（每个实例） |
| **数据库大小** | 单一数据库/弹性池 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 托管实例  | 32 GB – 8 TB | 不可用 | 32 GB – 4 TB |
| **存储大小** | 单一数据库/弹性池 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 托管实例  | 32 GB – 8 TB | 不可用 | 32 GB – 4 TB |
| **TempDB 大小** | 单一数据库/弹性池 | [每个 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) | [每个 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute) | [每个 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) |
| | 托管实例  | [每个 vCore 24 GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | 不可用 | 最大 4 TB - [受存储大小限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **日志写入吞吐量** | 单一数据库 | [每个 vCore 1.875 MB/秒（最大 30 MB/秒）](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) | 100 MB/秒 | [每个 vCore 6 MB/秒（最大 96 MB/秒）](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) |
| | 托管实例 | [每个 vCore 3 MB/秒（最大 22 MB/秒）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | 不可用 | [每个 vCore 4 MB/秒（最大 48 MB/秒）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**可用性**|All| 99.99% |  [99.95%（具有一个次要副本），99.99%（具有更多副本）](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [99.995%（具有区域冗余单一数据库）](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**备份**|All|RA-GRS，7-35 天（默认为 7 天）| RA-GRS，7 天，恒定的时间时点恢复 (PITR) | RA-GRS，7-35 天（默认为 7 天） |
|**内存中 OLTP** | | 不可用 | 不可用 | 可用 |
|**只读副本**| | 0  | 0 - 4 | 1（内置，包含在价格中） |
|**定价/计费** | 单一数据库 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>IOPS 不收取费用。 | [每个副本的 vCore 和已用存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>尚未收费的 IOPS。 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>IOPS 不收取费用。 |
|| 托管实例 | [vCore 和保留存储](https://azure.microsoft.com/pricing/details/sql-database/managed/)收费。 <br/>IOPS 不收取费用。<br/>备份存储尚不收费。 | 不可用 | [vCore 和保留存储](https://azure.microsoft.com/pricing/details/sql-database/managed/)收费。 <br/>IOPS 不收取费用。<br/>备份存储尚不收费。 | 
|**折扣模型**| | [预订实例](sql-database-reserved-capacity.md)<br/>[Azure 混合权益](sql-database-service-tiers-vcore.md#azure-hybrid-benefit)（不适用于开发/测试订阅）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和即[用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅| [Azure 混合权益](sql-database-service-tiers-vcore.md#azure-hybrid-benefit)（不适用于开发/测试订阅）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和即[用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅| [预订实例](sql-database-reserved-capacity.md)<br/>[Azure 混合权益](sql-database-service-tiers-vcore.md#azure-hybrid-benefit)（不适用于开发/测试订阅）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和即[用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅|

有关详细信息，请参阅[单一数据库 (vCore)](sql-database-vcore-resource-limits-single-databases.md)、[单一数据库池 (vCore)](sql-database-dtu-resource-limits-single-databases.md)、[单一数据库 (DTU)](sql-database-dtu-resource-limits-single-databases.md)、[单一数据库池 (DTU)](sql-database-dtu-resource-limits-single-databases.md)和[托管实例](sql-database-managed-instance-resource-limits.md)页中服务层级之间的详细差异。

> [!NOTE]
> 若要了解基于 vCore 的购买模型中的“超大规模”服务层级，请参阅[“超大规模”服务层级](sql-database-service-tier-hyperscale.md)。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>数据和日志存储

以下因素会影响用于数据文件和日志文件的存储量，并适用于常规用途和业务关键。 有关超大规模中的数据和日志存储的详细信息，请参阅[超大规模服务层](sql-database-service-tier-hyperscale.md)。

- 分配的存储由数据文件 (MDF) 和日志文件 (LDF) 使用。
- 每个单一数据库计算大小支持一个最大数据库大小，默认最大大小为 32 GB。
- 配置所需的单一数据库大小（MDF 文件大小）时，系统会自动额外添加 30% 的存储来支持 LDF 文件。
- SQL 数据库托管实例的存储大小必须指定为 32 GB 的倍数。
- 可以选择介于 10 GB 与受支持最大值之间的任何单一数据库大小。
  - 对于“标准”或“常规用途”服务层级中的存储，按 10 GB 增量增减大小。
  - 对于“高级”或“业务关键”服务层级中的存储，按 250 GB 增量增减大小。
- 在“常规用途”服务层级中，`tempdb` 使用附加的 SSD，此存储成本包含在 vCore 价格中。
- 在“业务关键”服务层级中，`tempdb` 与 MDF 和 LDF 文件共享附加的 SSD，`tempdb` 存储成本已包含在 vCore 价格中。

> [!IMPORTANT]
> 需要支付分配给 MDF 和 LDF 文件的总存储费用。

若要监视 MDF 和 LDF 文件的当前总大小，请使用 [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要监视单个 MDF 和 LDF 文件的当前大小，请使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>备份和存储

为数据库备份分配存储，以支持 SQL 数据库的时间点还原 (PITR) 和[长期保留 (LTR)](sql-database-long-term-retention.md) 功能。 此存储空间针对每个数据库单独分配，并根据数据库费用分开计费。

- **PITR**：自动将各个数据库备份复制到[读取访问权限异地冗余存储 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 创建新备份时，存储大小动态递增。 存储由每周完整备份、每日差异备份和 5 分钟复制一次的事务日志备份使用。 存储消耗量取决于数据库变化率和备份保留期。 可单独为每个数据库配置 7 到 35 天的保留期。 提供与 100%（1 倍）数据库大小相等的最小存储量，不收取额外费用。 对于大多数数据库而言，此容量足以将备份存储 7 天。
- **LTR**：SQL 数据库提供相应的选项，用于将完整备份的长期保留期配置为最多 10 年。 如果设置了 LTR 策略，则这些备份将自动存储在 RA-GRS 存储中，但你可以控制备份的复制频率。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 所选配置决定了多少存储将用于 LTR 备份。 若要估算 LTR 存储成本，可以使用 LTR 定价计算器。 有关详细信息，请参阅 [SQL 数据库长期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>后续步骤

- 若要详细了解适用于“常规用途”和“业务关键”服务层级中的单一数据库的特定计算大小和存储大小，请参阅[适用于单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 若要详细了解适用于“常规用途”和“业务关键”服务层级中的弹性池的特定计算大小和存储大小，请参阅[适用于弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
