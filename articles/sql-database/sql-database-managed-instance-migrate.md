---
title: 从 SQL Server 迁移到托管实例
description: 了解如何将 SQL Server 实例中的数据库迁移到 Azure SQL 数据库 - 托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 802dfa7e3b2d0b9deac957662ac1e7604d085fd9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828075"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>将 SQL Server 实例迁移到 Azure SQL 数据库托管实例

本文介绍了如何将 SQL Server 2005 或更高版本实例迁移到 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。 有关迁移到单一数据库或弹性池的信息，请参阅[迁移到单一数据库或入池数据库](sql-database-cloud-migrate.md)。 有关从其他平台迁移的迁移信息，请参阅 [Azure 数据库迁移指南](https://datamigration.microsoft.com/)。

> [!NOTE]
> 若要快速启动并试用托管实例，可转到[快速入门指南](sql-database-managed-instance-quickstart-guide.md)而不是本页。 

概括而言，数据库迁移过程如下所示：

![迁移过程](./media/sql-database-managed-instance-migration/migration-process.png)

- [评估托管实例兼容性](#assess-managed-instance-compatibility)，应确保不存在可能阻止迁移的阻碍性问题。
  - 此步骤还包括创建[性能基线](#create-performance-baseline)来确定源 SQL Server 实例上的资源用量。 若要部署大小适当的托管实例并验证迁移之后的性能是否受影响，需要执行此步骤。
- [选择应用连接选项](sql-database-managed-instance-connect-app.md)
- [部署到优化大小的托管实例](#deploy-to-an-optimally-sized-managed-instance)，选择托管实例的技术特征（vCore 数目、内存量）和性能层（“业务关键”、“常规用途”）。
- [选择迁移方法并迁移](#select-migration-method-and-migrate)，使用脱机迁移（本机备份/还原、数据库导入/导出）或联机迁移（数据迁移服务、事务复制）迁移数据库。
- [监视应用程序](#monitor-applications)，确保获得预期的性能。

> [!NOTE]
> 若要将单个数据库迁移到单个数据库或弹性池，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-single-database-migrate.md)。

## <a name="assess-managed-instance-compatibility"></a>评估托管实例兼容性

首先，确定托管实例是否与应用程序的数据库要求相符。 托管实例部署选项旨在，方便直接迁移大多数在本地或在虚拟机中使用 SQL Server 的现有应用程序。 但是，我们有时可能需要用到一些目前尚不支持的功能，而实施某种解决方法的成本过高。

使用[数据迁移助手 (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) 可以检测影响 Azure SQL 数据库功能的潜在兼容性问题。 虽然 DMA 尚不支持将托管实例用作迁移目标，但建议针对 Azure SQL 数据库运行评估，并根据产品文档仔细查看报告的功能奇偶一致性和兼容性问题列表。 请参阅 [Azure SQL 数据库功能](sql-database-features.md)，以检查托管实例中是否不存在报告的一些阻塞性问题，因为大多数阻止迁移到 Azure SQL 数据库的阻塞性问题已通过托管实例进行删除。 例如，托管实例中提供跨数据库查询、同一实例中的跨数据库事务、链接到到其他 SQL 源的链接服务器、CLR、全局临时表、实例级视图、Service Broker 等功能。

如果报告的一些阻塞性问题未通过托管实例部署选项进行删除，可能需要考虑备用选项，如 [Azure 虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)。 下面是一些示例：

- 需要直接访问操作系统或文件系统（例如，为了在装有 SQL Server 的同一个虚拟机上安装第三方代理或自定义代理）。
- 严重依赖于目前尚不支持的功能，例如 FileStream/FileTable、PolyBase 和跨实例事务。
- 绝对需要保持使用特定的 SQL Server 版本（例如 2012）。
- 计算要求比托管实例的要求低得多（例如，只需一个 vCore），并且数据库整合不可接受。

如果已解决所有已识别的迁移阻碍并继续迁移到托管实例，请注意，某些更改可能会影响工作负荷的性能：
- 如果你定期使用简单/批量记录的模型或按需停止备份，则强制性完整恢复模型和定期自动备份计划可能会影响工作负荷或维护/ETL 操作的性能。
- 不同的服务器或数据库级别配置，例如跟踪标志或兼容性级别
- 使用的新功能（例如透明数据库加密 (TDE) 或自动故障转移组）可能会影响 CPU 和 IO 使用率。

即使发生严重的情况，托管实例也能保证 99.99% 的可用性，因此，无法禁用这些功能造成的开销。 有关详细信息，请参阅[可能导致 SQL Server 和托管实例上出现不同性能的根本原因](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)。

### <a name="create-performance-baseline"></a>创建性能基线

如果需要将托管实例上工作负荷的性能与 SQL Server 上运行的原始工作负荷的性能进行比较，需要创建用于比较的性能基线。 

性能基线是一组参数，例如平均/最大 CPU 使用率、平均/最大磁盘 IO 延迟、吞吐量、IOPS、平均/最大页生存期、平均/最大 tempdb 大小。 迁移后，你希望获得相似甚至更好的参数，因此，测量并记录这些参数的基线值非常重要。 除系统参数以外，还需要在工作负荷中选择一组代表性的查询或最重要的查询，并测量最小/平均/最大持续时间，以及所选查询的 CPU 使用率。 使用这些值可将托管实例上运行的工作负荷的性能与源 SQL Server 上的原始值进行比较。

需要在 SQL Server 实例上测量的一些参数如下： 
- [监视 SQL Server 实例上的 CPU 使用率](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131)，并记录平均和峰值 CPU 使用率。
- [监视 SQL Server 实例上的内存使用率](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage)，并确定不同组件（例如缓冲池、计划缓存、列存储池、[内存中 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)等）使用的内存量。此外，还应查找 "页生存期" 内存性能计数器的平均值和峰值值。
- 使用 [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) 视图或[性能计数器](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)监视源 SQL Server 实例上的磁盘 IO 使用率。
- 若要从 SQL Server 2016+ 版本迁移，请检查动态管理视图或查询存储，以监视工作负荷和查询性能或 SQL Server 实例。 确定工作负荷中最重要查询的平均持续时间和 CPU 使用率，以将其与托管实例上运行的查询进行比较。

> [!Note]
> 如果你注意到 SQL Server 工作负荷有任何问题（例如 CPU 使用率过高、一直存在内存压力、tempdb 或参数化问题），在创建基线和迁移之前，应尝试在源 SQL Server 实例上解决这些问题。 将已知问题带到任何新系统可能会导致意外的结果，并使得任何性能比较失效。

作为此活动的结果，应该记录源系统上的平均和峰值 CPU、内存和 IO 使用率，以及工作负荷中首要和最关键查询的平均和最大持续时间与 CPU 使用率。 以后应使用这些值将托管实例上工作负荷的性能与源 SQL Server 上工作负荷的基线性能进行比较。

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到大小最适合的托管实例

托管实例是为要迁移到云中的本地工作负载量身定制。 它引入了一个[新的购买模型](sql-database-service-tiers-vcore.md)，这让你在为工作负荷选择适当的资源级别时更具灵活性。 在本地环境中，你可能习惯于使用物理核心和 IO 带宽来调整这些工作负荷的大小。 托管实例的购买模型以虚拟核心 (vCore) 为依据，同时单独提供更多存储和 IO 资源。 借助 vCore 模型可以更方便地根据当前在本地使用的计算资源，来了解云中的计算要求。 使用此新模型可以适当地调整云中目标环境的大小。 下面介绍了一些可帮助你选择适当服务层级和特征的常规指导：
- 根据基线 CPU 使用率，可以预配一个与 SQL Server 上使用的核心数相匹配的托管实例，但请注意，可能需要缩放 CPU 特征才能匹配[安装了托管实例的 VM 的特征](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
- 根据基线内存使用率选择[具有匹配内存的服务层级](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。 无法直接选择内存量，因此，需要选择 vCore 数量与内存相匹配（例如，在 Gen5 中为 5.1 GB/vCore）的托管实例。 
- 根据文件子系统的基线 IO 延迟，在“常规用途”（延迟大于 5 毫秒）与“业务关键”服务层级（延迟小于 3 毫秒）之间进行选择。
- 根据基线吞吐量预先分配数据或日志文件的大小，以获得预期的 IO 性能。

可在部署时选择计算和存储资源，稍后再通过 [Azure 门户](sql-database-scale-resources.md)更改它，而不必让应用程序关闭：

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

迁移到托管实例后，应跟踪应用程序的行为和工作负荷的性能。 此过程包括以下活动：
- 将[托管实例上运行的工作负荷的性能](#compare-performance-with-the-baseline)与[在源 SQL Server 上创建的性能基线](#create-performance-baseline)进行比较。
- 持续[监视工作负荷的性能](#monitor-performance)，以识别潜在问题并做出改善。

### <a name="compare-performance-with-the-baseline"></a>将性能与基线进行比较

成功迁移后需要立即采取的第一个活动是将工作负荷的性能与基线工作负荷性能进行比较。 此活动旨在确认托管实例上的工作负荷性能是否符合需求。 

在大多数情况下，将数据库迁移到托管实例会保留数据库设置及其原始兼容性级别。 在可能的情况下会保留原始设置，以减少相比源 SQL Server，性能出现一定的下降的风险。 如果某个用户数据库的兼容级别在迁移之前为 100 或更高，则迁移后，其兼容级别保持相同。 如果用户数据库的兼容性级别在迁移前为 90，那么在升级后的数据库中，兼容性级别设置为 100，这是托管实例支持的最低兼容性级别。 系统数据库的兼容级别为 140。 由于迁移到托管实例实际上是迁移到最新版 SQL Server 数据库引擎，因此应注意，需要重新测试工作负荷的性能，以避免出现一些惊人的性能问题。

作为先决条件，请确保已完成以下活动：
- 通过调查各种实例、数据库、temdb 设置和配置，使托管实例上的设置与源 SQL Server 实例中的设置相符。 在运行首次性能比较之前，请确保未更改兼容性级别或加密等设置，否则需要承受启用的某些新功能影响某些查询的风险。 为了减少迁移风险，请只在完成性能监视之后更改数据库兼容级别。
- 实施[适用于“常规用途”服务层级的存储最佳实践指导原则](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525)，例如，预先分配文件大小以获得更好的性能。
- 了解[可能导致托管实例与 SQL Server 之间出现性能差别的关键环境差异]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)，并识别可能影响性能的风险。
- 确保在托管实例上保持启用“查询存储”和“自动优化”。 这些功能可让你衡量工作负荷的性能，并自动修复潜在的性能问题。 根据[在升级到较新 SQL Server 版本期间保持性能稳定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)中所述，了解在更改数据库兼容级别之前和之后，如何使用 Query Store 作为最佳工具来获取有关工作负荷性能的信息。
准备好尽量与本地环境相当的环境后，可以开始运行工作负荷并衡量性能。 衡量过程应包括[在源 SQL Server 上创建工作负荷的基线性能测量值](#create-performance-baseline)时所测得的相同参数。
因此，应将性能参数与基线进行比较，并识别关键差异。

> [!NOTE]
> 在许多情况下，托管实例和 SQL Server 上的性能无法做到完全一致。 托管实例是一个 SQL Server 数据库引擎，但托管实例上的基础结构和高可用性配置可能会造成某种差异。 你可能已预料到有些查询的速度较快，而有些查询的速度可能较慢。 比较的目标是验证托管实例中的工作负荷性能是否与 SQL Server 上的性能相匹配（平均值），并确定是否有性能与原始性能不符的关键查询。

性能比较的结果可能是：
- 托管实例上的工作负荷性能与 SQL Server 上的性能相当，或者优于后者。 如果存在这种情况，则表示迁移成功。
- 工作负荷中的大多数性能参数和查询可正常工作，但也存在一些例外和性能下降的情况。 对于这种情况，需要识别差异及其重要性。 如果某些重要查询出现性能下降，则你应该调查基础 SQL 计划是否已更改，或者查询是否遇到了某些资源限制。 这种情况的缓解措施可能是直接或使用计划指南对关键查询应用一些提示（例如，更改兼容性级别、传统基数估算器，重新生成或创建可能会影响计划的统计信息和索引。 
- 与源 SQL Server 相比，托管实例上的大多数查询速度较慢。 在这种情况下，请尝试确定差异的根本原因，[例如 IO]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics)限制、内存限制、实例日志速率限制等。如果没有可能导致差异的资源限制，请尝试更改数据库的兼容级别或更改数据库设置（如旧基数估计），然后重新启动测试。 查看托管实例或查询存储视图中提供的建议来识别使性能倒退的查询。

> [!IMPORTANT]
> 托管实例具有内置的自动计划更正功能（默认已启用）。 此功能确保过去正常工作的查询在将来不会导致性能下降。 在更改新设置，使托管实例能够了解基线性能和计划之前，请确保启用此功能，并使用旧设置执行工作负荷足够长的时间。

更改参数或升级服务层级以融合到最佳配置，直到工作负荷的性能符合需求为止。

### <a name="monitor-performance"></a>监视性能

托管实例提供许多高级工具用于监视和故障排除，你应使用这些工具来监视实例的性能。 需要监视的某些参数如下：
- 实例上的 CPU 使用率：确定预配的 vCore 数目是否与工作负荷适当匹配。
- 托管实例上的页生存期：确定[是否需要增加内存](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)。
- 等待统计信息（例如 `INSTANCE_LOG_GOVERNOR` 或 `PAGEIOLATCH`）：告知是否存在存储 IO 问题，尤其是在“常规用途”层级上，可能需要在其中预先分配文件才能获得更好的 IO 性能。

## <a name="leverage-advanced-paas-features"></a>利用高级 PaaS 功能

进入完全托管的平台并已确认工作负荷性能与 SQL Server 工作负荷性能相匹配后，即可获得 SQL 数据库服务自动提供的优势。 

即使迁移过程中未在托管实例中进行一些更改，也仍很有很可能需要在操作实例时启用一些新功能，以利用数据库引擎的最新改进功能。 一些更改只会在[数据库兼容性级别已更改](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)后才启用。


例如，你无需在托管实例上创建备份，因为服务会自动执行备份。 不再需要考虑计划、创建和管理备份。 在托管实例中，可使用[时点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) 还原到此保留期内的任意时间点。 此外，无需考虑设置高可用性，因为系统中内置了[高可用性](sql-database-high-availability.md)。

若要增强安全性，请考虑使用 [Azure Active Directory 身份验证](sql-database-security-overview.md)、[审核](sql-database-managed-instance-auditing.md)、[威胁检测](sql-database-advanced-data-security.md)、[行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)和[动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)。

除了高级管理和安全功能以外，托管实例还提供一组高级工具来帮助你[监视和优化工作负荷](sql-database-monitor-tune-overview.md)。 使用[AZURE SQL analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql)可以监视大量的托管实例，并集中监视大量实例和数据库。 托管实例中的[自动优化](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)会持续监视 SQL 计划执行统计信息的性能，并自动修复已识别到的性能问题。

## <a name="next-steps"></a>后续步骤

- 若要了解托管实例，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 有关介绍了如何从备份还原的教程，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关介绍了如何使用 DMS 进行迁移的教程，请参阅[使用 DMS 将本地数据库迁移到托管实例](../dms/tutorial-sql-server-to-managed-instance.md)。  
