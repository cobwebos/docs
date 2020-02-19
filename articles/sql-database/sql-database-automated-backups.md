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
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461760"
---
# <a name="automated-backups"></a>自动备份

SQL 数据库会自动创建在配置的保留期内保留的数据库备份，并使用 Azure[读取访问异地冗余存储（GRS）](../storage/common/storage-redundancy.md)来确保即使数据中心不可用，也会保留这些备份。 这些备份是自动创建的。 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果你的安全规则要求你的备份长时间可用（长达10年），可以在单一数据库和弹性池上配置[长期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？

SQL 数据库使用 SQL Server 技术每周创建[完整备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每隔12小时创建一次[差异备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每5-10 分钟创建一次[事务日志备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 备份存储在[GRS 存储 blob](../storage/common/storage-redundancy.md)中，这些 blob 复制到[配对的数据中心](../best-practices-availability-paired-regions.md)，以防止数据中心服务中断。 还原数据库时，服务会确定需要还原哪些完整、差异备份和事务日志备份。

可使用这些备份执行以下任务：

- 使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 将**现有数据库还原到保持期过去的时间点**。 在单一数据库和弹性池中，此操作会在与原始数据库相同的服务器中创建新的数据库。 在托管实例中，此操作可在同一订阅下创建数据库的副本或相同或不同的托管实例。
- 将**已删除的数据库还原到它删除的时间**或保留期内的任何时间。 删除的数据库只能在创建原始数据库的同一逻辑服务器或托管实例中还原。
- **将数据库还原到另一个地理区域**。 在无法访问服务器和数据库的情况下，异地还原可帮助从地理位置灾难中恢复。 它会在全球任意位置的任意现有服务器中创建新数据库。
- 如果数据库已配置长期保留策略（LTR），请从单一数据库或弹性池上**的特定长期备份还原数据库**。 LTR 允许使用[Azure 门户](sql-database-long-term-backup-retention-configure.md#using-azure-portal)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)还原旧版本的数据库，以满足符合性请求或运行旧版本的应用程序。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。
- 若要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语*复制*是指将文件从一个位置复制到另一个位置。 SQL 的“数据库复制”是指让多个辅助数据库与主数据库保持同步。

可以使用以下示例尝试一些操作：

| | Azure 门户 | Azure PowerShell |
|---|---|---|
| 更改备份保留期 | [单一数据库](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [托管实例](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [单一数据库](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[托管实例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 更改长期备份保留 | [单个数据库](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>托管实例-N/A  | [单一数据库](sql-database-long-term-backup-retention-configure.md)<br/>托管实例-N/A  |
| 从时间点还原数据库 | [单个数据库](sql-database-recovery-using-backups.md#point-in-time-restore) | [单个数据库](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 还原已删除的数据库 | [单个数据库](sql-database-recovery-using-backups.md) | [单个数据库](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [托管实例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 从 Azure Blob 存储还原数据库 | 单一数据库-不适用 <br/>托管实例-N/A  | 单一数据库-不适用 <br/>[托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>备份频率

### <a name="point-in-time-restore"></a>时点还原

SQL 数据库通过自动创建完整备份、差异备份和事务日志备份支持时间点还原 (PITR) 的自助服务。 每周创建一次完整数据库备份，一般每隔 12 小时创建一次差异数据库备份，一般每隔 5 - 10 分钟创建一次事务日志备份，具体频率取决于计算大小和数据库活动量。 会在数据库创建后立即计划第一次完整备份。 完整备份通常可在 30 分钟内完成，但如果数据库很大，花费的时间可能更长。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，会在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。 不能更改或禁用备份作业。

PITR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅[Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="long-term-retention"></a>长期保留

单一数据库和共用数据库提供选项，用于在 Azure Blob 存储中将完整备份的长期保留 (LTR) 配置为最多 10 年。 如果启用了 LTR 策略，每周完整备份将自动复制到不同的 RA-GRS 存储容器。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 存储消耗量取决于所选的备份频率和保留期。 可以使用 [LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储成本。

与 PITR 一样，LTR 备份通过异地冗余存储进行保护。 有关详细信息，请参阅[Azure 存储冗余](../storage/common/storage-redundancy.md)。

有关详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

## <a name="backup-storage-consumption"></a>备份存储消耗 

对于单一数据库，将按如下所示计算总备份存储使用量：   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size` 列中的一个值匹配。

对于弹性池，总备份存储大小在池级别聚合，并按如下所示进行计算：   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage` 列中的一个值匹配。 

早于保留期的备份会根据其时间戳自动清除。 因为差异备份和日志备份需要较早的完整备份才能有用，所以它们将在每周的区块中一起清除。 

Azure SQL Database 会将总保留备份存储计算为累积值。 每小时，此值将报告给 Azure 计费管道，该管道负责聚合这一小时的使用情况，以便在每月结束时计算消耗。 删除数据库后，消耗会缩短为备份期限。 一旦备份早于保持期，计费就会停止。 

   > [!IMPORTANT]
   > 即使已删除数据库，数据库的备份仍将保留指定的保持期。 通常，在删除和重新创建数据库时，可能会节省存储和计算成本，因为我们会在每个已删除的数据库的每个删除数据库保留时间（最低为7天）时保留备份存储成本。 



### <a name="monitor-consumption"></a>监视器消耗

每种类型的备份（完整备份、差异备份和日志备份）都作为单独的指标在 "数据库监视" 边栏选项卡上报告。 下图显示了如何监视单一数据库的备份存储消耗情况。 此功能当前不可用于托管实例。

![监视 Azure 门户的 "数据库监视" 边栏选项卡上的数据库备份消耗](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>微调备份存储消耗

多余的备份存储消耗将取决于各个数据库的工作负荷和大小。 可以考虑实现以下某些优化技术，进一步减少备份存储消耗：

* 将[备份保持期](#change-pitr-backup-retention-period-using-azure-portal)缩短到可能的最小需求。
* 避免比需要更频繁地执行大型写入操作，如索引重新生成。
* 对于大型数据加载操作，请考虑使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非聚集索引的数目，并考虑行计数约为1000000的大容量加载操作。
* 在常规用途的服务层中，预配的数据存储的成本低于额外的备份存储的价格，因为在这种情况下，可能需要增加数据存储，以便将数据存储在备份存储。
* 在 ETL 逻辑中使用 TempDB 来存储临时结果，而不是永久表（仅适用于托管实例）。
* 对于不包含敏感数据的数据库（例如，开发或测试数据库），请考虑关闭 TDE 加密。 通常使用较高的压缩率压缩非加密数据库的备份。

> [!IMPORTANT]
> 对于分析和数据仓库 \ 数据仓库工作负荷，强烈建议使用[聚集列存储索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，减少非聚集索引的数量，还可以考虑使用行数大约为1000000的大容量加载操作来减少多余的备份存储消耗。


## <a name="storage-costs"></a>存储成本

如果使用 DTU 模型或 vCore 模型，存储的价格会有所不同。 

### <a name="dtu-model"></a>DTU 模型

使用 DTU 模型对数据库和弹性池的备份存储不收取额外费用。 

### <a name="vcore-model"></a>vCore 模型

对于单一数据库，提供了等于100% 的数据库大小的最小备份存储量，无需额外付费。 对于弹性池和托管实例，提供的最小备份存储量仅等于为池或实例大小分配的数据存储的100%，无需额外付费。 超出此部分的其他备份存储用量将以 GB 为单位每月进行收费。 这一额外消耗将取决于各个数据库的工作负荷和大小。

Azure SQL DB 会将总保留备份存储计算为累积值。 每小时将此值报告给 Azure 计费管道，该管道负责聚合这一小时的使用情况，以便在每月结束时使用。 删除数据库后，会将使用情况减少为备份保留时间。 一旦它们早于保持期，计费就会停止。 由于所有日志备份和差异备份都保留了完整的保留期，因此，修改过的数据库将具有更高的备份费用。 

假设数据库的备份存储量为 744 GB，此数量在整个月内保持不变。 若要将此累积存储消耗量转换为每小时使用，我们将除以744.0 （每月31天 * 24 小时/天）。 因此，SQL DB 每小时报告数据库使用了 1 GB 的 PITR 备份。 Azure 计费将聚合此项，并显示整月 744 GB 的使用情况，并根据你所在地区的 $/GB/mo 费率计算成本。 

现在，这是一个更复杂的示例。 假设数据库的保留期增加到了月份中间的14天，此（假设）会导致总备份存储量翻倍到 1488 GB。 SQL DB 会报告小时1-372 的使用量为 1 GB，然后将其报告为 2 GB，时间为373-744。 这会被聚合为 1116 GB/mo 的最终帐单。 

### <a name="monitor-costs"></a>监视成本

若要了解备份存储成本，请从 Azure 门户中转到 "**成本管理 + 计费**"，选择 "**成本管理**"，然后选择 "**成本分析**"。 选择所需的订阅作为**作用域**，然后筛选所需的时间段和服务。 

为 "**服务名称**" 添加筛选器，然后从下拉菜单中选择 " **sql 数据库**"。 使用 "**计量子类别**" 筛选器选择服务的帐单计数器。 对于单一数据库或弹性池，请选择 "**单一/弹性池 pitr 备份存储**"。 对于托管实例，请选择 " **mi pitr 备份存储**"。 尽管**存储**和**计算**子类别不与备份存储成本相关联，但也可能会对其感兴趣。 

![备份存储成本分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>备份保留

所有 Azure SQL 数据库（单一数据库、共用实例数据库和托管实例数据库）的默认备份保持期为**七**天。 你可以[将备份保持期更改为最多35天](#change-pitr-backup-retention-period)。

如果删除一个数据库，SQL 数据库将保留其备份，就像保留联机数据库的备份一样。 例如，如果删除保留期为 7 天的基本数据库，已保留 4 天的备份将继续保存 3 天。

如果需要备份保留时间超过最长的保持期，则可以修改备份属性，以向数据库添加一个或多个长期保持期。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果删除了托管 SQL 数据库的 Azure SQL 服务器，则属于该服务器的所有弹性池和数据库也会被删除且不可恢复。 无法还原已删除的服务器。 但是，如果配置了长期保留，将不会删除具有 LTR 的数据库的备份，并且可以还原这些数据库。

## <a name="encrypted-backups"></a>加密备份

如果使用 TDE 加密数据库，备份会在静止时自动加密，包括 LTR 备份。 为 Azure SQL 数据库启用 TDE 时，也会加密备份。 默认情况下，将所有新的 Azure SQL 数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>备份完整性

在日常工作中，Azure SQL 数据库工程团队会自动测试还原逻辑服务器和弹性池中的数据库的自动数据库备份（这在托管实例中不可用）。 在时间点还原时，数据库还使用 DBCC CHECKDB 接收完整性检查。

托管实例在迁移完成后使用本机 `RESTORE` 命令或数据迁移服务还原 `CHECKSUM` 数据库的自动初始备份。

在完整性检查期间发现的任何问题都将导致向工程团队发出警报。 有关 Azure SQL 数据库中数据完整性的详细信息，请参阅 [Azure SQL 数据库中的数据完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="compliance"></a>合规性

将数据库从基于 DTU 的服务层迁移到基于 vCore 的服务层时，会保留 PITR 保留，以确保应用程序的数据恢复策略不会受到侵害。 如果默认保留期不满足符合性要求，可以使用 PowerShell 或 REST API 更改 PITR 保留期。 有关详细信息，请参阅[更改备份保持期](#change-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>更改 PITR 备份保持期

你可以使用 Azure 门户、PowerShell 或 REST API 更改默认的 PITR 备份保留期。 以下示例演示如何将 PITR 保留期更改为 28 天。

> [!WARNING]
> 如果你缩短当前的保留期，则早于新的保持期的所有现有备份都将不再可用。 如果延长当前保留期，SQL 数据库将保留现有备份，直至达到更长的保留期。

> [!NOTE]
> 这些 API 将只影响 PITR 保留期。 如果为数据库配置了 LTR，它将不受影响。 有关如何更改 LTR 保持期的详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>使用 Azure 门户更改 PITR 备份保持期

若要使用 Azure 门户更改 PITR 备份保持期，请导航到要在门户中更改其保持期的服务器对象，然后根据要修改的服务器对象选择合适的选项。

#### <a name="single-database--elastic-pools"></a>[单一数据库和弹性池](#tab/single-database)

在服务器级别执行单个 Azure SQL 数据库的 PITR 备份保留。 在服务器级别所做的更改适用于该服务器上的数据库。 若要从 Azure 门户更改 Azure SQL 数据库服务器的 PITR，请导航到 "服务器概述" 边栏选项卡，在导航菜单上单击 "管理备份"，然后单击导航栏中的 "配置保留"。

![更改 PITR Azure 门户](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[托管实例](#tab/managed-instance)

SQL 数据库托管实例的 PITR 备份保留更改是在单个数据库级别执行的。 若要从 Azure 门户更改实例数据库的 PITR 备份保留，请导航到 "单个数据库概述" 边栏选项卡，然后在导航栏上单击 "配置备份保留"。

![更改 PITR Azure 门户](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>使用 PowerShell 更改 PITR 备份保留期

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 数据库仍支持 PowerShell Azure 资源管理器模块，但所有将来的开发都适用于 Az .Sql 模块。 有关这些 cmdlet，请参阅[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令的参数完全相同。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>使用 REST API 更改 PITR 保留期

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
- 要使用 Azure 门户还原到某个时间点，请参阅[使用 Azure 门户将数据库还原到某个时间点](sql-database-recovery-using-backups.md)。
- 要使用 PowerShell 还原到某个时间点，请参阅[使用 PowerShell 将数据库还原到某个时间点](scripts/sql-database-restore-database-powershell.md)。
- 若要使用 Azure 门户在 Azure Blob 存储中配置和管理自动备份的长期保留，并从中进行还原，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
- 若要使用 PowerShell 在 Azure Blob 存储中配置、管理和还原自动备份的长期保留，请参阅[使用 Powershell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
