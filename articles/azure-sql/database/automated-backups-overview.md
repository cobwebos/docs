---
title: 自动异地冗余备份
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL 数据库和 Azure SQL 托管实例每隔几分钟自动创建一个本地数据库备份，并使用 Azure 读取访问异地冗余存储来实现异地冗余。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 07/20/2020
ms.openlocfilehash: 0eea1b696d8eae8606c0b6009f248a215d12db57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515100"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自动备份-Azure SQL 数据库 & SQL 托管实例

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>什么是数据库备份？

数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为它们可防止数据损坏或删除。

SQL 数据库和 SQL 托管实例都使用 SQL Server 技术每周创建一次[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每12-24 小时执行一次[差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每5到10分钟创建一次[事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 事务日志备份的频率取决于计算大小和数据库活动量。

还原数据库时，服务会确定需要还原哪些完整备份、差异备份和事务日志备份。

通过这些备份，可以在配置的保留期内将数据库还原到某个时间点。 备份存储为[GRS 存储 blob](../../storage/common/storage-redundancy.md) ，这些 blob 复制到[配对区域](../../best-practices-availability-paired-regions.md)，以防止影响主要区域中的备份存储的中断。 

如果你的数据保护规则要求你的备份长时间可用（长达10年），则可以为单一数据库和池数据库配置[长期保留](long-term-retention-overview.md)。

可使用这些备份执行以下任务：

- 使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 将[现有数据库还原到保持期过去的某个时间点](recovery-using-backups.md#point-in-time-restore)。 对于单个和共用数据库，此操作将在与原始数据库相同的服务器上创建一个新的数据库，但使用不同的名称，以避免覆盖原始数据库。 还原完成后，可以删除或[重命名](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database)原始数据库，并将还原的数据库重命名为具有原始数据库名称。 在托管实例上，此操作同样可以在同一订阅和同一区域中的相同或不同的托管实例上创建数据库的副本。
- 将[删除的数据库还原到删除时的时间](recovery-using-backups.md#deleted-database-restore)点或保留期内的任何时间点。 删除的数据库只能还原到创建原始数据库的同一服务器或托管实例上。 删除数据库时，该服务在删除之前将执行最终事务日志备份，以防止任何数据丢失。
- 将数据库还原到其他地理区域。 使用异地还原，可以在无法访问主要区域中的数据库或备份时，从地理灾难中恢复。 它在任何 Azure 区域中的任何现有服务器或托管实例上创建新数据库。
- 如果已使用长期保留策略（LTR）配置了数据库，则从单个数据库或共用数据库的[特定长期备份还原数据库](long-term-retention-overview.md)。 LTR 允许使用 [Azure 门户](long-term-backup-retention-configure.md#using-the-azure-portal)或 [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) 还原旧版本的数据库，以满足合规性请求或运行旧版本的应用程序。 有关详细信息，请参阅 [长期保留](long-term-retention-overview.md)。

若要执行还原，请参阅[从备份还原数据库](recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语 "*复制*" 指的是将 blob 从一个位置复制到另一个位置。 在 SQL 中，*数据库复制*是指用于使多个辅助数据库与主数据库保持同步的各种技术。

可以使用以下示例尝试备份配置和还原操作：

| | Azure 门户 | Azure PowerShell |
|---|---|---|
| **更改备份保留** | [单一数据库](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [托管实例](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [单一数据库](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **更改长期备份保留** | [单一数据库](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>托管实例 - 不可用  | [单一数据库](long-term-backup-retention-configure.md)<br/>托管实例 - 不可用  |
| **从某个时间点还原数据库** | [单一数据库](recovery-using-backups.md#point-in-time-restore) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **还原已删除的数据库** | [单一数据库](recovery-using-backups.md) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **从 Azure Blob 存储还原数据库** | 单一数据库 - 不可用 <br/>托管实例 - 不可用  | 单一数据库 - 不可用 <br/>[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>备份计划

在创建或还原新数据库后，将立即计划第一次完整备份。 此备份通常可在 30 分钟内完成，但如果数据库较大，花费的时间可能更长。 例如，在还原的数据库或数据库副本上初始备份所需的时间可能较长，这通常会大于新的数据库。 第一次完整备份后，将自动计划和管理所有后续备份。 所有数据库备份的确切时间取决于 SQL 数据库或 SQL 托管实例服务，因为它会平衡整体系统工作负荷。 不能更改备份作业的计划或禁用备份作业。

> [!IMPORTANT]
> 对于新的、还原或复制的数据库，时间点还原功能将在创建初始完整备份之后的初始事务日志备份时可用。

## <a name="backup-storage-consumption"></a>备份存储消耗量

使用 SQL Server 备份和还原技术，将数据库还原到某个时间点需要一个不间断的备份链，其中包含一个完整备份、一个差异备份和一个或多个事务日志备份。 SQL 数据库和 SQL 托管实例备份计划每周包含一次完整备份。 因此，若要在整个保留期内启用 PITR，系统必须将额外的完整备份、差异备份和事务日志备份的存储时间延长到配置的保持期。 

换句话说，在保持期内的任何时间点，都必须具有早于保持期最早时间的完整备份，以及从该完整备份到下一次完整备份之前的差异和事务日志备份的不间断链。

> [!NOTE]
> 若要启用 PITR，最多可存储比配置的保留期更长的一周的备份。 备份存储按所有备份的相同费率进行收费。 

将自动删除不再需要提供 PITR 功能的备份。 由于差异备份和日志备份需要较早的完整备份才能恢复，因此所有这三种备份类型都将在每周集中进行清除。

对于包含[TDE 加密](transparent-data-encryption-tde-overview.md)数据库的所有数据库，将压缩备份以减少备份存储压缩和成本。 平均备份压缩率为3-4 倍，但是，根据数据的性质以及数据库中是否使用了数据压缩，可以显著降低或更高。

SQL 数据库和 SQL 托管实例将总使用的备份存储计算为累积值。 此值每隔一小时就会报告给 Azure 计费管道，该管道负责聚合此每小时用量，以便在每月底计算消耗量。 删除数据库后，消耗会随着备份的存在而降低，并被删除。 删除所有备份后，不再可能 PITR，计费将停止。
   
> [!IMPORTANT]
> 即使已删除数据库，也会保留数据库备份以启用 PITR。 删除和重新创建数据库时，可能会节省存储和计算成本，因为服务会在每次删除数据库时保留每个已删除数据库的备份。 

### <a name="monitor-consumption"></a>监视消耗

对于 vCore 数据库，每种类型的备份所使用的存储（完整备份、差异备份和日志记录）都作为单独的指标在 "数据库监视" 边栏选项卡上报告。 下图显示了如何监视单一数据库的备份存储消耗情况。 此功能当前不可用于托管实例。

![在 Azure 门户中监视数据库备份消耗量](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微调备份存储消耗量

不会对数据库的最大数据大小的备份存储消耗收费。 超出备份存储消耗将取决于每个数据库的工作负荷和最大大小。 考虑实施下面一些优化技术，以减少备份存储消耗量：

- 根据需要将[备份保留期](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)缩短至尽量最小。
- 避免以超过需要的频率执行大型写入操作，例如索引重建。
- 对于大型数据加载操作，请考虑使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)和以下相关[最佳实践](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，并/或减少非聚集索引的数量。
- 在常规用途服务层，预配的数据存储的开销低于备份存储的价格。 如果持续增加了额外的备份存储费用，则可以考虑增加数据存储，以便在备份存储上保存。
- 在应用程序逻辑中使用 TempDB （而非永久表）来存储临时结果和/或暂时性数据。

## <a name="backup-retention"></a>备份保留

对于所有新的、还原的和复制的数据库，Azure SQL 数据库和 Azure SQL 托管实例保留足够的备份，以便在过去7天内默认 PITR。 除了超大规模数据库之外，可以更改1-35 天范围内每个活动数据库的[备份保留期](#change-the-pitr-backup-retention-period)。 如[备份存储消耗](#backup-storage-consumption)中所述，存储到 enable PITR 的备份可能早于保持期。 仅适用于 Azure SQL 托管实例，一旦在0-35 天内删除了数据库，就可以设置 PITR 备份保留速率。 

如果删除了某个数据库，则系统会将备份的备份方式与其特定的保留期相同。 不能更改已删除数据库的备份保留期。

> [!IMPORTANT]
> 如果删除服务器或托管实例，则该服务器或托管实例上的所有数据库也会被删除，并且无法恢复。 您无法还原已删除的服务器或托管实例。 但是，如果您已为数据库或托管实例配置了长期保留（LTR），则不会删除长期保留备份，并且可以使用它将不同服务器或同一订阅中的托管实例上的数据库还原到执行长期保留备份的时间点。

过去1-35 天内 PITR 的备份保留期有时称为短期备份保留。 如果需要将备份保留超过35天的最高短期保留期，可以启用长期[保留](long-term-retention-overview.md)。

### <a name="long-term-retention"></a>长期保留

对于单个和共用的数据库和托管实例，可以在 Azure Blob 存储中将完整备份的长期保留（LTR）配置为长达10年。 如果启用 LTR 策略，每周的完整备份会自动复制到其他 GRS 存储容器。 为了满足不同的符合性要求，你可以为每周、每月和/或每年完整备份选择不同的保留期。 存储消耗取决于所选的 LTR 备份频率和保留期。 可以使用 [LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储成本。

与 PITR 备份一样，LTR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅 [Azure 存储冗余](../../storage/common/storage-redundancy.md)。

有关 LTR 的详细信息，请参阅[长期备份保留](long-term-retention-overview.md)。

## <a name="storage-costs"></a>存储费用

存储价格会根据你使用的是 DTU 模型还是 vCore 模型而有所不同。

### <a name="dtu-model"></a>DTU 模型

在 DTU 模型中，数据库和弹性池的备份存储没有额外的费用。 备份存储的价格是数据库或池价格的一部分。

### <a name="vcore-model"></a>vCore 模型

对于 SQL 数据库中的单一数据库，提供的备份存储量等于数据库最大数据存储大小的100%，无需额外付费。 对于弹性池和托管实例，提供了等于最大数据存储空间的100% 的备份存储量，或分别提供最大实例存储大小。 

对于单一数据库，此公式用于计算总可计费备份存储使用量：

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

对于共用数据库，总可计费备份存储大小在池级别聚合，并按如下所示进行计算：

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

对于托管实例，总可计费备份存储大小在实例级别聚合，并按如下方式计算：

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

可计费备份存储总计（如果有）将按 GB/月收费。 此备份存储消耗取决于每个数据库、弹性池和托管实例的工作负荷和大小。 经过大量修改的数据库具有较大的差异备份和日志备份，因为这些备份的大小与数据更改量成正比。 因此，此类数据库的备份费用会更高。

SQL 数据库和 SQL 托管实例会将总可计费备份存储计算为所有备份文件的累计值。 每小时将此值报告给 Azure 计费管道，这将聚合这一小时的使用情况，以便在每月结束时获得备份存储消耗量。 如果删除了数据库，备份存储消耗将逐渐降低，因为较旧的备份会过期并被删除。 由于差异备份和日志备份需要较早的完整备份才能恢复，因此所有这三种备份类型都将在每周集中进行清除。 删除所有备份后，计费将停止。 

作为简化的示例，假定数据库的备份存储量为 744 GB，此数量在整个月内保持不变，因为数据库已完全空闲。 若要将此累积存储消耗量转换为每小时用量，可将此数量除以 744.0（每月 31 天 * 每天 24 小时）。 SQL 数据库将报告 Azure 计费管道，数据库每小时使用 1 GB 的 PITR 备份，按固定费率进行。 Azure 计费服务将聚合此消耗量，并显示整月用量为 744 GB。 费用将基于你所在地区的金额/GB/月费率。

下面是一个更复杂的示例。 假设同一空闲数据库的保留期从7天增加到了该月的14天。 这会导致总备份存储翻倍到 1488 GB。 SQL 数据库会报告小时1到372（月上半年）使用了 1 GB 的用量。 它会将使用情况报告为 2 GB，为小时373至744（月份的后半部分）。 此用量将聚合到每月 1,116 GB 的最终帐单中。

实际的备份计费方案更复杂。 由于数据库中的更改速率取决于工作负荷，并且随着时间的推移可变，因此每个差异备份和日志备份的大小也会随之改变，从而导致每小时备份存储消耗相应地波动。 而且，每个差异备份都包含自上次完整备份后在数据库中所做的所有更改，因此，所有差异备份的总大小会在一周的某一周内逐渐增加，然后在一组较早的完整、差异和日志备份过期后就会急剧增加。例如，如果在完整备份完成后立即运行了大量写入活动（如索引重新生成），则索引重新生成所做的修改将包含在重建持续时间、下一个差异备份和每个差异备份中，直到下一次完整备份执行为止。 对于较大的数据库中的后一种方案，如果差异备份的情况非常大，则服务中的优化将创建完整备份，而不是差异备份。 这会减少所有差异备份的大小，直到执行以下完整备份。

你可以根据[监视消耗](#monitor-consumption)中所述，监视每个备份类型（完整备份、差异事务日志）的总备份存储消耗量。

### <a name="monitor-costs"></a>监视成本

要了解备份存储成本，请在 Azure 门户中的 "**成本管理 + 计费**" 下，选择 "**成本管理**"，然后选择 "**成本分析**"。 选择所需的订阅作为**作用域**，然后筛选所需的时间段和服务。

为 "**服务名称**" 添加筛选器，然后在下拉列表中选择 " **sql 数据库**"。 使用 "**计量子类别**" 筛选器选择服务的帐单计数器。 对于单一数据库或弹性数据库池，请选择 "**单一/弹性池 pitr 备份存储**"。 对于托管实例，请选择 " **mi pitr 备份存储**"。 **存储**和**计算**子类别可能也会给您带来兴趣，但它们并不与备份存储成本相关联。

![备份存储成本分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="encrypted-backups"></a>加密备份

如果使用 TDE 加密了数据库，则备份（包括 LTR 备份）会自动静态加密。 默认情况下，Azure SQL 中的所有新数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[Sql 数据库 & sql 托管实例透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

Azure SQL 工程团队不断地会自动测试自动数据库备份的还原。 （此测试当前不适用于 SQL 托管实例。）在时间点还原时，数据库还会收到 DBCC CHECKDB 完整性检查。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关详细信息，请参阅[SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

所有数据库备份都带有 CHECKSUM 选项，以提供额外的备份完整性。

## <a name="compliance"></a>合规性

将数据库从基于 DTU 的服务层级迁移到基于 vCore 的服务层级时，将保留 PITR 保留期以确保不会违反应用程序的数据恢复策略。 如果默认保留期不符合您的符合性要求，则可以更改 PITR 保留期。 有关详细信息，请参阅[更改 PITR 备份保留期](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>更改 PITR 备份保留期

可以使用 Azure 门户、PowerShell 或 REST API 更改默认 PITR 备份保留期。 以下示例演示如何将 PITR 保留期更改为 28 天。

> [!WARNING]
> 如果您缩短当前的保留期，则您将无法还原到超过新的保持期的时间点。 将删除不再需要在新的保留期内提供 PITR 的备份。 如果增加当前保持期，则不会立即在新的保留期内还原到较早的时间点。 随着时间的推移，您可以获得这一功能，因为系统会开始保留备份的时间。

> [!NOTE]
> 这些 API 只影响 PITR 保留期。 如果为数据库配置了 LTR，LTR 不会受到影响。 有关如何更改 LTR 保留期的信息，请参阅[长期保留](long-term-retention-overview.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 门户更改 PITR 备份保留期

若要使用 Azure 门户更改活动数据库的 PITR 备份保持期，请转到包含要更改其保持期的数据库的服务器或托管实例。 

#### <a name="sql-database"></a>[SQL 数据库](#tab/single-database)

对 SQL 数据库的 PITR 备份保留项的更改是在门户的 "服务器" 页上完成的。 若要为服务器上的数据库更改 PITR 保留，请转到 "服务器概述" 边栏选项卡。 选择左窗格中的 "**管理备份**"，选择更改范围内的数据库，然后在屏幕顶部选择 "**配置保留**"：

![更改 PITR 保留期，服务器级别](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL 托管实例](#tab/managed-instance)

对 SQL 托管实例的 PITR 备份保留的更改是在单独的数据库级别进行的。 若要从 Azure 门户更改实例数据库的 PITR 备份保留期，请转到单个数据库的概述边栏选项卡。 然后在屏幕顶部选择“配置备份保留期”：

![更改 PITR 保留期，托管实例](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 更改 PITR 备份保留期

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> SQL 数据库和 SQL 托管实例仍支持 PowerShell AzureRM 模块，但所有将来的开发都适用于 Az .Sql 模块。 有关详细信息，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

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

零（0）天保留期表示备份立即被删除并且不再保留给已删除的数据库。
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
