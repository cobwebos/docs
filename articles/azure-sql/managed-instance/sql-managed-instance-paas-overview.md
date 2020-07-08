---
title: 什么是 Azure SQL 托管实例？
description: 了解 Azure SQL 托管实例如何与最新的 SQL Server （企业版）数据库引擎提供接近100% 的兼容性
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 06/25/2020
ms.openlocfilehash: 43fad6249d5c6f528353a819e03dd7401440e05d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391003"
---
# <a name="what-is-azure-sql-managed-instance"></a>什么是 Azure SQL 托管实例？
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure sql 产品系列是 azure sql 托管实例的一部分，它是一个智能、可缩放的云数据库服务，它将最广泛的 SQL Server 数据库引擎兼容性与完全托管和最长的平台即服务的所有优势相结合。 SQL 托管实例与最新的 SQL Server （企业版）数据库引擎接近100% 的兼容性，它提供了一个用于解决常见安全问题的本机[虚拟网络（VNet）](../../virtual-network/virtual-networks-overview.md)实现，以及一个适用于现有 SQL Server 客户的[业务模型](https://azure.microsoft.com/pricing/details/sql-database/)。 通过 SQL 托管实例，现有 SQL Server 客户可以通过最少的应用程序和数据库更改，将其本地应用程序直接迁移到云。 同时，SQL 托管实例保留了所有 PaaS 功能（自动修补和版本更新、[自动备份](../database/automated-backups-overview.md)、[高可用性](../database/high-availability-sla.md)），可大幅降低管理开销和 TCO。

> [!IMPORTANT]
> 有关 SQL 托管实例当前可用的区域的列表，请参阅[支持的区域](resource-limits.md#supported-regions)。

下图概述了 SQL 托管实例的主要功能：

![主要功能](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL 托管实例适用于想要将大量应用从本地或 IaaS、自构建或 ISV 提供的环境迁移到完全托管的 PaaS 云环境的客户，尽可能降低迁移工作量。 使用完全自动化的[Azure 数据迁移服务](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)，客户可以将现有 SQL Server 实例提升并转移到 SQL 托管实例，这可提供与 SQL Server 的兼容性，并可完全隔离具有本机 VNet 支持的客户实例。  使用软件保障，你可以使用[SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来交换现有许可证，以获得 SQL 托管实例的折扣率。 SQL 托管实例是云中的最佳迁移目标，适用于需要高安全性的 SQL Server 实例和丰富的可编程性图面。

## <a name="key-features-and-capabilities"></a>主要特性和功能

SQL 托管实例结合了可用于 Azure SQL 数据库和 SQL Server 数据库引擎的最佳功能。

> [!IMPORTANT]
> SQL 托管实例使用 SQL Server 的最新版本的所有功能运行，包括联机操作、自动计划更正和其他企业性能增强功能。 [功能比较： AZURE SQL 托管实例与 SQL Server](../database/features-comparison.md)中介绍了可用的功能比较。

| **PaaS 优势** | **业务连续性** |
| --- | --- |
|无需采购和管理硬件 <br>不产生底层基础结构的管理开销 <br>快速预配和服务缩放 <br>自动修补和版本升级 <br>与其他 PaaS 数据服务集成 |99.99% 的运行时间 SLA  <br>内置的[高可用性](../database/high-availability-sla.md) <br>使用[自动备份](../database/automated-backups-overview.md)保护数据 <br>客户可配置的备份保留期 <br>用户发起的[备份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[时间点数据库还原](../database/recovery-using-backups.md#point-in-time-restore)功能 |
|**安全性和符合性** | **Management**|
|隔离的环境（[VNet 集成](connectivity-architecture-overview.md)、单租户服务、专用的计算和存储资源） <br>[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory （Azure AD）身份验证](../database/authentication-aad-overview.md)、单一登录支持 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 服务器主体（登录名）</a>  <br>遵循与 Azure SQL Database 相同的符合性标准 <br>[SQL 审核](auditing-configure.md) <br>[高级威胁防护](threat-detection-configure.md) |用于自动预配和缩放服务的 Azure 资源管理器 API <br>用于手动预配和缩放服务的 Azure 门户功能 <br>数据迁移服务

> [!IMPORTANT]
> Azure SQL 托管实例已针对多个符合性标准进行了认证。 有关详细信息，请参阅 " [Microsoft Azure 符合性产品/服务](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers)"，其中列出了 " **sql 数据库**" 下列出的 sql 托管实例相容性认证的最新列表。

下表显示了 SQL 托管实例的主要功能：

|功能 | 描述|
|---|---|
| SQL Server 版本/内部版本 | SQL Server 数据库引擎（最新稳定） |
| 受管理的自动备份 | 是 |
| 内置的实例和数据库监视与指标 | 是 |
| 自动软件修补 | 是 |
| 最新的数据库引擎功能 | 是 |
| 每个数据库的数据文件 (ROWS) 数目 | 多个 |
| 每个数据库的日志文件 (LOG) 数目 | 1 |
| VNet - Azure 资源管理器部署 | 是 |
| VNet - 经典部署模型 | 否 |
| 门户支持 | 是|
| 内置集成服务 (SSIS) | 否 - SSIS 属于 [Azure 数据工厂 PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| 内置分析服务 (SSAS) | 否 - SSAS 是单独的 [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| 内置报表服务 (SSRS) | 不会在 Azure VM 上改为使用[Power BI 分页报表](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi)或托管 SSRS。 尽管 SQL 托管实例不能将 SSRS 作为服务运行，但它可以使用 SQL Server 身份验证为安装在 Azure 虚拟机上的报表服务器托管[SSRS 目录数据库](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements)。 |
|||

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

SQL 托管实例[基于 vCore 的购买模型](../database/service-tiers-vcore.md)提供了灵活性、控制、透明性，并通过一种简单的方式将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求来更改计算、内存和存储。 对于 SQL Server 的[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)，vCore 模型还享有高达55% 的节约。

在 vCore 模型中，可以在硬件的各代之间进行选择。

- **Gen4**逻辑 Cpu 基于 Intel E5-2673 V3 （Haswell） 2.4 GHz 处理器、附加的 SSD、物理内核、每个内核 7 GB RAM 以及8到 24 vcore 之间的计算大小。
- **Gen5**逻辑 Cpu 基于 Intel E5-2673 V4 （Broadwell） 2.3-GHz 和 intel SP-8160 （Skylake）处理器，快速 NVMe SSD，超线程逻辑核心，以及4到80内核之间的计算大小。

在[SQL 托管实例资源限制](resource-limits.md#hardware-generation-characteristics)中查找有关硬件生成之间的差异的详细信息。

## <a name="service-tiers"></a>服务层

SQL 托管实例在两个服务层中提供：

- **常规用途**：专为具有典型性能和 i/o 延迟要求的应用程序而设计。
- **业务关键**：专为具有低 i/o 延迟要求的应用程序设计，对工作负荷的底层维护操作的影响最小。

这两个服务层级保证 99.99% 的可用性，可让你独立选择存储大小和计算容量。 有关 Azure SQL 托管实例的高可用性体系结构的详细信息，请参阅[高可用性和 AZURE sql 托管实例](../database/high-availability-sla.md)。

### <a name="general-purpose-service-tier"></a>“常规用途”服务层级

以下列表描述了常规用途服务层的主要特征：

- 针对具有典型性能要求的大多数业务应用程序而设计
- 高性能 Azure Blob 存储 (8 TB)
- 基于可靠 Azure Blob 存储和 Azure 的内置[高可用性](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) [Service Fabric](../../service-fabric/service-fabric-overview.md)

有关详细信息，请参阅[常规用途层中的存储层](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)和[SQL 托管实例的存储性能最佳方案和注意事项（常规用途）](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)。

在[SQL 托管实例资源限制](resource-limits.md#service-tier-characteristics)中查找有关服务层之间的差异的详细信息。

### <a name="business-critical-service-tier"></a>“业务关键”服务层级

业务关键服务层是为具有高 i/o 要求的应用程序而构建的。 它通过使用多个独立副本，提供故障恢复能力最高。

以下列表概述了“业务关键”服务层级的主要特征：

- 为具有最严苛性能和 HA 要求的商业应用程序设计
- 附带超高速本地 SSD 存储（第 4 代最多 1 TB，第 5 代最多 4 TB）
- 基于[Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)的内置[高可用性](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability)
- 内置的附加[只读数据库副本](../database/read-scale-out.md)，可用于报表和其他只读工作负荷
- [内存中 OLTP](../in-memory-oltp-overview.md)，可用于具有高性能要求的工作负荷  

在[SQL 托管实例资源限制](resource-limits.md#service-tier-characteristics)中查找有关服务层之间的差异的详细信息。

## <a name="management-operations"></a>管理操作

Azure SQL 托管实例提供管理操作，你可以使用这些操作来自动部署新的托管实例，更新实例属性，以及在不再需要时删除实例。 本部分提供有关管理操作及其典型持续时间的信息。

为了支持[Azure 虚拟网络中的部署](../../virtual-network/virtual-network-for-azure-services.md)并为客户提供隔离和安全性，SQL 托管实例依赖于[虚拟群集](connectivity-architecture-overview.md#high-level-connectivity-architecture)，这表示在客户的虚拟网络子网中部署的一组专用虚拟机。 实质上，每在一个空子网中部署一个托管实例，就会组建一个新的虚拟群集。

对已部署的托管实例的后续操作也可能对基础虚拟群集产生影响。 这会影响管理操作的持续时间，因为部署更多的虚拟机会附带某种开销，在规划新的部署或者更新现有托管实例时，需要考虑到这种开销。

所有管理操作可分类为：

- 实例部署（新实例的创建）。
- 实例更新（更改实例属性，例如 vCore 数或预留存储）。
- 实例删除。

通常，在虚拟群集上执行的操作花费的时间最长。 虚拟群集上的操作持续时间可变–下面是根据现有的服务遥测数据，通常可以预期的值：

- **虚拟群集创建**：这是实例管理操作中的一个同步步骤。 **90% 的操作可在 4 小时内完成**。
- **虚拟群集大小调整（扩展或收缩）**：扩展是一种同步步骤，而收缩是异步执行的（不影响实例管理操作的持续时间）。 **90% 的群集扩展操作可在 2.5 小时内完成**。
- **虚拟群集删除**：删除是一个异步步骤，但也可以在空虚拟群集上[手动启动](virtual-cluster-delete.md)，在这种情况下，它会同步执行。 **90% 的虚拟群集删除操作可在 1.5 小时内完成**。

此外，实例管理还可能包括托管数据库上的某个操作，这会导致持续时间变长：

- **附加 Azure 存储中的数据库文件**：这是一个同步步骤，如计算（vCore）或在常规用途服务层中向上或向下缩放存储。 **90% 的此类操作可在 5 分钟内完成**。
- **Always On 可用性组种子设定**：这是一个同步步骤，如计算（vCore）或业务关键服务层中的存储缩放，以及将服务层从常规用途更改为业务关键（反之亦然）。 此操作的持续时间与总数据库大小以及当前数据库活动（活动的事务数）成正比。 更新实例时执行数据库活动可能会使总持续时间发生明显的变化。 **90% 的这些操作的执行频率为 220 GB/小时或更高**。

下表汇总了操作及其典型的总持续时间：

|类别  |操作  |长时间运行的分段  |预计持续时间  |
|---------|---------|---------|---------|
|**部署** |空子网中的第一个实例|虚拟群集的创建|90% 的操作在4小时内完成。|
|部署 |非空子网中另一个硬件代系的第一个实例（例如，包含第 4 代实例的子网中的第一个 5 代实例）|虚拟群集的创建*|90% 的操作在4小时内完成。|
|部署 |在空或非空子网中创建包含 4 个 vCore 的第一个实例|虚拟群集的创建**|90% 的操作在4小时内完成。|
|部署 |在非空子网中创建后续实例（第 2 个、第 3 个 ... 实例）|虚拟群集大小调整|90% 的操作在2.5 小时内完成。|
|**更新** |实例属性更改（管理员密码、Azure AD 登录名、Azure 混合权益标志）|不适用|最多1分钟。|
|更新 |实例存储纵向缩放（“常规用途”服务层级）|附加数据库文件|90% 的操作在5分钟内完成。|
|更新 |实例存储纵向缩放（“业务关键”服务层级）|- 虚拟群集大小调整<br>- Always On 可用性组种子设定|90% 的操作将在2.5 小时内完成，并对所有数据库（220 GB/小时）进行种子设定。|
|更新 |实例计算 (vCore) 纵向缩放（“常规用途”）|- 虚拟群集大小调整<br>- 附加数据库文件|90% 的操作在2.5 小时内完成。|
|更新 |实例计算 (vCore) 纵向缩放（“业务关键”）|- 虚拟群集大小调整<br>- Always On 可用性组种子设定|90% 的操作将在2.5 小时内完成，并对所有数据库（220 GB/小时）进行种子设定。|
|更新 |实例缩减为 4 个 vCore（“常规用途”）|- 虚拟群集大小调整（如果是首次执行，则可能需要创建虚拟群集**）<br>- 附加数据库文件|90% 的操作完成时间为4小时5分钟 * *|
|更新 |实例可缩减为 4 个 vCore（“业务关键”层级）|- 虚拟群集大小调整（如果是首次执行，则可能需要创建虚拟群集**）<br>- Always On 可用性组种子设定|90% 的操作将在4小时内完成，并对所有数据库进行种子计算（220 GB/小时）。|
|更新 |实例服务层级更改（从“常规用途”更改为“业务关键”，或反之）|- 虚拟群集大小调整<br>- Always On 可用性组种子设定|90% 的操作将在2.5 小时内完成，并对所有数据库（220 GB/小时）进行种子设定。|
|**删除**|实例删除|所有数据库的尾部日志备份|90% 的操作在最多 1 分钟内即可完成。<br>注意：如果删除了子网中的最后一个实例，此操作将在12小时后计划删除虚拟群集。 * * *|
|删除|虚拟群集的删除（用户启动的操作）|虚拟群集的删除|90% 的操作最多可完成1.5 小时。|

\* 虚拟群集是按硬件代系构建的。

\*\*4 Vcore 选项已于 6 2019 月发布，需要新的虚拟群集版本。 如果在 6 月 12 日之前创建的目标子网中创建了实例，则会自动部署一个新的虚拟群集来托管 4 个 vCore 实例。

\*\*\* 12 小时是当前配置，但将来可能会更改，因此，请不要过度依赖于此配置。 如果你之前需要删除虚拟群集（例如，要释放子网），请参阅删除[托管实例后删除子网](virtual-cluster-delete.md)。

### <a name="instance-availability-during-management-operations"></a>管理操作期间的实例可用性

SQL 托管实例**在更新操作期间可用**，但在更新结束时发生的故障转移导致的短暂停机时间除外。 即使经过[加速的数据库恢复](../accelerated-database-recovery.md)，它通常还会持续10秒钟，即使中断长时间运行的事务也是如此。

> [!IMPORTANT]
> 不建议缩放 Azure SQL 托管实例的计算或存储，也不建议同时使用长时间运行的事务（数据导入、数据处理作业、索引重新生成等）更改服务层。 在操作结束时执行的数据库故障转移将取消所有正在进行的事务。

在部署和删除操作过程中，SQL 托管实例不能用于客户端应用程序。

### <a name="management-operations-cross-impact"></a>管理操作交叉影响

托管实例上的管理操作可能会影响置于同一虚拟群集内的实例的其他管理操作。 这包括：

- 虚拟群集中**长时间运行的还原操作**会将其他实例创建或缩放操作置于同一子网中。<br/>**示例：** 如果有长时间运行的还原操作，并且在同一子网中存在创建或缩放请求，则完成此请求将需要更长时间，因为它将等待还原操作完成，然后再继续。
    
- **后续实例创建或缩放**操作由先前启动的实例创建或启动虚拟群集大小的实例缩放置于保持状态。<br/>**示例：** 如果在同一虚拟群集下的同一子网中存在多个创建和/或缩放请求，并且其中一个请求启动了虚拟群集大小调整，则在需要虚拟群集大小的情况下，在该虚拟群集大小超过5分钟后提交的所有请求都将持续比预期更长的时间。

- **在5分钟的时间段内提交的创建/缩放操作**将以并行方式进行批处理和执行。<br/>**示例：** 对于在5分钟的时间段内提交的所有操作（从执行第一个操作请求那一刻开始测量），只会执行一次虚拟群集大小调整。 如果提交第一个请求后超过5分钟提交另一个请求，则会在执行开始之前等待虚拟群集大小调整完成。

> [!IMPORTANT]
> 满足继续执行的条件后，因另一个操作正在进行而被暂停的管理操作将自动恢复运行。 不需要用户执行任何操作即可临时恢复暂停的管理操作。

### <a name="canceling-management-operations"></a>取消管理操作

下表总结了取消特定管理操作和典型总持续时间的功能：

类别  |操作  |可取消  |估计取消持续时间  |
|---------|---------|---------|---------|
|部署 |实例创建 |否 |  |
|更新 |实例存储纵向缩放（“常规用途”服务层级） |否 |  |
|更新 |实例存储纵向缩放（“业务关键”服务层级） |是 |90% 的操作在5分钟内完成。 |
|更新 |实例计算 (vCore) 纵向缩放（“常规用途”） |是 |90% 的操作在5分钟内完成。 |
|更新 |实例计算 (vCore) 纵向缩放（“业务关键”） |是 |90% 的操作在5分钟内完成。 |
|更新 |实例服务层级更改（从“常规用途”更改为“业务关键”，或反之） |是 |90% 的操作在5分钟内完成。 |
|删除 |实例删除 |否 |  |
|删除 |虚拟群集的删除（用户启动的操作） |否 |  |

要取消管理操作，请切换到 "概述" 边栏选项卡，并单击正在进行的操作的通知框。 在右侧，将出现带有正在进行的操作的屏幕，并且将显示 "取消" 操作按钮。 第一次单击之后，系统会要求您再次单击并确认您要取消该操作。

[![取消操作](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

提交并处理取消请求后，如果取消提交成功，则会收到通知。

如果取消成功，将在几分钟内取消管理操作，从而导致失败。

![正在取消操作结果](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

如果取消请求失败或 "取消" 按钮处于非活动状态，则表示管理操作已进入不可取消的状态，并将在几分钟内完成。 管理操作将继续执行，直到完成。

> [!IMPORTANT]
> 当前仅在门户中支持取消操作。

## <a name="advanced-security-and-compliance"></a>高级安全性和符合性

SQL 托管实例附带了 Azure 平台和 SQL Server 数据库引擎提供的高级安全功能。

### <a name="security-isolation"></a>安全隔离

SQL 托管实例通过 Azure 平台上的其他租户提供额外的安全隔离。 安全隔离包括：

- 使用 Azure ExpressRoute 或 VPN 网关[实现本机虚拟网络实现](connectivity-architecture-overview.md)并连接到本地环境。
- 在默认部署中，SQL 终结点仅通过专用 IP 地址公开，允许从私有 Azure 或混合网络进行安全连接。
- 具有专用底层基础结构（计算、存储）的单一租户。

下图概述了应用程序的各种连接选项：

![高可用性](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

若要详细了解子网级别的 VNet 集成和网络策略实施情况，请参阅[托管实例的 VNet 体系结构](connectivity-architecture-overview.md)和[将应用程序连接到托管实例](connect-application-instance.md)。

> [!IMPORTANT]
> 将多个托管实例放置在同一子网中，无论安全要求在何处，都能带来额外的好处。 同一子网中的共同定位实例可大大简化网络基础结构维护并缩短实例预配时间，因为较长的预配持续时间与在子网中部署第一个托管实例的成本相关。

### <a name="security-features"></a>安全功能

Azure SQL 托管实例提供一组可用于保护数据的高级安全功能。

- [SQL 托管实例审核](auditing-configure.md)跟踪数据库事件，并将其写入到放置在 Azure 存储帐户中的审核日志文件。 审核可帮助你一直保持符合法规、了解数据库活动，以及深入了解可以指明业务考量因素或疑似安全违规的偏差和异常。
- 运动中的数据加密-SQL 托管实例通过使用传输层安全性为动作中的数据提供加密来保护数据。 除了传输层安全性外，SQL 托管实例还通过[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)在数据处理过程中以及在查询处理期间提供敏感数据的保护。 Always Encrypted 提供数据安全以防泄露关键数据。 例如，借助 Always Encrypted，信用卡号即使在查询处理期间也始终加密存储在数据库中，允许经授权员工或需要处理该数据的应用程序在使用时进行解密。
- [高级威胁防护](threat-detection-configure.md)是对[审核](auditing-configure.md)的补充，它在服务中提供一个内置的附加安全智能层，用于检测企图访问或使用数据库的异常的潜在有害尝试。 出现可疑活动、潜在漏洞、 SQL 注入攻击和异常数据库访问模式时，它会发出警报。 可以从 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看高级威胁防护警报。 它们提供可疑活动的详细信息，并提供有关如何调查和缓解威胁的建议操作。  
- [动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)功能通过对非特权用户模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码允许指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，会在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。
- 利用[行级安全性](/sql/relational-databases/security/row-level-security)（RLS），您可以基于执行查询的用户的特征（例如，按组成员身份或执行上下文）来控制对数据库表中的行的访问权限。 RLS 简化了应用程序中安全性的设计和编码。 使用 RLS 可针对数据行访问实施限制。 例如，确保工作人员只能访问与其部门相关的数据行，或者将可访问的数据限制为相关的数据。
- [透明数据加密（TDE）](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)对 SQL 托管实例数据文件（称为静态静态数据）进行加密。 TDE 针对数据和日志文件执行实时 I/O 加密和解密。 加密使用数据库加密密钥 (DEK)，它存储在数据库引导记录中，可在恢复时使用。 可使用透明数据加密保护托管实例中的所有数据库。 TDE 是一种 SQL Server 的静态加密技术，由许多符合性标准所要求的，以防止存储介质被盗。

通过 Azure 数据库迁移服务或本机还原支持将加密数据库迁移到 SQL 托管实例。 如果计划使用本机还原迁移加密的数据库，则需要将现有 TDE 证书从 SQL Server 实例迁移到 SQL 托管实例。 有关迁移选项的详细信息，请参阅[SQL Server 迁移到 SQL 托管实例](migrate-to-instance-from-sql-server.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 集成

SQL 托管实例支持与 Azure AD 集成的传统 SQL Server 数据库引擎登录名和登录名。 Azure AD 服务器主体（登录）（**公共预览版**）是你在本地环境中使用的本地数据库登录名的 Azure 云版本。 利用 Azure AD 服务器主体（登录名），可以将 Azure AD 租户中的用户和组指定为真正的实例范围内的主体，这可以执行任何实例级别的操作，包括同一托管实例内的跨数据库查询。

引入了用来创建 Azure AD 服务器主体（登录名）（公共预览版）的一个新语法：**FROM EXTERNAL PROVIDER**。 有关语法的详细信息，请参阅<a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">创建登录名</a>，并查看为[SQL 托管实例预配 Azure Active Directory 管理员](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)文章。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 集成和多重身份验证

SQL 托管实例使你可以通过[Azure Active Directory 集成](../database/authentication-aad-overview.md)来集中管理数据库用户和其他 Microsoft 服务的标识。 此功能简化了权限管理，增强了安全性。 Azure Active Directory 支持[多重身份验证](../database/authentication-mfa-ssms-configure.md)，以便在支持单一登录过程的同时提高数据和应用程序安全性。

### <a name="authentication"></a>身份验证

SQL 托管实例身份验证是指用户在连接到数据库时如何证明其身份。 SQL 托管实例支持两种类型的身份验证：  

- **SQL 身份验证**：

  此身份验证方法使用用户名和密码。
- **Azure Active Directory 身份验证**：

  此身份验证方法使用由 Azure Active Directory 托管的标识，并且受托管域和集成域支持。 请[尽可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 身份验证（集成安全性）。

### <a name="authorization"></a>授权

授权是指用户可以在 Azure SQL 托管实例中的数据库中执行的操作，并且由用户帐户的数据库角色成员身份和对象级权限控制。 SQL 托管实例与 SQL Server 2017 具有相同的授权功能。

## <a name="database-migration"></a>数据库迁移

SQL 托管实例面向从本地或 IaaS 数据库实现进行大容量数据库迁移的用户方案。 SQL 托管实例支持多个数据库迁移选项：

### <a name="backup-and-restore"></a>备份和还原  

迁移方法利用 Azure Blob 存储的 SQL 备份。 存储在 Azure 存储 blob 中的备份可直接通过[T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)还原到托管实例。

- 有关介绍如何还原 Wide World Importers - 标准数据库备份文件的快速入门，请参阅[将备份文件还原到托管实例](restore-sample-database-quickstart.md)。 本快速入门介绍了如何将备份文件上传到 Azure Blob 存储，并使用共享访问签名（SAS）密钥对其进行保护。
- 有关从 URL 还原的信息，请参阅[从 URL 本机还原](migrate-to-instance-from-sql-server.md#native-restore-from-url)。

> [!IMPORTANT]
> 来自托管实例的备份只能还原到另一个托管实例。 它们不能还原到 SQL Server 实例或 Azure SQL 数据库。

### <a name="database-migration-service"></a>数据库迁移服务

Azure 数据库迁移服务是一项完全托管的服务，旨在实现在最短的停机时间内从多个数据库源无缝迁移到 Azure 数据平台。 此服务简化了将现有的第三方和 SQL Server 数据库迁移到 azure 虚拟机上的 azure SQL 数据库、Azure SQL 托管实例和 SQL Server 所需的任务。 请参阅[如何使用数据库迁移服务将本地数据库迁移到 SQL 托管实例](https://aka.ms/migratetoMIusingDMS)。

## <a name="sql-features-supported"></a>支持的 SQL 功能

SQL 托管实例旨在通过分阶段发布计划提供接近100% 的 surface 区域与最新 SQL Server 版本的兼容性。 有关功能和比较列表，请参阅[sql 托管实例功能比较](../database/features-comparison.md)，有关 sql 托管实例与 SQL Server 的 t-sql 差异列表，请参阅[sql 托管实例与 SQL Server 的 t-sql 差异](transact-sql-tsql-differences-sql-server.md)。

SQL 托管实例支持 SQL Server 2008 数据库向后兼容。 支持从 SQL Server 2005 数据库服务器直接迁移，并将迁移的 SQL Server 2005 数据库的兼容级别更新为 SQL Server 2008。
  
下图概述了 SQL 托管实例中的外围应用兼容性：  

![迁移](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>SQL Server 本地与 SQL 托管实例之间的主要差异

SQL 托管实例的好处是，在云中始终保持最新状态，这意味着 SQL Server 中的某些功能可能已过时、已停用或具有替代项。 在某些情况下，工具需要识别特定功能的工作方式略有不同，或者服务在不完全控制的环境中运行。

一些主要区别：

- 高可用性是内置的，并使用类似于[Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技术进行预配置。
- 只有自动备份和时间点还原。 客户可以启动 `copy-only` 不干扰自动备份链的备份。
- 不支持指定完整物理路径，因此必须以不同的方式支持所有对应的方案： RESTORE DB 不支持移动，CREATE DB 不允许使用物理路径，BULK INSERT 仅适用于 Azure blob 等。
- SQL 托管实例支持将[Azure AD 身份验证](../database/authentication-aad-overview.md)作为 Windows 身份验证的云替代方法。
- SQL 托管实例会自动为包含内存中 OLTP 对象的数据库管理 XTP 文件组和文件。
- SQL 托管实例支持 SQL Server Integration Services （SSIS），并可以承载存储 SSIS 包的 SSIS 目录（SSISDB），但会在 Azure 数据工厂中的托管 Azure-SSIS Integration Runtime （IR）上执行。 请参阅[在数据工厂中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 若要比较 SSIS 功能，请参阅[将 Sql 数据库与 sql 托管实例进行比较](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。

### <a name="administration-features"></a>管理功能

SQL 托管实例使系统管理员可以花费更少的时间来执行管理任务，因为服务会为您执行这些任务，也可以极大地简化这些任务。 例如， [OS/RDBMS 安装和修补](../database/high-availability-sla.md)、[动态实例大小调整和配置](../database/single-database-scale.md)、[备份](../database/automated-backups-overview.md)、[数据库复制](replication-between-two-instances-configure-tutorial.md)（包括系统数据库）、[高可用性配置](../database/high-availability-sla.md)，以及运行状况和[性能监视](../../azure-monitor/insights/azure-sql.md)数据流的配置。

有关详细信息，请参阅[支持和不支持的 sql 托管实例功能的列表](../database/features-comparison.md)，以及[sql 托管实例和 SQL Server 之间的 t-sql 差异](transact-sql-tsql-differences-sql-server.md)。

### <a name="programmatically-identify-a-managed-instance"></a>以编程方式标识托管实例

下表显示了几个可通过 Transact-sql 访问的属性，您可以使用这些属性来检测您的应用程序是否在使用 SQL 托管实例和检索重要属性。

|Property|值|注释|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值与 SQL 数据库中的值相同。 此值**并不**表示 SQL 引擎版本 12 (SQL Server 2014)。 SQL 托管实例始终运行最新稳定的 SQL 引擎版本，该版本等于或高于 SQL Server 的最新可用 RTM 版本。  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值与 SQL 数据库中的值相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值唯一标识托管实例。|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|采用以下格式的完整实例 DNS 名称：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中，`<instanceName>` 是客户提供的名称，`<dnsPrefix>` 是自动生成的名称部分，保证 DNS 名称的全局唯一性（例如“wcus17662feb9ce98”）|示例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅[SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关内置疑难解答智能的 SQL 托管实例数据库性能的高级监视，请参阅[使用 Azure SQL Analytics 监视 AZURE SQL 托管实例](../../azure-monitor/insights/azure-sql.md)。
- 有关定价信息，请参阅[SQL 数据库定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
