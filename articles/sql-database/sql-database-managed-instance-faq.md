---
title: 托管实例常见问题解答
description: SQL 数据库托管实例常见问题解答 (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80364154"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL 数据库托管实例常见问题解答 (FAQ)

本文包含有关 [SQL 数据库托管实例](sql-database-managed-instance.md)的多种最常见问题的解答。

## <a name="supported-features"></a>支持的功能

**在何处可以找到托管实例支持的功能列表？**

有关托管实例支持的功能列表，请参阅 [Azure SQL 数据库与 SQL Server](sql-database-features.md)。

有关 Azure SQL 数据库托管实例与本地 SQL Server 之间的语法和行为差异，请参阅 [T-SQL 与 SQL Server 之间的差异](sql-database-managed-instance-transact-sql-information.md)。


## <a name="tech-spec--resource-limits"></a>技术规范和资源限制
 
**在何处可以找到托管实例的技术特征和资源限制？**

有关可用硬件的代系特征，请参阅[硬件代系的技术差异](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
有关可用的服务层级及其特征，请参阅[服务层级之间的技术差异](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="known-issues--bugs"></a>已知问题和 bug

**在何处可以找到已知问题和 bug？**

有关 bug 和已知问题，请参阅[已知问题](sql-database-release-notes.md#known-issues)。

## <a name="new-features"></a>新增功能

**在何处可以找到最新功能和公共预览版功能？**

对于新功能和预览功能，请参阅[发行说明](sql-database-release-notes.md?tabs=managed-instance)。

## <a name="deployment-times"></a>部署时间 

**创建或更新实例或还原数据库需要多长时间？**

创建新的托管实例或更改服务层（vCore、存储）所需的时间取决于多个因素。 请查看[管理操作](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>命名约定

**托管实例是否可与本地 SQL Server 同名？**

不支持更改托管实例名称。

托管实例默认 DNS 区域 *。 database.windows.net*可以更改。 

若要使用另一个 DNS 区域而不是默认的，例如*contoso.com*： 
- 使用 CliConfig 定义别名。 该工具只是一个注册表设置包装器，因此也可以使用组策略或脚本完成此操作
- 将 *CNAME* 与 *TrustServerCertificate=true* 选项一起使用。

## <a name="move-db-from-mi"></a>从 MI 移动数据库 

**如何将数据库从托管实例移回到 SQL Server 或 Azure SQL 数据库？**

可[将数据库导出到 BACPAC](sql-database-export.md)，然后[导入 BACPAC 文件]( sql-database-import.md)。 如果数据库小于 100 GB，我们建议使用此方法。

如果数据库中的所有表具有主键，则可以使用事务复制。

由于托管实例的数据库版本高于 SQL Server，因此无法将从托管实例创建的本机 `COPY_ONLY` 备份还原到 SQL Server。

## <a name="migrate-instance-db"></a>迁移实例数据库

**如何将实例数据库迁移到单个 Azure SQL 数据库？**

一种做法是[将数据库导出到 BACPAC](sql-database-export.md)，然后[导入 BACPAC 文件](sql-database-import.md)。 

如果数据库小于 100 GB，我们建议使用此方法。 如果数据库中的所有表具有主键，则可以使用事务复制。

## <a name="switch-hardware-generation"></a>切换硬件代系 

**能否在第 4 代和第 5 代托管实例硬件代系之间联机切换？**

如果这两种硬件代系都可以在预配托管实例的区域中使用，则可以在硬件代系之间自动联机切换。 在这种情况下，可以查看说明如何在硬件生成之间切换的[vCore model 概述页](sql-database-service-tiers-vcore.md)。

这是一个长时间运行的操作，因为新托管实例将在后台预配，数据库将在旧实例与新实例之间自动转移，该过程结束时，可以快速故障转移。 

如果同一区域不能同时支持这两种硬件代系，可以更改硬件代系，但必须手动完成该操作。 这需要在所需硬件代系可用的区域中预配一个新实例，并在旧实例与新实例之间手动备份和还原数据。


## <a name="tune-performance"></a>调整性能

**如何优化托管实例的性能？**

常规用途托管实例使用远程存储，因为数据和日志文件的大小对性能的影响很大。 有关详细信息，请参阅[日志文件大小对常规用途托管实例性能的影响](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

如果工作负荷包含大量小型事务，请考虑将连接类型从代理切换为重定向模式。

## <a name="maximum-storage-size"></a>最大存储大小

**托管实例的最大存储大小是多少？**

托管实例的存储大小取决于所选的服务层级（“常规用途”或“业务关键”）。 有关这些服务层级的存储限制，请参阅[服务层级特征](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="back-up-storage-cost"></a>备份存储成本 

**备份存储是否是从托管实例存储中扣减出来的？**

不是，备份存储不是从托管实例的存储空间中扣减出来的。 备份存储与实例存储空间无关，其大小不受限制。 备份存储受实例数据库备份的保留期限（可配置为 7 到 35 天）的限制。 有关详细信息，请参阅[自动化备份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。

## <a name="track-billing"></a>跟踪计费

**是否有一种方法来跟踪托管实例的计费成本？**

可以使用[Azure 成本管理解决方案](/azure/cost-management/)实现此目的。 导航到[Azure 门户](https://portal.azure.com)中的 "**订阅**"，然后选择 "**成本分析**"。 

使用**累积成本**选项，并按**资源类型**筛选`microsoft.sql/managedinstances`。 
  
## <a name="inbound-nsg-rules"></a>入站 NSG 规则

**如何针对管理端口设置入站 NSG 规则？**

托管实例控制平面维护保护管理端口的 NSG 规则。

下面是用于的管理端口：

端口 9000 和 9003 由 Service Fabric 基础结构使用。 Service Fabric 的主要作用是使虚拟群集保持正常，并根据组件副本数保持目标状态。

节点代理使用端口1438、1440和1452。 节点代理是群集中运行的一个应用程序，控制平面使用它来执行管理命令。

除了 NSG 规则外，内置防火墙还会保护网络层上的实例。 在应用程序层通信上，用证书来保护。
  
有关详细信息以及如何验证内置防火墙，请参阅 [Azure SQL 数据库托管实例的内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="mitigate-data-exfiltration-risks"></a>缓解数据渗透风险  

**如何缓解数据渗透风险？**

为了缓解任何数据渗透的风险，建议客户应用一组安全设置和控制：

- 针对所有数据库启用[透明数据加密 (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql)。
- 禁用公共语言运行时 (CLR)。 也建议在本地禁用 CLR。
- 仅使用 Azure Active Directory (AAD) 身份验证。
- 使用低特权 DBA 帐户访问实例。
- 为 sysadmin 帐户配置 JiT jumpbox 访问权限。
- 启用 [SQL 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，并将其与警报机制相集成。
- 启用[高级数据安全 (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 套件中的[威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>节约成省用例

**在何处可以找到用例，以及使用托管实例可实现的成本节省？**

托管实例案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
为了更好地了解与部署 Azure SQL 数据库托管实例相关的优势、成本和风险，另外还提供了一个 Forrester 的研究：[英里的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS 刷新 

**是否可以执行 DNS 刷新？**

目前我们不提供刷新托管实例 DNS 服务器配置的功能。

DNS 配置最终会刷新：

- 当 DHCP 租约过期时。
- 平台升级时。

一种解决方法是将托管实例降级为 4 个 vCore，然后再次将其升级。 这样刷新 DNS 配置会产生一种负面影响。


## <a name="ip-address"></a>IP 地址

**能否使用 IP 地址连接到托管实例？**

不支持使用 IP 地址连接到托管实例。 托管实例主机名映射到托管实例虚拟群集前面的负载均衡器。 由于一个虚拟群集可以托管多个托管实例连接，因此无法将其路由到适当的托管实例，无需指定其名称。

有关托管实例虚拟群集体系结构的详细信息，请参阅[虚拟群集连接体系结构](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture)。

**托管实例是否可以使用静态 IP 地址？**

在罕见但必要的情况下，我们可能需要将托管实例联机迁移到新的虚拟群集。 需要进行这种迁移的原因是，我们的技术堆栈发生了变化，旨在提高服务的安全性和可靠性。 迁移到新的虚拟群集会导致映射到托管实例主机名的 IP 地址发生变化。 托管实例服务不会提出静态 IP 地址支持，且有权在不另行通知的情况下，在定期维护周期更改此 IP 地址。

出于此原因，我们强烈反对依赖于 IP 地址的不可变性，因为这可能会导致不必要的停机时间。

## <a name="change-time-zone"></a>更改时区

**是否可以更改现有托管实例的时区？**

首次预配托管实例时可以设置时区配置。 不支持更改现有托管实例的时区。 有关详细信息，请参阅[时区限制](sql-database-managed-instance-timezone.md#limitations)。

解决方法包括使用适当的时区创建新的托管实例，然后执行手动备份和还原，我们建议执行[跨实例时间点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="resolve-performance-issues"></a>解决性能问题

**如何解决托管实例的性能问题？**

若要在托管实例与 SQL Server 之间进行性能比较，可以从[有关 Azure SQL 托管实例与 SQL Server 之间的性能比较的最佳做法](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)一文入手。

由于必需的完整恢复模型以及事务日志写入吞吐量[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)方面的原因，托管实例上的数据加载速度通常比 SQL Server 中的速度更慢。 有时，通过将瞬态数据载入 tempdb 而不是用户数据库，或者使用聚集列存储或内存优化表，可以解决此问题。


## <a name="restore-encrypted-backup"></a>还原已加密的备份

**是否可将加密的数据库还原到托管实例？**

可以，无需解密数据库即可将其还原到托管实例。 需将一个在源系统中用作加密密钥保护器的证书/密钥提供给托管实例，才能从加密的备份文件中读取数据。 要运行此操作有两个可行的方式：

- 将证书保护器上传到托管实例。** 只能使用 PowerShell 执行此操作。 [示例脚本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)描述了整个过程。
- 将非对称密钥保护器上传到 Azure Key Vault (AKV)，并将托管实例指向该保护器。** 此方法类似于自带密钥 (BYOK) TDE 用例，该用例也使用 AKV 集成来存储加密密钥。 如果你不想使用密钥作为加密密钥保护程序，并且只是想让托管实例提供密钥来还原加密的数据库，请按照[设置 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的说明进行操作，并不要选中 "*将所选密钥设为默认 TDE 保护*程序" 复选框。

将加密保护器提供给托管实例使用后，可以继续执行标准的数据库还原过程。

## <a name="migrate-from-single-db"></a>从单一数据库迁移 

**如何从 Azure SQL 数据库单一池或弹性池迁移到托管实例？**

托管实例根据计算和存储大小提供与其他 Azure SQL 数据库部署选项相同的性能级别。 若要在单一实例上合并数据，或者只是需要一种仅在托管实例中受支持的功能，可以使用导出/导入 (BACPAC) 功能来迁移数据。
