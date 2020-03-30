---
title: 备份和还原 - 快照、异地冗余
description: 了解备份和还原在 Azure 同步分析 SQL 池中的工作方式。 使用备份将数据仓库还原到主区域中的还原点。 使用异地冗余备份可还原到不同的地理区域。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: ae53380572e753a8bcfa20fcd165fa015766263e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349269"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Azure 突触 SQL 池中的备份和还原

了解如何在 Azure Synapse SQL 池中使用备份和还原。 使用 SQL 池还原点恢复数据仓库或将数据仓库复制到主区域中的上一个状态。 使用数据仓库异地冗余备份可还原到不同的地理区域。

## <a name="what-is-a-data-warehouse-snapshot"></a>什么是数据仓库快照

数据仓库快照会创建一个还原点，利用该还原点可将数据仓库恢复或复制到以前的状态。**  由于 SQL 池是分布式系统，因此数据仓库快照由位于 Azure 存储中的许多文件组成。 快照捕获数据仓库中存储的数据的增量更改。

数据仓库还原是基于现有数据仓库或已删除数据仓库的还原点创建的新数据仓库。** 还原数据仓库是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库还原可以在意外损坏或删除数据后重新创建数据。 此外，数据仓库是出于测试或开发目的创建数据仓库副本的强大机制。  SQL 池还原速率可能因数据库大小和目标数据仓库的位置而异。 

## <a name="automatic-restore-points"></a>自动还原点

快照是创建还原点的服务的内置功能。 不需要启用此功能。 但是，SQL 池应处于活动状态，以便还原点创建。 如果 SQL 池经常暂停，则可能无法创建自动还原点，因此请确保在暂停 SQL 池之前创建用户定义的还原点。 用户目前无法删除自动还原点，因为服务使用这些还原点来维护 SLA 以进行恢复。

数据仓库的快照全天拍摄，创建 7 天的还原点。 无法更改此保留期。 SQL 池支持八小时恢复点目标 （RPO）。 可从过去 7 天创建的任一快照还原主要区域中的数据仓库。

要查看上次快照何时启动，请在联机 SQL 池上运行此查询。

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>用户定义的还原点

使用此功能，可以在大型修改之前和之后手动触发快照，以便创建数据仓库的还原点。 此功能可确保在出现工作负荷中断或用户错误的情况下，还原点在逻辑上是一致的，这样可以提供额外的数据保护，缩短恢复时间。 用户定义的还原点可用 7 天，7 天后系统会自动将其删除。 无法更改用户定义的还原点的保留期。 无论在任何时间点，均会保证 **42 个用户定义的还原点**，因此，它们必须在创建另一个还原点之前[删除](https://go.microsoft.com/fwlink/?linkid=875299)。 可以通过 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) 或 Azure 门户触发快照来创建用户定义的还原点。

> [!NOTE]
> 如需将还原点保留 7 天以上，请在[此处](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)为此功能投票。 此外，可以创建用户定义的还原点，然后从新建的还原点还原到新数据仓库。 还原后，SQL 池将联机，并可以无限期暂停它以节省计算成本。 暂停的数据库按 Azure 高级存储费率收取存储费用。 如需已还原数据仓库的活动副本，可以执行恢复，只需花费几分钟时间。

### <a name="restore-point-retention"></a>还原点保留期

下面列出了还原点保留期的详细信息：

1. SQL 池在达到 7 天保留期**且**至少有 42 个总还原点（包括用户定义和自动）时删除还原点。
2. 暂停 SQL 池时不会拍摄快照。
3. 还原点的年龄由自采集还原点（包括暂停 SQL 池时）起的绝对日历日来衡量。
4. 在任何时候，只要这些还原点未达到 7 天的保留期，SQL 池就保证能够存储多达 42 个用户定义的还原点和 42 个自动还原点
5. 如果拍摄快照，SQL 池将暂停超过 7 天，然后继续，还原点将一直持续到有 42 个总还原点（包括用户定义和自动）

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>删除 SQL 池时的快照保留

删除 SQL 池时，将创建最终快照并保存 7 天。 您可以将 SQL 池还原到删除时创建的最终还原点。 如果 SQL 池处于暂停状态，则不会拍摄快照。 在这种情况下，请确保在删除 SQL 池之前创建用户定义的还原点。

> [!IMPORTANT]
> 如果删除某个逻辑 SQL Server 实例，则属于该实例的所有数据库也会删除，且无法恢复。 无法还原已删除的服务器。

## <a name="geo-backups-and-disaster-recovery"></a>异地备份和灾难恢复

地理备份每天创建一次到[配对的数据中心](../../best-practices-availability-paired-regions.md)。 异地还原的 RPO 为 24 小时。 您可以将地理备份还原到支持 SQL 池的任何其他区域中的服务器。 使用异地备份可在无法访问主要区域中的还原点时还原数据仓库。

> [!NOTE]
> 如果希望异地备份的 RPO 更短，请在[此处](https://feedback.azure.com/forums/307516-sql-data-warehouse)为此功能投票。 此外，可以创建用户定义的还原点，然后从新建的还原点还原到其他区域中的新数据仓库。 还原后，数据仓库将会联机，可以无限期将其暂停，以节省计算成本。 暂停的数据库按 Azure 高级存储费率收取存储费用。 如需数据仓库的活动副本，可以执行恢复，只需花费几分钟时间。

## <a name="backup-and-restore-costs"></a>备份和还原成本

Azure 帐单上将列出存储的明细项目，以及灾难恢复存储的明细项目。 存储费用是将数据存储在主区域以及快照捕获的增量更改的总成本。 有关快照收费方式的更详细说明，请参阅[了解快照如何收取费用](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)。 异地冗余费用是指存储异地备份的费用。  

主数据仓库和 7 天快照更改的总费用根据 TB 数的舍入近似值计算。 例如，如果数据仓库为 1.5 TB，快照捕获了 100 GB，则会根据 Azure 高级存储费率计收 2 TB 数据的费用。

如果使用的是异地冗余存储，则会单独收取异地存储费。 异地冗余存储按标准的读取访问异地冗余存储 (RA-GRS) 费率计费。

有关 Azure 突触定价的详细信息，请参阅[Azure 突触定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。 跨区域还原时，不会对数据流出量收费。

## <a name="restoring-from-restore-points"></a>从还原点还原

每个快照创建一个代表快照开始时间的还原点。 要还原数据仓库，请选择一个还原点，并发出还原命令。  

可以保留还原的数据仓库和当前的数据仓库，也可以删除其中一个。 如果要将当前数据仓库替换为还原的数据仓库，可以使用[ALTER 数据库 （SQL 池）](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)将数据仓库重命名为"修改名称"选项。

要还原数据仓库，请参阅[还原 SQL 池](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)。

若要还原已删除或已暂停的数据仓库，则可以[创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="cross-subscription-restore"></a>交叉订阅还原

如果您需要直接跨订阅进行还原，[请在此处](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)投票选出此功能。 还原到其他逻辑服务器，并在订阅之间["移动"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)服务器以执行交叉订阅还原。 

## <a name="geo-redundant-restore"></a>异地冗余还原

您可以将[SQL 池还原](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell)到任何支持 SQL 池的区域，以您选择的性能级别。

> [!NOTE]
> 若要执行异地冗余还原，不能选择退出此功能。

## <a name="next-steps"></a>后续步骤

有关灾难规划的详细信息，请参阅[业务连续性概述](../../sql-database/sql-database-business-continuity.md)
