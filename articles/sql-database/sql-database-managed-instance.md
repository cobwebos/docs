---
title: Azure SQL 数据库托管实例概述 | Microsoft Docs
description: 本主题介绍 Azure SQL 数据库托管实例，解释其工作原理，并说明它与 Azure SQL 数据库中的单一数据库的差别。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 12/03/2018
ms.openlocfilehash: 2807e989436aa80fa812b337340db8cb534b2b28
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994753"
---
# <a name="use-sql-database-managed-instance-with-virtual-networks-and-near-100-compatibility"></a>使用具有虚拟网络和近 100% 兼容性的 SQL 数据库托管实例

Azure SQL 数据库托管实例是 Azure SQL 数据库的一个新部署模型，几乎与最新的 SQL Server 本地 (Enterprise Edition) 数据库引擎完全兼容。它提供一个本机[虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 实现来解决常见的安全问题，并提供本地 SQL Server 客户惯用的[业务模型](https://azure.microsoft.com/pricing/details/sql-database/)。 托管实例允许现有 SQL Server 客户将其本地应用程序即时转移到云中，而只需对应用程序和数据库做出极少量的更改。 同时，托管实例保留了所有 PaaS 功能（自动修补和版本更新、[自动备份](sql-database-automated-backups.md)、[高可用性](sql-database-high-availability.md)），可大幅降低管理开销和总拥有成本。

> [!IMPORTANT]
> 若要查看托管实例当前可用的区域列表，请参阅[支持区域](sql-database-managed-instance-resource-limits.md#supported-regions)。

下图概括描绘了托管实例的主要功能：

![主要功能](./media/sql-database-managed-instance/key-features.png)

Azure SQL 数据库托管实例面向想要以最少的迁移工作量，将大量应用从本地或 IaaS、自我构建的或 ISV 提供的环境迁移到完全托管的 PaaS 云环境的客户。 使用 Azure 中完全自动化的[数据迁移服务 (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)，客户可将其本地 SQL Server 即时转移到托管实例，从而实现与本地 SQL Server 的兼容，并通过本机 VNet 支持实现客户实例的完全隔离。  借助软件保障，可以使用[适用于 SQL Server 的 Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)交换现有许可证，以获得 SQL 数据库托管实例的折扣价格。  SQL 数据库托管实例是 SQL Server 实例在云中的最佳迁移目标，需要很高的安全性和丰富的编程接口。

在正式版推出之前，托管实例旨在通过分阶段的发布计划，实现外围应用与最新本地 SQL Server 版本的近乎 100% 的兼容性。

若要在 Azure SQL 数据库单一数据库、Azure SQL 数据库托管实例和虚拟机中托管的 SQL Server IaaS 之间作出抉择，请参阅[如何在 Azure 云中选择适当版本的 SQL Server](sql-database-paas-vs-sql-server-iaas.md)。

## <a name="key-features-and-capabilities"></a>主要特性和功能

Azure SQL 数据库托管实例结合了 Azure SQL 数据库和 SQL Server 数据库引擎提供的最佳功能。

> [!IMPORTANT]
> 托管实例使用最新版 SQL Server 的所有功能（包括联机操作、自动计划更正和其他企业性能增强功能）运行。 [功能比较：Azure SQL 数据库与 SQL Server](sql-database-features.md)中对可用功能进行了比较。

| **PaaS 优势** | **业务连续性** |
| --- | --- |
|无需采购和管理硬件 <br>不产生底层基础结构的管理开销 <br>快速预配和服务缩放 <br>自动修补和版本升级 <br>与其他 PaaS 数据服务集成 |99.99% 的运行时间 SLA  <br>内置[高可用性](sql-database-high-availability.md) <br>使用[自动备份](sql-database-automated-backups.md)保护数据 <br>客户可配置的备份保留期 <br>用户发起的[备份](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[数据库时间点还原](sql-database-recovery-using-backups.md#point-in-time-restore)功能 |
|**安全性和符合性** | **管理**|
|隔离的环境（[VNet 集成](sql-database-managed-instance-connectivity-architecture.md)、单租户服务、专用的计算和存储资源） <br>[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD 身份验证](sql-database-aad-authentication.md)、单一登录支持 <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD 登录名</a>（**公共预览版**） <br>符合 Azure SQL 数据库遵循的相同法规标准 <br>[SQL 审核](sql-database-managed-instance-auditing.md) <br>[威胁检测](sql-database-managed-instance-threat-detection.md) |用于自动预配和缩放服务的 Azure 资源管理器 API <br>用于手动预配和缩放服务的 Azure 门户功能 <br>数据迁移服务

下表显示托管实例的主要功能：

|Feature | Description|
|---|---|
| SQL Server 版本/内部版本 | SQL Server 数据库引擎（最新稳定版） |
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
| 内置报表服务 (SSRS) | 否 - 使用 Power BI 或 SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

托管实例中[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)提供了灵活性、控制力和透明度，并且还提供了一种简单明了的方法来将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求来更改计算、内存和存储。 此外，借助[适用于 SQL Server 的 Azure SQL Server 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)，还能使用 vCore 模型节省高达 30% 的费用。

在 vCore 模型中，可在以下两代硬件中进行选择。

- 第 4 代逻辑 CPU 基于 Intel E5-2673 v3 (Haswell) 2.4-GHz 处理器，采用附加 SSD，物理核心（每个核心 7 GB RAM），计算大小为 8 到 24 个 vCore。
- 第 5 代逻辑 CPU 基于 Intel E5-2673 v4 (Broadwell) 2.3-GHz 处理器，采用快速 eNVM SSD，超线程逻辑核心，计算大小为 8 到 80 个核心。

若要详细了解两代硬件之间的区别，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。

## <a name="managed-instance-service-tiers"></a>托管实例服务层

托管实例可在两个服务层中提供：

- **常规用途**：适用于具有典型性能和 IO 延迟要求的应用程序。
- **业务关键**：适用于具有低 IO 延迟要求，对工作负荷中基础维护操作影响最低的应用程序。

这两个服务层保证 99.99% 的可用性，可让你独立选择存储大小和计算容量。 有关 Azure SQL 数据库高可用性体系结构的详细信息，请参阅[高可用性和 Azure SQL 数据库](sql-database-high-availability.md)。

### <a name="general-purpose-service-tier"></a>常规用途服务层

以下列表描述了常规用途服务层的主要特征：

- 适用于具有典型性能要求的大多数业务应用程序
- 高性能 Azure 高级存储 (8 TB)
- 基于可靠的 Azure 高级存储和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) 的内置[高可用性](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)

有关详细信息，请参阅[常规用途层中的存储层](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c)和 [Azure SQL DB 托管实例（常规用途）的存储性能最佳做法和注意事项](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)。

若要详细了解两种服务层之间的区别，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

### <a name="business-critical-service-tier"></a>“业务关键”服务层

业务关键服务层适用于具有高 IO 要求的应用程序。 它使用多个独立副本，提供最高级别的故障恢复能力。

以下列表概述了业务关键服务层的主要特征：

- 为具有最严苛性能和 HA 要求的商业应用程序设计
- 附带超高速 SSD 存储（第 4 代最多 1 TB，第 5 代最多 4 TB）
- 基于 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) 的内置[高可用性](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)。
- 内置附加的[只读数据库副本](sql-database-read-scale-out.md)，可用于报表和其他只读工作负荷
- [内存中 OLTP](sql-database-in-memory.md)，可用于具有高性能要求的工作负荷  

若要详细了解两种服务层之间的区别，请参阅[托管实例资源限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="advanced-security-and-compliance"></a>高级安全性和符合性

Azure SQL 数据库托管实例结合了 Azure 云和 SQL Server 数据库引擎提供的高级安全功能。

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Azure 云中的托管实例安全隔离

使用托管实例可以进一步实现与 Azure 云中其他租户的安全隔离。 安全隔离包括：

- 使用 Azure Express Route 或 VPN 网关[实现本机虚拟网络](sql-database-managed-instance-connectivity-architecture.md)并连接到本地环境。
- 仅通过专用 IP 地址公开 SQL 终结点，以便从专用 Azure 或混合网络建立安全连接。
- 具有专用底层基础结构（计算、存储）的单一租户。

下图概述了应用程序的各种连接选项：

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

若要详细了解子网级别的 VNet 集成和网络策略实施情况，请参阅 [Azure SQL 数据库托管实例的 VNet 体系结构](sql-database-managed-instance-connectivity-architecture.md)和[将应用程序连接到 Azure SQL 数据库托管实例](sql-database-managed-instance-connect-app.md)。

> [!IMPORTANT]
> 只要安全要求允许，就将多个托管实例放在同一子网中，因为这会带来额外的好处。 在同一子网中并置实例，将显著简化网络基础结构维护，还会减少预配时间，因为预配持续时间长与在子网中部署第一个托管实例的成本相关。

### <a name="azure-sql-database-security-features"></a>Azure SQL 数据库安全功能

Azure SQL 数据库提供一组可用于保护数据的高级安全功能。

- [托管实例审核](sql-database-managed-instance-auditing.md)功能可跟踪数据库事件，并将其写入 Azure 存储帐户中的审核日志文件。 借助审核可以保持合规、了解数据库活动，以及深入了解可能指示业务考量因素或疑似安全违规的偏差和异常。
- 动态数据加密 - 托管实例提供动态数据加密，使用传输层安全性保护数据。 除传输层安全性以外，SQL 数据库托管实例使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 在动态、静态和查询处理期间提供敏感数据的保护。 Always Encrypted 是业界首创功能，可针对涉及关键数据被盗的漏洞提供无与伦比的数据安全性。 例如，借助 Always Encrypted，信用卡号即使在查询处理期间也始终加密存储在数据库中，允许经授权员工或需要处理该数据的应用程序在使用时进行解密。
- [威胁检测](sql-database-managed-instance-threat-detection.md)是[托管实例审核](sql-database-managed-instance-auditing.md)的补充，它在服务中提供一个内置的附加安全智能层，用于检测企图访问或使用数据库的异常的潜在有害尝试。 出现可疑活动、潜在漏洞、 SQL 注入攻击和异常数据库访问模式时，它会发出警报。 可在 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看威胁检测警报，此警报提供可疑活动的详细信息以及如何调查和缓解威胁的建议操作。  
- [动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)功能通过对非特权用户模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码允许指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，会在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。
- 使用[行级别安全性](/sql/relational-databases/security/row-level-security)可以根据执行查询的用户特征（例如，按组成员身份或执行上下文），控制对数据库表中的行的访问。 行级别安全性 (RLS) 简化了应用程序中的安全性设计和编程。 使用 RLS 可针对数据行访问实施限制。 例如，确保工作人员只能访问与其部门相关的数据行，或者将可访问的数据限制为相关的数据。
- [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 可以加密 Azure SQL 数据库托管实例数据文件，称为静态数据加密。 TDE 针对数据和日志文件执行实时 I/O 加密和解密。 加密使用数据库加密密钥 (DEK)，它存储在数据库引导记录中，可在恢复时使用。 可使用透明数据加密保护托管实例中的所有数据库。 TDE 是 SQL Server 经验证的静态加密技术，许多符合性标准都需要它来防止存储介质被盗。

通过 Azure 数据库迁移服务 (DMS) 或本机还原，支持将加密数据库迁移到 SQL 托管实例。 如果打算使用本机还原迁移加密数据库，则必须将现有 TDE 证书从本地 SQL Server 或 SQL Server VM 迁移到托管实例。 有关迁移选项的详细信息，请参阅[将 SQL Server 实例迁移到 Azure SQL 数据库托管实例](sql-database-managed-instance-migrate.md)。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 集成

Azure SQL 数据库托管实例支持传统的 SQL Server 数据库引擎登录名，以及与 Azure Active Directory (AAD) 集成的登录名。 AAD 登录名（**公共预览版**）是在本地环境中使用的本地数据库登录名的 Azure 云版本。 使用 AAD 登录名，可以将你的 Azure Active Directory 租户中的用户和组指定为真正的实例范围的主体，能够执行任何实体级操作，包括在同一托管实例内执行跨数据库查询。

引入了用来创建 AAD 登录名（**公共预览版**）的一个新语法：**FROM EXTERNAL PROVIDER**。 有关该语法的详细信息，请参阅 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>，并查看[为托管实例预配 Azure Active Directory 管理员](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)一文。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 集成和多重身份验证

借助托管实例，可以使用 [Azure Active Directory 集成](sql-database-aad-authentication.md)集中管理数据库用户和其他 Microsoft 服务的标识。 此功能简化了权限管理，增强了安全性。 Azure Active Directory 支持[多重身份验证](sql-database-ssms-mfa-authentication-configure.md) (MFA)，以便在支持单一登录进程的同时提高数据和应用程序安全性。

### <a name="authentication"></a>身份验证

托管实例身份验证是指用户连接到数据库时如何证明其身份。 SQL 数据库支持两种类型的身份验证：  

- **SQL 身份验证**：

  此身份验证方法使用用户名和密码。
- **Azure Active Directory 身份验证**：

  此身份验证方法使用由 Azure Active Directory 托管的标识，并且受托管域和集成域支持。 请[尽可能](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)使用 Active Directory 身份验证（集成安全性）。

### <a name="authorization"></a>授权

授权是指用户可以在 Azure SQL 数据库中执行哪些操作，由用户帐户的数据库角色成员身份和对象级权限控制。 托管实例的授权功能与 SQL Server 2017 相同。

## <a name="database-migration"></a>数据库迁移

托管实例面向需要从本地或 IaaS 数据库实施项目迁移大量数据库的用户方案。 托管实例支持多个数据库迁移选项：

### <a name="back-up-and-restore"></a>备份和还原  

迁移方法利用从 SQL 到 Azure Blob 存储的备份。 可以使用 [T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)将 Azure 存储 Blob 中存储的备份直接还原到托管实例。

- 有关介绍如何还原 Wide World Importers - 标准数据库备份文件的快速入门，请参阅[将备份文件还原到托管实例](sql-database-managed-instance-get-started-restore.md)。 本快速入门介绍如何将备份文件上传到 Azure 博客存储并使用共享访问签名 (SAS) 密钥对其进行保护。
- 有关从 URL 还原的信息，请参阅[从 URL 本机还原](sql-database-managed-instance-migrate.md#native-restore-from-url)。

> [!IMPORTANT]
> 来自托管实例的备份只能还原到另一个托管实例。 它们无法还原到本地 SQL Server 或 Azure SQL 数据库逻辑服务器单一数据库或入池数据库。

### <a name="data-migration-service"></a>数据迁移服务

Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。 此服务简化了将现有的第三方数据库和 SQL Server 数据库移动到 Azure SQL 数据库（单一数据库、弹性池和托管实例）以及移动到 Azure VM 中的 SQL Server 时需要执行的任务。 请参阅[如何使用 DMS 将本地数据库迁移到托管实例](https://aka.ms/migratetoMIusingDMS)。

## <a name="sql-features-supported"></a>支持的 SQL 功能

在服务正式版推出之前，托管实例旨在通过分阶段的计划，实现外围应用与本地 SQL Server 的近乎 100% 的兼容性。 有关功能和比较列表，请参阅 [SQL 数据库功能比较](sql-database-features.md)；有关托管实例与 SQL Server 中 T-SQL 差异的列表，请参阅[托管实例与 SQL Server 的 T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)。

托管实例支持与 SQL 2008 数据库的向后兼容。 支持从 SQL 2005 数据库服务器直接迁移，迁移后的 SQL 2005 数据库的兼容级别将更新为 SQL 2008。
  
下图概括描绘了托管实例中外围应用的兼容性：  

![迁移](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>本地 SQL Server 与托管实例之间的主要差异

托管实例受益于云中的一贯最新状态，这意味着，本地 SQL Server 中的某些功能可能会过时、被弃用或被取代。 在某些情况下，当工具需要识别特定的功能是否以略微不同的方式工作或者服务是否不在某个环境中运行时，你无法完全控制这一点：

- 高可用性是通过类似 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技术内置和预配的。
- 自动备份和时间点还原。 客户可以启动 `copy-only` 备份，而不会干扰自动备份链。
- 托管实例不允许指定完整的物理路径，因此必须以不同的方式为相应的方案提供支持：RESTORE DB 不支持 WITH MOVE，CREATE DB 不允许使用物理路径，BULK INSERT 仅适用于 Azure Blob，等等。
- 托管实例支持使用 [Azure AD 身份验证](sql-database-aad-authentication.md)作为 Windows 身份验证的云替代方法。
- 对于包含内存中 OLTP 对象的数据库，托管实例会自动管理 XTP 文件组和文件
- 托管实例支持 SQL Server Integration Services (SSIS)，并且可以托管存储 SSIS 包的 SSIS 目录 (SSISDB)，但它们在 Azure 数据工厂 (ADF) 的托管 Azure-SSIS 集成运行时 (IR) 上执行，请参阅[在 ADF 中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 若要比较 SQL 数据库和托管实例中的 SSIS 功能，请参阅[比较 SQL 数据库逻辑服务器和托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance)。

### <a name="managed-instance-administration-features"></a>托管实例管理功能

托管实例可让系统管理员专注于业务中最重要的事务。 许多系统管理员/DBA 活动都是不需要的，或者很简单。 例如，OS/RDBMS 安装和修补、动态实例大小调整和配置、备份、[数据库复制](replication-with-sql-database-managed-instance.md)（包括系统数据库）、高可用性配置，以及运行状况和[性能监视](../azure-monitor/insights/azure-sql.md)数据流。

> [!IMPORTANT]
> 有关支持、部分支持和不支持的功能列表，请参阅 [SQL 数据库功能](sql-database-features.md)。 有关托管实例与 SQL Server 的 T-SQL 差异列表，请参阅[托管实例与 SQL Server 的 T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>如何以编程方式标识托管实例

下表显示了可通过 Transact SQL 访问的几个属性。使用这些属性可以检测应用程序是否正在使用托管实例和检索重要属性。

|属性|值|注释|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值与 SQL 数据库中的值相同。|
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值与 SQL 数据库中的值相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值唯一标识托管实例。|
|`@@SERVERNAME`、`SERVERPROPERTY ('ServerName')`|采用以下格式的完整实例 DNS 名称：`<instanceName>`.`<dnsPrefix>`.database.windows.net，其中，`<instanceName>` 是客户提供的名称，`<dnsPrefix>` 是自动生成的名称部分，保证 DNS 名称的全局唯一性（例如“wcus17662feb9ce98”）|示例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](sql-database-managed-instance-get-started.md)。
- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关 VNet 配置的详细信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-connectivity-architecture.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。
- 有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关使用内置故障排除智能对托管实例数据库性能进行的高级监视，请参阅[使用 Azure SQL Analytics 监视 Azure SQL 数据库](../azure-monitor/insights/azure-sql.md)
- 有关定价信息，请参阅 [SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
