---
title: 自动异地冗余备份
description: SQL 数据库每隔几分钟会自动创建一个本地数据库备份，并使用 Azure 读取访问异地冗余存储来提供异地冗余。
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
ms.openlocfilehash: 7cbe0015eeb9b46cd72496a220ce7f7d094cb61d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198558"
---
# <a name="automated-backups"></a>自动备份

Azure SQL 数据库自动创建数据库备份，这些备份会进行保存，保存的时间长度为已配置的保留期。 Azure SQL 数据库使用 Azure [读取访问异地冗余存储 (RA-GRS)](../storage/common/storage-redundancy.md) 来确保保留这些备份（即使数据中心不可用）。

数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果安全规则要求备份在较长时间（最长 10 年）内可用，可以在单一数据库和弹性数据库池上配置[长期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？

SQL 数据库使用 SQL Server 技术，每周创建[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每隔 12 小时创建[差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每隔 5 到 10 分钟创建[事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 备份存储在[GRS 存储 blob](../storage/common/storage-redundancy.md)中，这些 blob 复制到[配对的数据中心](../best-practices-availability-paired-regions.md)，以防止数据中心中断。 还原数据库时，服务会确定需要还原哪些完整备份、差异备份和事务日志备份。

可使用这些备份：

- 使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 将现有的数据库还原到过去的某个时间点（在保留期内）。  对于单一数据库和弹性数据库池，此操作会在与原始数据库相同的服务器中创建新数据库。 在托管实例中，此操作可以创建数据库的一个副本，或者在同一订阅下创建相同或不同托管实例。
- 将已删除的数据库还原到删除时间或保留期内的任意时间。  仅可在创建原始数据库的同一逻辑服务器或托管实例上还原已删除的数据库。
- 将数据库还原到其他地理区域。  在无法访问服务器和数据库的情况下，异地还原可用于从某个地理区域内发生的灾难中恢复。 它会在全球任意位置的任意现有服务器中创建新数据库。
- 从特定的长期备份将数据库还原到单一数据库或弹性数据库池中（如果为数据库配置了长期保留策略 (LTR)）。  LTR 允许使用 [Azure 门户](sql-database-long-term-backup-retention-configure.md#using-azure-portal)或 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) 还原旧版本的数据库，以满足合规性请求或运行旧版本的应用程序。 有关详细信息，请参阅 [长期保留](sql-database-long-term-retention.md)。

若要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语“复制”指将文件从一个位置复制到另一个位置  。 SQL Server 数据库复制是指让多个辅助数据库与主数据库保持同步  。

可以使用以下示例尝试这其中的部分操作：

| | Azure 门户 | Azure PowerShell |
|---|---|---|
| 更改备份保留 | [单一数据库](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [托管实例](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [单一数据库](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 更改长期备份保留 | [单一数据库](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>托管实例 - 不可用  | [单一数据库](sql-database-long-term-backup-retention-configure.md)<br/>托管实例 - 不可用  |
| 从某个时间点还原数据库 | [单一数据库](sql-database-recovery-using-backups.md#point-in-time-restore) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 还原已删除的数据库 | [单一数据库](sql-database-recovery-using-backups.md) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 从 Azure Blob 存储还原数据库 | 单一数据库 - 不可用 <br/>托管实例 - 不可用  | 单一数据库 - 不可用 <br/>[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>备份频率

### <a name="point-in-time-restore"></a>时间点还原

SQL 数据库支持通过自动创建完整备份、差异备份和事务日志备份来进行自助时间点还原 (PITR)，。 完整数据库备份是每周创建的，差异数据库备份通常是每隔 12 小时创建的。 事务日志备份通常是每隔 5 到 10 分钟创建的。 事务日志备份的频率取决于计算大小和数据库活动量。 

会在数据库创建后立即计划第一次完整备份。 此备份通常可在 30 分钟内完成，但如果数据库较大，花费的时间可能更长。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。 不能更改或禁用备份作业。

### <a name="default-backup-retention-period"></a>默认备份保持期

PITR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关 PITR 的详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。

### <a name="long-term-retention"></a>长期保留

对于单一数据库和共用数据库，可以配置完整备份的长期保留 (LTR)，使其在 Azure Blob 存储中最长保存 10 年。 如果启用 LTR 策略，每周完整备份将自动复制到不同的 RA-GRS 存储容器。 为了满足不同的合规性要求，可为每周、每月和/或每年备份选择不同的保留期。 存储消耗量取决于所选的备份频率和保留期。 可以使用 [LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储成本。

与 PITR 备份一样，LTR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关 LTR 的详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

## <a name="backup-storage-consumption"></a>备份存储消耗量

对于单一数据库，可使用以下公式来计算备份存储总用量：

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

对于弹性数据库池，备份存储总大小将在池级别进行聚合，计算方式如下：

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

在保留期结束之前创建的备份将根据其时间戳自动予以清除。 由于差异备份和日志备份需要早期的完整备份才可供使用，因此它们将在每周区块中一并清除。

Azure SQL 数据库将保留期内的备份存储总量计算为累积值。 此值每隔一小时就会报告给 Azure 计费管道，该管道负责聚合此每小时用量，以便在每月底计算消耗量。 删除数据库后，消耗量会随着备份的陈旧而降低。 备份的保留时间超过保留期后，计费将会停止。

   > [!IMPORTANT]
   > 即使已删除数据库，也会保留数据库的备份，保留时间取决于指定的保留期。 在删除和重新创建数据库时，可能会频繁地节省存储和计算成本，因为对于每个已删除的数据库，Microsoft 在每个删除的数据库中保留的备份保留指定的保留期（最少7天）。

### <a name="monitor-consumption"></a>监视消耗情况

每种类型的备份（完整备份、差异备份和日志备份）在数据库监视边栏选项卡上作为单独的指标进行报告。 下图显示了如何监视单一数据库的备份存储消耗情况。 此功能目前不适用于托管实例。

![在 Azure 门户中监视数据库备份消耗量](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微调备份存储消耗量

额外的备份存储消耗量取决于各个数据库的工作负荷和大小。 考虑实施下面一些优化技术，以减少备份存储消耗量：

* 根据需要将[备份保留期](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)缩短至尽量最小。
* 避免以超过需要的频率执行大型写入操作，例如索引重建。
* 对于大型数据加载操作，考虑使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非聚集索引的数目，并且在行计数大约为 100 万行的情况下，考虑使用批量加载操作。
* 在常规用途服务层级中，预配数据存储的价格低于额外备份存储的价格。 如果你的额外备份存储成本一直较高，可以考虑增大数据存储，以节省备份存储的费用。
* 在 ETL 逻辑中使用 TempDB 而不是永久性表来存储临时结果。 （仅适用于托管实例。）
* 对于不包含敏感数据的数据库（例如开发或测试数据库），请考虑禁用 TDE 加密。 未加密数据库的备份通常使用较高的压缩比压缩。

> [!IMPORTANT]
> 对于与分析相关的数据市场/数据仓库工作负荷，我们强烈建议使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非聚集索引的数量，并在行计数大约为 100 万行的情况下考虑使用批量加载操作，以减少额外的备份存储消耗量。

## <a name="storage-costs"></a>存储费用

存储价格会根据你使用的是 DTU 模型还是 vCore 模型而有所不同。

### <a name="dtu-model"></a>DTU 模型

如果使用的是 DTU 模型，则数据库和弹性数据库池不会产生额外的备份存储费用。

### <a name="vcore-model"></a>vCore 模型

对于单一数据库，会提供与 100% 数据库大小相等的最小备份存储量，不收取额外的费用。 对于弹性数据库池和托管实例，会分别提供与根据池或实例大小分配的数据存储的 100% 相等的最小备份存储量，不收取额外的费用。 超出此部分的其他备份存储用量将以 GB 为单位每月进行收费。 此额外用量将取决于各个数据库的工作负荷和大小。

Azure SQL 数据库将保留期内的备份存储总量计算为累积值。 此值每隔一小时就会报告给 Azure 计费管道，该管道负责聚合此每小时用量，以便在每月底计算消耗量。 删除数据库后，Microsoft 会将消耗降低为备份保留时间。 备份的保留时间超过保留期后，计费将会停止。 由于所有日志备份和差异备份的保留时间为整个保留期，因此，经过重度修改的数据库会产生更高的备份费用。

假设数据库累积了 744 GB 的备份存储，并且此数量在整个月内将保持恒定。 若要将此累积存储消耗量转换为每小时用量，可将此数量除以 744.0（每月 31 天 * 每天 24 小时）。 因此，SQL 数据库将报告数据库每小时使用了 1 GB 的 PITR 备份。 Azure 计费服务将聚合此消耗量，并显示整月用量为 744 GB。 费用将基于你所在地区的 $/GB/month 率。

下面是一个更复杂的示例。 假设数据库的保留期在当月的中途增加到了 14 天。 假设这种增长会导致备份存储总量翻倍至 1488 GB。 SQL 数据库将报告第 1 到 372 小时的用量为 1 GB。 它会报告第 373 到 744 小时的用量为 2 GB。 此用量将聚合到每月 1,116 GB 的最终帐单中。

### <a name="monitor-costs"></a>监视成本

要了解备份存储成本，请在 Azure 门户中的 "**成本管理 + 计费**" 下，选择 "**成本管理**"，然后选择 "**成本分析**"。 选择所需的订阅作为**作用域**，然后筛选所需的时间段和服务。

为 "**服务名称**" 添加筛选器，然后在下拉列表中选择 " **sql 数据库**"。 使用 "**计量子类别**" 筛选器选择服务的帐单计数器。 对于单一数据库或弹性数据库池，请选择 "**单一/弹性池 pitr 备份存储**"。 对于托管实例，请选择 " **mi pitr 备份存储**"。 **存储**和**计算**子类别可能也会给您带来兴趣，但它们并不与备份存储成本相关联。

![备份存储成本分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>备份保留

所有 Azure SQL 数据库（单一数据库、共用数据库和托管实例数据库）的默认备份保留期为 7 天。 可[将备份保留期更改](#change-the-pitr-backup-retention-period)为最长 35 天。

如果删除了某个数据库，SQL 数据库以保存联机数据库的相同方式保存其备份。 例如，如果删除了保留期为 7 天的某个基本数据库，已保存 4 天的备份将继续保存 3 天。

如果需要备份保留时间超过最长的保持期，则可以修改备份属性，以向数据库添加一个或多个长期保持期。 有关详细信息，请参阅 [长期保留](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果删除了托管 SQL 数据库的 Azure SQL 服务器，则属于该服务器的所有弹性数据库池和数据库也会删除， 且无法恢复。 无法还原已删除的服务器。 但是，如果配置了长期保留，则不会删除具有 LTR 的数据库的备份，并且可以还原这些数据库。

## <a name="encrypted-backups"></a>加密备份

如果使用 TDE 加密了数据库，则备份（包括 LTR 备份）会自动静态加密。 为 Azure SQL 数据库启用 TDE 时，也会加密备份。 默认情况下，所有新的 Azure SQL 数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

Azure SQL 数据库工程团队会持续自动测试放置在逻辑服务器和弹性数据库池中放置的数据库的自动数据库备份的还原。 （无法在托管实例中进行这种测试。）完成时间点还原后，数据库还会接受 DBCC CHECKDB 完整性检查。

在迁移完成后，托管实例会通过 `CHECKSUM` 对使用本机 `RESTORE` 命令或 Azure 数据迁移服务还原的数据库进行自动初始备份。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关详细信息，请参阅 [Azure SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="compliance"></a>合规性

将数据库从基于 DTU 的服务层级迁移到基于 vCore 的服务层级时，将保留 PITR 保留期以确保不会违反应用程序的数据恢复策略。 如果默认保留期不满足合规要求，可以使用 PowerShell 或 REST API 更改 PITR 保留期。 有关详细信息，请参阅[更改 PITR 备份保留期](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>更改 PITR 备份保留期

可以使用 Azure 门户、PowerShell 或 REST API 更改默认 PITR 备份保留期。 以下示例演示如何将 PITR 保留期更改为 28 天。

> [!WARNING]
> 如果缩短当前保留期，那么保留时间已超过新保留期的所有现有备份将不再可用。 如果延长当前保留期，SQL 数据库将保留现有备份，直至更长的保留期结束。

> [!NOTE]
> 这些 API 只影响 PITR 保留期。 如果为数据库配置了 LTR，LTR 不会受到影响。 有关如何更改 LTR 保留期的信息，请参阅[长期保留](sql-database-long-term-retention.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 门户更改 PITR 备份保留期

若要使用 Azure 门户更改 PITR 备份保留期，请转到要在门户中更改其保留期的服务器对象。 然后根据要修改的服务器对象选择相应的选项。

#### <a name="single-database-and-elastic-database-pools"></a>[单一数据库和弹性数据库池](#tab/single-database)

对单一 Azure SQL 数据库的 PITR 备份保留期更改是在服务器级别进行的。 在服务器级别做出的更改将应用到该服务器上的数据库。 若要从 Azure 门户更改 Azure SQL 数据库服务器的 PITR 保留期，请转到服务器概述边栏选项卡。 在左侧窗格中选择“管理备份”，然后在屏幕顶部选择“配置保留期”：  

![更改 PITR 保留期，服务器级别](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[托管实例](#tab/managed-instance)

对 SQL 数据库托管实例的 PITR 备份保留期更改是在单个数据库级别进行的。 若要从 Azure 门户更改实例数据库的 PITR 备份保留期，请转到单个数据库的概述边栏选项卡。 然后在屏幕顶部选择“配置备份保留期”： 

![更改 PITR 保留期，托管实例](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 更改 PITR 备份保留期

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM 模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块进行的。 有关详细信息，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

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

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
- 获取有关如何[使用 Azure 门户将数据库还原到某个时间点](sql-database-recovery-using-backups.md)的详细信息。
- 获取有关如何[使用 PowerShell 将数据库还原到某个时间点](scripts/sql-database-restore-database-powershell.md)的详细信息。
- 有关如何使用 Azure 门户配置和管理 Azure Blob 存储中的自动备份的长期保留，并从这些备份进行还原的信息，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
- 有关如何使用 PowerShell 配置和管理 Azure Blob 存储中的自动备份的长期保留，并从这些备份进行还原的信息，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
