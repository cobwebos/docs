---
title: "云业务连续性 - 数据库恢复 - SQL 数据库 | Microsoft 文档"
description: "了解 Azure SQL 数据库如何支持云业务连续性和数据库恢复以及如何帮助保持运行任务关键型云应用程序。"
keywords: "业务连续性, 云业务连续性, 数据库灾难恢复, 数据库恢复"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 18e5d3f1-bfe5-4089-b6fd-76988ab29822
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: sashan
ms.openlocfilehash: c2c5f18b736c83c281d56e0bdda977a9f8c48101
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2018
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>使用 Azure SQL 数据库确保业务连续性的相关概述

本概述介绍 Azure SQL 数据库针对业务连续性和灾难恢复所提供的功能。 了解用于从干扰性事件恢复的选项、建议和教程，这些事件可能导致数据丢失或者数据库和应用程序无法使用。 了解对一些情况的处理方式，包括用户或应用程序错误影响数据完整性、Azure 区域服务中断，或者应用程序需要维护。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>有利于业务连续性的 SQL 数据库功能

SQL 数据库提供若干业务连续性功能，包括自动备份和可选的数据库复制。 对于最近发生的事务，每种功能在估计恢复时间 (ERT) 和可能丢失的数据方面都有不同的特性。 了解这些选项后，便可从中进行选择 — 在大多数方案中，可以针对不同方案将其搭配使用。 制定业务连续性计划时，需了解应用程序在干扰性事件之后完全恢复的最大可接受时间，即恢复时间目标 (RTO)。 此外，还需要了解从干扰性事件恢复时，应用程序可忍受丢失的最近数据更新（时间间隔）最大数量，即恢复点目标 (RPO)。

下表比较了三种最常见方案的 ERT 和 RPO。

| 功能 | 基本层 | 标准层 | 高级层 |
| --- | --- | --- | --- |
| 从备份执行时间点还原 |7 天内的任何还原点 |35 天内的任何还原点 |35 天内的任何还原点 |
| 从异地复制的备份执行异地还原 |ERT < 12 小时，RPO < 1 小时 |ERT < 12 小时，RPO < 1 小时 |ERT < 12 小时，RPO < 1 小时 |
| 从 Azure 备份保管库还原 |ERT < 12 小时，RPO < 1 周 |ERT < 12 小时，RPO < 1 周 |ERT < 12 小时，RPO < 1 周 |
| 活动异地复制 |ERT < 30 秒，RPO < 5 秒 |ERT < 30 秒，RPO < 5 秒 |ERT < 30 秒，RPO < 5 秒 |

### <a name="use-database-backups-to-recover-a-database"></a>使用数据库备份恢复数据库

SQL 数据库每周自动执行完整数据库备份，每小时自动执行差异数据库备份，每 5 到 10 分钟自动执行事务日志备份，防止企业丢失数据。 对于标准和高级服务层中的数据库，这些备份会在异地冗余存储中存储 35 天；对于基本服务层中的数据库，则存储 7 天。 有关详细信息，请参阅[服务层](sql-database-service-tiers.md)。 如果服务层的保留期不符合企业需求，可[更改服务层](sql-database-service-tiers.md)来延长保留期。 完整和差异数据库备份也会复制到[配对的数据中心](../best-practices-availability-paired-regions.md)，以防数据中心中断。 有关更多详细信息，请参阅[自动数据库备份](sql-database-automated-backups.md)。

如果内置保留期不足以用于应用程序，则可为数据库配置长期保留策略来延长保留期。 有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

通过这些自动数据库备份，可在自己的数据中心以及其他数据中心内从各种干扰性事件中恢复数据库。 使用自动数据库备份时，估计恢复时间取决于若干因素，包括在相同区域同时进行恢复的数据库总数、数据库大小、事务日志大小以及网络带宽。 恢复时间通常少于 12 小时。 恢复到另一个数据区域时，因为每小时会针对异地冗余存储进行差异数据库备份，因此最多只可能丢失 1 小时的数据。

> [!IMPORTANT]
> 若要使用自动备份进行恢复，必须是 SQL Server 参与者角色的成员或订阅所有者 — 请参阅 [RBAC：内置角色](../active-directory/role-based-access-built-in-roles.md)。 可以使用 Azure 门户、PowerShell 或 REST API 进行恢复。 但不能使用 Transact-SQL。
>

如果应用程序符合以下条件，则可以会自动备份作为业务连续性和恢复机制：

* 不是任务关键型应用程序。
* 没有约束性的 SLA，因此，停机 24 小时或更长时间不会导致财务责任。
* 数据更改率低（每小时事务数少），并且最多可接受丢失一小时的数据更改。
* 成本有限。

如需更快速的恢复，请使用[活动异地复制](sql-database-geo-replication-overview.md)（接下来将讨论）。 如果需要从 35 天前一段时间中恢复数据，请使用[长期备份保留](sql-database-long-term-retention.md)。 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>使用活动异地复制和自动故障转移组（预览）缩短恢复时间，并限制与恢复相关的数据丢失

除了在发生业务中断时使用数据库备份来恢复数据库之外，还可以使用[活动异地复制](sql-database-geo-replication-overview.md)来配置数据库，在所选区域中最多可拥有 4 个可读辅助数据库。 这些辅助数据库使用异步复制机制与主数据库保持同步。 在数据中心中断或应用程序升级期间，此功能可以防止出现业务中断。 活动异地复制还可为地理位置分散的用户提高只读查询的查询性能。

要启用自动和透明故障转移，应使用 SQL 数据库的[自动故障转移组](sql-database-geo-replication-overview.md)（预览）功能，将异地复制数据库整理成组。

如果主数据库意外脱机，或者需要脱机维护，可以将辅助数据库快速提升为主数据库（也称为故障转移），并配置应用程序，将它们连接到提升的主数据库。 如果使用故障转移组侦听器将应用程序连接到数据库，则在故障转移后无需更改 SQL 连接字符串配置。 进行计划内故障转移时，不会丢失任何数据。 进行计划外故障转移时，由于异步复制的性质使然，最近的事务可能会丢失少量数据。 使用自动故障转移组（预览），可以自定义故障转移策略，将潜在数据丢失降低到最小。 故障转移之后，可以根据计划或在数据中心重新联机时回复故障。 无论在什么情况下，用户都会经历短暂的停机，并需要重新连接。

> [!IMPORTANT]
> 若要使用活动异地复制和自动故障转移组（预览），必须是订阅所有者或在 SQL Server 中拥有管理权限。 可通过 Azure 订阅的权限使用 Azure 门户、PowerShell 或 REST API 进行配置和故障转移，也可以通过 SQL Server 中的权限使用 Transact-SQL 进行配置和故障转移。
> 

如果应用程序符合以下任意条件，就使用活动异地复制和自动故障转移组（预览）：

* 是任务关键型应用程序。
* 具有服务级别协议 (SLA)，不允许停机 24 小时或更长时间。
* 停机可能导致财务责任。
* 具有很高的数据更改率，而且不接受丢失一小时的数据。
* 活动异地复制的额外成本低于潜在财务责任和相关业务损失所付出的代价。

>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>在用户或应用程序错误之后恢复数据库
*人无完人。 用户可能会不小心删除某些数据、无意中删除重要的表，甚至删除整个数据库。 或者，应用程序可能因为自身缺陷，意外以错误数据覆盖正确数据。

在此方案中，可使用以下恢复选项。

### <a name="perform-a-point-in-time-restore"></a>执行时间点还原
可使用自动备份，将数据库副本恢复到数据库保留期内的已知时间点。 数据库还原之后，可以将原始数据库替换为还原的数据库，或从还原的数据将所需数据复制到原始数据库。 如果数据库使用活动异地复制，建议从还原的副本将所需数据复制到原始数据库。 如果将原始数据库替换为还原的数据库，需要重新配置并重新同步活动异地复制（大型数据库可能要花费很长时间）。 尽管这会将数据库还原到最后一个可用时间点，但目前不支持将异地辅助数据库还原到任何时间点。

有关使用 Azure 门户或 PowerShell 将数据库还原至某个时间点的详细信息及步骤，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。 不能使用 Transact-SQL 进行恢复。

### <a name="restore-a-deleted-database"></a>还原已删除的数据库
如果已删除数据库，但逻辑服务器尚未删除，可以将已删除的数据库还原到它被删除的时间点。 这会将数据库备份还原到之前删除数据库的同一个逻辑 SQL 服务器。 可以使用原始名称还原，也可以为还原的数据库提供新名称。

有关使用 Azure 门户或 PowerShell 还原已删除数据库的详细信息和步骤，请参阅[还原已删除数据库](sql-database-recovery-using-backups.md#deleted-database-restore)。 不能使用 Transact-SQL 进行还原。

> [!IMPORTANT]
> 如果逻辑服务器已删除，则无法恢复已删除的数据库。
>
>

### <a name="restore-from-azure-backup-vault"></a>从 Azure 备份保管库还原
如果数据丢失发生在自动备份的当前保留期之外且数据库已配置长期保留，则可从 Azure 备份保管库中的每周备份还原到新数据库。 此时，可以将原始数据库替换为还原的数据库，或从还原的数据库将所需数据复制到原始数据库。 如果需要在升级主要应用程序前检索旧版数据库，满足审核员或法律指令的要求，则可以使用 Azure 备份保管库中保存的完整备份创建数据库。  有关详细信息，请参阅[长期保留](sql-database-long-term-retention.md)。

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Azure 区域数据中心中断时会数据库恢复到另一个区域
<!-- Explain this scenario -->

Azure 数据中心会罕见地发生中断。 发生中断时，业务可能仅中断几分钟，也可能持续数小时。

* 用户可以选择等待数据中心中断结束，数据库重新联机。 这适用于可以容忍数据库脱机的应用程序。 例如无需一直处理的开发项目或免费试用版。 数据中心中断时，不知道中断会持续多久，因此该选项仅适用于暂时不需要数据库的情况。
* 另一个选项是故障转移到另一个数据区域（如果使用活动异地复制）或使用异地冗余数据库备份恢复数据库（异地还原）。 故障转移只需几秒钟，而从备份恢复数据库需要数小时。

执行操作时，恢复所需的时间以及数据丢失量会有所不同，这具体取决于用户要在应用程序中使用哪些业务连续性功能。 事实上，可以根据应用程序需求，选择结合使用数据库备份与活动异地复制。 若要探讨使用这些业务连续性功能为独立数据库和弹性池设计应用程序时的注意事项，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

以下各节概述使用数据库备份或活动异地复制进行恢复的步骤。 若要了解包括计划需求在内的详细步骤、恢复后步骤，以及如何模拟中断以执行灾难恢复演练，请参阅[在中断时恢复 SQL 数据库](sql-database-disaster-recovery.md)。

### <a name="prepare-for-an-outage"></a>为中断做好准备
无论使用哪种业务连续性功能，都必须：

* 识别并准备目标服务器，包括服务器级防火墙规则、登录名和 master 数据库级权限。
* 确定如何将客户端和客户端应用程序重定向到新服务器
* 记录其他依赖项，例如审核设置和警报

如果准备不适当，故障转移或恢复数据库后，需要花更多时间让应用程序联机，还有可能要在这种情况下进行故障排除 — 可谓雪上加霜。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>故障转移到异地复制的辅助数据库
如果使用活动异地复制和自动故障转移组（预览）作为恢复机制，则可以配置自动故障转移策略，或使用[手动故障转移](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group)。 一旦启用，辅助数据库通过故障转移提升为新的主数据库，它可以记录新事务并响应查询 - 仅丢失极少尚未复制的数据。 有关设计故障转移过程的信息，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

> [!NOTE]
> 当数据中心恢复联机，旧主数据库自动重新连接至新主数据库，且其自身转为辅助数据库。 如果需要将主数据库重定位至原始区域，可以手动启动计划的故障转移（故障回复）。 
> 

### <a name="perform-a-geo-restore"></a>执行异地还原
如果将自动备份和异地冗余存储复制搭配作为恢复机制，请[使用异地还原启动数据库恢复](sql-database-disaster-recovery.md#recover-using-geo-restore)。 恢复通常在 12 小时内进行 — 最多丢失 1 小时的数据，具体取决于最后一次每小时差异备份的执行和复制时间。 在恢复完成之前，数据库无法记录任何事务或响应任何查询。 尽管这会将数据库还原到最后一个可用时间点，但目前不支持将异地辅助数据库还原到任何时间点。

> [!NOTE]
> 如果数据中心在应用程序切换到恢复的数据库之前就重新联机，只要取消恢复即可。  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>执行故障转移/恢复后任务
从任一恢复机制恢复后，都必须执行以下附加任务，用户和应用程序才能重新运行：

* 将客户端和客户端应用程序重定向到新服务器和还原的数据库
* 确保设置适当的服务器级防火墙规则，供用户连接（或使用[数据库级防火墙](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)）
* 确保设置适当的登录名和 master 数据库级权限（或使用[包含用户](https://msdn.microsoft.com/library/ff929188.aspx)）
* 视情况配置审核
* 视情况配置警报

## <a name="upgrade-an-application-with-minimal-downtime"></a>在停机时间最短的情况下升级应用程序
有时，应用程序由于计划内维护（例如应用程序升级）而必须脱机。 [管理应用程序升级](sql-database-manage-application-rolling-upgrade.md)介绍如何使用活动异地复制滚动升级云应用程序，将升级时的停机时间缩到最短，并在发生错误时提供恢复路径。 

## <a name="next-steps"></a>后续步骤
若要探讨为独立数据库和弹性池设计应用程序时的注意事项，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
