---
title: 云业务连续性 - 数据库恢复 - SQL 数据库 | Microsoft 文档
description: 了解 Azure SQL 数据库如何支持云业务连续性和数据库恢复以及如何帮助保持运行任务关键型云应用程序。
keywords: 业务连续性, 云业务连续性, 数据库灾难恢复, 数据库恢复
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/23/2018
ms.openlocfilehash: 9dab136795094350abe29e7d779a22ec1c94ef70
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986640"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>使用 Azure SQL 数据库确保业务连续性的相关概述

Azure SQL 数据库是针对 Azure 云环境配置并优化的最新稳定 SQL Server 数据库引擎的一种实现，它提供[高可用性](sql-database-high-availability.md)，并能够弹性应对可能影响业务流程的错误。 Azure SQL 数据库中的**业务连续性**是指在遇到中断（尤其是计算基础结构的中断）时，使企业能够继续运营的机制、策略和过程。 在大多数情况下，Azure SQL 数据库将会处理云环境中可能发生的中断事件，并让业务流程保持运行。 但是，SQL 数据库无法处理某些中断事件，例如：

- 用户意外删除或更新了表中的某行。
- 恶意攻击者成功删除了数据或数据库。
- 地震导致断电和暂时性的数据中心停运。

Azure SQL 数据库无法控制这种情况，因此，需要使用 SQL 数据库中的业务连续性功能来恢复数据和保持应用程序的运行。

本概述介绍 Azure SQL 数据库针对业务连续性和灾难恢复所提供的功能。 了解用于从干扰性事件恢复的选项、建议和教程，这些事件可能导致数据丢失或者数据库和应用程序无法使用。 了解对一些情况的处理方式，包括用户或应用程序错误影响数据完整性、Azure 区域服务中断，或者应用程序需要维护。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>有利于业务连续性的 SQL 数据库功能

从数据库角度看，主要有四种潜在的中断场景：

- 影响数据库节点的本地硬件或软件故障，例如磁盘驱动器故障。
- 通常由应用程序 bug 或人为失误导致的数据损坏或删除。 此类故障在本质上与特定的应用程序相关，一般而言，基础结构无法检测或缓解它们。
- 可能由自然灾害造成的数据中心服务中断。 针对这种情况，需要采取某种程度的异地冗余，并将应用程序故障转移到备用数据中心。
- 升级或维护错误、计划内升级或维护期间应用程序或数据库发生的意外问题，可能需要快速回退到以前的数据库状态。

SQL 数据库提供多种可以缓解此类情况的业务连续性功能，包括自动备份和可选的数据库复制。 首先，需要了解 SQL 数据库[高可用性体系结构](sql-database-high-availability.md)如何针对可能影响业务流程的某些中断事件提供 99.99% 的可用性和复原能力。
然后，可以了解能够使用其他哪些机制，在发生 SQL 数据库高可用性体系结构无法处理的中断事件时进行恢复，例如：

- 使用[时态表](sql-database-temporal-tables.md)可以从任意时间点还原行版本。
- 使用[内置自动备份](sql-database-automated-backups.md)和[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)可将整个数据库还原到过去 35 天内的某个时间点。
- 如果**逻辑服务器尚未删除**，可[将已删除的数据库还原](sql-database-recovery-using-backups.md#deleted-database-restore)到删除时的时间点。
- 使用[长期备份保留](sql-database-long-term-retention.md)可将备份保留长达 10 年之久。
- 发生数据中心规模的中断时，应用程序可以通过[自动故障转移组](sql-database-geo-replication-overview.md#auto-failover-group-capabilities)自动恢复。

对于最近发生的事务，每种功能在估计恢复时间 (ERT) 和可能丢失的数据方面都有不同的特性。 了解这些选项后，便可从中进行选择 — 在大多数方案中，可以针对不同方案将其搭配使用。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限（时间间隔）。 可以承受更新丢失的时限称为恢复点目标 (RPO)。

下表针对三种最常见方案比较了每个服务层的 ERT 和 RPO。

| 功能 | 基本 | 标准 | 高级 | 常规用途 | 业务关键
| --- | --- | --- | --- |--- |--- |
| 从备份执行时间点还原 |7 天内的任何还原点 |35 天内的任何还原点 |35 天内的任何还原点 |所配置的时间段（最长为 35 天）内的任何还原点|所配置的时间段（最长为 35 天）内的任何还原点|
| 从异地复制的备份执行异地还原 |ERT < 12 小时<br> RPO < 1 小时 |ERT < 12 小时<br>RPO < 1 小时 |ERT < 12 小时<br>RPO < 1 小时 |ERT < 12 小时<br>RPO < 1 小时|ERT < 12 小时<br>RPO < 1 小时|
| 自动故障转移组 |RTO = 1 小时<br>RPO < 5 秒 |RTO = 1 小时<br>RPO < 5 秒 |RTO = 1 小时<br>RPO < 5 秒 |RTO = 1 小时<br>RPO < 5 秒|RTO = 1 小时<br>RPO < 5 秒|

## <a name="recover-a-database-to-the-existing-server"></a>将数据库恢复到现有服务器

SQL 数据库每周自动执行完整数据库备份，每隔 12 小时通常自动执行差异数据库备份，每隔 5 - 10 分钟自动执行事务日志备份，以防止企业丢失数据。 所有服务层的备份在 RA-GRS 存储中存储 35 天；但基本 DTU 服务层除外，其中的备份将存储 7 天。 有关更多详细信息，请参阅[自动数据库备份](sql-database-automated-backups.md)。 可以使用 Azure 门户、PowerShell 或 REST API，将自动备份中的现有数据库还原到早期的时间点，作为同一逻辑服务器上的新数据库。 有关详细信息，请参阅[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)。

如果支持的最长时间点还原 (PITR) 保留期对你的应用程序而言不足，可以通过为数据库配置长期保留 (LTR) 策略来延长保留期。 有关详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

通过这些自动数据库备份，可在自己的数据中心以及其他数据中心内从各种干扰性事件中恢复数据库。 恢复时间通常少于 12 小时。 恢复非常大或活动的数据库可能需要更长时间。 使用自动数据库备份时，估计恢复时间取决于若干因素，包括在相同区域同时进行恢复的数据库总数、数据库大小、事务日志大小以及网络带宽。 有关恢复时间的详细信息，请参阅[数据库恢复时间](sql-database-recovery-using-backups.md#recovery-time)。 恢复到另一个数据区域时，由于使用异地冗余备份，因此最多只可能丢失 1 小时的数据。

如果应用程序符合以下条件，则可将自动备份和[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)用作业务连续性和恢复机制：

- 不是任务关键型应用程序。
- 没有约束性的 SLA，因此，停机 24 小时或更长时间不会导致财务责任。
- 数据更改率低（每小时事务数少），并且最多可接受丢失一小时的数据更改。
- 成本有限。

如需更快速的恢复，请使用[故障转移组](sql-database-geo-replication-overview.md#auto-failover-group-capabilities
)（接下来将讨论）。 如果需要从 35 天前的一个时间段恢复数据，请使用[长期保留](sql-database-long-term-retention.md)。

## <a name="recover-a-database-to-another-region"></a>将数据库恢复到另一区域

Azure 数据中心会罕见地发生中断。 发生中断时，业务可能仅中断几分钟，也可能持续数小时。

- 用户可以选择等待数据中心中断结束，数据库重新联机。 这适用于可以容忍数据库脱机的应用程序。 例如无需一直处理的开发项目或免费试用版。 数据中心中断时，不知道中断会持续多久，因此该选项仅适用于暂时不需要数据库的情况。
- 另一个选项是使用[异地冗余数据库备份](sql-database-recovery-using-backups.md#geo-restore)（异地还原）来还原任何 Azure 区域中任何服务器上的数据库。 异地还原使用异地冗余备份作为源，即使由于停电而无法访问数据库或数据中心，也依然能够使用它来恢复数据库。
- 最后，如果为数据库配置了[自动故障转移组](sql-database-geo-replication-overview.md#auto-failover-group-capabilities)，则可以快速从中断中恢复。 可以自定义故障转移策略以使用自动故障转移或手动故障转移。 虽然故障转移本身只需几秒钟，但服务至少需要 1 小时才能激活它。 这是确保故障转移符合中断规模所必需的。 此外，由于异步复制的性质，故障转移可能导致少量数据丢失。 有关自动故障转移 RTO 和 RPO 的详细信息，请参阅本文中前面的表。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>
> [!IMPORTANT]
> 若要使用活动异地复制和自动故障转移组，则必须是订阅所有者或在 SQL Server 中拥有管理权限。 可通过 Azure 订阅的权限使用 Azure 门户、PowerShell 或 REST API 进行配置和故障转移，也可以通过 SQL Server 中的权限使用 Transact-SQL 进行配置和故障转移。

如果应用程序符合以下任意条件，请使用活动自动故障转移组：

- 是任务关键型应用程序。
- 具有服务级别协议 (SLA)，不允许停机 12 小时或更长时间。
- 停机可能导致财务责任。
- 具有很高的数据更改率，而且不接受丢失 1 小时的数据。
- 活动异地复制的额外成本低于潜在财务责任和相关业务损失所付出的代价。

执行操作时，恢复所需的时间以及数据丢失量会有所不同，这具体取决于用户要在应用程序中使用哪些业务连续性功能。 事实上，可以根据应用程序需求，选择结合使用数据库备份与活动异地复制。 若要探讨使用这些业务连续性功能为独立数据库和弹性池设计应用程序时的注意事项，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

以下各节概述使用数据库备份或活动异地复制进行恢复的步骤。 若要了解包括计划需求在内的详细步骤、恢复后步骤，以及如何模拟中断以执行灾难恢复演练，请参阅[在中断时恢复 SQL 数据库](sql-database-disaster-recovery.md)。

### <a name="prepare-for-an-outage"></a>为中断做好准备

无论使用哪种业务连续性功能，都必须：

- 识别并准备目标服务器，包括服务器级防火墙规则、登录名和 master 数据库级权限。
- 确定如何将客户端和客户端应用程序重定向到新服务器
- 记录其他依赖项，例如审核设置和警报

如果准备不适当，故障转移或恢复数据库后，需要花更多时间让应用程序联机，还有可能要在这种情况下进行故障排除 — 可谓雪上加霜。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>故障转移到异地复制的辅助数据库

如果使用活动异地复制和自动故障转移组作为恢复机制，则可以配置自动故障转移策略，或使用[手动故障转移](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group)。 一旦启用，辅助数据库通过故障转移提升为新的主数据库，它可以记录新事务并响应查询 - 仅丢失极少尚未复制的数据。 有关设计故障转移过程的信息，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

> [!NOTE]
> 当数据中心恢复联机，旧主数据库自动重新连接至新主数据库，且其自身转为辅助数据库。 如果需要将主数据库重定位至原始区域，可以手动启动计划的故障转移（故障回复）。

### <a name="perform-a-geo-restore"></a>执行异地还原

如果要将自动备份用于异地冗余存储（默认情况下已启用），则可以使用[异地还原](sql-database-disaster-recovery.md#recover-using-geo-restore)恢复数据库。 恢复通常在 12 小时内进行 - 最多丢失 1 小时的数据，具体取决于上次日志备份的执行和复制时间。 在恢复完成之前，数据库无法记录任何事务或响应任何查询。 请注意，异地还原仅将数据库还原到上一个可用时间点。

> [!NOTE]
> 如果数据中心在应用程序切换到恢复的数据库之前就重新联机，只要取消恢复即可。

### <a name="perform-post-failover--recovery-tasks"></a>执行故障转移/恢复后任务

从任一恢复机制恢复后，都必须执行以下附加任务，用户和应用程序才能重新运行：

- 将客户端和客户端应用程序重定向到新服务器和还原的数据库
- 确保设置适当的服务器级防火墙规则，供用户连接（或使用[数据库级防火墙](sql-database-firewall-configure.md#creating-and-managing-firewall-rules)）
- 确保设置适当的登录名和 master 数据库级权限（或使用[包含用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)）
- 视情况配置审核
- 视情况配置警报

> [!NOTE]
> 如果使用故障转移组并使用读写侦听器连接到数据库，则在故障转移后应用程序将自动透明地进行重定向。

## <a name="upgrade-an-application-with-minimal-downtime"></a>在停机时间最短的情况下升级应用程序

有时，应用程序由于计划内维护（例如应用程序升级）而必须脱机。 [管理应用程序升级](sql-database-manage-application-rolling-upgrade.md)介绍如何使用活动异地复制滚动升级云应用程序，将升级时的停机时间缩到最短，并在发生错误时提供恢复路径。

## <a name="next-steps"></a>后续步骤

若要探讨为独立数据库和弹性池设计应用程序时的注意事项，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
