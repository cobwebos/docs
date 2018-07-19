---
title: Azure SQL 数据库自动化异地冗余备份 | Microsoft Docs
description: SQL 数据库每隔几分钟会自动创建一个本地数据库备份，使用 Azure 读取访问异地冗余存储来提供异地冗余。
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 05/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 558480d0e58a92277a0c56d0f197ee3b5c1c3f60
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "35631765"
---
# <a name="learn-about-automatic-sql-database-backups"></a>了解 SQL 数据库自动备份

SQL 数据库会自动创建数据库备份，并使用 Azure 读取访问异地冗余存储 (RA-GRS) 来提供异地冗余。 可以自动创建这些备份且不收取额外费用。 无需执行任何操作即可创建这些备份。 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果安全规则要求备份在较长时间内可用，可以配置长期备份保留策略。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？

SQL 数据库使用 SQL Server 技术创建[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差异](http://msdn.microsoft.com/library/ms175526.aspx)和[事务日志](https://msdn.microsoft.com/library/ms191429.aspx)备份，以便用于时间点还原 (PITR)。 一般每隔 5-10 分钟创建一次事务日志备份，具体频率取决于性能级别和数据库活动量。 借助事务日志备份以及完整备份和差异备份，可将数据库还原到托管数据库的服务器上的特定时间点。 还原数据库时，服务会确定需要还原哪些完整、差异备份和事务日志备份。


可使用这些备份执行以下任务：

* 在保留期内将数据库还原到某个时间点。 此操作会在与原始数据库相同的服务器中创建新数据库。
* 将已删除的数据库还原到删除时的时间点或保留期内的任意时间点。 仅可在创建原始数据库所在的同一服务器中还原已删除的数据库。
* 将数据库还原到其他地理区域。 在无法访问服务器和数据库的情况下，此操作可帮助从地理位置灾难中恢复， 它会在全球任意位置的任意现有服务器中创建新数据库。 
* 如果数据库已配置了长期保留策略 (LTR)，请从特定的长期备份还原数据库。 此操作允许还原旧版本的数据库，以满足符合性请求或运行旧版本的应用程序。 请参阅[长期保留](sql-database-long-term-retention.md)。
* 若要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语*复制*是指将文件从一个位置复制到另一个位置。 SQL 的“数据库复制”是指让多个辅助数据库与主数据库保持同步。 
> 

## <a name="how-long-are-backups-kept"></a>备份保留多长时间？
每个 SQL 数据库备份都有一个基于数据库服务层的默认保留期，并且[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型（预览版）](sql-database-service-tiers-vcore.md)之间存在差异。 可以更新数据库的备份保留期。 请参阅[更改备份保留期](#how-to-change-backup-retention-period)，了解详细信息。

如果删除一个数据库，SQL 数据库将保留其备份，就像保留联机数据库的备份一样。 例如，如果删除保留期为 7 天的基本数据库，已保留 4 天的备份将继续保存 3 天。

如果需要备份保留时间超过最长的 PITR 保持期，可以修改备份属性，向数据库添加一个或多个长期保持期。 请参阅[长期备份保留](sql-database-long-term-retention.md)，了解详细信息。

> [!IMPORTANT]
> 如果删除了托管 SQL 数据库的 Azure SQL 服务器，则属于该服务器的所有弹性池和数据库也会被删除且不可恢复。 无法还原已删除的服务器。 但是，如果配置了长期保留，将不会删除具有 LTR 的数据库的备份，并且可以还原这些数据库。

### <a name="pitr-retention-for-dtu-based-service-tiers"></a>基于 DTU 的服务层的 PITR 保留
使用基于 DTU 的购买模型创建的数据库的默认保持期取决于服务层：

* 基本服务层为 1 周。
* 标准服务层为 5 周。
* 高级服务层为 5 周。

如果缩短当前 PITR 保持期，早于新保持期的所有现有备份将不再可用。 

如果延长当前 PITR 保持期，SQL 数据库将保留现有备份，直至达到更长的保持期。

### <a name="pitr-retention-for-the-vcore-based-service-tiers-preview"></a>基于 vCore 的服务层的 PITR 保留（预览）

在预览期间，使用基于 vCore 的购买模型创建的数据库的 PITR 保持期设置为 7 天。 已包含免费的关联存储。    


## <a name="how-often-do-backups-happen"></a>备份频率如何？
### <a name="backups-for-point-in-time-restore"></a>时间点还原的备份
SQL 数据库通过自动创建完整备份、差异备份和事务日志备份支持时间点还原 (PITR) 的自助服务。 每周创建完整数据库备份，每隔数小时创建差异数据库备份，每隔 5-10 分钟创建事务日志备份。 会在数据库创建后立即计划第一次完整备份。 完整备份通常可在 30 分钟内完成，但如果数据库很大，花费的时间可能更长。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，会在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。

PITR 备份异地冗余且受 [Azure 存储跨区域复制](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保护

有关详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>长期保留的备份
SQL 数据库提供选项，用于将完整备份的长期保留 (LTR) 配置为最多 10 年。 如果启用了 LTR 策略，每周完整备份将自动复制到不同的 RA-GRS 存储容器。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 存储消耗量取决于所选的备份频率和保留期。 可以使用 [LTR 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=sql-database)来估算 LTR 存储成本。 

类似于 PITR，LTR 备份异地冗余且受 [Azure 存储跨区域复制](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)保护。

有关详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

## <a name="are-backups-encrypted"></a>备份已加密？

如果使用 TDE 加密数据库，备份会在静止时自动加密，包括 LTR 备份。 为 Azure SQL 数据库启用 TDE 时，也会加密备份。 默认情况下，将所有新的 Azure SQL 数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="how-do-automated-backups-impact-my-compliance"></a>自动备份会如何影响符合性？

将数据库从默认 PITR 保留期为 35 天的基于 DTU 的服务层迁移到基于 vCore 的服务层时，将保留 PITR 保留期以确保不会违反应用程序的数据恢复策略。 如果默认保留期不满足符合性要求，可以使用 PowerShell 或 REST API 更改 PITR 保留期。 请参阅[更改备份保留期](#how-to-change-backup-retention-period)，了解详细信息。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>如何更改备份保留期
可以使用 REST API 或 PowerShell 更改默认保留期。 支持的值为：7、14、21、28 或 35 天。以下示例演示如何将 PITR 保留期更改为 28 天。 

> [!NOTE]
> API 将只影响 PITR 保留期。 如果为数据库配置了 LTR，它将不受影响。 请参阅[长期备份保留](sql-database-long-term-retention.md)，详细了解如何更改 LTR 保留期。

### <a name="change-pitr-backup-retention-period-using-powershell"></a>使用 PowerShell 更改 PITR 备份保留期
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> 此 API 包含在从 [4.7.0-预览](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview)版开始的 AzureRM.Sql PowerShell 模块中。 

### <a name="change-pitr-retention-period-using-rest-api"></a>使用 REST API 更改 PITR 保留期
**示例请求**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**请求正文**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**示例响应**

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
请参阅[备份保留 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)，了解详细信息。

## <a name="next-steps"></a>后续步骤

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
- 要使用 Azure 门户还原到某个时间点，请参阅[使用 Azure 门户将数据库还原到某个时间点](sql-database-recovery-using-backups.md)。
- 要使用 PowerShell 还原到某个时间点，请参阅[使用 PowerShell 将数据库还原到某个时间点](scripts/sql-database-restore-database-powershell.md)。
- 若要使用 Azure 门户在 Azure 恢复服务保管库中配置和管理自动备份的长期保留，并从中进行还原，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
- 若要使用 PowerShell 在 Azure 恢复服务保管库中配置和管理自动备份的长期保留，并从中进行还原，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
