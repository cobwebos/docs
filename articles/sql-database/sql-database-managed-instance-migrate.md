---
title: 将数据库从 SQL Server 实例迁移到 Azure SQL 数据库-托管实例 |Microsoft Docs
description: 了解如何将数据库从 SQL Server 实例迁移到 Azure SQL 数据库-托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 11/07/2019
ms.openlocfilehash: 7cf54b79fac87905117e321574571890c59315e6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827077"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>将 SQL Server 实例迁移到 Azure SQL 数据库托管实例

本文介绍了如何将 SQL Server 2005 或更高版本实例迁移到 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。 有关迁移到单一数据库或弹性池的信息，请参阅[迁移到单一数据库或入池数据库](sql-database-cloud-migrate.md)。 有关从其他平台迁移的迁移信息，请参阅 [Azure 数据库迁移指南](https://datamigration.microsoft.com/)。

> [!NOTE]
> 如果你想要快速启动并试用托管实例，您可能想要转到[快速入门指南](/sql-database-managed-instance-quickstart-guide.md)而不是此页。 

概括而言，数据库迁移过程如下所示：

![迁移过程](./media/sql-database-managed-instance-migration/migration-process.png)

- [评估托管的实例兼容性](#assess-managed-instance-compatibility)您应确保不存在阻塞问题可能会阻止迁移。
  - 此步骤还包括创建[性能基线](#create-performance-baseline)若要确定源 SQL Server 实例上的资源使用情况。 如果你想 o，则需要此步骤部署正确大小的托管实例，并验证迁移后的性能不会受到影响。
- [选择应用连接选项](sql-database-managed-instance-connect-app.md)
- [将部署到具有最佳大小的托管实例](#deploy-to-an-optimally-sized-managed-instance)您将在其中选择技术特征 （Vcore 数、 内存量） 和性能层 （业务关键、 常规用途） 的托管实例。
- [选择迁移方法，然后迁移](#select-migration-method-and-migrate)您将使用脱机迁移 （本机备份/还原、 数据库 importe/导出） 或联机迁移 （数据迁移服务、 事务复制） 将数据库迁移。
- [监视应用程序](#monitor-applications)以确保您具有预期性能。

> [!NOTE]
> 若要将单个数据库迁移到单个数据库或弹性池，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-single-database-migrate.md)。

## <a name="assess-managed-instance-compatibility"></a>评估托管实例兼容性

首先，确定托管实例是否与应用程序的数据库要求相符。 托管实例部署选项旨在，方便直接迁移大多数在本地或在虚拟机中使用 SQL Server 的现有应用程序。 但是，我们有时可能需要用到一些目前尚不支持的功能，而实施某种解决方法的成本过高。

使用[数据迁移助手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) 可以检测影响 Azure SQL 数据库功能的潜在兼容性问题。 虽然 DMA 尚不支持将托管实例用作迁移目标，但建议针对 Azure SQL 数据库运行评估，并根据产品文档仔细查看报告的功能奇偶一致性和兼容性问题列表。 请参阅 [Azure SQL 数据库功能](sql-database-features.md)，以检查托管实例中是否不存在报告的一些阻塞性问题，因为大多数阻止迁移到 Azure SQL 数据库的阻塞性问题已通过托管实例进行删除。 例如，托管实例中提供跨数据库查询、同一实例中的跨数据库事务、链接到到其他 SQL 源的链接服务器、CLR、全局临时表、实例级视图、Service Broker 等功能。

如果报告的一些阻塞性问题未通过托管实例部署选项进行删除，可能需要考虑备用选项，如 [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 下面是一些可能的恶意活动：

- 需要直接访问操作系统或文件系统（例如，为了在装有 SQL Server 的同一个虚拟机上安装第三方代理或自定义代理）。
- 严重依赖于目前尚不支持的功能，例如 FileStream/FileTable、PolyBase 和跨实例事务。
- 如果绝对需要保持在 SQL Server 的特定版本 (2012 年，例如)。
- 计算要求比托管实例的要求低得多（例如，只需一个 vCore），并且数据库整合不可接受。

如果你已解决所有标识迁移阻止程序，才能继续迁移到托管实例，请注意，某些更改可能会影响工作负荷的性能：
- 强制性的完整恢复模式和定期自动备份计划可能会影响性能的工作负荷或维护/ETL 操作，如果定期使用简单/大容量日志模式或按需停止备份。
- 不同服务器或数据库级别配置，例如跟踪标志或兼容性级别
- 使用例如透明数据库加密 (TDE) 或自动故障转移组的新功能可能会影响 CPU 和 IO 使用情况。

管理实例保证 99.99%的可用性，甚至在严重情况下，因此不能禁用这些功能所引起的开销。 有关详细信息，请参阅[可能导致 SQL Server 和托管实例的不同的性能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-performance-baseline"></a>创建性能基线

如果你需要对托管实例上使用 SQL Server 上运行你原始工作负荷的工作负荷的性能进行比较，您需要创建将用于比较的性能基线。 

性能基准是一组的参数，如平均/最大 CPU 使用率、 平均/最大磁盘 IO 延迟、 吞吐量、 IOPS、 平均/最大页生存期，平均 tempdb 的最大大小。 你想要迁移后，具有相似或甚至更好的参数，因此务必要测量和记录这些参数的基线值。 除了系统参数，将需要选择在你的工作负荷和度量值最小/平均/最大持续时间内，针对所选查询的 CPU 使用率的一组有代表性的查询或最重要的查询。 这些值将使你来比较工作负荷的托管实例的原始值上运行你的源 SQL Server 的性能。

下面是一些需要度量值的 SQL Server 实例的参数： 
- [监视 SQL Server 实例上的 CPU 使用率](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)并记录平均值和峰值 CPU 使用情况。
- [监视 SQL Server 实例上的内存使用情况](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage)确定使用的不同组件，如缓冲池的内存量和计划缓存中，列存储池[内存中 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)，等等。此外，您应发现 Page Life Expectancy 内存性能计数器的平均值和峰值值。
- 监视源 SQL Server 实例使用的磁盘 IO 使用情况[sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)视图或[性能计数器](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)。
- 通过检查动态管理视图或查询存储，如果要从 SQL Server 2016 及更高版本迁移工作负荷和查询性能或 SQL Server 实例监视。 确定平均持续时间和 CPU 使用率的要与托管实例运行的查询工作负荷中最重要的查询。

> [!Note]
> 如果使用 SQL Server 上将工作负荷，如 CPU 使用率过高、 常量内存压力、 tempdb 或之内问题发现任何问题，应尝试使基线和迁移前源 SQL Server 实例上进行解析。 迁移知道到任何新的系统 migh 问题会导致意外的结果，并使任何性能比较无效。

此活动的结果作为您应该已记录平均值和最大值为 CPU、 内存和 IO 使用情况，在源系统上，，以及平均和最大持续时间和 CPU 使用率的基准和最关键的查询工作负荷中。 应使用这些值更高版本以托管实例上的工作负荷的性能与源 SQL Server 上的工作负荷的基线性能进行比较。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到大小最适合的托管实例

托管实例是为要迁移到云中的本地工作负载量身定制。 它引入了一个[新的购买模型](sql-database-service-tiers-vcore.md)，这让你在为工作负荷选择适当的资源级别时更具灵活性。 在本地环境中，你可能习惯于使用物理核心和 IO 带宽来调整这些工作负荷的大小。 托管实例的购买模型以虚拟核心 (vCore) 为依据，同时单独提供更多存储和 IO 资源。 借助 vCore 模型可以更方便地根据当前在本地使用的计算资源，来了解云中的计算要求。 使用此新模型可以适当地调整云中目标环境的大小。 此处介绍了一些通用准则可帮助你选择适当的服务层和特征：
- 根据基线可以预配托管实例相匹配的 SQL Server 使用的内核数量的 CPU 使用情况，具有记住该 CPU 特征可能需要进行缩放，以匹配[托管实例所在的 VM 特征安装](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
- 根据基线内存使用情况选择[有匹配的内存的服务层](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。 不能直接选择的内存量，因此需要选择具有匹配的内存 (例如 5.1 GB/vCore Gen5 中) 的 Vcore 量的托管实例。 
- 基于在基线 IO 延迟文件子系统的选择常规用途 （大于 5 毫秒延迟） 和业务关键服务层之间 (延迟不超过 3 毫秒)。
- 根据基线的吞吐量预分配数据的大小或日志文件，以获取预期 IO 性能。

你可以选择计算和存储资源在部署时间和以后可进行更改而不会造成应用程序使用[Azure 门户](sql-database-scale-resources.md):

![托管实例大小调整](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

若要了解如何创建 VNet 基础结构和托管实例，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。

> [!IMPORTANT]
> 请务必始终根据[托管实例 VNet 要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)保留目标 VNet 和子网。 任何不兼容性问题都可能导致无法创建新实例或使用已创建的实例。 详细了解如何[新建](sql-database-managed-instance-create-vnet-subnet.md)网络和[配置现有](sql-database-managed-instance-configure-vnet-subnet.md)网络。

## <a name="select-migration-method-and-migrate"></a>选择迁移方法，然后迁移

托管实例部署选项面向，需要从本地或 IaaS 数据库实现迁移大容量数据库的用户方案。 如果需要即时转移定期使用实例级功能和/或跨数据库功能的应用程序的后端，则托管实例是最佳选择。 若采用此方案，可将整个实例转移到 Azure 中对应的环境，而无需重新构建应用程序。

若要转移 SQL 实例，需要认真规划：

- 迁移需要并置的所有数据库（在同一个实例上运行的数据库）
- 迁移应用程序依赖的实例级对象，包括登录名、凭据、SQL 代理作业和操作员，以及服务器级触发器。

托管实例是一项托管服务，可便于将一些常规 DBA 活动委托给平台，就像内置其中一样。 因此，不需要迁移某些实例级数据，例如日常备份的维护作业或 Always On 配置，因为系统中内置了[高可用性](sql-database-high-availability.md)。

托管实例支持以下数据库迁移选项（目前仅支持这些迁移方法）：

- Azure 数据库迁移服务 - 几乎无需停机即可实现迁移。
- 本机 `RESTORE DATABASE FROM URL` - 使用来自 SQL Server 的本机备份，且需要停机一段时间。

### <a name="azure-database-migration-service"></a>Azure 数据库迁移服务

[Azure 数据库迁移服务 (DMS)](../dms/dms-overview.md) 是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。 此服务简化了将现有第三方和 SQL Server 数据库移到 Azure 所要执行的任务。 公共预览版中的部署选项包括，Azure SQL 数据库中的数据库和 Azure 虚拟机中的 SQL Server 数据库。 DMS 是迁移企业工作负荷的建议方法。

如果在本地 SQL Server 上使用 SQL Server Integration Services (SSIS)，虽然 DMS 尚不支持迁移存储 SSIS 包的 SSIS 目录 (SSISDB)，但可在 Azure 数据工厂 (ADF) 中预配 Azure-SSIS Integration Runtime (IR)，这会在托管实例中新建 SSISDB，随后即可将包重新部署到它，请参阅[在 ADF 中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

若要详细了解此方案和 DMS 配置步骤，请参阅[使用 DMS 将本地数据库迁移到托管实例](../dms/tutorial-sql-server-to-managed-instance.md)。  

### <a name="native-restore-from-url"></a>从 URL 本机还原

托管实例部署选项的重要功能之一是，还原取自本地 SQL Server 或[虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)、且在 [Azure 存储](https://azure.microsoft.com/services/storage/)中可用的本机备份（.bak 文件），可便于快速轻松地执行脱机数据库迁移。

下图高度概括了该过程：

![迁移流](./media/sql-database-managed-instance-migration/migration-flow.png)

下表提供了可以根据所运行的源 SQL Server 版本使用的方法的详细信息：

|步骤|SQL 引擎和版本|备份/还原方法|
|---|---|---|
|将备份放入 Azure 存储|低于 SQL 2012 SP1 CU2|将 .bak 文件直接上传到 Azure 存储|
||2012 SP1 CU2 - 2016|使用已弃用的 [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) 语法直接备份|
||2016 和更高版本|使用 [WITH SAS CREDENTIAL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) 直接备份|
|从 Azure 存储还原到托管实例|[使用 SAS CREDENTIAL 执行 RESTORE FROM URL](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - 使用本机还原选项将[透明数据加密](transparent-data-encryption-azure-sql.md)保护的数据库迁移到托管实例时，需要先迁移本地或 IaaS SQL Server 中的相应证书，再还原数据库。 有关详细步骤，请参阅[将 TDE 证书迁移到托管实例](sql-database-managed-instance-migrate-tde-certificate.md)
> - 不支持还原系统数据库。 若要迁移实例级对象（存储在 master 或 msdb 数据库中），我们建议编写 T-SQL 脚本，并在目标实例上运行这些脚本。

有关介绍如何使用 SAS 凭据将数据库备份还原到托管实例的快速入门，请参阅[从备份还原到托管实例](sql-database-managed-instance-get-started-restore.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>监视应用程序

完成迁移到托管实例后，您应该跟踪应用程序行为和工作负荷的性能。 此过程包括以下活动：
- [比较在托管实例上运行的工作负载的性能](#compare-performance-with-the-baseline)与[源 SQL Server 创建的性能基线](#create-performance-baseline)。
- 持续[监视工作负荷的性能](#monitor-performance)以识别潜在问题和改进。

### <a name="compare-performance-with-the-baseline"></a>性能与基线进行比较

需要成功迁移后立即执行的第一个活动是工作负荷的性能与基线工作负荷性能进行比较。 此活动的目标是确认你的托管实例上的工作负荷性能满足你的需求。 

数据库迁移到托管实例在大多数情况下保持数据库设置，并且其原始兼容级别。 在可能的情况以减少与您的源 SQL Server 相比一些性能下降的风险，会保留原始设置。 如果某个用户数据库的兼容级别在迁移之前为 100 或更高，则迁移后，其兼容级别保持相同。 如果用户数据库的兼容性级别在迁移前为 90，那么在升级后的数据库中，兼容性级别设置为 100，这是托管实例支持的最低兼容性级别。 系统数据库的兼容级别为 140。 由于迁移到托管实例实际迁移到 SQL Server 数据库引擎的最新版本，您应注意，您需要重新测试性能的工作负荷以避免出现一些令人惊讶的性能问题。

作为先决条件，请确保已完成以下活动：
- 通过调查各种实例、 数据库、 temdb 设置和配置对齐托管实例上使用源 SQL Server 实例中的设置的设置。 请确保你未设置兼容性级别或加密等更改然后再运行第一次的性能比较，或接受一些新启用的功能可能会影响某些查询的风险。 为了减少迁移风险，请只在完成性能监视之后更改数据库兼容级别。
- 实现[适用于常规用途存储最佳做法准则](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525)如预分配文件以获得更佳性能的大小。
- 了解如何[主要环境差别，可能会导致托管实例和 SQL Server 之间的性能差异]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)并标识可能会影响性能的风险。
- 请确保保持启用的查询存储并在你的托管实例上的自动优化。 这些功能，可测量工作负荷的性能并自动修复的潜在性能问题。 了解如何使用 Query Store 作为最佳工具用于获取有关数据库兼容性级别更改之前和之后的工作负荷性能的信息，如中所述[到较新的 SQL Server 版本在升级期间保持性能稳定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
在准备好环境，这与后尽可能多地与你的本地环境，可以开始运行工作负荷并衡量性能。 测量过程中应包括您所测量的相同参数[时在源 SQL Server 上创建的工作负荷度量值的基线性能](#create-performance-baseline)。
因此，应比较与基线性能参数，并识别关键差异。

> [!NOTE]
> 在许多情况下，将无法获取托管实例和 SQL Server 上完全匹配的性能。 托管实例是 SQL Server 数据库引擎，但基础结构和托管实例上的高可用性配置可能会引入的一些差异。 您所料，某些查询将会是更快，而其他一些可能速度较慢。 比较的目标是验证托管实例中的工作负荷性能与匹配 （在平均值） 中，SQL Server 的性能并确定是否有任何关键查询的性能与原始性能不匹配。

性能比较的结果可能是：
- 托管实例上的工作负荷性能对齐或更好的 SQL Server 上的工作负荷性能。 在这种情况下您已成功确认迁移是成功。
- 大部分性能参数和没有什么问题，使用部分异常和性能下降的工作负荷工作中的查询。 在这种情况下，您需要以标识差异和它们的重要性。 如果有一些重要的查询的性能下降，则应调查基础 SQL 计划更改或查询已达到某些资源限制。 缓解这种情况下可能是一些提示上的重要查询 （例如已更改的兼容性级别，旧版基数估算器） 可以直接应用或使用计划指南，请重新生成或创建统计信息和可能会影响计划的索引。 
- 大多数查询会比在源 SQL Server 托管实例上慢一些。 在这种情况下尝试标识的差异的根本原因，例如[达到某些资源限制]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits)喜欢 IO 限制、 内存限制、 实例日志速率限制等。如果不有任何资源限制，可能会导致不同之处，请尝试更改数据库的兼容性级别或更改数据库设置和重新启动测试等旧版基数估计。 查看提供的托管实例或查询存储视图的建议，以确定性能回归的查询。

> [!IMPORTANT]
> 托管的实例都有内置的自动计划更正功能，默认情况下启用。 此功能可确保不会在将来降低查询在粘贴中工作正常。 请确保启用此功能并且已使用旧的设置都执行足够长的时间的工作负荷之前为了使托管实例，若要了解有关基准性能和计划更改新的设置。

使参数的更改或升级服务层将聚合到的最佳配置，直到获得适合你需求的工作负荷性能。

### <a name="monitor-performance"></a>监视性能

托管的实例提供了大量用于监视和故障排除，高级工具，您应使用它们来监视你的实例上的性能。 某些参数，你将需要监视是：
- 要确定的实例上的 CPU 使用率会执行的预配的 Vcore 数所最符合你的工作负荷。
- 若要确定托管实例上的页生存期[是否需要额外的内存](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 等待统计数据，例如`INSTANCE_LOG_GOVERNOR`或`PAGEIOLATCH`它将告诉您是否有存储 IO 问题，尤其是在其中你可能需要预分配文件，以更好的 IO 性能的常规用途层上。

## <a name="leverage-advanced-paas-features"></a>利用高级的 PaaS 功能

后一个完全托管的平台上，并且已验证的工作负荷性能的匹配 SQL Server 工作负荷性能，需要 SQL 数据库服务的一部分自动提供的优势。 

即使在迁移期间，不要在托管实例中进行一些更改，也有进行操作以利用最新改进，数据库引擎实例时，将启用一些新功能的高可能性。 一些更改仅启用一次[更改数据库兼容性级别](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)。


例如，你无需在托管实例上创建备份，因为服务会自动执行备份。 不再需要考虑计划、创建和管理备份。 在托管实例中，可使用[时点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) 还原到此保留期内的任意时间点。 此外，无需考虑设置高可用性，因为系统中内置了[高可用性](sql-database-high-availability.md)。

若要增强安全性，请考虑使用[Azure Active Directory 身份验证](sql-database-security-overview.md)，[审核](sql-database-managed-instance-auditing.md)，[威胁检测](sql-database-advanced-data-security.md)，[行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)，并[动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking))。

除了高级的管理和安全功能，托管实例提供了一套高级工具，可帮助你[监视和优化你的工作负荷](sql-database-monitor-tune-overview.md)。 [Azure SQL analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)使您能够监视一大组的托管实例和集中管理的大量实例和数据库的监视。 [自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)托管实例中持续监视 SQL 计划执行统计信息的性能并自动修复已识别的性能问题。

## <a name="next-steps"></a>后续步骤

- 若要了解托管实例，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 有关介绍了如何从备份还原的教程，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关介绍了如何使用 DMS 进行迁移的教程，请参阅[使用 DMS 将本地数据库迁移到托管实例](../dms/tutorial-sql-server-to-managed-instance.md)。  
