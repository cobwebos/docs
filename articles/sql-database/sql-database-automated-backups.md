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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061764"
---
# <a name="automated-backups"></a>自动备份

Azure SQL 数据库会自动创建在配置的保留期内保留的数据库备份。 它使用 Azure[读取访问异地冗余存储 （RA-GRS）](../storage/common/storage-redundancy.md)来确保即使数据中心不可用，备份也会保留。

数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果安全规则要求备份在较长时间内（最多 10 年）可用，则可以在单例数据库和弹性数据库池上配置[长期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？

SQL 数据库使用 SQL Server 技术每周创建[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每 12 小时创建[一次差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每 5 到 10 分钟[创建一次事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 备份存储在[RA-GRS 存储 Blob 中](../storage/common/storage-redundancy.md)，这些 Blob 被复制到[配对的数据中心](../best-practices-availability-paired-regions.md)，以防范数据中心中断。 还原数据库时，该服务将确定需要还原的完整、差异和事务日志备份。

可使用这些备份执行以下任务：

- 通过使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API，**将现有数据库还原到过去保留期内的某一时间点**。 对于单个数据库和弹性数据库池，此操作将在与原始数据库相同的服务器上创建新数据库。 在托管实例中，此操作可以创建数据库的副本，也可以在同一订阅下创建相同或不同的托管实例的副本。
- **将已删除的数据库还原到删除时间**或保留期内的任何时间。 删除的数据库只能在创建原始数据库的同一逻辑服务器或托管实例上还原。
- **将数据库还原到另一个地理区域**。 当无法访问服务器和数据库时，地理还原允许您从地理灾难中恢复。 它在世界上任何现有服务器上创建新数据库。
- 如果数据库配置了长期保留策略 （LTR），则从单个数据库或弹性数据库池上**的特定长期备份还原数据库**。 LTR 允许您使用[Azure 门户](sql-database-long-term-backup-retention-configure.md#using-azure-portal)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)来还原数据库的旧版本，以满足合规性请求或运行应用程序的旧版本。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语*复制*是指将文件从一个位置复制到另一个位置。 SQL Server*数据库复制*是指使多个辅助数据库与主数据库同步。

您可以使用以下示例尝试其中一些操作：

| | Azure 门户 | Azure PowerShell |
|---|---|---|
| 更改备份保留 | [单一数据库](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [托管实例](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [单一数据库](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 更改长期备份保留 | [单一数据库](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>托管实例 - 不适用  | [单一数据库](sql-database-long-term-backup-retention-configure.md)<br/>托管实例 - 不适用  |
| 从某个时间点还原数据库 | [单一数据库](sql-database-recovery-using-backups.md#point-in-time-restore) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 还原已删除数据库 | [单一数据库](sql-database-recovery-using-backups.md) | [单一数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 从 Azure Blob 存储还原数据库 | 单一数据库 - 不可用 <br/>托管实例 - 不适用  | 单一数据库 - 不可用 <br/>[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>备份频率

### <a name="point-in-time-restore"></a>时点还原

SQL 数据库通过自动创建完整备份、差分备份和事务日志备份，支持时间点还原 （PITR） 的自助服务。 每周创建一次完整的数据库备份，差异数据库备份通常每 12 小时创建一次。 事务日志备份通常每 5 到 10 分钟创建一次。 事务日志备份的频率基于计算大小和数据库活动量。 

会在数据库创建后立即计划第一次完整备份。 此备份通常在 30 分钟内完成，但当数据库较大时可能需要更长时间。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，会在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。 不能更改或禁用备份作业。

PITR 备份受异地冗余存储的保护。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关 PITR 的详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。

### <a name="long-term-retention"></a>长期保留

对于单个数据库和池数据库，您可以在 Azure Blob 存储中配置完整备份的长期保留期 （LTR）长达 10 年。 如果启用 LTR 策略，则每周完整备份将自动复制到其他 RA-GRS 存储容器。 为了满足各种合规性要求，您可以选择不同的每周、每月和/或年度备份的保留期。 存储消耗取决于所选的备份频率以及保留期或期间。 您可以使用[LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储的成本。

与 PITR 备份一样，LTR 备份也受异地冗余存储的保护。 有关详细信息，请参阅 [Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关 LTR 的详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

## <a name="backup-storage-consumption"></a>备份存储消耗量

对于单个数据库，此公式用于计算备份存储的总使用情况：

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

对于弹性数据库池，总备份存储大小在池级别聚合，计算方式如下：

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

保留期之前发生的备份会根据其时间戳自动清除。 由于差异备份和日志备份需要更早的完整备份才能有用，因此它们按每周块一起清除。

Azure SQL 数据库将保留期备份存储总额计算为累积值。 每小时都会向 Azure 计费管道报告此值，该管道负责聚合此每小时使用情况，以计算每个月底的消耗量。 删除数据库后，消耗量会随着备份的陈旧而降低。 备份超过保留期后，计费将停止。

   > [!IMPORTANT]
   > 数据库的备份将保留指定保留期，即使数据库已被删除也是如此。 虽然删除和重新创建数据库通常可以节省存储和计算成本，但可能会增加备份存储成本，因为 Microsoft 为每个丢弃的数据库保留指定的保留期（至少 7 天）的备份， 每个数据库时间，它下降。

### <a name="monitor-consumption"></a>监控消耗

每种类型的备份（完整备份、差异备份和日志）都报告在数据库监视边栏选项卡上，作为单独的指标。 下图演示如何监视单个数据库的备份存储消耗。 此功能当前对于托管实例不可用。

![监视 Azure 门户中的数据库备份消耗](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微调备份存储消耗

过多的备份存储消耗将取决于工作负载和各个数据库的大小。 请考虑以下一些调优技术，以减少备份存储消耗：

* 根据需要将[备份保留期](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)缩短至尽量最小。
* 避免执行大型写入操作（如索引重建）的频率高于您需要的频率。
* 对于大型数据加载操作，请考虑使用[群集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非群集索引的数量，并考虑行数在 100 万左右的批量加载操作。
* 在通用服务层中，预配的数据存储比超额备份存储的价格便宜。 如果备份存储成本持续过高，可以考虑增加数据存储以节省备份存储。
* 使用 TempDB 而不是 ETL 逻辑中的永久表来存储临时结果。 （仅适用于托管实例。
* 请考虑关闭不包含敏感数据的数据库（例如开发或测试数据库）的 TDE 加密。 未加密数据库的备份通常使用较高的压缩比压缩。

> [!IMPORTANT]
> 对于分析数据集市和数据仓库工作负载，我们强烈建议您使用[群集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非群集索引的数量，并考虑行数约为 100 万的批量加载操作，以减少多余的备份存储消耗。

## <a name="storage-costs"></a>存储成本

存储价格因您使用的 DTU 型号还是 vCore 模型而异。

### <a name="dtu-model"></a>DTU 模型

如果使用 DTU 模型，则数据库和弹性数据库池的备份存储不收取额外费用。

### <a name="vcore-model"></a>vCore 模型

对于单个数据库，提供至少相当于数据库大小的 100% 的备份存储量，无需支付额外费用。 对于弹性数据库池和托管实例，最小备份存储量分别等于池或实例大小的已分配数据存储的 100%，无需支付额外费用。 超出此部分的其他备份存储用量将以 GB 为单位每月进行收费。 此额外用量将取决于各个数据库的工作负荷和大小。

Azure SQL 数据库将保留期内的备份存储总量计算为累积值。 每小时都会向 Azure 计费管道报告此值，该管道负责聚合此每小时使用情况，以便在每个月底获取使用。 数据库删除后，Microsoft 会随着备份年龄的降低而减少消耗。 备份超过保留期后，计费将停止。 由于所有日志备份和差异备份都保留在完全保留期内，因此经过大量修改的数据库将收取更高的备份费用。

假设数据库累积了 744 GB 的备份存储，并且此金额在整个月份保持不变。 要将此累积存储消耗转换为每小时使用量，请将此消耗除以 744.0（每月 31 天 = 每天 24 小时）。 因此，SQL 数据库将报告数据库每小时消耗 1 GB 的 PITR 备份。 Azure 计费将聚合此消耗，并显示整个月的使用量为 744 GB。 成本将基于您所在地区的 $/GB/月费率。

下面是一个更复杂的示例。 假设数据库的保留时间在月中增加到 14 天。 假设此增加（假设）会导致备份存储总数翻倍，达到 1，488 GB。 SQL 数据库将报告 1 到 372 小时 1 GB 的使用情况。 它将报告使用时间为 2 GB，小时为 373 到 744。 此使用量将聚合到 1，116 GB/月的最终帐单。

### <a name="monitor-costs"></a>监控成本

要了解备份存储成本，请转到 Azure 门户中的**成本管理 + 计费**，选择**成本管理**，然后选择**成本分析**。 选择所需的订阅作为**范围**，然后筛选您感兴趣的时间段和服务。

为**服务名称**添加筛选器，然后在下拉列表中选择**sql 数据库**。 使用**仪表子类别**筛选器为服务选择计费计数器。 对于单个数据库或弹性数据库池，请选择**单个/弹性池飞底器备份存储**。 对于托管实例，选择**mi pitr 备份存储**。 **存储**和**计算**子类别可能也让您感兴趣，但它们与备份存储成本无关。

![备份存储成本分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>备份保留

所有 Azure SQL 数据库（单个、池和托管实例数据库）的默认备份保留期为 7 天。 您可以将[备份保留期更改为](#change-the-pitr-backup-retention-period)长达 35 天。

如果删除一个数据库，SQL 数据库将保留其备份，就像保留联机数据库的备份一样。 例如，如果删除保留期为 7 天的"基本数据库"，则备份的保留时间为 4 天，则备份将保存三天以上。

如果需要备份保留时间超过最长的保持期，则可以修改备份属性，以向数据库添加一个或多个长期保持期。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果删除承载 SQL 数据库的 Azure SQL 服务器，则属于该服务器的所有弹性数据库池和数据库也会被删除。 他们无法恢复。 无法还原已删除的服务器。 但是，如果配置了长期保留，则不会删除具有 LTR 的数据库的备份，并且可以还原这些数据库。

## <a name="encrypted-backups"></a>加密备份

如果您的数据库使用 TDE 加密，则备份会在静态时自动加密，包括 LTR 备份。 为 Azure SQL 数据库启用 TDE 时，也会加密备份。 默认情况下，将所有新的 Azure SQL 数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

Azure SQL 数据库工程团队持续测试放置在逻辑服务器和弹性数据库池中的数据库的自动数据库备份的还原。 （此测试在托管实例中不可用。在时间点还原时，数据库还会收到 DBCC CHECKDB 完整性检查。

托管实例在迁移完成后，使用`CHECKSUM`使用本机`RESTORE`命令还原的数据库或 Azure 数据迁移服务进行自动初始备份。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关详细信息，请参阅[Azure SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="compliance"></a>合规性

当您将数据库从基于 DTU 的服务层迁移到基于 vCore 的服务层时，将保留 PITR 保留，以确保应用程序的数据恢复策略不会受到损害。 如果默认保留不符合合规性要求，则可以使用 PowerShell 或 REST API 更改 PITR 保留期。 有关详细信息，请参阅更改[PITR 备份保留期](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>更改 PITR 备份保留期

您可以使用 Azure 门户、PowerShell 或 REST API 更改默认 PITR 备份保留期。 以下示例说明了如何将 PITR 保留期更改为 28 天。

> [!WARNING]
> 如果减少当前保留期，则所有早于新保留期的现有备份将不再可用。 如果增加当前保留期，SQL 数据库将保留现有备份，直到达到较长的保留期结束。

> [!NOTE]
> 这些 API 将仅影响 PITR 保留期。 如果为数据库配置 LTR，则不会受到影响。 有关如何更改 LTR 保留期的信息，请参阅[长期保留](sql-database-long-term-retention.md)期。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 门户更改 PITR 备份保留期

要使用 Azure 门户更改 PITR 备份保留期，请使用要在门户中更改其保留期的服务器对象。 然后根据要修改的服务器对象选择适当的选项。

#### <a name="single-database-and-elastic-database-pools"></a>[单个数据库和弹性数据库池](#tab/single-database)

对单个 Azure SQL 数据库的 PITR 备份保留的更改是在服务器级别完成的。 在服务器级别所做的更改将应用于服务器上的数据库。 要从 Azure 门户更改 Azure SQL 数据库服务器的 PITR 保留，请访问服务器概述边栏选项卡。 选择"在左侧窗格中**管理备份**"，然后在屏幕顶部选择 **"配置保留**"：

![更改 PITR 保留、服务器级别](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[托管实例](#tab/managed-instance)

SQL 数据库托管实例的 PITR 备份保留的更改是在单个数据库级别完成的。 要从 Azure 门户更改实例数据库的 PITR 备份保留，请访问各个数据库概述边栏选项卡。 然后选择**在屏幕顶部配置备份保留**：

![更改 PITR 保留、托管实例](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 更改 PITR 备份保留期

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍然支持 PowerShell AzureRM 模块，但所有后续开发都针对 Az.Sql 模块。 有关详细信息，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块中命令的参数与 AzureRm 模块中的命令基本相同。

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
- 获取有关如何使用[Azure 门户将数据库还原到时间点](sql-database-recovery-using-backups.md)的详细信息。
- 获取有关如何使用[PowerShell 将数据库还原到时间点](scripts/sql-database-restore-database-powershell.md)的详细信息。
- 有关如何使用 Azure 门户在 Azure Blob 存储中长期保留自动备份来配置、管理和还原的信息，请参阅[使用 Azure 门户管理长期备份保留。](sql-database-long-term-backup-retention-configure.md)
- 有关如何使用 PowerShell 在 Azure Blob 存储中长期保留自动备份，有关如何配置、管理和还原的信息，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
