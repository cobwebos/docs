---
title: 常见问题 (FAQ)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL 托管实例常见问题（FAQ）
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708852"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Azure SQL 托管实例常见问题（FAQ）
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文包含有关[AZURE SQL 托管实例](sql-managed-instance-paas-overview.md)的最常见问题。

## <a name="supported-features"></a>支持的功能

**在哪里可以找到 SQL 托管实例上支持的功能列表？**

有关 SQL 托管实例中支持的功能的列表，请参阅[AZURE sql 托管实例功能](../database/features-comparison.md)。

有关 Azure SQL 托管实例和 SQL Server 之间的语法和行为的差异，请参阅[与 SQL Server 的 t-sql 差异](transact-sql-tsql-differences-sql-server.md)。


## <a name="tech-spec--resource-limits"></a>技术规范和资源限制
 
**在哪里可以找到 SQL 托管实例的技术特性和资源限制？**

有关可用的硬件生成特性，请参阅[硬件生成中的技术差异](resource-limits.md#hardware-generation-characteristics)。
有关可用的服务层及其特征，请参阅[服务层之间的技术差异](resource-limits.md#service-tier-characteristics)。

## <a name="known-issues--bugs"></a>已知问题和 bug

**在何处可以找到已知问题和 bug？**

有关错误和已知问题，请参阅[已知问题](../database/doc-changes-updates-release-notes.md#known-issues)。

## <a name="new-features"></a>新增功能

**在何处可以找到最新功能和公共预览版功能？**

对于新功能和预览功能，请参阅[发行说明](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)。

## <a name="deployment-times"></a>部署时间 

**创建或更新实例或还原数据库需要多长时间？**

创建托管实例或更改服务层（Vcore，存储）所需的时间取决于多个因素。 请参阅[管理操作](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations)。 

## <a name="naming-conventions"></a>命名约定

**托管实例是否可以与 SQL Server 本地实例同名？**

不支持更改托管实例的名称。

可以更改托管实例的默认 DNS 区域*database.windows.net* 。 

若要使用其他 DNS 区域而不是默认区域（例如“.contoso.com”**），请执行以下操作： 
- 使用 CliConfig 定义别名。 该工具只是一个注册表设置包装器，因此也可以使用组策略或脚本完成此操作。
- 将*CNAME*与*TrustServerCertificate = true*选项一起使用。

## <a name="move-a-database-from-sql-managed-instance"></a>将数据库从 SQL 托管实例 

**如何将数据库从 SQL 托管实例移回 SQL Server 或 Azure SQL 数据库？**

可以将[数据库导出到 bacpac](../database/database-export.md) ，然后[导入 bacpac 文件](../database/database-import.md)。 如果数据库小于 100 GB，我们建议使用此方法。

如果数据库中的所有表具有主键，则可以使用事务复制。

`COPY_ONLY`无法将从 sql 托管实例获取的本机备份还原到 SQL Server，因为 sql 托管实例的数据库版本比 SQL Server 更高。

## <a name="migrate-an-instance-database"></a>迁移实例数据库

**如何将实例数据库迁移到 Azure SQL 数据库？**

一种做法是[将数据库导出到 BACPAC](../database/database-export.md)，然后[导入 BACPAC 文件](../database/database-import.md)。 

如果数据库小于 100 GB，我们建议使用此方法。 如果数据库中的所有表具有主键，则可以使用事务复制。

## <a name="switch-hardware-generation"></a>切换硬件代系 

**能否在第4代和第5代联机之间切换 SQL 托管实例硬件生成？**

如果在预配 SQL 托管实例的区域中同时提供两个硬件，则可以在硬件生成之间进行自动联机切换。 在这种情况下，可以查看 " [vCore 模型概述" 页](../database/service-tiers-vcore.md)，其中说明了如何在硬件生成之间切换。

这是一个长时间运行的操作，因为新的托管实例将在后台预配，在新实例与新实例之间自动传输，并在进程结束时进行快速故障转移。 

**如果同一区域中不支持两个硬件代，会怎么样？**

如果同一区域不能同时支持这两种硬件代系，可以更改硬件代系，但必须手动完成该操作。 这要求你在需要提供硬件的区域中预配新的实例，并在新旧实例之间手动备份和还原数据。

**如果没有足够的 IP 地址来执行更新操作，该怎么办？**

如果在预配托管实例的子网中没有足够的 IP 地址，则必须在其中创建新的子网和新的托管实例。 此外，我们还建议创建新的子网，并分配更多的 IP 地址，以便将来的更新操作将避免类似情况。 （为正确的子网大小，请查看[如何确定 VNet 子网的大小](vnet-subnet-determine-size.md)。）预配新实例后，可以在新实例和新实例之间手动备份和还原数据，或执行跨实例[时间点还原](point-in-time-restore.md?tabs=azure-powershell)。 


## <a name="tune-performance"></a>调整性能

**如何实现 SQL 托管实例的优化性能？**

常规用途层中的 SQL 托管实例使用远程存储，因此，数据和日志文件的大小对性能很重要。 有关详细信息，请参阅[常规用途 SQL 托管实例性能上的日志文件大小的影响](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

如果工作负荷包含大量小型事务，请考虑将连接类型从代理切换为重定向模式。

## <a name="maximum-storage-size"></a>最大存储大小

**SQL 托管实例的最大存储大小是多少？**

SQL 托管实例的存储大小取决于所选的服务层（常规用途或业务关键）。 有关这些服务层的存储限制，请参阅[服务层特性](../database/service-tiers-general-purpose-business-critical.md)。

## <a name="backup-storage-cost"></a>备份存储成本 

**备份存储是否已从我的 SQL 托管实例存储中扣除？**

不会，不会从 SQL 托管实例存储空间中减少备份存储。 备份存储与实例存储空间无关，其大小不受限制。 备份存储受实例数据库备份的保留期限（可配置为 7 到 35 天）的限制。 有关详细信息，请参阅[自动化备份](../database/automated-backups-overview.md)。

## <a name="track-billing"></a>跟踪计费

**是否有一种方法来跟踪 SQL 托管实例的计费成本？**

可以使用[Azure 成本管理解决方案](/azure/cost-management/)实现此目的。 导航到[Azure 门户](https://portal.azure.com)中的 "**订阅**"，然后选择 "**成本分析**"。 

使用**累积成本**选项，并按**资源类型**筛选 `microsoft.sql/managedinstances` 。 
  
## <a name="inbound-nsg-rules"></a>入站 NSG 规则

**如何针对管理端口设置入站 NSG 规则？**

SQL 托管实例控制平面维护保护管理端口的 NSG 规则。

下面是管理端口的用途：

端口9000和9003由 Azure Service Fabric 基础结构使用。 Service Fabric 主要角色是使虚拟群集保持正常运行状态，并根据组件副本的数量保持目标状态。

节点代理使用端口1438、1440和1452。 节点代理是在群集中运行的应用程序，由控制平面用于执行管理命令。

除了 NSG 规则外，内置防火墙还会保护网络层上的实例。 在应用程序层上，通过证书来保护通信。

有关详细信息以及如何验证内置防火墙的详细信息，请参阅[AZURE SQL 托管实例内置防火墙](management-endpoint-verify-built-in-firewall.md)。


## <a name="mitigate-data-exfiltration-risks"></a>缓解数据透露风险  

**如何缓解数据透露风险？**

为了缓解任何数据透露风险，我们建议客户应用一组安全设置和控制：

- 针对所有数据库启用[透明数据加密 (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql)。
- 禁用公共语言运行时 (CLR)。 也建议在本地禁用 CLR。
- 仅使用 Azure Active Directory （Azure AD）身份验证。
- 使用低权限 DBA 帐户访问实例。
- 为 sysadmin 帐户配置 JIT jumpbox 访问权限。
- 启用 [SQL 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，并将其与警报机制相集成。
- 启用[高级数据安全（ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)套件中的[威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>节约成本的用例

**在哪里可以找到用例以及通过 SQL 托管实例节省的成本？**

SQL 托管实例案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

为了更好地了解与部署 Azure SQL 托管实例相关的优势、成本和风险，还有一个 Forrester 研究： [Microsoft Azure SQL 数据库托管实例的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS 刷新 

**是否可以执行 DNS 刷新？**

目前，我们不提供刷新 SQL 托管实例的 DNS 服务器配置的功能。

DNS 配置最终会刷新：

- 当 DHCP 租约过期时。
- 平台升级时。

一种解决方法是，将 SQL 托管实例降级到 4 Vcore 并在以后再升级。 这样刷新 DNS 配置会产生一种负面影响。


## <a name="ip-address"></a>IP 地址

**能否使用 IP 地址连接到 SQL 托管实例？**

不支持使用 IP 地址连接到 SQL 托管实例。 SQL 托管实例主机名映射到 SQL 托管实例虚拟群集前面的负载均衡器。 由于一个虚拟群集可以托管多个托管实例，因此无法将连接路由到适当的托管实例，无需显式指定名称。

有关 SQL 托管实例虚拟群集体系结构的详细信息，请参阅[虚拟群集连接体系结构](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)。

**SQL 托管实例是否可以有静态 IP 地址？**

在极少数情况下，可能需要将 SQL 托管实例联机迁移到新的虚拟群集。 需要进行这种迁移的原因是，我们的技术堆栈发生了变化，旨在提高服务的安全性和可靠性。 迁移到新的虚拟群集会导致更改映射到 SQL 托管实例主机名的 IP 地址。 SQL 托管实例服务不会声明静态 IP 地址支持，并保留在不另行通知的情况下将其更改为定期维护周期的权限。

出于此原因，我们强烈反对依赖于 IP 地址的不可变性，因为这可能会导致不必要的停机时间。

## <a name="change-time-zone"></a>更改时区

**是否可以更改现有托管实例的时区？**

首次预配托管实例时可以设置时区配置。 不支持更改现有托管实例的时区。 有关详细信息，请参阅时区[限制](timezones-overview.md#limitations)。

解决方法包括使用适当的时区创建新的托管实例，然后执行手动备份和还原，或执行 "[跨实例时间点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)"。


## <a name="resolve-performance-issues"></a>解决性能问题

**如何实现解决 SQL 托管实例的性能问题？**

对于 SQL 托管实例和 SQL Server 之间的性能比较，好的起点是[AZURE SQL 托管实例与 SQL Server 之间性能比较的最佳实践](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

由于必需的完整恢复模式和对事务日志写入吞吐量的[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)，SQL 托管实例上的数据加载通常比 SQL Server 中的更慢。 有时，通过将暂时性数据加载到 tempdb 而不是用户数据库中，或者通过使用聚集列存储或内存优化表，可以解决这种情况。


## <a name="restore-encrypted-backup"></a>还原已加密的备份

**是否可以将加密的数据库还原到 SQL 托管实例？**

是的，无需解密数据库即可将其还原到 SQL 托管实例。 你确实需要提供一个证书/密钥，将其用作源系统上的加密密钥保护程序托管实例，以便能够从加密的备份文件中读取数据。 要运行此操作有两个可行的方式：

- *将证书保护程序上载到 SQL 托管实例*。 只能使用 PowerShell 执行此操作。 [示例脚本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)描述了整个过程。
- 将*非对称密钥保护程序上载到 Azure Key Vault 并将 SQL 托管实例点到它*。 这种方法类似于自带密钥（BYOK） TDE 用例，该用例也使用 Key Vault 集成来存储加密密钥。 如果你不想使用密钥作为加密密钥保护程序，只想让 SQL 托管实例提供密钥来还原加密的数据库，请按照[设置 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的说明进行操作，并不要选中 "**将所选密钥设为默认的 TDE 保护**程序" 复选框。

使加密保护程序可供 SQL 托管实例使用后，可以继续执行标准数据库还原过程。

## <a name="migrate-from-sql-database"></a>从 SQL 数据库迁移 

**如何将 Azure SQL 数据库迁移到 SQL 托管实例？**

SQL 托管实例为每个计算和存储大小提供与 Azure SQL 数据库相同的性能级别。 如果要在单个实例上合并数据，或者只是需要仅在 SQL 托管实例中支持的功能，则可以使用导出/导入（BACPAC）功能迁移数据。

## <a name="password-policy"></a>密码策略 

**适用于 SQL 托管实例 SQL 登录名的密码策略是什么？**

Sql 托管实例用于 SQL 登录名的密码策略继承应用于构成托管实例的虚拟群集的虚拟机的 Azure 平台策略。 目前不可能更改这些设置，因为这些设置由 Azure 定义并由托管实例继承。

 > [!IMPORTANT]
 > Azure 平台可以更改策略要求，而不会通知服务依赖于该策略。

**当前的 Azure 平台策略是什么？**

每个登录名必须在登录时设置其密码，并在达到最长期限后更改其密码。

| **策略** | **安全设置** |
| --- | --- |
| 最长密码期限 | 42天 |
| 最短密码期限 | 1 天 |
| 最短密码长度 | 10个字符 |
| 密码必须符合复杂性要求 | Enabled |

**是否可以在登录级别禁用 SQL 托管实例中的密码复杂性和过期？**

是的，可以控制登录级别 CHECK_POLICY 和 CHECK_EXPIRATION 字段。 可以通过执行以下 T-sql 命令来检查当前设置：

```sql
SELECT *
FROM sys.sql_logins
```

之后，你可以通过执行以下操作来修改指定的登录设置：

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

（将 "test" 替换为所需的登录名，并调整策略和过期值）
