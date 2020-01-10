---
title: 托管实例常见问题解答
description: SQL 数据库托管实例常见问题（FAQ）
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 81f776428303ad5e6486ba52c1acdf70d051563e
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835018"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL 数据库托管实例常见问题（FAQ）

本文包含与[SQL 数据库托管实例](sql-database-managed-instance.md)有关的许多最常见问题。

## <a name="supported-features"></a>支持的功能

**在哪里可以找到托管实例支持的功能列表？**

有关托管实例中支持的功能的列表，请参阅[AZURE SQL 数据库与 SQL Server](sql-database-features.md)。

有关 Azure SQL 数据库托管实例与本地 SQL Server 之间的语法和行为之间的差异，请参阅[与 SQL Server 的 t-sql 差异](sql-database-managed-instance-transact-sql-information.md)。


## <a name="tech-spec--resource-limits"></a>技术规范 & 资源限制
 
**在哪里可以找到托管实例的技术特征和资源限制？**

有关可用的硬件生成特性，请参阅[硬件生成中的技术差异](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)。
有关可用的服务层及其特征，请参阅[服务层之间的技术差异](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。

## <a name="known-issues--bugs"></a>已知问题 & bug

**在哪里可以找到已知问题和 bug？**

对于 bug 和已知问题，请参阅[已知问题](sql-database-managed-instance-transact-sql-information.md#Issues)。

## <a name="new-features"></a>新增功能

**在哪里可以找到最新的功能和公共预览版中的功能？**

对于新功能和预览功能，请参阅[发行说明](/azure/sql-database/sql-database-release-notes?tabs=managed-instance)。

## <a name="deployment-times"></a>部署时间 

**创建或更新实例或还原数据库需要多长时间？**

创建新的托管实例或更改服务层（Vcore，存储）所需的时间取决于多个因素。 查看[管理操作](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>命名约定

**托管实例是否可以与本地 SQL Server 具有相同的名称？**

托管实例的名称必须以*database.windows.net*结尾。 若要使用另一个 DNS 区域而不是默认**值，例如，contoso.com**： 
- 使用 CliConfig 定义别名。 该工具只是一个注册表设置包装器，因此也可以使用组策略或脚本完成此操作。
- 将*CNAME*与*TrustServerCertificate = true*选项一起使用。

## <a name="move-db-from-mi"></a>从 MI 移动数据库 

**如何将数据库从托管实例移回 SQL Server 或 Azure SQL 数据库？**

可以将[数据库导出到 bacpac](sql-database-export.md) ，然后[导入 bacpac 文件]( sql-database-import.md)。 如果数据库小于 100 GB，则建议使用此方法。

如果数据库中的所有表都具有主键，则可以使用事务复制。

由于托管实例的数据库版本比 SQL Server 更高，因此无法将来自托管实例的本机 `COPY_ONLY` 备份还原到 SQL Server 中。

## <a name="migrate-instance-db"></a>迁移实例数据库

**如何将实例数据库迁移到单个 Azure SQL 数据库？**

一种选择是将[数据库导出到 bacpac](sql-database-export.md) ，然后[导入 bacpac 文件](sql-database-import.md)。 

如果数据库小于 100 GB，则建议使用此方法。 如果数据库中的所有表都具有主键，则可以使用事务复制。

## <a name="gen-4-vs-gen-5"></a>第4代和第5代 

**对于托管实例，如何实现在第4代和第5代硬件生成之间进行选择吗？**

这取决于你的工作负荷，因为某些类型的工作负荷更适合某些类型的工作负荷。 尽管性能主题是一个复杂的主题，但要简化这种情况，但影响工作负荷性能的硬件生成之间存在以下差异：
- 第4代提供更好的计算支持，因为它基于物理处理器，而不是基于 vCore 处理器的第5代。 这对于计算密集型工作负荷可能更有利。
- 第5代支持加速网络，导致更好的 IO 带宽到远程存储。 对于常规用途服务层上的 IO 密集型工作负荷，这一点可能很有利。 与第4代相比，第5代使用更快的 SSD 本地磁盘。 对于业务关键服务层上的 IO 密集型工作负荷，这一点可能很有利。

强烈建议在投入使用之前测试用于生产的实际工作负荷的性能，以确定哪些硬件生成在特定情况下的工作效果更佳。

## <a name="switch-hardware-generation"></a>交换机硬件生成 

**能否在第4代和第5代联机之间切换托管实例硬件生成？**

如果在预配托管实例的区域中同时提供两个硬件，则可以在硬件生成之间进行自动联机切换。 在这种情况下，可以使用[博客文章中的脚本](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Change-hardware-generation-on-Managed-Instance/ba-p/699824)，说明如何在硬件生成之间切换。

这是一个长时间运行的操作，因为新的托管实例将在后台预配，在该过程结束时，将自动在新实例和新实例之间传输数据库。 

如果同一区域中不支持这两个硬件生成，则可以更改硬件生成，但必须手动完成。 这要求你在需要提供硬件的区域中预配新的实例，并在新实例和新实例之间手动备份和还原数据。


## <a name="tune-performance"></a>调整性能

**如何实现优化托管实例的性能吗？**

常规用途托管实例使用远程存储，因为数据和日志文件的大小对于性能很重要。 有关详细信息，请参阅[常规用途托管实例性能的日志文件大小的影响](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)。

对于 IO 密集型工作负荷，请考虑使用第5代硬件，而不是使用第4代计算密集型工作负荷。 有关详细信息，请参阅[如何实现在第4代和第5代之间进行选择](#gen-4-vs-gen-5)。

如果工作负荷包含大量小事务，请考虑将连接类型从代理切换到重定向模式。

## <a name="maximum-storage-size"></a>最大存储大小

**托管实例的最大存储大小是多少？**

托管实例的存储大小取决于所选的服务层（常规用途或业务关键）。 有关这些服务层的存储限制，请参阅[服务层特性](sql-database-service-tiers-general-purpose-business-critical.md)。

## <a name="back-up-storage-cost"></a>备份存储成本 

**备份存储是否从托管实例存储中扣除？**

不会，不会从托管实例存储空间中扣除备份存储。 备份存储与实例存储空间无关，它的大小不受限制。 备份存储限制为保留实例数据库备份的时间段，可从7天到35天进行配置。 有关详细信息，请参阅[自动备份](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。

## <a name="track-billing"></a>跟踪计费

**是否有一种方法来跟踪托管实例的计费成本？**

可以使用[Azure 成本管理解决方案](/azure/cost-management/)实现此目的。 导航到[Azure 门户](https://portal.azure.com)中的 "**订阅**"，然后选择 "**成本分析**"。 

使用**累积成本**选项，然后按 `microsoft.sql/managedinstances`的**资源类型**进行筛选。 
  
## <a name="inbound-nsg-rules"></a>入站 NSG 规则

**如何设置管理端口上的入站 NSG 规则？**

内置防火墙功能可在群集中的所有虚拟机上配置 Windows 防火墙，以允许来自仅与 Microsoft 管理/部署计算机相关联的 IP 范围的入站连接，以及通过网络层有效阻止入侵的安全管理工作站。

下面是用于的端口：

端口9000和9003由 Service Fabric 基础结构使用。 Service Fabric 主要角色是使虚拟群集保持正常运行状态，并根据组件副本的数量保持目标状态。

节点代理使用端口1438、1440和1452。 节点代理是在群集中运行的应用程序，由控制平面用于执行管理命令。

除网络层上的内置防火墙外，还会用证书来保护通信。
  
有关详细信息以及如何验证内置防火墙的详细信息，请参阅[AZURE SQL 数据库托管实例内置防火墙](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)。


## <a name="mitigate-network-risks"></a>缓解网络风险  

**如何缓解网络风险？**

为了缓解任何网络风险，建议客户应用一组安全设置和控制：

- 在所有数据库上打开[透明数据加密（TDE）](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) 。
- 禁用公共语言运行时（CLR）。 这也建议在本地使用。
- 仅使用 Azure Active Directory （AAD）身份验证。
- 特权较低的 DBA 帐户的访问实例。
- 为 sysadmin 帐户配置 JiT jumpbox 访问权限。
- 启用[SQL 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)，并将其与警报机制集成。
- 启用[高级数据安全（ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)套件中的[威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)。


## <a name="cost-saving-use-cases"></a>节约成本的用例

**在哪里可以找到用例并使用托管实例节省成本？**

托管实例案例研究：

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
为了更好地了解与部署 Azure SQL 数据库托管实例相关的优势、成本和风险，另外还提供了一个 Forrester 的研究：[英里的总体经济影响](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)。


## <a name="dns-refresh"></a>DNS 刷新 

**能否进行 DNS 刷新？**

目前，我们不提供刷新托管实例的 DNS 服务器配置的功能。

最终刷新 DNS 配置：

- DHCP 租约过期的时间。
- 平台升级。

一种解决方法是，将托管实例降级为4个 vCore 并在以后再升级。 这具有刷新 DNS 配置的副作用。


## <a name="static-ip-address"></a>静态 IP 地址

**托管实例是否可以有静态 IP 地址？**

在极少数情况下，可能需要执行将托管实例联机迁移到新虚拟群集的操作。 如果需要，此迁移的原因是我们的技术堆栈发生了变化，旨在提高服务的安全性和可靠性。 迁移到新的虚拟群集会导致更改映射到托管实例主机名的 IP 地址。 托管实例服务不会声明静态 IP 地址支持，并保留在不另行通知的情况下将其更改为定期维护周期的权限。

出于此原因，我们强烈反对依赖 IP 地址的不可变性，因为这可能会导致不必要的停机时间。

## <a name="moving-mi"></a>移动 MI

**是否可以将托管实例或其 VNet 移到另一个资源组？**

不是，这是当前平台的限制。 创建托管实例后，不支持将托管实例或 VNet 移到另一个资源组或订阅。

## <a name="change-time-zone"></a>更改时区

**能否更改现有托管实例的时区？**

首次预配托管实例时，可以设置时区配置。 不支持更改现有托管实例的时区。 有关详细信息，请参阅时区[限制](sql-database-managed-instance-timezone.md#limitations)。

解决方法包括使用适当的时区创建新的托管实例，然后执行手动备份和还原，或建议执行[跨实例的时间点还原](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/)。


## <a name="resolve-performance-issues"></a>解决性能问题

**如何实现解决托管实例的性能问题吗？**

对于托管实例和 SQL Server 之间的性能比较，良好的起点是[AZURE SQL 托管实例与 SQL Server 文章之间性能比较的最佳实践](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)。

由于强制完整恢复模式和对事务日志写入吞吐量的[限制](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)，在托管实例上的数据加载通常比在 SQL Server 中。 有时，通过将暂时性数据加载到 tempdb 而不是用户数据库中，或者使用聚集列存储或内存优化表，可以解决这种情况。


## <a name="restore-encrypted-backup"></a>还原加密的备份

**是否可以将加密的数据库还原到托管实例？**

是的，无需解密数据库即可将其还原到托管实例。 你确实需要提供一个证书/密钥，将其用作源系统中的加密密钥保护程序，使其能够从加密的备份文件中读取数据。 要运行此操作有两个可行的方式：

- 将*证书保护程序上载到托管实例*。 只能使用 PowerShell 来完成此操作。 [示例脚本](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate)介绍了整个过程。
- 将*非对称密钥保护程序上载到 Azure Key Vault （AKV）并将其指向托管实例*。 这种方法类似于自带密钥（BYOK） TDE 用例，也使用 AKV 集成来存储加密密钥。 如果你不想使用密钥作为加密密钥保护程序，并且只是想让托管实例提供密钥来还原加密的数据库，请按照[设置 BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)的说明进行操作，并不要选中 "*将所选密钥设为默认 TDE 保护*程序" 复选框。

使加密保护程序可用于托管实例后，可以继续执行标准数据库还原过程。

## <a name="migrate-from-single-db"></a>从单一数据库迁移 

**如何从 Azure SQL Database 单一或弹性池迁移到托管实例？**

托管实例提供了与 Azure SQL 数据库的其他部署选项相同的每个计算和存储大小的性能级别。 如果要合并单个实例上的数据，或者只需要在托管实例中仅支持某个功能，可以使用导出/导入（BACPAC）功能迁移数据。
