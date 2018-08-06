---
title: Azure SQL 数据库托管实例概述 | Microsoft Docs
description: 本主题介绍 Azure SQL 数据库托管实例，解释其工作原理，并说明它与 Azure SQL 数据库中的单一数据库的差别。
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: bonova
ms.openlocfilehash: ff571035c9360730546732205f34d75ace38fbff
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414310"
---
# <a name="what-is-a-managed-instance-preview"></a>什么是托管实例（预览版）？

Azure SQL 数据库托管实例（预览版）是 Azure SQL 数据库的一项新功能，几乎与本地 SQL Server（Enterprise Edition）100% 兼容。它提供一个本机[虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 实现来解决常见的安全问题，并提供本地 SQL Server 客户惯用的[业务模型](https://azure.microsoft.com/pricing/details/sql-database/)。 托管实例允许现有 SQL Server 客户将其本地应用程序即时转移到云中，而只需对应用程序和数据库做出极少量的更改。 同时，托管实例保留了所有 PaaS 功能（自动修补和版本更新、备份、高可用性），可大幅降低管理开销和总拥有成本。

> [!IMPORTANT]
> 有关目前支持托管实例的区域列表，请参阅[使用 Azure SQL 数据库托管实例将数据库迁移到完全托管的服务](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/)。
 
下图概括描绘了托管实例的主要功能：

![主要功能](./media/sql-database-managed-instance/key-features.png)

可将托管实例视为以下方案的首选平台： 

- 本地 SQL Server/IaaS 客户希望在做出极少量的设计更改的情况下，将其应用程序迁移到完全托管的服务。
- 依赖于 SQL 数据库的 ISV 希望能够让客户迁移到云中，从而实现明显的竞争优势或者让产品覆盖全球。 

在正式版推出之前，托管实例旨在通过分阶段的发布计划，实现外围应用与最新本地 SQL Server 版本的近乎 100% 的兼容性。 

下表概述了 SQL IaaS、Azure SQL 数据库和 SQL 数据库托管实例之间的主要差别和预期使用方案：

| | 使用方案 | 
| --- | --- | 
|SQL 数据库托管实例 |对于想要从本地或 IaaS 迁移大量自我构建的或 ISV 提供的应用，并且希望尽量减少迁移工作量的客户，可能推荐托管实例。 使用 Azure 中完全自动化的[数据迁移服务 (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)，客户可将其本地 SQL Server 即时转移到托管实例，从而实现与本地 SQL Server 的兼容，并通过本机 VNET 支持实现客户实例的完全隔离。  借助软件保障，可以使用 [SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)交换现有许可证，以获得 SQL 数据库托管实例的折扣价格。  SQL 数据库托管实例是 SQL Server 实例在云中的最佳迁移目标，需要很高的安全性和丰富的编程接口。 |
|Azure SQL 数据库（单个或池） |**弹性池**：对于开发新 SaaS 多租户应用程序或有意将现有本地应用转换为 SaaS 多租户应用的客户，建议弹性池。 此模型的优点包括： <br><ul><li>将业务模式从销售许可证转换为销售服务订阅（适用于 ISV）</li></ul><ul><li>简单且高度安全的租户隔离</li></ul><ul><li>以数据库为中心的简化编程模型</li></ul><ul><li>在不超出硬性限制的情况下横向扩展的可能性</li></ul>**单一数据库**：对于想要开发除 SaaS 多租户应用以外的应用，并且其工作负荷较为稳定且可预测的客户，建议单一数据库。 此模型的优点包括：<ul><li>以数据库为中心的简化编程模型</li></ul>  <ul><li>每个数据库的性能可预测</li></ul>|
|SQL IaaS 虚拟机|对于需要自定义操作系统或数据库服务器，或者在配合 SQL Server 运行第三方应用（在同一 VM 上）方面有特定要求的客户，可以推荐 SQL VM/IaaS 作为最佳解决方案|
|||

## <a name="how-to-programmatically-identify-a-managed-instance"></a>如何以编程方式标识托管实例

下表显示了可通过 Transact SQL 访问的几个属性。使用这些属性可以检测应用程序是否正在使用托管实例和检索重要属性。

|属性|值|注释|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|此值与 SQL 数据库中的值相同。|
|`SERVERPROPERTY ('Edition')`|SQL Azure|此值与 SQL 数据库中的值相同。|
|`SERVERPROPERTY('EngineEdition')`|8|此值唯一标识托管实例。|
|`@@SERVERNAME`、`SERVERPROPERTY ('ServerName')`|采用以下格式的完整实例 DNS 名称：<instanceName>.<dnsPrefix>.database.windows.net，其中，<instanceName> 是客户提供的名称，<dnsPrefix> 是自动生成的名称部分，保证 DNS 名称的全局唯一性（例如“wcus17662feb9ce98”）|示例：my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>托管实例的主要特性和功能 

> [!IMPORTANT]
> 托管实例使用最新版 SQL Server 的所有功能（包括联机操作、自动计划更正和其他企业性能增强功能）运行。 

| **PaaS 优势** | **业务连续性** |
| --- | --- |
|无需采购和管理硬件 <br>不产生底层基础结构的管理开销 <br>快速预配和服务缩放 <br>自动修补和版本升级 <br>与其他 PaaS 数据服务集成 |99.99% 的运行时间 SLA  <br>内置高可用性 <br>使用自动备份保护数据 <br>客户可配置的备份保留期（公共预览版中固定为 7 天） <br>用户启动的备份 <br>数据库时间点还原功能 |
|**安全性和符合性** | **管理**|
|隔离的环境（VNet 集成、单租户服务、专用的计算和存储资源） <br>透明数据加密<br>Azure AD 身份验证、单一登录支持 <br>符合 Azure SQL 数据库遵循的相同法规标准 <br>SQL 审核 <br>威胁检测 |用于自动预配和缩放服务的 Azure 资源管理器 API <br>用于手动预配和缩放服务的 Azure 门户功能 <br>数据迁移服务 

![单一登录](./media/sql-database-managed-instance/sso.png) 

## <a name="vcore-based-purchasing-model"></a>基于 vCore 的购买模型

基于 vCore 的购买模型提供了灵活性、控制力和透明性，并且还提供了一种简单明了的方法来将本地工作负荷要求转换到云。 此模型允许根据工作负荷需求来缩放计算、内存和存储资源。 此外，借助[面向 SQL Server 的 Azure 混合使用权益](../virtual-machines/windows/hybrid-use-benefit-licensing.md)，vCore 模型能够节省高达 30% 的费用。

虚拟核心表示逻辑 CPU，提供不同代的硬件供客户选择。
- 第 4 代逻辑 CPU 基于 Intel E5-2673 v3 (Haswell) 2.4 GHz 处理器。
- 第 5 代逻辑 CPU 基于 Intel E5-2673 v4 (Broadwell) 2.3 GHz 处理器。

下表可帮助你了解如何选择计算、内存、存储和 I/O 资源的最佳配置。

||第 4 代|第 5 代|
|----|------|-----|
|硬件|Intel E5-2673 v3 (Haswell) 2.4 GHz 处理器、附加的 SSD vCore = 1 PP（物理核心）|Intel E5-2673 v4 (Broadwell) 2.3 GHz 处理器、快速 eNVM SSD、vCore=1 LP（超线程）|
|性能级别|8、16、24 个 vCore|8、16、24、32、40、64、80 个 vCore|
|内存|每个 vCore 7 GB|每个 vCore 5.5 GB|
||||

## <a name="managed-instance-service-tiers"></a>托管实例服务层

托管实例可在两个服务层中提供：
- **常规用途**：为具有典型可用性和一般 IO 延迟要求的应用程序设计。
- **业务关键**：为具有高可用性和低的 IO 延迟要求的应用程序设计。
 
> [!IMPORTANT]
> 在公共预览版中，不支持在常规用途和业务关键服务层之间切换。 如果想要将数据库迁移到不同服务层中的实例，可以创建新实例，从原始实例对数据库进行时间点还原，然后删除不再需要的原始实例。 

### <a name="general-purpose-service-tier"></a>常规用途服务层

以下列表描述了常规用途服务层的主要特征： 

- 针对具有典型性能和高可用性要求的大多数业务应用程序而设计 
- 高性能 Azure 高级存储 (8 TB) 
- 每个实例支持 100 个数据库 

在此层中，可以单独选择存储和计算容量。 

下图演示了此服务层中的活动计算节点和冗余节点。
 
![常规用途服务层](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

以下列表概述了常规用途服务层的主要特征：

|功能 | Description|
|---|---|
| vCore 数目* | 8、16、24（第 4 代）<br>8、16、24、32、40、64、80（第 5 代）|
| SQL Server 版本/内部版本 | SQL Server（最新可用版本） |
| 最小存储大小 | 32 GB |
| 最大存储大小 | 8 TB |
| 每个数据库的最大存储 | 由每个实例的最大存储大小决定 |
| 预期的存储 IOPS | 每个数据文件 500-7500 IOPS（取决于数据文件）。 请参阅[高级存储](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| 每个数据库的数据文件 (ROWS) 数目 | 多个 | 
| 每个数据库的日志文件 (LOG) 数目 | 1 | 
| 受管理的自动备份 | 是 |
| 高可用性 | 基于远程存储和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| 内置的实例和数据库监视与指标 | 是 |
| 自动软件修补 | 是 |
| VNet - Azure 资源管理器部署 | 是 |
| VNet - 经典部署模型 | 否 |
| 门户支持 | 是|
|||


  \* 虚拟核心表示逻辑 CPU，提供不同代的硬件供客户选择。 第 4 代逻辑 CPU 基于 Intel E5-2673 v3 (Haswell) 2.4 GHz 处理器，第 5 代逻辑 CPU 基于 Intel E5-2673 v4 (Broadwell) 2.3 GHz 处理器。 

### <a name="business-critical-service-tier"></a>“业务关键”服务层

业务关键服务层适用于具有高 IO 要求的应用程序。 它使用多个 Always On 独立副本，提供最高级别的故障恢复能力。 下图演示了此服务层的基础体系结构：

![“业务关键”服务层](./media/sql-database-managed-instance/business-critical-service-tier.png)  

以下列表概述了业务关键服务层的主要特征： 
-   为具有最严苛性能和 HA 要求的商业应用程序设计 
-   附带超高速 SSD 存储（第 4 代最多 1 TB，第 5 代最多 4 TB）
-   每个实例最多支持 100 个数据库 

|功能 | Description|
|---|---|
| vCore 数目* | 8、16、24、32（第 4 代）<br>8、16、24、32、40、64、80（第 5 代）|
| SQL Server 版本/内部版本 | SQL Server（最新可用版本） |
| 其他功能 | [In-Memory OLTP](sql-database-in-memory.md)<br> 1 个额外的只读副本（[读取扩展](sql-database-read-scale-out.md)）
| 最小存储大小 | 32 GB |
| 最大存储大小 | 第 4 代：1 TB（所有 vCore 大小<br> 第 5 代：<ul><li>8、16 个 Vcore 1 TB</li><li>24 个 Vcore 2 TB</li><li>32、40、64、80 个 Vcore 4 TB</ul>|
| 每个数据库的最大存储 | 由每个实例的最大存储大小决定 |
| 每个数据库的数据文件 (ROWS) 数目 | 多个 | 
| 每个数据库的日志文件 (LOG) 数目 | 1 | 
| 受管理的自动备份 | 是 |
| 高可用性 | 基于 [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)和 [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| 内置的实例和数据库监视与指标 | 是 |
| 自动软件修补 | 是 |
| VNet - Azure 资源管理器部署 | 是 |
| VNet - 经典部署模型 | 否 |
| 门户支持 | 是|
|||

## <a name="advanced-security-and-compliance"></a>高级安全性和符合性 

### <a name="managed-instance-security-isolation"></a>托管实例安全隔离 

使用托管实例可以进一步实现与 Azure 云中其他租户的安全隔离。 安全隔离包括： 

- 使用 Azure Express Route 或 VPN 网关[实现本机虚拟网络](sql-database-managed-instance-vnet-configuration.md)并连接到本地环境 
- 仅通过专用 IP 地址公开 SQL 终结点，以便从专用 Azure 或混合网络建立安全连接
- 具有专用底层基础结构（计算、存储）的单一租户

下图概述了应用程序的各种连接选项： 

![高可用性](./media/sql-database-managed-instance/application-deployment-topologies.png)  

若要详细了解子网级别的 VNet 集成和网络策略实施情况，请参阅[为 Azure SQL 数据库托管实例配置 VNet](sql-database-managed-instance-vnet-configuration.md) 和[将应用程序连接到 Azure SQL 数据库托管实例](sql-database-managed-instance-connect-app.md)。 

> [!IMPORTANT]
> 只要安全要求允许，就将多个托管实例放在同一子网中，因为这会带来额外的好处。 在同一子网中并置实例，将显著简化网络基础结构维护，还会减少预配时间，因为预配持续时间长与在子网中部署第一个托管实例的成本相关。


### <a name="auditing-for-compliance-and-security"></a>符合性和安全性审核 

[托管实例审核](sql-database-managed-instance-auditing.md)功能可跟踪数据库事件，并将事件写入 Azure 存储帐户中的审核日志。 借助审核可以保持合规、了解数据库活动，以及深入了解可能指示业务考量因素或疑似安全违规的偏差和异常。 

### <a name="data-encryption-in-motion"></a>动态数据加密 

托管实例提供动态数据加密，使用传输层安全性保护数据。

除传输层安全性以外，SQL 数据库托管实例使用 [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) 在动态、静态和查询处理期间提供敏感数据的保护。 Always Encrypted 是业界首创功能，可针对涉及关键数据被盗的漏洞提供无与伦比的数据安全性。 例如，借助 Always Encrypted，信用卡号即使在查询处理期间也始终加密存储在数据库中，允许经授权员工或需要处理该数据的应用程序在使用时进行解密。 

### <a name="data-encryption-at-rest"></a>静态数据加密 
[透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) 可以加密 Azure SQL 托管实例数据文件，称为静态数据加密。 TDE 针对数据和日志文件执行实时 I/O 加密和解密。 加密使用数据库加密密钥 (DEK)，它存储在数据库引导记录中，可在恢复时使用。 可使用透明数据加密保护托管实例中的所有数据库。 TDE 是 SQL 经验证的静态加密技术，许多符合性标准都需要它来防止存储介质被盗。 公共预览期间，支持自动密钥管理模型（由 PaaS 平台执行）。 

通过 Azure 数据库迁移服务 (DMS) 或本机还原，支持将加密数据库迁移到 SQL 托管实例。 如果打算使用本机还原迁移加密数据库，则必须将现有 TDE 证书从本地 SQL Server 或 SQL Server VM 迁移到托管实例。 有关迁移选项的详细信息，请参阅[将 SQL Server 实例迁移到 Azure SQL 数据库托管实例](sql-database-managed-instance-migrate.md)。

### <a name="dynamic-data-masking"></a>动态数据掩码 

SQL 数据库的[动态数据掩码](/sql/relational-databases/security/dynamic-data-masking)功能通过对非特权用户模糊化敏感数据来限制此类数据的泄漏。 动态数据掩码允许指定在对应用层产生最小影响的前提下可以透露的敏感数据量，从而帮助防止未经授权的用户访问敏感数据。 它是一种基于策略的安全功能，会在针对指定的数据库字段运行查询后返回的结果集中隐藏敏感数据，同时保持数据库中的数据不变。 

### <a name="row-level-security"></a>行级别安全性 

使用[行级别安全性](/sql/relational-databases/security/row-level-security)可以根据执行查询的用户特征（例如，按组成员身份或执行上下文），控制对数据库表中的行的访问。 行级别安全性 (RLS) 简化了应用程序中的安全性设计和编程。 使用 RLS 可针对数据行访问实施限制。 例如，确保工作人员只能访问与其部门相关的数据行，或者将可访问的数据限制为相关的数据。 

### <a name="threat-detection"></a>威胁检测 

[托管实例威胁检测](sql-database-managed-instance-threat-detection.md)是[托管实例审核](sql-database-managed-instance-auditing.md)的补充，它在服务中提供一个内置的附加安全智能层，用于检测企图访问或使用数据库的异常的潜在有害尝试。 出现可疑活动、潜在漏洞、 SQL 注入攻击和异常数据库访问模式时，它会发出警报。 可在 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)查看威胁检测警报，此警报提供可疑活动的详细信息以及如何调查和缓解威胁的建议操作。  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 集成和多重身份验证 

通过 SQL 数据库，可使用 [Azure Active Directory 集成](sql-database-aad-authentication.md)集中管理数据库用户和其他 Microsoft 服务的身份。 此功能简化了权限管理，增强了安全性。 Azure Active Directory 支持[多重身份验证](sql-database-ssms-mfa-authentication-configure.md) (MFA)，以便在支持单一登录进程的同时提高数据和应用程序安全性。 

### <a name="authentication"></a>身份验证 
SQL 数据库身份验证是指用户连接到数据库时如何证明其身份。 SQL 数据库支持两种类型的身份验证：  

- SQL 身份验证，使用用户名和密码。
- Azure Active Directory 身份验证，使用 Azure Active Directory 管理的标识，支持托管域和集成域。 

### <a name="authorization"></a>授权

授权是指用户可以在 Azure SQL 数据库中执行哪些操作，由用户帐户的数据库角色成员身份和对象级权限控制。 托管实例的授权功能与 SQL Server 2017 相同。 

## <a name="database-migration"></a>数据库迁移 

托管实例面向需要从本地或 IaaS 数据库实施项目迁移大量数据库的用户方案。 托管实例支持多个数据库迁移选项： 

### <a name="data-migration-service"></a>数据迁移服务

Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最小化停机时间。 此服务简化了将现有第三方和 SQL Server 数据库移到 Azure 所要执行的任务。 公共预览版中的部署选项包括 Azure SQL 数据库、托管实例和 Azure VM 中的 SQL Server。 请参阅[如何使用 DMS 将本地数据库迁移到托管实例](https://aka.ms/migratetoMIusingDMS)。 

### <a name="backup-and-restore"></a>备份和还原  

迁移方法利用从 SQL 到 Azure Blob 存储的备份。 可将 Azure 存储 Blob 中存储的备份直接还原到托管实例。 若要将现有 SQL 数据库还原到托管实例，可以：

- 使用[数据迁移服务 (DMS)](../dms/dms-overview.md)。 有关教程，请参阅[使用 Azure 数据库迁移服务 (DMS) 迁移到托管实例](../dms/tutorial-sql-server-to-managed-instance.md)，以从数据库备份文件还原
- 使用 [T-SQL RESTORE 命令](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)。 
  - 有关介绍如何还原 Wide World Importers - 标准数据库备份文件的教程，请参阅[将备份文件还原到托管实例](sql-database-managed-instance-restore-from-backup-tutorial.md)。 本教程介绍如何将备份文件上传到 Azure 博客存储并使用共享访问签名 (SAS) 密钥进行保护。
  - 有关从 URL 还原的信息，请参阅[从 URL 本机还原](sql-database-managed-instance-migrate.md#native-restore-from-url)。

## <a name="sql-features-supported"></a>支持的 SQL 功能 

在服务正式版推出之前，托管实例旨在通过分阶段的计划，实现外围应用与本地 SQL Server 的近乎 100% 的兼容性。 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
 
托管实例支持与 SQL 2008 数据库的向后兼容。 支持从 SQL 2005 数据库服务器直接迁移，迁移后的 SQL 2005 数据库的兼容级别将更新为 SQL 2008。 
 
下图概括描绘了托管实例中外围应用的兼容性：  

![迁移](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>本地 SQL Server 与托管实例之间的主要差异 

托管实例受益于云中的一贯最新状态，这意味着，本地 SQL Server 中的某些功能可能会过时、被弃用或被取代。 在某些情况下，当工具需要识别特定的功能是否以略微不同的方式工作或者服务是否不在某个环境中运行时，你无法完全控制这一点： 

- 高可用性是内置的，且是预先配置的。 Always On 高可用性功能的公开方式不同于 SQL IaaS 实施项目的类似功能 
- 自动备份和时间点还原。 客户可以启动 `copy-only` 备份，而不会干扰自动备份链。 
- 托管实例不允许指定完整的物理路径，因此，必须以不同的方式支持所有相应方案：RESTORE DB 不支持 WITH MOVE，CREATE DB 不允许物理路径，BULK INSERT 仅适用于 Azure Blob，等等。 
- 托管实例支持使用 [Azure AD 身份验证](sql-database-aad-authentication.md)作为 Windows 身份验证的云替代方法。 
- 对于包含内存中 OLTP 对象的数据库，托管实例会自动管理 XTP 文件组和文件
- 托管实例支持 SQL Server Integration Services (SSIS)，并且可以托管存储 SSIS 包的 SSIS 目录 (SSISDB)，但它们在 Azure 数据工厂 (ADF) 的托管 Azure-SSIS 集成运行时 (IR) 上执行，请参阅[在 ADF 中创建 Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。 若要比较 SQL 数据库和托管实例中的 SSIS 功能，请参阅[比较 SQL 数据库和托管实例（预览版）](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview)。

### <a name="managed-instance-administration-features"></a>托管实例管理功能  

托管实例可让系统管理员专注于业务中最重要的事务。 许多系统管理员/DBA 活动都是不需要的，或者很简单。 例如，OS/RDBMS 安装和修补、动态实例大小调整和配置、备份、数据库复制（包括系统数据库）、高可用性配置，以及运行状况和性能监视数据流的配置。 

> [!IMPORTANT]
> 有关支持、部分支持和不支持的功能列表，请参阅 [SQL 数据库功能](sql-database-features.md)。 有关托管实例与 SQL Server 的 T-SQL 差异列表，请参阅[托管实例与 SQL Server 的 T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>后续步骤

- 有关功能和比较列表，请参阅 [SQL 常用功能](sql-database-features.md)。
- 有关 VNet 配置的详细信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-vnet-configuration.md)。
- 有关创建托管实例以及从备份文件还原数据库的教程，请参阅[创建托管实例](sql-database-managed-instance-create-tutorial-portal.md)。
- 有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。
- 有关定价信息，请参阅 [SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
