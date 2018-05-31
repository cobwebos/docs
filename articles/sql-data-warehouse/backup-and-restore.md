---
title: Azure SQL 数据仓库备份和还原 - 快照，异地冗余 | Microsoft Docs
description: 了解 Azure SQL 数据仓库中备份和还原的工作方式。 使用数据仓库备份可以将数据仓库还原到主要区域的某个还原点。 使用异地冗余备份可还原到不同的地理区域。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a4f24aad95f13315eaeac790c9006ca00f61af69
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187593"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的备份和还原
了解 Azure SQL 数据仓库中备份和还原的工作方式。 使用数据仓库备份可以将数据仓库还原到主要区域的某个还原点。 使用异地冗余备份可还原到不同的地理区域。 

## <a name="what-is-backup-and-restore"></a>什么是备份和还原？
数据仓库备份是可用于还原数据仓库的数据库的副本。  由于 SQL 数据仓库属于分布式系统，因此数据仓库备份包含许多位于 Azure 存储中的文件。 数据仓库备份包括本地数据库快照、所有数据库的异地备份以及与数据仓库关联的文件。 

数据仓库还原是指通过现有数据仓库或已删除数据仓库的备份创建的新数据仓库。 还原的数据仓库重新创建了在特定时间备份的数据仓库。 还原数据仓库是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库还原可以在意外损坏或删除数据后重新创建数据。

本地和异地还原功能均为 SQL 数据仓库灾难恢复功能的一部分。 

## <a name="local-snapshot-backups"></a>本地快照备份
本地快照备份是该服务的内置功能。  无需启用它们。 

SQL 数据仓库全天对数据仓库捕获快照。 快照的可用期为七天。 SQL 数据仓库支持八小时恢复点目标 (RPO)。 可将主要区域中的数据仓库还原至过去七天内所捕获的任意快照中。

若要查看上一个快照的启动时间，可对联机 SQL 数据仓库运行以下查询。 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>数据仓库被暂停时的快照保留期
暂停数据仓库时，SQL 数据仓库将不会创建快照，也不会让快照过期。 暂停数据仓库时，快照年龄不变。 快照保留期取决于数据仓库处于联机状态的天数，而不是日历日期。

例如，如果快照的启动时间是 10 月 1 日下午 4 点，而数据仓库在 10 月 3 日下午 4 点暂停，则快照的保留期最多为 2 天。 不管数据仓库何时回到联机状态，快照的保留期始终为 2 天。 如果数据仓库在 10 月 5 日下午 4 点回到联机状态，则快照的年龄为 2 天，还剩 5 天过期。

当数据仓库回到联机状态以后，SQL 数据仓库将继续创建新的快照，并且会让其数据年龄超过 7 天的快照过期。

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>删除数据仓库时的快照保留期
删除数据仓库时，SQL 数据仓库将创建一个最终快照并保存七天。 可以将数据仓库还原至删除时所创建的最终还原点。 

> [!IMPORTANT]
> 如果删除某个逻辑 SQL Server 实例，则属于该实例的所有数据库也会删除，且无法恢复。 无法还原已删除的服务器。
> 

## <a name="geo-backups"></a>异地备份
SQL 数据仓库对[已配对数据中心](../best-practices-availability-paired-regions.md)每天执行一次异地备份。 异地还原的 RPO 为 24 小时。 你可以将异地备份恢复到支持 SQL 数据仓库的任何其他地区的服务器。 异地备份可确保用户能够在无法访问主要区域中的快照的情况下还原数据仓库。

默认情况下，异地备份处于启用状态。 如果数据仓库为 Gen1，则可按需[选择退出](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)。 请勿选择退出 Gen2 的异地备份，因为数据保护是内置保证。

## <a name="backup-costs"></a>备份成本
Azure 帐单上将列出 Azure 高级存储的明细项目以及异地冗余存储的明细项目。 高级存储费用是指在主要区域中存储数据（包括快照）的总费用。  异地冗余费用是指存储异地备份的费用。  

主数据仓库和 7 天 Azure Blob 快照的总费用按最接近的 TB 数进行计算。 例如，如果数据仓库为 1.5 TB，快照使用了 100 GB，则会按 2 TB 的数据以 Azure 高级存储费率计费。 

> [!NOTE]
> 每个快照最初是空的，随着用户对主数据仓库的更改而增大。 所有快照的大小都随数据仓库的更改而增加。 因此，快照的存储费用也会随因更改而导致的费率变化而增加。
> 
> 

如果使用的是异地冗余存储，则会单独收取异地存储费。 异地冗余存储按标准的读取访问异地冗余存储 (RA-GRS) 费率计费。

有关 SQL 数据仓库定价的详细信息，请参阅 [SQL 数据仓库定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="restoring-from-restore-points"></a>从还原点还原
每个快照都有一个还原点，代表启动快照的时间。 要还原数据仓库，请选择一个还原点，并发出还原命令。  

SQL 数据仓库始终将备份还原到新的数据仓库。 可以保留还原的数据仓库和当前的数据仓库，也可以删除其中一个。 如果希望使用已还原数据仓库替换当前数据仓库，可使用 [ALTER DATABASE（Azure SQL 数据仓库）](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)的“修改名称”选项对其进行重命名。 

若要还原数据仓库，请参阅[使用 Azure 门户还原数据仓库](sql-data-warehouse-restore-database-portal.md)、[使用 PowerShell 还原数据仓库](sql-data-warehouse-restore-database-powershell.md) 或 [使用 T-SQL 还原数据仓库](sql-data-warehouse-restore-database-rest-api.md)。

若要还原已删除或已暂停的数据仓库，则可以[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。 


## <a name="geo-redundant-restore"></a>异地冗余还原
可以将数据仓库还原到支持所选性能级别的 Azure SQL 数据仓库的任何区域。 

> [!NOTE]
> 若要执行异地冗余还原，不能选择退出此功能。
> 
> 

## <a name="restore-timeline"></a>还原时间线
可以将数据库还原到过去 7 天的任何可用还原点。 快照 4 到 8 小时启动一次，可供使用 7 天。 快照超过 7 天将过期，其还原点不再可用。 

备份不会发生在已暂停数据仓库中。 如果数据仓库暂停超过七天，则不具有任何还原点。 

## <a name="restore-costs"></a>还原费用
已还原的数据仓库的存储费用按 Azure 高级存储费率计算。 

如果暂停还原的数据仓库，则存储费用按 Azure 高级存储费率计算。 暂停的好处是不会对计算资源收费。

有关 SQL 数据仓库定价的详细信息，请参阅 [SQL 数据仓库定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。

## <a name="restore-use-cases"></a>还原用例
数据仓库还原的主要用途是在意外丢失或损坏数据后恢复数据。 数据仓库还原还可用于保留那些时间超过 7 天的备份。 还原备份以后，数据仓库处于联机状态，可以无限次将其暂停以节省计算费用。 暂停的数据库按 Azure 高级存储费率收取存储费用。 

## <a name="next-steps"></a>后续步骤
有关灾难规划的详细信息，请参阅[业务连续性概述](../sql-database/sql-database-business-continuity.md)
