---
title: 云业务连续性-数据库恢复
description: 了解 Azure SQL 数据库如何支持云业务连续性和数据库恢复以及如何帮助保持运行任务关键型云应用程序。
keywords: 业务连续性, 云业务连续性, 数据库灾难恢复, 数据库恢复
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 5a6a96f478c4402a830cc522657f56cfd11fa56f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821825"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>使用 Azure SQL 数据库确保业务连续性的相关概述

Azure SQL 数据库中的**业务连续性**是指在遇到中断（尤其是计算基础结构的中断）时，使企业能够继续运营的机制、策略和过程。 在大多数情况下，Azure SQL 数据库将会处理云环境中可能发生的中断事件，并让应用程序和业务流程保持运行。 但是，SQL 数据库无法自动处理某些中断事件，例如：

- 用户意外删除或更新了表中的某行。
- 恶意攻击者成功删除了数据或数据库。
- 地震导致断电和暂时性的数据中心停运。

本概述介绍 Azure SQL 数据库针对业务连续性和灾难恢复所提供的功能。 了解用于从干扰性事件恢复的选项、建议和教程，这些事件可能导致数据丢失或者数据库和应用程序无法使用。 了解对一些情况的处理方式，包括用户或应用程序错误影响数据完整性、Azure 区域服务中断，或者应用程序需要维护。

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>有利于业务连续性的 SQL 数据库功能

从数据库角度看，主要有四种潜在的中断场景：

- 影响数据库节点的本地硬件或软件故障，例如磁盘驱动器故障。
- 通常由应用程序 bug 或人为失误导致的数据损坏或删除。 此类故障特定于应用程序，通常无法通过数据库服务检测到。
- 可能由自然灾害造成的数据中心服务中断。 针对这种情况，需要采取某种程度的异地冗余，并将应用程序故障转移到备用数据中心。
- 升级或维护错误 - 在执行计划内基础结构维护或升级期间发生意外的问题时，可能需要快速回退到以前的数据库状态。

为了缓解本地硬件和软件故障问题，SQL 数据库包含一个[高可用性体系结构](sql-database-high-availability.md)，可以保证在出现这些故障时自动进行恢复，其可用性 SLA 高达 99.995%。  

为了防止企业丢失数据，SQL 数据库每周自动创建完整数据库备份，每 12 小时自动创建差异数据库备份，每隔 5 - 10 分钟自动创建事务日志备份。 所有服务层级的备份在 RA-GRS 存储中存储至少 7 天。 对于时间点还原，所有服务层级（“基本”除外）都支持可配置的备份保留期，最长为 35 天。 

SQL 数据库还提供多种业务连续性功能，用于缓解各种计划外方案问题。 

- 使用[时态表](sql-database-temporal-tables.md)可以从任意时间点还原行版本。
- 使用[内置自动备份](sql-database-automated-backups.md)和[时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)可将整个数据库还原到已配置保留期（最长为 35 天）内的某个时间点。
- 如果 [SQL 数据库服务器尚未删除](sql-database-recovery-using-backups.md#deleted-database-restore)，可将**已删除的数据库还原**到删除时的时间点。
- 使用[长期备份保留](sql-database-long-term-retention.md)可将备份保留长达 10 年之久。
- 使用[活动异地复制](sql-database-active-geo-replication.md)，可以创建可读取的副本，并且在发生数据中心中断或应用程序升级期间手动故障转移到任何副本。
- 发生数据中心中断时，应用程序可以通过[自动故障转移组](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities)自动恢复。

## <a name="recover-a-database-within-the-same-azure-region"></a>恢复同一 Azure 区域内的数据库

可以使用自动数据库备份将数据库还原到过去的某个时间点。 可以通过这种方式从人为错误导致的数据损坏中恢复。 使用时间点还原，可以在同一服务器中创建新的数据库，该数据库表示损坏事件之前的数据状态。 大多数数据库的还原操作需要不到 12 小时的时间。 恢复非常大或十分活跃的数据库可能需要更长时间。 有关恢复时间的详细信息，请参阅[数据库恢复时间](sql-database-recovery-using-backups.md#recovery-time)。 

如果支持的最长时间点还原 (PITR) 备份保留期对你的应用程序而言不足，可以通过为数据库配置长期保留 (LTR) 策略来延长保留期。 有关详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

## <a name="compare-geo-replication-with-failover-groups"></a>将异地复制与故障转移组进行比较

[自动故障转移组](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities)简化了[异地复制](sql-database-active-geo-replication.md)的部署和使用，并添加了其他功能，如下表中所述：

|                                              | 异地复制 | 故障转移组  |
|:---------------------------------------------| :-------------- | :----------------|
| 自动故障转移                           |     否          |      是         |
| 同时故障转移多个数据库  |     否          |      是         |
| 在故障转移后更新连接字符串      |     是         |      否          |
| 支持托管实例                   |     否          |      是         |
| 可以与主服务器位于同一区域             |     是         |      否          |
| 多个副本                            |     是         |      否          |
| 支持读取缩放                          |     是         |      是         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>将数据库恢复到现有服务器

Azure 数据中心会罕见地发生中断。 发生中断时，业务可能仅中断几分钟，也可能持续数小时。

- 用户可以选择等待数据中心中断结束，数据库重新联机。 这适用于可以容忍数据库脱机的应用程序。 例如无需一直处理的开发项目或免费试用版。 数据中心中断时，不知道中断会持续多久，因此该选项仅适用于暂时不需要数据库的情况。
- 另一个选项是使用[异地冗余数据库备份](sql-database-recovery-using-backups.md#geo-restore)（异地还原）来还原任何 Azure 区域中任何服务器上的数据库。 异地还原使用异地冗余备份作为源，即使由于停电而无法访问数据库或数据中心，也依然能够使用它来恢复数据库。
- 最后，如果你使用[活动异地复制](sql-database-active-geo-replication.md)配置了异地辅助数据库或者为数据库配置了[自动故障转移组](sql-database-auto-failover-group.md)，则可以快速从中断中恢复。 你可以使用手动或自动故障转移，具体取决于你选择的这些技术。 虽然故障转移本身只需几秒钟，但服务至少需要 1 小时才能激活它。 这是确保故障转移符合中断规模所必需的。 此外，由于异步复制的性质，故障转移可能导致少量数据丢失。 

制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从计划外中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限（时间间隔）。 可能的数据丢失称为恢复点目标 (RPO)。

不同的恢复方法提供不同级别的 RPO 和 RTO。 可以选择特定的恢复方法，也可以使用各种方法的组合，以便实现应用程序的完全恢复。 下表比较了每个恢复选项的 RPO 和 RTO。 自动故障转移组简化了异地复制的部署和使用，并添加了其他功能，如下表所述。

| 恢复方法 | RTO | RPO |
| --- | --- | --- | 
| 从异地复制的备份执行异地还原 | 12 小时 | 1 小时 |
| 自动故障转移组 | 1 小时 | 5 秒 |
| 手动数据库故障转移 | 30 秒 | 5 秒 |

> [!NOTE]
> “手动数据库故障转移”是指使用[计划外模式](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities)将单一数据库故障转移到其异地复制的辅助数据库。
有关自动故障转移 RTO 和 RPO 的详细信息，请参阅本文中前面的表。


如果应用程序符合以下任意条件，请使用自动故障转移组：

- 是任务关键型应用程序。
- 具有服务级别协议 (SLA)，不允许停机 12 小时或更长时间。
- 停机可能导致财务责任。
- 具有很高的数据更改率，而且不接受丢失 1 小时的数据。
- 活动异地复制的额外成本低于潜在财务责任和相关业务损失所付出的代价。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

可以根据应用程序需求，选择结合使用数据库备份与活动异地复制。 若要探讨使用这些业务连续性功能为独立数据库和弹性池进行设计时的注意事项，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。

以下各节概述使用数据库备份或活动异地复制进行恢复的步骤。 若要了解包括计划需求在内的详细步骤、恢复后步骤，以及如何模拟中断以执行灾难恢复演练，请参阅[在中断时恢复 SQL 数据库](sql-database-disaster-recovery.md)。

### <a name="prepare-for-an-outage"></a>为中断做好准备

无论使用哪种业务连续性功能，都必须：

- 识别并准备目标服务器，包括服务器级 IP 防火墙规则、登录名和 master 数据库级权限。
- 确定如何将客户端和客户端应用程序重定向到新服务器
- 记录其他依赖项，例如审核设置和警报

如果准备不适当，故障转移或恢复数据库后，需要花更多时间让应用程序联机，还有可能要在这种情况下进行故障排除 — 可谓雪上加霜。

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>故障转移到异地复制的辅助数据库

如果你使用活动异地复制或自动故障转移组作为恢复机制，则可以配置自动故障转移策略，或使用[手动计划外故障转移](sql-database-active-geo-replication-portal.md#initiate-a-failover)。 一旦启用，辅助数据库通过故障转移提升为新的主数据库，它可以记录新事务并响应查询 - 仅丢失极少尚未复制的数据。 有关设计故障转移过程的信息，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)。

> [!NOTE]
> 当数据中心恢复联机，旧主数据库自动重新连接至新主数据库，且其自身转为辅助数据库。 如果需要将主数据库重定位至原始区域，可以手动启动计划的故障转移（故障回复）。

### <a name="perform-a-geo-restore"></a>执行异地还原

如果要将自动备份用于异地冗余存储（默认情况下已启用），则可以使用[异地还原](sql-database-disaster-recovery.md#recover-using-geo-restore)恢复数据库。 恢复通常在 12 小时内进行 - 最多丢失 1 小时的数据，具体取决于上次日志备份的执行和复制时间。 在恢复完成之前，数据库无法记录任何事务或响应任何查询。 请注意，异地还原仅将数据库还原到上一个可用时间点。

> [!NOTE]
> 如果数据中心在应用程序切换到恢复的数据库之前就重新联机，只要取消恢复即可。

### <a name="perform-post-failover--recovery-tasks"></a>执行故障转移/恢复后任务

从任一恢复机制恢复后，都必须执行以下附加任务，用户和应用程序才能重新运行：

- 将客户端和客户端应用程序重定向到新服务器和还原的数据库
- 确保设置适当的服务器级 IP 防火墙规则，以便用户能够连接或使用[数据库级防火墙](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)，启用合适的规则。
- 确保设置适当的登录名和 master 数据库级权限（或使用[包含用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable)）
- 视情况配置审核
- 视情况配置警报

> [!NOTE]
> 如果使用故障转移组并使用读写侦听器连接到数据库，则在故障转移后应用程序将自动透明地进行重定向。

## <a name="upgrade-an-application-with-minimal-downtime"></a>在停机时间最短的情况下升级应用程序

有时，应用程序由于计划内维护（例如应用程序升级）而必须脱机。 [管理应用程序升级](sql-database-manage-application-rolling-upgrade.md)介绍如何使用活动异地复制滚动升级云应用程序，将升级时的停机时间缩到最短，并在发生错误时提供恢复路径。

## <a name="next-steps"></a>后续步骤

若要探讨为独立数据库和弹性池设计应用程序时的注意事项，请参阅[设计用于云灾难恢复的应用程序](sql-database-designing-cloud-solutions-for-disaster-recovery.md)和[弹性池灾难恢复策略](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)。
