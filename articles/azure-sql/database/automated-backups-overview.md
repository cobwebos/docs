---
title: 自动异地冗余备份
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL 数据库和 Azure SQL 托管实例每隔几分钟会自动创建一个本地数据库备份，并使用 Azure 读取访问异地冗余存储来提供异地冗余。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 10/05/2020
ms.openlocfilehash: 43551ca17180cbb3614c670490a19aeaae6c7701
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728652"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自动备份 - Azure SQL 数据库和 SQL 托管实例

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>什么是数据库备份？

数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭损坏或删除。 这些备份可在配置的保留期内将数据库还原到某个时间点。 如果数据保护规则要求备份在较长时间（最长 10 年）内可用，可以同时为单一数据库和共用数据库配置[长期保留](long-term-retention-overview.md)。

### <a name="backup-frequency"></a>备份频率

SQL 数据库和 SQL 托管实例都使用 SQL Server 技术，每周创建[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每 12-24 小时创建[差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每 5 到 10 分钟创建[事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 事务日志备份的频率取决于计算大小和数据库活动量。

还原数据库时，服务会确定需要还原哪些完整备份、差异备份和事务日志备份。

### <a name="backup-storage-redundancy"></a>备份存储冗余

默认情况下，SQL 数据库和 SQL 托管实例将数据存储在复制到[配对区域](../../best-practices-availability-paired-regions.md)的异地冗余 (RA-GRS) [存储 Blob](../../storage/common/storage-redundancy.md) 中。 这有助于防止影响主区域备份存储的中断，并且你可在发生灾难时将服务器还原到其他区域。 

配置备份存储冗余的选项可让你灵活地在 SQL 托管实例或 SQL 数据库的本地冗余、区域冗余或异地冗余存储 blob 之间进行选择。 若要确保你的数据在部署托管实例或 SQL 数据库的同一区域内，可以更改默认异地冗余备份存储冗余，并为备份配置本地冗余 (LRS) 或区域冗余 (ZRS) 存储 blob。 Azure 存储冗余机制会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 已配置的备份存储冗余适用于用于时间点还原的短期备份保留设置 (PITR) ，以及用于长期备份的长期保留备份 (LTR) 。 

对于 SQL 数据库，可以在创建数据库时配置备份存储冗余，也可以针对现有数据库进行更新。对现有数据库所做的更改仅适用于将来的备份。 更新现有数据库的备份存储冗余后，最多可能需要48小时才能应用所做的更改。 请注意，在将数据库更新为使用本地或区域冗余存储时，将立即禁用异地还原。 


> [!IMPORTANT]
> 请在托管实例创建过程中配置备份存储冗余，因为预配资源后无法再更改存储冗余。 

> [!IMPORTANT]
> 区域冗余存储目前仅在 [特定区域](../../storage/common/storage-redundancy.md#zone-redundant-storage)中可用。 

> [!NOTE]
> 适用于 Azure SQL 数据库的可配置备份存储冗余目前仅在东南亚 Azure 区域的公共预览版中提供。 此功能尚不可用于超大规模层。 

### <a name="backup-usage"></a>备份使用情况

可使用这些备份：

- 现有数据库的时间点还原 - 通过使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API，[将现有的数据库还原到过去的某个时间点](recovery-using-backups.md#point-in-time-restore)。 对于 SQL 数据库，此操作会在与原始数据库相同的服务器上创建新的数据库，但会使用不同的名称，以避免覆盖原始数据库。 还原完成后，可删除原始数据库。 另外，可以[重命名](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database)还原的数据库，然后使其具有原始数据库的名称。 同样，对于 SQL 托管实例，此操作可以在同一订阅和同一区域中的相同或不同的托管实例上创建数据库地副本。
- 删除的数据库的时间点还原 - [将已删除的数据库还原到删除时间](recovery-using-backups.md#deleted-database-restore)或者还原到保留期内的任意时间点。 仅可在创建原始数据库所在的同一服务器或托管实例上还原已删除的数据库。 删除数据库时，该服务会在删除前执行最终事务日志备份，以防止任何数据丢失。
- 异地还原 - [将数据库还原到其他地理区域](recovery-using-backups.md#geo-restore)。 在无法访问主要区域中的数据库和备份时，异地还原可帮助从地理位置灾难中恢复。 它可在任何 Azure 区域中的任意现有服务器或托管实例上创建新的数据库。
   > [!IMPORTANT]
   > 异地还原仅适用于配置了异地冗余备份存储的 SQL 数据库或托管实例。
- 从长期备份保留 - 如果为数据库配置了长期保留策略 (LTR)，可从单一数据库或共用数据库的[某个特定长期备份来还原数据库](long-term-retention-overview.md)。 LTR 允许使用 [Azure 门户](long-term-backup-retention-configure.md#using-the-azure-portal)或 [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) 还原旧版本的数据库，以满足合规性请求或运行旧版本的应用程序。 有关详细信息，请参阅 [长期保留](long-term-retention-overview.md)。

若要执行还原，请参阅[从备份还原数据库](recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语“复制”指将 blob 从一个位置复制到另一个位置。 在 SQL 中，“数据库复制”指用于让多个辅助数据库与主数据库保持同步的各种技术。

可以参考下列示例尝试执行备份配置和还原操作：

| 操作 | Azure 门户 | Azure PowerShell |
|---|---|---|
| **更改备份保留** | [SQL 数据库](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL 托管实例](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL 数据库](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL 托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **更改长期备份保留** | [SQL 数据库](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL 托管实例 - N/A  | [SQL 数据库](long-term-backup-retention-configure.md)<br/>[SQL 托管实例](../managed-instance/long-term-backup-retention-configure.md)  |
| **从某个时间点还原数据库** | [SQL 数据库](recovery-using-backups.md#point-in-time-restore)<br>[SQL 托管实例](../managed-instance/point-in-time-restore.md) | [SQL 数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL 托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **还原已删除的数据库** | [SQL 数据库](recovery-using-backups.md)<br>[SQL 托管实例](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL 数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL 托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **从 Azure Blob 存储还原数据库** | SQL 数据库 - N/A <br/>SQL 托管实例 - N/A  | SQL 数据库 - N/A <br/>[SQL 托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>备份计划

会在新的数据库创建或还原后立即计划第一次完整备份。 此备份通常可在 30 分钟内完成，但如果数据库较大，花费的时间可能更长。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间，该备份通常比新数据库还大。 在完成首次完整备份后，会自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库或 SQL 托管实例服务会确定所有数据库备份的确切时间。 不能更改备份作业的计划或者禁用备份作业。

> [!IMPORTANT]
> 对于新的、还原的或复制的数据库，从初始完整备份之后创建了初始事务日志备份时开始，可以使用时间点还原功能。

## <a name="backup-storage-consumption"></a>备份存储消耗量

如果使用 SQL Server 备份和还原技术，将数据库还原到某个时间点需要一个不间断的备份链，其中包括一个完整备份、可选的一个差异备份以及一个或多个事务日志备份。 SQL 数据库和 SQL 托管实例备份计划包括每周执行一次完整备份。 因此，要在整个保留期内启用 PITR，系统需要额外存储完整备份、差异备份和事务日志备份，且存储时间最多比配置的保留期长一周。 

换句话说，对于保留期内的任何时间点，都需要具有早于保留期最早时间的完整备份，以及从该完整备份到下一次完整备份期前的差异备份和事务日志备份的不间断备份链。

> [!NOTE]
> 要启用 PITR，额外备份的存储时间最多比配置的保留期长一周。 备份存储按与所有备份相同的费率收费。 

将自动删除不再需要为提供 PITR 功能而保留的备份。 由于差异备份和日志备份需要早期的完整备份才可恢复，因此所有这三种备份类型会在每周都一并清除一次。

对于包括 [TDE 加密](transparent-data-encryption-tde-overview.md)数据库在内的所有数据库，将压缩备份，以减少未来的备份存储压缩需要和成本。 平均备份压缩率为 3-4 倍，但是，根据数据的性质以及数据库中是否启用了数据压缩，平均备份压缩率可能会显著降低或提高。

SQL 数据库和 SQL 托管实例按累积值形式计算使用的总备份存储量。 此值每隔一小时就会报告给 Azure 计费管道，该管道负责聚合此每小时用量，以便在每月底计算消耗量。 删除数据后，随着备份的过期以及删除，消耗量将减少。 删除所有备份后，PITR 不再可用，计费会停止。
   
> [!IMPORTANT]
> 即使已删除数据库，为了使用 PITR，仍会保留数据库的备份。 删除和重建数据库可能会节省存储和计算成本，但可能会增加备份存储成本，因为该服务在每次删除数据库都时会保留每个已删除数据库的备份。 

### <a name="monitor-consumption"></a>监视消耗情况

对于 vCore 数据库，每种类型的备份（完整备份、差异备份和日志备份）所使用的存储量采用单独的指标在数据库监视边栏选项卡上进行报告。 下图显示了如何监视单一数据库的备份存储消耗情况。 此功能目前不适用于托管实例。

![在 Azure 门户中监视数据库备份消耗量](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微调备份存储消耗量

只要备份存储消耗量不超过数据库的最大数据大小，就不会对备份收费。 额外的备份存储消耗量取决于各个数据库的工作负荷和最大大小。 考虑实施下面一些优化技术，以减少备份存储消耗量：

- 将[备份保留期](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)减少到所需的最小值。
- 避免以超过需要的频率执行大型写入操作，例如索引重建。
- 对于大规模数据加载操作，请考虑使用[聚集列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview)以及下列相关[最佳做法](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，和/或减少非聚集索引的数目。
- 在常规用途服务层级中，预配数据存储的价格低于备份存储的价格。 如果额外备份存储成本一直较高，可以考虑增大数据存储，以便节省备份存储的费用。
- 在应用程序逻辑中使用 TempDB 而不是永久性表来存储临时结果和/或暂时性数据。
- 尽可能使用本地冗余备份存储（例如开发/测试环境）

## <a name="backup-retention"></a>备份保留

对于所有新的、还原和复制的数据库，Azure SQL 数据库和 Azure SQL 托管实例会默认保留足以实现过去 7 天的 PITR 的备份量。 除了超大规模数据库之外，可以按每个活动数据库[更改备份保持期](#change-the-pitr-backup-retention-period)，更改幅度可为 1-35 天。 如[备份存储消耗量](#backup-storage-consumption)中所述，为启用 PITR 而存储的备份可能早于保留期。 仅对于 Azure SQL 托管实例，在 0-35 天范围内删除了数据库后，可以设置 PITR 备份保留率。 

如果删除数据库，系统会保留数据库的备份至特定的保留期，与为任何一个联机数据库的保留方式一样。 不能更改已删除的数据库的备份保留期。

> [!IMPORTANT]
> 如果删除服务器或托管实例，则该服务器或该托管实例上所有的数据库也会删除，且无法恢复。 无法还原已删除的服务器或托管实例。 但是，如果为数据库或托管实例配置了长期保留 (LTR)，则不会删除长期保留备份，并且可以使用它在同一订阅的另一个服务器或托管实例上将数据库还原到执行长期保留备份的时间点。

出于 PITR 目的，过去 1-35 天内的备份保留有时称为短期备份保留。 如果需要将备份保留至超过 35 天的最长短期保留期，可以启用[长期保留](long-term-retention-overview.md)。

### <a name="long-term-retention"></a>长期保留

对于 SQL 数据库 SQL 托管实例，可以配置完整备份的长期保留 (LTR)，使其在 Azure Blob 存储中最长保存 10 年。 配置启用 LTR 策略后，每周完整备份将自动复制到不同的存储容器。 为了满足不同的合规性要求，可为每周、每月和/或每年完整备份选择不同的保留期。 存储消耗量取决于所选的频率和 LTR 备份的保留期。 可以使用 [LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储成本。

> [!IMPORTANT]
> 更新现有 Azure SQL 数据库的备份存储冗余，只适用于为数据库执行的未来备份。 数据库的所有现有 LTR 备份都将继续驻留在现有的存储 blob 中，新的备份将存储在请求的存储 blob 类型上。 

有关 LTR 的详细信息，请参阅[长期备份保留](long-term-retention-overview.md)。

## <a name="storage-costs"></a>存储费用

备份存储的价格取决于购买模型（DTU 或 vCore）、选择的备份存储冗余选项以及区域。 备份存储按每月使用的 GB 数计费，有关定价的信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)页和 [Azure SQL 托管实例定价](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)页。

### <a name="dtu-model"></a>DTU 模型

在 DTU 模型中，数据库和弹性池不会产生额外的备份存储费用。 备份存储的价格构成数据库或池价格的一部分。

### <a name="vcore-model"></a>vCore 模型

对于 SQL 数据库中的单一数据库，会提供与数据库最大数据存储大小 100% 相等的备份存储量，不收取额外的费用。 对于弹性池和托管实例，会分别提供与池的最大数据存储量或最大实例存储大小 100% 相等的备份存储量，不收取额外的费用。 

对于单一数据库，可使用以下公式来计算可计费的备份存储总用量：

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

对于共用数据库，可计费的备份存储总大小在池级别进行聚合，计算方式如下：

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

对于托管实例，可计费备份存储总大小在实例级别进行聚合，计算方式如下：

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

可计费备份存储总计将按每月使用的备份存储的速率按 GB/月收费。 备份存储消耗量取决于各个数据库、弹性池以及托管实例的工作负荷和大小。 经过大量修改的数据库具有相对较大的差异备份和日志备份，因为这些备份的大小与数据更改量成比例。 因此，此类数据库的备份费用会更高。

SQL 数据库和 SQL 托管实例按累积值形式计算所有备份文件的总可计费备份存储量。 此值每隔一小时就会报告给 Azure 计费管道，该管道聚合此每小时用量，以便在每月底获取备份存储消耗量。 如果删除数据库，备份存储消耗量将逐渐降低，因为较早的备份会过期并被删除。 由于差异备份和日志备份需要早期的完整备份才可恢复，因此所有这三种备份类型会在每周都一并清除一次。 当所有备份都被删除后，计费会停止。 

举一个简化的示例，假设数据库累积了 744 GB 的备份存储，并且此数量因数据库完全空闲会在整个月内保持恒定。 若要将此累积存储消耗量转换为每小时用量，可将此数量除以 744.0（每月 31 天 * 每天 24 小时）。 SQL 数据库将以固定速率向 Azure 计费管道报告数据库每小时使用了 1 GB 的 PITR 备份。 Azure 计费服务将聚合此消耗量，并显示整月用量为 744 GB。 成本基于所在区域的用量/GB/月费率。

下面是一个更复杂的示例。 假设同一空闲数据库的保留期在当月的中途从 7 天增加到了 14 天。 这种增长会导致总备份存储翻倍至 1,488 GB。 SQL 数据库将报告第 1 到 372 小时（上半月）的用量为每小时 1 GB。 它会报告第 373 到 744 小时（下半月）的用量为每小时 2 GB。 此用量将聚合到每月 1,116 GB 的最终帐单中。

实际的备份计费方案更加复杂。 由于数据库中的变化率取决于工作负荷，并且会随时间而改变，因此每个差异备份和日志备份的大小也会随之改变，从而导致每小时备份存储消耗量出现相应波动。 此外，每个差异备份都包含自上次完整备份后数据库中的所有更改，因此，所有差异备份的总大小会在一周过程中逐渐增加，然后在早期的完整备份、差异备份和日志备份过期后急剧减少。例如，如果在完整备份完成后立即运行量较大的写入活动（如索引重新生成），则因重新生成索引所发生的修改将包含在重建期间获取的事务日志备份、下一个差异备份以及下一次完全备份发生之前执行的每个差异备份中。 如果在较大数据库中发生后一种情况，如果差异备份过大，则服务中的一项优化会导致创建完整备份而不是差异备份。 这会减少下一次完整备份之前的所有差异备份的大小。

如[监视消耗情况](#monitor-consumption)中所述，可以监视每个备份类型（完整备份、差异备份、事务日志备份）随时间推移的总备份存储消耗量。

### <a name="backup-storage-redundancy"></a>备份存储冗余

备份存储冗余按以下方式影响备份成本：
- LRS 价格 = x
- ZRS 价格 = 1.25 x
- RA-GRS 价格 = 2x

有关备份存储定价的详细信息，请访问 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)和 [Azure SQL 托管实例定价页](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

> [!IMPORTANT]
> 适用于 SQL 托管实例的可配置备份存储冗余适用于所有 Azure 区域，目前仅适用于 SQL 数据库的东南亚 Azure 区域。 对于托管实例只能在创建托管实例过程中指定。 预配资源后，不能更改备份存储冗余选项。

### <a name="monitor-costs"></a>监视成本

要了解备份存储成本，请在 Azure 门户中的 " **成本管理 + 计费** " 下，选择 " **成本管理**"，然后选择 " **成本分析**"。 选择所需的订阅作为 **作用域**，然后筛选所需的时间段和服务。

为 " **服务名称**" 添加筛选器，然后在下拉列表中选择 " **sql 数据库** "。 使用 " **计量子类别** " 筛选器选择服务的帐单计数器。 对于单一数据库或弹性数据库池，请选择 " **单一/弹性池 PITR 备份存储**"。 对于托管实例，请选择 " **MI PITR 备份存储**"。 **存储**和**计算**子类别可能也会给您带来兴趣，但它们并不与备份存储成本相关联。

![备份存储成本分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 计量仅对当前正在使用的计数器可见。 如果某个计数器不可用，则可能当前未使用该类别。 例如，对于未部署托管实例的客户，将不会显示托管实例计数器。 同样，不使用存储的资源也不会显示存储计数器。 

## <a name="encrypted-backups"></a>加密备份

如果使用 TDE 加密了数据库，则备份（包括 LTR 备份）会自动静态加密。 默认情况下，Azure SQL 中所有新的数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 SQL 数据库和 SQL 托管实例进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

Azure SQL 工程团队持续不断地自动测试自动数据库备份的还原。 （此测试当前不适用于 SQL 托管实例。）完成时间点还原后，数据库还会接受 DBCC CHECKDB 完整性检查。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关详细信息，请参阅 [SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

所有类型的数据库备份都提供 CHECKSUM 选项，以便增加备份完整度。

## <a name="compliance"></a>合规性

将数据库从基于 DTU 的服务层级迁移到基于 vCore 的服务层级时，将保留 PITR 保留期以确保不会违反应用程序的数据恢复策略。 如果默认保留期不满足合规性要求，可以更改 PITR 保留期。 有关详细信息，请参阅[更改 PITR 备份保留期](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>更改 PITR 备份保留期

可以使用 Azure 门户、PowerShell 或 REST API 更改默认 PITR 备份保留期。 以下示例演示如何将 PITR 保留期更改为 28 天。

> [!WARNING]
> 如果缩短当前的保留期，则无法还原到早于新保留期的时间点。 会删除新保留期内不再需要为提供 PITR 而保留的备份。 如果延长当前的保留期，则无法立即在新的保留期内获得恢复到旧时间点的能力。 随着时间推移，你将获得这一能力，因为系统开始将备份保留更长时间。

> [!NOTE]
> 这些 API 只影响 PITR 保留期。 如果为数据库配置了 LTR，LTR 不会受到影响。 有关如何更改 LTR 保留期的信息，请参阅[长期保留](long-term-retention-overview.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 门户更改 PITR 备份保留期

若要通过使用 Azure 门户更改活动数据库的 PITR 备份保持期，请转到具有要更改保持期的数据库的服务器或托管实例。 

#### <a name="sql-database"></a>[SQL 数据库](#tab/single-database)

对 SQL 数据库的 PITR 备份保留期更改是在门户中的服务器级别进行的。 若要为服务器上的数据库更改 PITR 保留期，请转到服务器概述边栏选项卡。 在左侧窗格中选择“管理备份”，并选择更改范围内的数据库，然后在屏幕顶部选择“配置保留期” ：

![更改 PITR 保留期，服务器级别](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL 托管实例](#tab/managed-instance)

对 SQL 托管实例的 PITR 备份保留期更改是在单一数据库级别进行的。 若要从 Azure 门户更改实例数据库的 PITR 备份保留期，请转到单个数据库的概述边栏选项卡。 然后在屏幕顶部选择“配置备份保留期”：

![更改 PITR 保留期，托管实例](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 更改 PITR 备份保留期

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM 模块仍受 SQL 数据库和 SQL 托管实例的支持，但所有未来的开发都是针对 Az.Sql 模块的。 有关详细信息，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

#### <a name="sql-database"></a>[SQL 数据库](#tab/single-database)

若要更改活动 Azure SQL 数据库的 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL 托管实例](#tab/managed-instance)

若要更改单个活动 SQL 托管实例数据库的 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

若要更改所有活动 SQL 托管实例数据库的 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

若要更改单个已删除 SQL 托管实例数据库的 PITR 备份保留，请使用以下 PowerShell 示例。
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

若要更改所有已删除 SQL 托管实例数据库的 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

零 (0) 天的保留表示立即删除备份，并且不再为已删除的数据库保留备份。
已删除数据库的 PITR 备份保留减少后，就不能再增加。

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>使用 REST API 更改 PITR 备份保留期

#### <a name="sample-request"></a>示例请求

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>请求正文

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>示例响应

状态代码：200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

有关详细信息，请参阅[备份保持期 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)。

#### <a name="sample-request"></a>示例请求

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>请求正文

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>示例响应

状态代码：200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

有关详细信息，请参阅[备份保持期 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)。

## <a name="configure-backup-storage-redundancy"></a>配置备份存储冗余

> [!NOTE]
> 只能在创建托管实例过程期间指定 SQL 托管实例的备份的可配置存储冗余。 预配资源以后，不能更改备份存储冗余选项。 对于 SQL 数据库，此功能的公共预览版目前仅适用于东南亚 Azure 区域。 

只能在创建实例期间设置托管实例的备份存储冗余。 对于 SQL 数据库，可以在创建数据库时设置，也可以为现有数据库进行更新。 默认值为异地冗余存储 (RA-GRS)。 有关本地冗余 (LRS) 的区域冗余 (ZRS) 和异地冗余 (RA-GRS) 备份存储的定价差异，请访问 [托管实例定价页](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>使用 Azure 门户配置备份存储冗余

#### <a name="sql-database"></a>[SQL 数据库](#tab/single-database)

在 Azure 门户中，可以在 " **创建 SQL 数据库** " 边栏选项卡上配置备份存储冗余。 "备份存储冗余" 部分下提供了相应的选项。 
![打开 "创建 SQL 数据库" 边栏选项卡](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL 托管实例](#tab/managed-instance)

在 Azure 门户中，用于更改备份存储冗余的选项位于“计算 + 存储”边栏选项卡上，在创建 SQL 托管实例时，可以从“基本信息”选项卡上的“配置托管实例”选项访问该边栏选项卡  。
![打开“计算 + 存储”配置边栏选项卡](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

在“计算 + 存储”边栏选项卡上找到选择备份存储冗余的选项。
![配置备份存储冗余](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>使用 PowerShell 配置备份存储冗余

#### <a name="sql-database"></a>[SQL 数据库](#tab/single-database)

若要在创建新数据库时配置备份存储冗余，可以指定-BackupStoageRedundancy 参数。 可能的值为 "地理区域"、"区域" 和 "本地"。 默认情况下，所有 SQL 数据库均使用异地冗余存储进行备份。 如果使用本地或区域冗余备份存储创建了数据库，则会禁用异地还原。 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

有关详细信息，请访问 [AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)。

若要更新现有数据库的备份存储冗余，可以使用-BackupStorageRedundancy 参数。 可能的值为 "地理区域"、"区域" 和 "本地"。
请注意，将对数据库应用所做的更改可能需要长达48小时。 从异地冗余备份存储切换到本地或区域冗余存储将禁用异地还原。 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

有关详细信息，请访问 [AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)

> [!NOTE]
> 若要将-BackupStorageRedundancy 参数与数据库还原、数据库复制或创建辅助操作一起使用，请使用 Azure PowerShell 版本 Az .Sql 2.11.0。 


#### <a name="sql-managed-instance"></a>[SQL 托管实例](#tab/managed-instance)

若要在托管实例创建过程中配置备份存储冗余，可以指定-BackupStoageRedundancy 参数。 可能的值为 "地理区域"、"区域" 和 "本地"。

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

有关更多详细信息，请访问 [AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)。

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>使用 Azure 策略强制实施备份存储冗余

如果数据驻留要求需要将所有数据保留在单个 Azure 区域中，则可能要使用 Azure 策略为 SQL 数据库或托管实例强制实施区域冗余或本地冗余备份。 Azure 策略是一种服务，可用于创建、分配和管理将规则应用于 Azure 资源的策略。 Azure 策略可帮助确保这些资源符合公司标准和服务级别协议。 有关详细信息，请参阅 [Azure Policy 概述](https://docs.microsoft.com/azure/governance/policy/overview)。 

### <a name="built-in-backup-storage-redundancy-policies"></a>内置备份存储冗余策略 

添加了以下新的内置策略，可在订阅或资源组级别分配该策略，阻止创建新的数据库 () 或实例)  (异地冗余备份存储。 

[SQL 数据库应避免使用 GRS 备份冗余](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[SQL 托管实例应避免使用 GRS 备份冗余](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

可在 [此处](https://docs.microsoft.com/azure/azure-sql/database/policy-reference)找到 SQL 数据库和托管实例的内置策略定义的完整列表。

若要在组织级别强制执行数据驻留要求，可以将这些策略分配到订阅。 在订阅级别分配这些数据后，给定订阅中的用户将无法通过 Azure 门户或 Azure PowerShell 创建具有异地冗余备份存储的数据库或托管实例。 

> [!IMPORTANT]
> 通过 T-sql 创建数据库时，不会强制实施 Azure 策略。 若要在使用 T-sql 创建数据库时强制数据驻留，请 [在 CREATE database 语句中使用 "LOCAL" 或 "ZONE" 作为 BACKUP_STORAGE_REDUNDANCY 参数的输入](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#create-database-using-zone-redundancy-for-backups)。

了解如何使用[Azure 门户](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal)或[Azure PowerShell](https://docs.microsoft.com/azure/governance/policy/assign-policy-powershell)分配策略


## <a name="next-steps"></a>后续步骤

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
- 获取有关如何[使用 Azure 门户将数据库还原到某个时间点](recovery-using-backups.md)的详细信息。
- 获取有关如何[使用 PowerShell 将数据库还原到某个时间点](scripts/restore-database-powershell.md)的详细信息。
- 有关如何使用 Azure 门户配置和管理 Azure Blob 存储中的自动备份的长期保留，并从这些备份进行还原的信息，请参阅[使用 Azure 门户管理长期备份保留](long-term-backup-retention-configure.md)。
- 有关如何使用 PowerShell 配置和管理 Azure Blob 存储中的自动备份的长期保留，并从这些备份进行还原的信息，请参阅[使用 PowerShell 管理长期备份保留](long-term-backup-retention-configure.md)。
- 若要了解如何优化备份存储的保留期和 Azure SQL 托管实例的成本，请参阅 [托管实例的微调备份存储成本](https://aka.ms/mi-backup-tuning)。
