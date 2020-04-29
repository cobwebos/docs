---
title: 自动异地冗余备份
description: SQL 数据库每隔几分钟会自动创建一个本地数据库备份，使用 Azure 读取访问异地冗余存储来提供异地冗余。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061764"
---
# <a name="automated-backups"></a>自动备份

Azure SQL Database 会自动创建在配置的保留期内保留的数据库备份。 它使用 Azure[读取访问异地冗余存储（GRS）](../storage/common/storage-redundancy.md)来确保即使数据中心不可用，也会保留备份。

数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果你的安全规则要求你的备份长时间可用（长达10年），则可以配置单一数据库和弹性数据库池的长期[保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？

SQL 数据库使用 SQL Server 技术每周创建[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每隔12小时创建一次[差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每5到10分钟创建一次[事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 备份存储在[GRS 存储 blob](../storage/common/storage-redundancy.md)中，这些 blob 复制到[配对的数据中心](../best-practices-availability-paired-regions.md)，以防止数据中心中断。 还原数据库时，服务会确定需要还原的完整备份、差异备份和事务日志备份。

可使用这些备份执行以下任务：

- 使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 将**现有数据库还原到保持期过去的某个时间点**。 对于单一数据库和弹性数据库池，此操作将在与原始数据库相同的服务器上创建一个新数据库。 在托管实例中，此操作可在同一订阅下创建数据库副本或相同或不同的托管实例。
- 将**删除的数据库还原到删除时**或保持期内的任何时间。 删除的数据库只能还原到创建原始数据库的同一逻辑服务器或托管实例上。
- **将数据库还原到另一个地理区域**。 异地还原允许您在无法访问服务器和数据库时从地理灾难中恢复。 它在世界上任何位置的任何现有服务器上创建一个新数据库。
- 如果数据库配置了长期保留策略（LTR），则在单个数据库或弹性数据库池上，**从特定的长期备份还原数据库**。 LTR 允许使用[Azure 门户](sql-database-long-term-backup-retention-configure.md#using-azure-portal)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)来还原旧版本的数据库，以满足符合性请求或运行旧版本的应用程序。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

若要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语 "*复制*" 是指将文件从一个位置复制到另一个位置。 SQL Server*数据库复制*是指使多个辅助数据库与主数据库保持同步。

你可以使用以下示例尝试一些操作：

| | Azure 门户 | Azure PowerShell |
|---|---|---|
| 更改备份保留 | [单一数据库](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [托管实例](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [单一数据库](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 更改长期备份保留 | [单个数据库](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>托管实例-不适用  | [单一数据库](sql-database-long-term-backup-retention-configure.md)<br/>托管实例-不适用  |
| 从时间点还原数据库 | [单个数据库](sql-database-recovery-using-backups.md#point-in-time-restore) | [单个数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 还原已删除数据库 | [单个数据库](sql-database-recovery-using-backups.md) | [单个数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 从 Azure Blob 存储还原数据库 | 单一数据库 - 不可用 <br/>托管实例-不适用  | 单一数据库 - 不可用 <br/>[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>备份频率

### <a name="point-in-time-restore"></a>时点还原

SQL 数据库通过自动创建完整备份、差异备份和事务日志备份，支持自助时间点还原（PITR）。 每周创建完整数据库备份，通常每隔12小时创建一次差异数据库备份。 通常每5到10分钟创建一次事务日志备份。 事务日志备份的频率取决于计算大小和数据库活动量。 

会在数据库创建后立即计划第一次完整备份。 此备份通常在30分钟内完成，但如果数据库很大，则可能需要更长时间。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，会在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。 不能更改或禁用备份作业。

PITR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关 PITR 的详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。

### <a name="long-term-retention"></a>长期保留

对于单个和共用数据库，可以在 Azure Blob 存储中将完整备份的长期保留（LTR）配置为长达10年。 如果启用 LTR 策略，每周的完整备份会自动复制到其他 GRS 存储容器。 为了满足不同的符合性要求，你可以为每周、每月和/或每年备份选择不同的保留期。 存储消耗取决于所选的备份频率和保留期。 可以使用[ltr 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 ltr 存储的成本。

与 PITR 备份一样，通过异地冗余存储来保护 LTR 备份。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关 LTR 的详细信息，请参阅长期[备份保留](sql-database-long-term-retention.md)。

## <a name="backup-storage-consumption"></a>备份存储消耗量

对于单一数据库，此公式用于计算总备份存储使用量：

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

对于弹性数据库池，总备份存储大小在池级别聚合，并按如下所示进行计算：

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

保留期之前发生的备份会根据其时间戳自动清除。 因为差异备份和日志备份需要较早的完整备份才能有用，所以它们将在每周的区块中一起清除。

Azure SQL 数据库会将总保留备份存储计算为累积值。 此值每小时报告一次 Azure 计费管道，负责聚合这一小时的使用情况，以便在每月结束时计算消耗。 删除数据库后，消耗量会随着备份的陈旧而降低。 备份超过保持期后，会停止计费。

   > [!IMPORTANT]
   > 即使已删除数据库，也会保留数据库的备份，保留时间取决于指定的保留期。 在删除和重新创建数据库时，可能会频繁地节省存储和计算成本，因为对于每个已删除的数据库，Microsoft 在每个删除的数据库中保留的备份保留指定的保留期（最少7天）。

### <a name="monitor-consumption"></a>监视消耗情况

每种类型的备份（完整备份、差异备份和日志）在 "数据库监视" 边栏选项卡上报告为单独的指标。 下图显示了如何监视单一数据库的备份存储消耗情况。 此功能目前不适用于托管实例。

![监视 Azure 门户中的数据库备份消耗](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微调备份存储消耗

超出备份存储消耗将取决于每个数据库的工作负荷和大小。 请考虑使用以下优化技术来减少备份存储消耗：

* 根据需要将[备份保留期](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)缩短至尽量最小。
* 避免执行大型写入操作（如索引重新生成），而不是所需的频率。
* 对于大型数据加载操作，请考虑使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非聚集索引的数目，并考虑行计数约为1000000的大容量加载操作。
* 在常规用途的服务层，预配的数据存储的成本低于额外的备份存储的价格。 如果你的备份存储成本持续过高，则可以考虑增加要保存在备份存储上的数据存储。
* 在 ETL 逻辑中使用 TempDB 而不是永久表来存储临时结果。 （仅适用于托管实例。）
* 对于不包含敏感数据的数据库（例如，开发或测试数据库），请考虑关闭 TDE 加密。 未加密数据库的备份通常使用较高的压缩比压缩。

> [!IMPORTANT]
> 对于分析数据集市 \ 数据仓库工作负荷，我们强烈建议使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非聚集索引的数目，并考虑使用行数大约为1000000的大容量加载操作来减少多余的备份存储消耗。

## <a name="storage-costs"></a>存储成本

存储的价格取决于你使用的是 DTU 模型还是 vCore 模型。

### <a name="dtu-model"></a>DTU 模型

如果使用 DTU 模型，数据库和弹性数据库池的备份存储没有额外的费用。

### <a name="vcore-model"></a>vCore 模型

对于单一数据库，提供了等于100% 的数据库大小的最小备份存储量，无需额外付费。 对于弹性数据库池和托管实例，为池或实例大小分别等于100% 的已分配数据存储的最小备份存储量，无需额外付费。 超出此部分的其他备份存储用量将以 GB 为单位每月进行收费。 此额外用量将取决于各个数据库的工作负荷和大小。

Azure SQL 数据库将保留期内的备份存储总量计算为累积值。 此值每小时报告一次 Azure 计费管道，负责聚合这一小时的使用情况，以便在每月结束时获得消耗。 删除数据库后，Microsoft 会将消耗降低为备份保留时间。 备份超过保持期后，会停止计费。 由于所有日志备份和差异备份都保留了完整的保留期，因此，大量修改的数据库将具有更高的备份费用。

假设数据库的备份存储量为 744 GB，此数量在整个月内保持不变。 若要将此累积存储消耗量转换为每小时使用，请将其除以744.0 （每月31天 * 24 小时/天）。 因此，SQL 数据库将报告数据库每小时使用了 1 GB 的 PITR 备份。 Azure 计费将聚合此消耗，并显示整月 744 GB 的使用情况。 费用将基于你所在地区的 $/GB/month 率。

下面是一个更复杂的示例。 假设该数据库的保留期增加到了月份中间的14天。 假设此增加（假设）会导致总备份存储翻倍到 1488 GB。 SQL 数据库会报告小时1到372的使用量为 1 GB。 它会报告小时373到744的使用情况为 2 GB。 此使用情况聚合为最后 1116 GB/月。

### <a name="monitor-costs"></a>监视成本

要了解备份存储成本，请在 Azure 门户中的 "**成本管理 + 计费**" 下，选择 "**成本管理**"，然后选择 "**成本分析**"。 选择所需的订阅作为**作用域**，然后筛选所需的时间段和服务。

为 "**服务名称**" 添加筛选器，然后在下拉列表中选择 " **sql 数据库**"。 使用 "**计量子类别**" 筛选器选择服务的帐单计数器。 对于单一数据库或弹性数据库池，请选择 "**单一/弹性池 pitr 备份存储**"。 对于托管实例，请选择 " **mi pitr 备份存储**"。 **存储**和**计算**子类别可能也会给您带来兴趣，但它们并不与备份存储成本相关联。

![备份存储成本分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>备份保留

所有 Azure SQL 数据库（单个、共用和托管实例数据库）都有7天的默认备份保持期。 你可以[将备份保持期更改](#change-the-pitr-backup-retention-period)为长达35天。

如果删除一个数据库，SQL 数据库将保留其备份，就像保留联机数据库的备份一样。 例如，如果删除保留期为七天的基本数据库，则四天的备份将保存3天。

如果需要备份保留时间超过最长的保持期，则可以修改备份属性，以向数据库添加一个或多个长期保持期。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果删除了托管 SQL 数据库的 Azure SQL server，则也将删除所有属于该服务器的弹性数据库池和数据库。 不能恢复它们。 无法还原已删除的服务器。 但是，如果配置了长期保留，则不会删除带 LTR 的数据库的备份，并且可以还原这些数据库。

## <a name="encrypted-backups"></a>加密备份

如果数据库是通过 TDE 加密的，则会自动加密静态备份，包括 LTR 备份。 为 Azure SQL 数据库启用 TDE 时，也会加密备份。 默认情况下，将所有新的 Azure SQL 数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

在日常工作中，Azure SQL 数据库工程团队会自动测试在逻辑服务器和弹性数据库池中放置的数据库的自动数据库备份。 （此测试在托管实例中不可用。）在时间点还原时，数据库还会收到 DBCC CHECKDB 完整性检查。

在迁移完成后，托管实例`CHECKSUM`将使用随本机`RESTORE`命令还原的数据库或使用 Azure 数据迁移服务进行自动初始备份。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关详细信息，请参阅[AZURE SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="compliance"></a>合规性

将数据库从基于 DTU 的服务层迁移到基于 vCore 的服务层时，会保留 PITR 保留，以确保应用程序的数据恢复策略不受侵害。 如果默认保留期不符合您的符合性要求，则可以使用 PowerShell 或 REST API 更改 PITR 保持期。 有关详细信息，请参阅[更改 PITR 备份保持期](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>更改 PITR 备份保持期

您可以使用 Azure 门户、PowerShell 或 REST API 来更改默认的 PITR 备份保持期。 下面的示例演示如何将 PITR 保持更改为28天。

> [!WARNING]
> 如果你缩短当前的保留期，则早于新的保持期的所有现有备份都将不再可用。 如果增加当前的保留期，SQL 数据库将保留现有备份，直到达到更长的保留期结束。

> [!NOTE]
> 这些 Api 只会影响 PITR 的保持期。 如果为数据库配置了 LTR，则不会受到影响。 有关如何更改 LTR 保留期的信息，请参阅长期[保留](sql-database-long-term-retention.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 门户更改 PITR 备份保持期

若要使用 Azure 门户更改 PITR 备份保持期，请转到要在门户中更改其保持期的服务器对象。 然后，根据要修改的服务器对象选择适当的选项。

#### <a name="single-database-and-elastic-database-pools"></a>[单一数据库和弹性数据库池](#tab/single-database)

单个 Azure SQL 数据库的 PITR 备份保留更改是在服务器级别上完成的。 在服务器级别所做的更改适用于服务器上的数据库。 若要从 Azure 门户更改 Azure SQL 数据库服务器的 PITR 保留，请转到 "服务器概述" 边栏选项卡。 选择左窗格中的 "**管理备份**"，并在屏幕顶部选择 "**配置保留**"：

![更改 PITR 保留期，服务器级别](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[托管实例](#tab/managed-instance)

对 SQL 数据库托管实例的 PITR 备份保留的更改是在单个数据库级别上完成的。 若要从 Azure 门户更改实例数据库的 PITR 备份保留，请转到 "单个数据库概述" 边栏选项卡。 然后在屏幕顶部选择 "**配置备份保留**"：

![更改 PITR 保留期，托管实例](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 更改 PITR 备份保持期

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell AzureRM 模块，但所有将来的开发都适用于 Az .Sql 模块。 有关详细信息，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块中的命令的自变量与 AzureRm 模块中的命令参数完全相同。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>使用 REST API 更改 PITR 备份保持期

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

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
- 获取有关如何[使用 Azure 门户将数据库还原到某个时间点](sql-database-recovery-using-backups.md)的详细信息。
- 获取有关如何[使用 PowerShell 将数据库还原到某个时间点](scripts/sql-database-restore-database-powershell.md)的详细信息。
- 有关如何使用 Azure 门户在 Azure Blob 存储中配置、管理和还原自动备份的长期保留的信息，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
- 若要了解如何使用 PowerShell 在 Azure Blob 存储中配置、管理和还原自动备份的长期保留，请参阅[使用 Powershell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
