---
title: 常规用途和业务关键
description: 本文介绍了基于 vCore 的购买模型中的常规用途和业务关键服务层。
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
ms.openlocfilehash: 5e3cc12351313b8fb1dedf795031202070ac7cf7
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558987"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL 数据库服务层级

Azure SQL 数据库基于 SQL Server 数据库引擎体系结构，针对云环境进行调整，以确保99.99% 的可用性，即使存在基础结构故障也是如此。 在 Azure SQL 数据库中使用三个服务层，每个层都有不同的体系结构模型。 这些服务层级为：

- [常规用途](sql-database-service-tier-general-purpose.md)，适用于面向预算的工作负荷。
- [超大规模](sql-database-service-tier-hyperscale.md)，适用于大多数业务工作负荷，可提供高度可缩放的存储、读取和快速数据库还原功能。
- [业务关键](sql-database-service-tier-business-critical.md)，适用于具有高延迟的低延迟工作负荷，并可进行故障恢复和快速故障转移。

本文讨论了在基于 vCore 的购买模型中，对于常规用途和业务关键服务层，·补间的服务层、存储和备份注意事项。

## <a name="service-tier-comparison"></a>服务层比较

下表介绍了最新一代服务层（Gen5）之间的主要差异。 请注意，单一数据库和托管实例中的服务层特征可能有所不同。

| | 资源类型 | 常规用途 |  超大规模 | 业务关键 |
|:---:|:---:|:---:|:---:|:---:|
| **最适用于** | |  提供以预算导向的、均衡的计算和存储选项。 | 大多数业务工作负荷。 自动缩放存储大小，最高可达 100 TB、流体垂直和水平计算缩放、快速数据库还原。 | 事务速率较高和 IO 延迟较低的 OLTP 应用程序。 使用多个同步更新的副本提供最高的故障恢复能力和快速故障转移。|
|  **资源类型中提供：** ||单一数据库/弹性池/托管实例 | 单个数据库 | 单一数据库/弹性池/托管实例 |
| **计算大小**|单一数据库/弹性池 | 1 - 80 个 vCore | 1到 80 Vcore | 1 - 80 个 vCore |
| | 托管实例 | 4、8、16、24、32、40、64、80 Vcore | N/A | 4、8、16、24、32、40、64、80 Vcore |
| | 托管实例池 | 2，4，8，16，24，32，40，64，80 Vcore | N/A | N/A |
| **存储类型** | 所有 | 高级远程存储（每个实例） | 具有本地 SSD 缓存的分离的存储（每个实例） | 超快的本地 SSD 存储（每个实例） |
| **数据库大小** | 单一数据库/弹性池 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 托管实例  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **存储大小** | 单一数据库/弹性池 | 5 GB – 4 TB | 最多 100 TB | 5 GB – 4 TB |
| | 托管实例  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **TempDB 大小** | 单一数据库/弹性池 | [每 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [每 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [每 vCore 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 托管实例  | [每 vCore 24 GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | 最大 4 TB-受[存储大小限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **日志写入吞吐量** | 单个数据库 | [每 vCore 1.875 MB/秒（最多 30 MB/秒）](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/秒 | [每个 vCore 6 MB/秒（最大 96 MB/秒）](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | 托管实例 | [每个 vCore 3 MB/秒（最多 22 MB/秒）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/A | [每个 vcore 4 MB/s （最大 48 MB/秒）](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**可用性**|所有| 99.99% |  [99.95%，具有一个辅助副本，99.99%，具有更多副本](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [具有区域冗余单一数据库的99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**备份**|所有|RA-GRS，7-35 天（默认为 7 天）| GRS，7天，固定时间点恢复（PITR） | RA-GRS，7-35 天（默认为 7 天） |
|**内存中 OLTP** | | N/A | N/A | 可用 |
|**只读副本**| | 0  | 0 - 4 | 1（内置，包含在价格中） |
|**定价/计费** | 单个数据库 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>IOPS 不收取费用。 | [为每个副本和使用的存储收取 vCore](https://azure.microsoft.com/pricing/details/sql-database/single/) 。 <br/>尚未收费的 IOPS。 | [vCore、保留存储和备份存储](https://azure.microsoft.com/pricing/details/sql-database/single/)收费。 <br/>IOPS 不收取费用。 |
|| 托管实例 | [vCore 和预留存储](https://azure.microsoft.com/pricing/details/sql-database/managed/)收费。 <br/>IOPS 不收取费用。<br/>备份存储尚未收费。 | N/A | [vCore 和预留存储](https://azure.microsoft.com/pricing/details/sql-database/managed/)收费。 <br/>IOPS 不收取费用。<br/>备份存储尚未收费。 | 
|**折扣模型**| | [预订实例](sql-database-reserved-capacity.md)<br/>[Azure 混合权益](sql-database-azure-hybrid-benefit.md)（不适用于开发/测试订阅）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和即[用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅| [Azure 混合权益](sql-database-azure-hybrid-benefit.md)（不适用于开发/测试订阅）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和即[用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅| [预订实例](sql-database-reserved-capacity.md)<br/>[Azure 混合权益](sql-database-azure-hybrid-benefit.md)（不适用于开发/测试订阅）<br/>[企业](https://azure.microsoft.com/offers/ms-azr-0148p/)和即[用即付](https://azure.microsoft.com/offers/ms-azr-0023p/)开发/测试订阅|

有关详细信息，请参阅单一数据库中的服务层[（vCore）](sql-database-vcore-resource-limits-single-databases.md)、[单一数据库池（vCore）](sql-database-dtu-resource-limits-single-databases.md)、[单一数据库（dtu）](sql-database-dtu-resource-limits-single-databases.md)、[单一数据库池（dtu）](sql-database-dtu-resource-limits-single-databases.md)和[托管实例](sql-database-managed-instance-resource-limits.md)页面之间的详细区别。

> [!NOTE]
> 有关基于 vCore 的购买模型中超大规模服务层的信息，请参阅[超大规模服务层](sql-database-service-tier-hyperscale.md)。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>数据和日志存储

以下因素会影响用于数据文件和日志文件的存储量，并适用于常规用途和业务关键。 有关超大规模中的数据和日志存储的详细信息，请参阅[超大规模服务层](sql-database-service-tier-hyperscale.md)。

- 已分配的存储由数据文件（MDF）和日志文件（.LDF）使用。
- 每个数据库计算大小都支持最大数据库大小，默认的最大大小为 32 GB。
- 配置所需的单一数据库大小（MDF 文件的大小）时，将自动添加额外的额外存储空间以支持 LDF 文件。
- SQL 数据库托管实例的存储大小必须以 32 GB 的倍数指定。
- 您可以选择 10 GB 到最大支持值之间的任意单一数据库大小。
  - 对于标准或常规用途服务层中的存储，以 10 GB 为增量增加或减少大小。
  - 对于 "高级" 或 "业务关键" 服务层中的存储，增加或减少 250 MB 的增量。
- 在常规用途的服务层中，`tempdb` 使用附加的 SSD，此存储成本包括在 vCore 价格中。
- 在 "业务关键" 服务层中，`tempdb` 与 MDF 和 LDF 文件共享附加的 SSD，而 `tempdb` 存储成本包含在 vCore 价格中。

> [!IMPORTANT]
> 需要为 MDF 和 LDF 文件分配的总存储收费。

若要监视你的 MDF 和 LDF 文件的当前总大小，请使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要监视单个 MDF 和 LDF 文件的当前大小，请使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>备份和存储

为数据库备份分配存储，以支持 SQL 数据库的时间点还原（PITR）和[长期保留（LTR）](sql-database-long-term-retention.md)功能。 为每个数据库单独分配此存储，并作为两个单独的每个数据库费用进行计费。

- **PITR**：自动将单个数据库备份复制到[读取访问地域冗余（RA-GRS）存储](../storage/common/storage-designing-ha-apps-with-ragrs.md)。 创建新的备份时，存储大小会动态增加。 存储由每周完整备份、每日差异备份和事务日志备份使用，每5分钟复制一次。 存储占用量取决于数据库的更改速率和备份的保持期。 可单独为每个数据库配置 7 到 35 天的保留期。 仅提供等于100% （1x）的数据库大小的最小存储量，无需额外付费。 对于大多数数据库而言，此容量足以将备份存储 7 天。
- **LTR**：通过 SQL 数据库，你可以选择将完整备份的长期保留配置为长达10年。 如果设置了 LTR 策略，则这些备份会自动存储在 GRS 存储中，但你可以控制复制备份的频率。 为了满足不同的符合性要求，你可以为每周、每月和/或每年备份选择不同的保留期。 你选择的配置确定将用于 LTR 备份的存储量。 若要估算 LTR 存储的开销，可以使用 LTR 定价计算器。 有关详细信息，请参阅[SQL 数据库长期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>后续步骤

- 有关 "常规用途" 和 "业务关键" 服务层中的单一数据库可用的特定计算大小和存储大小的详细信息，请参阅[针对单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关 "常规用途" 和 "业务关键" 服务层中的弹性池可用的特定计算大小和存储大小的详细信息，请参阅[针对弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
