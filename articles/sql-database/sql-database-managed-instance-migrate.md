---
title: 将 SQL Server 实例迁移到 Azure SQL 数据库托管实例 | Microsoft Docs
description: 了解如何将 SQL Server 实例迁移到 Azure SQL 数据库托管实例。
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
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098351"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>将 SQL Server 实例迁移到 Azure SQL 数据库托管实例

本文介绍了如何将 SQL Server 2005 或更高版本实例迁移到 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。 有关迁移到单一数据库或弹性池的信息，请参阅[迁移到单一数据库或入池数据库](sql-database-cloud-migrate.md)。 有关从其他平台迁移的迁移信息，请参阅 [Azure 数据库迁移指南](https://datamigration.microsoft.com/)。

概括而言，数据库迁移过程如下所示：

![迁移过程](./media/sql-database-managed-instance-migration/migration-process.png)

- [评估托管实例兼容性](#assess-managed-instance-compatibility)
- [选择应用连接选项](sql-database-managed-instance-connect-app.md)
- [部署到大小最适合的托管实例](#deploy-to-an-optimally-sized-managed-instance)
- [选择迁移方法，然后迁移](#select-migration-method-and-migrate)
- [监视应用程序](#monitor-applications)

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

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>部署到大小最适合的托管实例

托管实例是为要迁移到云中的本地工作负载量身定制。 它引入了一个[新的采购模型](sql-database-service-tiers-vcore.md)，这让你在为工作负荷选择适当的资源级别时更具灵活性。 在本地环境中，你可能习惯于使用物理核心和 IO 带宽来调整这些工作负荷的大小。 托管实例的购买模型以虚拟核心 (vCore) 为依据，同时单独提供更多存储和 IO 资源。 借助 vCore 模型可以更方便地根据当前在本地使用的计算资源，来了解云中的计算要求。 使用此新模型可以适当地调整云中目标环境的大小。

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

迁移后跟踪应用程序行为和性能。 在托管实例中，一些更改只会在[数据库兼容性级别已更改](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)后才启用。 在大多数情况下，迁移到 Azure SQL 数据库的数据库会保留其原始兼容级别。 如果某个用户数据库的兼容级别在迁移之前为 100 或更高，则迁移后，其兼容级别保持相同。 如果用户数据库的兼容性级别在迁移前为 90，那么在升级后的数据库中，兼容性级别设置为 100，这是托管实例支持的最低兼容性级别。 系统数据库的兼容级别为 140。

为了减少迁移风险，请只在完成性能监视之后更改数据库兼容级别。 在更改数据库兼容级别之前和之后，请根据[在升级到较新 SQL Server 版本期间保持性能稳定性](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)中所述，使用 Query Store 作为最佳工具来获取有关工作负荷性能的信息。

转到完全托管的平台后，便可以利用 SQL 数据库服务自动提供的优势。 例如，你无需在托管实例上创建备份，因为服务会自动执行备份。 不再需要考虑计划、创建和管理备份。 在托管实例中，可使用[时点恢复 (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore) 还原到此保留期内的任意时间点。 此外，无需考虑设置高可用性，因为系统中内置了[高可用性](sql-database-high-availability.md)。

若要增强安全性，请考虑使用某些可用功能：

- 数据库级别的 Azure Active Directory 身份验证
- 使用[审核](sql-database-managed-instance-auditing.md)、[威胁检测](sql-database-advanced-data-security.md)、[行级别安全](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)和[动态数据掩码](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)等[高级安全功能](sql-database-security-overview.md)保护实例。

## <a name="next-steps"></a>后续步骤

- 若要了解托管实例，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 有关介绍了如何从备份还原的教程，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关介绍了如何使用 DMS 进行迁移的教程，请参阅[使用 DMS 将本地数据库迁移到托管实例](../dms/tutorial-sql-server-to-managed-instance.md)。  
