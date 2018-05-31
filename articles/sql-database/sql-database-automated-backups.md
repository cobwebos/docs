---
title: Azure SQL 数据库自动化异地冗余备份 | Microsoft Docs
description: SQL 数据库每隔几分钟会自动创建一个本地数据库备份，使用 Azure 读取访问异地冗余存储来提供异地冗余。
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189062"
---
# <a name="learn-about-automatic-sql-database-backups"></a>了解 SQL 数据库自动备份

SQL 数据库会自动创建数据库备份，并使用 Azure 读取访问异地冗余存储 (RA-GRS) 来提供异地冗余。 可以自动创建这些备份且不收取额外费用。 无需执行任何操作即可创建这些备份。 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 如果想要在自己的存储容器中保存备份，可以配置长期备份保留策略。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

## <a name="what-is-a-sql-database-backup"></a>什么是 SQL 数据库备份？

SQL 数据库使用 SQL Server 技术创建[完整](https://msdn.microsoft.com/library/ms186289.aspx)、[差异](https://msdn.microsoft.com/library/ms175526.aspx)和[事务日志](https://msdn.microsoft.com/library/ms191429.aspx)备份，以便用于时间点还原 (PITR)。 一般每隔 5-10 分钟创建一次事务日志备份，具体频率取决于性能级别和数据库活动量。 借助事务日志备份以及完整备份和差异备份，可将数据库还原到托管数据库的服务器上的特定时间点。 还原数据库时，服务会确定需要还原哪些完整、差异备份和事务日志备份。


可使用这些备份执行以下任务：

* 在保留期内将数据库还原到某个时间点。 此操作会在与原始数据库相同的服务器中创建新数据库。
* 将已删除的数据库还原到删除时的时间点或保留期内的任意时间点。 仅可在创建原始数据库所在的同一服务器中还原已删除的数据库。
* 将数据库还原到其他地理区域。 在无法访问服务器和数据库的情况下，此操作可帮助从地理位置灾难中恢复， 它会在全球任意位置的任意现有服务器中创建新数据库。 
* 如果数据库已配置了长期保留策略，请从特定的长期备份还原数据库。 此操作允许还原旧版本的数据库，以满足符合性请求或运行旧版本的应用程序。 请参阅[长期保留](sql-database-long-term-retention.md)。
* 若要执行还原，请参阅[从备份还原数据库](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存储中，术语*复制*是指将文件从一个位置复制到另一个位置。 SQL *数据库复制*是指让多个辅助数据库与主数据库保持同步。 
> 

## <a name="how-often-do-backups-happen"></a>备份频率如何？
每周执行一次完整数据库备份，通常每隔数小时执行一次差异数据库备份，通常每隔 5-10 分钟执行一次事务日志备份。 会在数据库创建后立即计划第一次完整备份。 完整备份通常可在 30 分钟内完成，但如果数据库很大，花费的时间可能更长。 例如，对已还原的数据库或数据库副本执行初始备份可能需要更长时间。 在完成首次完整备份后，会在后台以静默方式自动计划和管理所有后续备份。 在平衡整体系统工作负荷时，SQL 数据库服务会确定所有数据库备份的确切时间。 

备份存储异地复制根据 Azure 存储复制计划执行。

## <a name="how-long-do-you-keep-my-backups"></a>备份的保留时间有多长？
每个 SQL 数据库备份都有一个基于数据库服务层的保留期，并且[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型（预览版）](sql-database-service-tiers-vcore.md)之间存在差异。 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>基于 DTU 的购买模型的数据保留
在基于 DTU 的购买模型中，数据库的保留期取决于服务层。 各服务层中数据库的保留期如下：

* 基本服务层为 7 天。
* 标准服务层为 35 天。
* 高级服务层为 35 天。
* 常规用途层最多可配置为 35 天（默认为 7 天）*
* 业务关键层（预览版）最多可配置为 35 天（默认为 7 天）*

\* 在预览版期间，备份保留期不可配置，固定为 7 天。

如果将具有较长备份保留期的数据库转换为具有较短保留期的数据库，则比目标层保留期旧的所有现有备份将不再可用。

如果将具有较短保留期的数据库升级为具有较长保留期的数据库，则 SQL 数据库将保留现有备份，直至达到较长的保留期。 

如果删除了某个数据库，SQL 数据库将保存其备份，就像保存联机数据库的备份一样。 例如，假设删除了保留期为 7 天的某个基本数据库。 已保存 4 天的备份将继续保存 3 天。

> [!IMPORTANT]
> 如果删除了托管 SQL 数据库的 Azure SQL 服务器，则属于该服务器的所有数据库也会被删除且不可恢复。 无法还原已删除的服务器。

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>基于 vCore 的购买模型（预览版）的数据保留

为数据库备份分配存储，以支持 SQL 数据库的时间点还原 (PITR) 和长期保留 (LTR) 功能。 此存储空间针对每个数据库单独分配，并根据数据库费用分开计费。 

- **PITR**：自动将各个数据库备份复制到 RA-GRS 存储。 创建新备份时，存储大小动态递增。  存储由每周完整备份、每日差异备份和 5 分钟复制一次的事务日志备份使用。 存储消耗量取决于数据库变化率和保留期。 可单独为每个数据库配置 7 到 35 天的保留期。 提供与 1 倍数据库大小相等的最小存储量，不收取额外费用。 对于大多数数据库而言，此容量足以将备份存储 7 天。 有关详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)
- **LTR**：SQL 数据库提供相应的选项用于将完整备份的长期保留期配置为最多 10 年。 如果启用了 LTR 策略，则这些备份将自动存储在 RA-GRS 存储中，但你可以控制复制备份的频率。 为了满足不同的符合性要求，可为每周、每月和/或每年备份选择不同的保留期。 此配置将定义要为 LTR 备份使用多少存储。 可以使用 LTR 定价计算器来估算 LTR 存储成本。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

## <a name="how-to-extend-the-backup-retention-period"></a>如何延长备份保留期？

如果应用程序要求备份在比最长 PITR 备份保留期更长的时间期限内可用，可以为各个数据库配置长期备份保留策略（LTR 策略）。 允许将内置保留期从最长 35 天延长为最多 10 年。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

使用 Azure 门户或 API 向数据库添加 LTR 策略后，每周完整数据库备份将自动复制到一个单独的用于长期保留的 RA-GRS 存储容器（LTR 存储）。 如果使用 TDE 加密数据库，备份会在静止时自动加密。 SQL 数据库会根据时间戳和 LTR 策略自动删除过期的备份。 在设置策略后，你无需管理备份计划，也不用担心旧文件的清除工作。 可使用 Azure 门户或 PowerShell 查看、还原或删除这些备份。

## <a name="are-backups-encrypted"></a>备份已加密？

为 Azure SQL 数据库启用 TDE 时，也会加密备份。 默认情况下，将所有新的 Azure SQL 数据库都配置为启用 TDE。 有关 TDE 的详细信息，请参阅[使用 Azure SQL 数据库进行透明数据加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>自动备份是否遵守 GDPR？
如果备份包含受常规数据保护法规 (GDPR) 制约的个人数据，则会要求你应用增强的安全措施来保护数据免受未经授权的访问。 为了遵守 GDPR，你需要采用不必访问备份的方法来管理数据所有者的数据请求。  对于短期备份，一种解决方案是将备份时段缩短到 30 天以下（此为完成数据访问请求所允许的时长）。  如果需要长期备份，则建议仅在备份中存储“使用假名的”数据。 例如，如果需要删除或更新有关某人的数据，不需要删除或更新现有备份。 可以在[针对 GDPR 符合性的数据治理](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html)中找到有关 GDPR 最佳做法的更多信息。

## <a name="next-steps"></a>后续步骤

- 数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
- 要使用 Azure 门户还原到某个时间点，请参阅[使用 Azure 门户将数据库还原到某个时间点](sql-database-recovery-using-backups.md)。
- 要使用 PowerShell 还原到某个时间点，请参阅[使用 PowerShell 将数据库还原到某个时间点](scripts/sql-database-restore-database-powershell.md)。
- 若要使用 Azure 门户在 Azure 恢复服务保管库中配置和管理自动备份的长期保留，并从中进行还原，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
- 若要使用 PowerShell 在 Azure 恢复服务保管库中配置和管理自动备份的长期保留，并从中进行还原，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。
