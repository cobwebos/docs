---
title: 自动异地冗余备份
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL 数据库和 Azure SQL 托管实例每隔几分钟会自动创建一个本地数据库备份，并使用 Azure 读取访问异地冗余存储来提供异地冗余。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 08/04/2020
ms.openlocfilehash: c24a78413b09de04a10266f883e11617bb7a2f27
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87554033"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自动备份 - Azure SQL 数据库和 SQL 托管实例

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>什么是数据库备份？

数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭损坏或删除。

SQL 数据库和 SQL 托管实例都使用 SQL Server 技术，每周创建[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每 12-24 小时创建[差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每 5 到 10 分钟创建[事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 事务日志备份的频率取决于计算大小和数据库活动量。

还原数据库时，服务会确定需要还原哪些完整备份、差异备份和事务日志备份。

这些备份可在配置的保留期内将数据库还原到某个时间点。 会将备份存储为 [RA-GRS 存储 blob](../../storage/common/storage-redundancy.md)，后者会复制到[配对区域](../../best-practices-availability-paired-regions.md)，用于保护主要区域中的备份存储，使其免受中断影响。 

如果数据保护规则要求备份在较长时间（最长 10 年）内可用，可以同时为单一数据库和共用数据库配置[长期保留](long-term-retention-overview.md)。

可使用这些备份：

- 通过使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API，[将现有的数据库还原到过去的某个时间点](recovery-using-backups.md#point-in-time-restore)。 对于单一数据库和共用数据库，此操作将在与原始数据库相同的服务器上创建新的数据库，但会使用不同的名称，以避免覆盖原始数据库。 还原完成后，可以删除或[重命名](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database)原始数据库，并重命名还原的数据库，使其具有原始数据库的名称。 在托管实例中，此操作可以类似方式在同一订阅和同一区域中的相同或不同的托管实例上创建数据库地副本。
- [将已删除的数据库还原到删除时间](recovery-using-backups.md#deleted-database-restore)或者还原到保留期内的任意时间点。 仅可在创建原始数据库所在的同一服务器或托管实例上还原已删除的数据库。 删除数据库时，该服务会在删除前执行最终事务日志备份，以防止任何数据丢失。
- [将数据库还原到其他地理区域](recovery-using-backups.md#geo-restore)。 在无法访问主要区域中的数据库和备份时，异地还原可帮助从地理位置灾难中恢复。 它可在任何 Azure 区域中的任意现有服务器或托管实例上创建新的数据库。
- 如果为数据库配置了长期保留策略 (LTR)，可[从单一数据库或共用数据库的某个特定长期备份来还原数据库](long-term-retention-overview.md)。 LTR 允许使用 [Azure 门户](long-term-backup-retention-configure.md#using-the-azure-portal)或 [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) 还原旧版本的数据库，以满足合规性请求或运行旧版本的应用程序。 有关详细信息，请参阅 [长期保留](long-term-retention-overview.md)。

若要执行还原，请参阅[从备份还原数据库](recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语“复制”指将 blob 从一个位置复制到另一个位置。 在 SQL 中，“数据库复制”指用于让多个辅助数据库与主数据库保持同步的各种技术。

可以参考下列示例尝试执行备份配置和还原操作：

| | Azure 门户 | Azure PowerShell |
|---|---|---|
| **更改备份保留** | [单一数据库](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [托管实例](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [单一数据库](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **更改长期备份保留** | [单一数据库](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>托管实例 - 不可用  | [单一数据库](long-term-backup-retention-configure.md)<br/>托管实例 - 不可用  |
| **从某个时间点还原数据库** | [单一数据库](recovery-using-backups.md#point-in-time-restore) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **还原已删除的数据库** | [单一数据库](recovery-using-backups.md) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **从 Azure Blob 存储还原数据库** | 单一数据库 - 不适用 <br/>托管实例 - 不可用  | 单一数据库 - 不适用 <br/>[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

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
- 对于大规模数据加载操作，请考虑使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)以及下列相关[最佳做法](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，和/或减少非聚集索引的数目。
- 在常规用途服务层级中，预配数据存储的价格低于备份存储的价格。 如果额外备份存储成本一直较高，可以考虑增大数据存储，以便节省备份存储的费用。
- 在应用程序逻辑中使用 TempDB 而不是永久性表来存储临时结果和/或暂时性数据。

## <a name="backup-retention"></a>备份保留

对于所有新的、还原和复制的数据库，Azure SQL 数据库和 Azure SQL 托管实例会默认保留足以实现过去 7 天的 PITR 的备份量。 除了超大规模数据库之外，可以更改1-35 天范围内每个活动数据库的[备份保留期](#change-the-pitr-backup-retention-period)。 如[备份存储消耗量](#backup-storage-consumption)中所述，为启用 PITR 而存储的备份可能早于保留期。 仅适用于 Azure SQL 托管实例，一旦在0-35 天内删除了数据库，就可以设置 PITR 备份保留速率。 

如果删除数据库，系统会保留数据库的备份至特定的保留期，与为任何一个联机数据库的保留方式一样。 不能更改已删除的数据库的备份保留期。

> [!IMPORTANT]
> 如果删除服务器或托管实例，则该服务器或该托管实例上所有的数据库也会删除，且无法恢复。 无法还原已删除的服务器或托管实例。 但是，如果为数据库或托管实例配置了长期保留 (LTR)，则不会删除长期保留备份，并且可以使用它在同一订阅的另一个服务器或托管实例上将数据库还原到执行长期保留备份的时间点。

出于 PITR 目的，过去 1-35 天内的备份保留有时称为短期备份保留。 如果需要将备份保留至超过 35 天的最长短期保留期，可以启用[长期保留](long-term-retention-overview.md)。

### <a name="long-term-retention"></a>长期保留

对于单一数据库和共用数据库以及托管实例，可以配置完整备份的长期保留 (LTR)，使其在 Azure Blob 存储中最长保存 10 年。 如果启用 LTR 策略，每周完整备份将自动复制到不同的 RA-GRS 存储容器。 为了满足不同的合规性要求，可为每周、每月和/或每年完整备份选择不同的保留期。 存储消耗量取决于所选的 LTR 备份频率以及保留期。 可以使用 [LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储成本。

与 PITR 备份一样，LTR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅 [Azure 存储冗余](../../storage/common/storage-redundancy.md)。

有关 LTR 的详细信息，请参阅[长期备份保留](long-term-retention-overview.md)。

## <a name="storage-costs"></a>存储费用

备份存储的价格取决于你使用的是 DTU 模型还是 vCore 模型，还取决于你所在的区域。 备份存储按每 GB/月的使用量计费，有关定价，请参阅[AZURE Sql 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/single/)页和[azure sql 托管实例定价](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)页。

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

可计费的备份存储总用量按 GB/月进行收费。 备份存储消耗量取决于各个数据库、弹性池以及托管实例的工作负荷和大小。 经过大量修改的数据库具有相对较大的差异备份和日志备份，因为这些备份的大小与数据更改量成比例。 因此，此类数据库的备份费用会更高。

SQL 数据库和 SQL 托管实例按累积值形式计算所有备份文件的总可计费备份存储量。 此值每隔一小时就会报告给 Azure 计费管道，该管道聚合此每小时用量，以便在每月底获取备份存储消耗量。 如果删除数据库，备份存储消耗量将逐渐降低，因为较早的备份会过期并被删除。 由于差异备份和日志备份需要早期的完整备份才可恢复，因此所有这三种备份类型会在每周都一并清除一次。 当所有备份都被删除后，计费会停止。 

举一个简化的示例，假设数据库累积了 744 GB 的备份存储，并且此数量因数据库完全空闲会在整个月内保持恒定。 若要将此累积存储消耗量转换为每小时用量，可将此数量除以 744.0（每月 31 天 * 每天 24 小时）。 SQL 数据库将以固定速率向 Azure 计费管道报告数据库每小时使用了 1 GB 的 PITR 备份。 Azure 计费服务将聚合此消耗量，并显示整月用量为 744 GB。 成本基于所在区域的用量/GB/月费率。

下面是一个更复杂的示例。 假设同一空闲数据库的保留期在当月的中途从 7 天增加到了 14 天。 这种增长会导致总备份存储翻倍至 1,488 GB。 SQL 数据库将报告第 1 到 372 小时（上半月）的用量为每小时 1 GB。 它会报告第 373 到 744 小时（下半月）的用量为每小时 2 GB。 此用量将聚合到每月 1,116 GB 的最终帐单中。

实际的备份计费方案更加复杂。 由于数据库中的变化率取决于工作负荷，并且会随时间而改变，因此每个差异备份和日志备份的大小也会随之改变，从而导致每小时备份存储消耗量出现相应波动。 此外，每个差异备份都包含自上次完整备份后数据库中的所有更改，因此，所有差异备份的总大小会在一周过程中逐渐增加，然后在早期的完整备份、差异备份和日志备份过期后急剧减少。例如，如果在完整备份完成后立即运行量较大的写入活动（如索引重新生成），则因重新生成索引所发生的修改将包含在重建期间获取的事务日志备份、下一个差异备份以及下一次完全备份发生之前执行的每个差异备份中。 如果在较大数据库中发生后一种情况，如果差异备份过大，则服务中的一项优化会导致创建完整备份而不是差异备份。 这会减少下一次完整备份之前的所有差异备份的大小。

如[监视消耗情况](#monitor-consumption)中所述，可以监视每个备份类型（完整备份、差异备份、事务日志备份）随时间推移的总备份存储消耗量。

### <a name="monitor-costs"></a>监视成本

要了解备份存储成本，请在 Azure 门户中的 "**成本管理 + 计费**" 下，选择 "**成本管理**"，然后选择 "**成本分析**"。 选择所需的订阅作为**作用域**，然后筛选所需的时间段和服务。

为 "**服务名称**" 添加筛选器，然后在下拉列表中选择 " **sql 数据库**"。 使用 "**计量子类别**" 筛选器选择服务的帐单计数器。 对于单一数据库或弹性数据库池，请选择 "**单一/弹性池 pitr 备份存储**"。 对于托管实例，请选择 " **mi pitr 备份存储**"。 **存储**和**计算**子类别可能也会给您带来兴趣，但它们并不与备份存储成本相关联。

![备份存储成本分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 计量仅对当前正在使用的计数器可见。 如果某个计数器不可用，则可能当前未使用该类别。 例如，对于未部署托管实例的客户，将不会显示托管实例计数器。 同样，不使用存储的资源也不会显示存储计数器。 

## <a name="encrypted-backups"></a>加密备份

如果使用 TDE 加密了数据库，则备份（包括 LTR 备份）会自动静态加密。 默认情况下，Azure SQL 中所有新的数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 SQL 数据库和 SQL 托管实例进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

Azure SQL 工程团队持续不断地自动测试自动数据库备份的还原。 （此测试当前不适用于 SQL 托管实例。）完成时间点还原后，数据库还会接受 DBCC CHECKDB 完整性检查。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关详细信息，请参阅[SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

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

若要使用 Azure 门户更改活动数据库的 PITR 备份保持期，请转到包含要更改其保持期的数据库的服务器或托管实例。 

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

若要更改活动的 Azure SQL 数据库的 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL 托管实例](#tab/managed-instance)

若要为**单个活动**的 SQL 托管实例数据库更改 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

若要为**所有活动**的 SQL 托管实例数据库更改 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

若要为**单个已删除**的 SQL 托管实例数据库更改 PITR 备份保留，请使用以下 PowerShell 示例。
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

若要更改**所有已删除**SQL 托管实例数据库的 PITR 备份保留，请使用以下 PowerShell 示例。

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

零 (0) 天保留会表明备份立即被删除并且不再保留给已删除的数据库。
一旦已删除的数据库的 PITR 备份保留期减少，就不能再增加了。

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

## <a name="next-steps"></a>后续步骤

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
- 获取有关如何[使用 Azure 门户将数据库还原到某个时间点](recovery-using-backups.md)的详细信息。
- 获取有关如何[使用 PowerShell 将数据库还原到某个时间点](scripts/restore-database-powershell.md)的详细信息。
- 有关如何使用 Azure 门户配置和管理 Azure Blob 存储中的自动备份的长期保留，并从这些备份进行还原的信息，请参阅[使用 Azure 门户管理长期备份保留](long-term-backup-retention-configure.md)。
- 有关如何使用 PowerShell 配置和管理 Azure Blob 存储中的自动备份的长期保留，并从这些备份进行还原的信息，请参阅[使用 PowerShell 管理长期备份保留](long-term-backup-retention-configure.md)。
- 若要了解如何优化备份存储的保留期和 Azure SQL 托管实例的成本，请参阅[托管实例的微调备份存储成本](https://aka.ms/mi-backup-tuning)。
