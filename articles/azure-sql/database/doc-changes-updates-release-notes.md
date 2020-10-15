---
title: 新增功能
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 了解 Azure SQL 数据库和 SQL 托管实例的新增功能和文档改进。
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: sstein
ms.openlocfilehash: 027a816e846996aa7c61a1747327128f9a0feed0
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079201"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>Azure SQL 数据库和 SQL 托管实例中的新增功能有哪些？
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文列出了当前以公共预览版形式提供的 Azure SQL 数据库和 Azure SQL 托管实例的功能。 有关 SQL 数据库和 SQL 托管实例更新和改进，请参阅 [Sql 数据库 & sql 托管实例服务更新](https://azure.microsoft.com/updates/?product=sql-database)。 有关对其他 Azure 服务的更新和改进，请参阅 [服务更新](https://azure.microsoft.com/updates)。

## <a name="whats-new"></a>新增功能

Azure SQL 数据库和 Azure SQL 托管实例的相关文档已拆分为单独的部分。 我们还将托管实例的指代方式从“Azure SQL 数据库托管实例”更新为“Azure SQL 托管实例” 。

这样做是因为单一数据库与托管实例之间的某些特性和功能存在极大的出入，并且越来越难以在同时介绍二者的单个文章中解释清楚 Azure SQL 数据库和 Azure SQL 托管实例之间的细微差别。

这种关于不同 Azure SQL 产品的明确区分应能够简化在 Azure 中使用 SQL Server 数据库引擎的过程，无论是 Azure SQL 数据库中的单个托管数据库、Azure SQL 托管实例中托管多个数据库的功能完善的托管实例还是托管在 Azure 中虚拟机上的常用本地 SQL Server 产品。

请注意，这是一项进展中的工作，有些文章可能尚未更新。 例如，有关 Transact-SQL (T-SQL) 语句、存储过程以及 Azure SQL 数据库与 Azure SQL 托管实例之间的许多共有功能的文档尚待完成，因此，感谢你耐心等待我们不断更新内容。 

下表提供了术语更改后的简要对比： 


|**新术语**  | **旧术语**  |**解释** |
|---------|---------|---------|
|**Azure SQL 托管实例** | Azure SQL 数据库托管实例| Azure SQL 托管实例是 Azure SQL 的系列产品之一，而不只是 Azure SQL 数据库中的部署选项。 | 
|**Azure SQL 数据库**|Azure SQL 数据库单一数据库| 除非另有明确说明，否则产品名称 Azure SQL 数据库的含义将同时涵盖单一数据库和部署到弹性池的数据库。 |
|**Azure SQL 数据库**|Azure SQL 数据库弹性池| 除非另有明确说明，否则产品名称 Azure SQL 数据库的含义将同时涵盖单一数据库和部署到弹性池的数据库。  |
|**Azure SQL 数据库** |Azure SQL 数据库 | 尽管术语没有变化，但它现在仅适用于单一数据库和弹性池部署，并且不包括托管实例。 |
| **Azure SQL**| 空值 | 这是指 Azure 中提供的 SQL Server 数据库引擎产品系列：Azure VM 上的 Azure SQL 数据库、Azure SQL 托管实例和 SQL Server。 | 

## <a name="features-in-public-preview"></a>处于公共预览版的功能

### <a name="azure-sql-database"></a>[Azure SQL 数据库](#tab/single-database)

| 功能 | 详细信息 |
| ---| --- |
| 通过单一数据库和弹性池加速的数据库恢复 | 有关信息，请参阅[加速的数据库恢复](../accelerated-database-recovery.md)。|
| 数据发现和分类  |有关详细信息，请参阅 [Azure SQL 数据库和 Azure Synapse Analytics 数据发现和分类](data-discovery-and-classification-overview.md)。|
| 弹性数据库作业（预览版） | 有关信息，请参阅[创建、配置和管理弹性作业](elastic-jobs-overview.md)。 |
| 弹性查询 | 有关信息，请参阅[弹性查询概述](elastic-query-overview.md)。 |
| 弹性事务 | [跨云数据库的分布式事务](elastic-transactions-overview.md)。 |
| Azure 门户中的查询编辑器 |有关信息，请参阅[使用 Azure 门户的 SQL 查询编辑器进行连接并查询数据](connect-query-portal.md)。|
| 带有单一数据库和弹性池的 R services/机器学习 |有关信息，请参阅 [AZURE SQL 数据库中的机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
|SQL Analytics|有关信息，请参阅 [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md)。|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL 托管实例](#tab/managed-instance)

| 功能 | 详细信息 |
| ---| --- |
| <a href="/azure/azure-sql/database/elastic-transactions-overview">分布式事务</a> | 跨托管实例的分布式事务。 |
| <a href="/azure/sql-database/sql-database-instance-pools">实例池</a> | 将较小的 SQL 实例迁移到云的方便且经济高效的方式。 |
| <a href="https://aka.ms/managed-instance-aadlogins">实例级 Azure AD 服务器主体（登录名）</a> | 使用 <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 语句创建实例级登录名。 |
| [事务复制](../managed-instance/replication-transactional-overview.md) | 将表中的更改复制到 SQL 托管实例、SQL 数据库或 SQL Server 中的其他数据库。 或当 SQL 托管实例或 SQL Server 的其他实例中某些行发生更改时更新表。 若要了解信息，请参阅[在 Azure SQL 托管实例中配置复制](../managed-instance/replication-between-two-instances-configure-tutorial.md)。 |
| 威胁检测 |若要了解信息，请参阅[在 Azure SQL 托管实例中配置威胁检测](../managed-instance/threat-detection-configure.md)。|
| 长期备份保留 | 若要了解信息，请参阅[在 Azure SQL 托管实例中配置长期备份保留](../managed-instance/long-term-backup-retention-configure.md)（当前为功能有限的公共预览版）。 | 

---

## <a name="new-features"></a>新增功能

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL 托管实例 H2 2019 更新

- [服务辅助子网配置](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/)是一种用来管理子网配置的安全而方便的方法。使用此方法，你可以控制数据流量，而托管实例可确保管理流量不断流。
- [自带密钥 (BYOK) 的透明数据加密 (TDE)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) 为静态数据保护实现了自带密钥 (BYOK) 方案，并使组织能够在密钥管理和数据管理方面实现职责分离。
- 使用[自动故障转移组](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)可以将主实例中的所有数据库复制到另一个区域中的辅助实例。
- 使用[全局跟踪标志](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)，可以配置 SQL 托管实例行为。

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL 托管实例 H1 2019 更新

在 H1 2019 的 SQL 托管实例部署模型中启用了以下功能：
  - 支持具有 <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 订阅者的 Azure 每月额度</a>和增加的[区域限制](../managed-instance/resource-limits.md#regional-resource-limitations)的订阅。
  - 支持 <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 和 SharePoint 2019 </a> 以及 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central。</a>
  - 使用所选的<a href="https://aka.ms/managed-instance-collation">实例级排序规则</a>和<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">时区</a>创建托管实例。
  - 托管实例现在使用[内置防火墙](../managed-instance/management-endpoint-verify-built-in-firewall.md)进行保护。
  - 配置 SQL 托管实例以使用[公共终结点](../managed-instance/public-endpoint-configure.md)、[代理覆盖](connectivity-architecture.md#connection-policy)连接以获得更好的网络性能，<a href="https://aka.ms/four-cores-sql-mi-update">Gen5 硬件代系有 4 个 vCore</a> 或<a href="https://aka.ms/managed-instance-configurable-backup-retention">将备份保留期配置为最多 35 天</a>以便进行时间点还原。 [长期备份保留](long-term-retention-overview.md#sql-managed-instance-support)（最长 10 年）目前处于有限公共预览版状态。  
  - 利用新功能，可以<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">使用 PowerShell 将数据库异地还原到另一个数据中心</a>、[重命名数据库](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[删除虚拟群集](../managed-instance/virtual-cluster-delete.md)。
  - 新的内置[实例参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)使职责分离 (SoD) 遵从安全原则并符合企业标准。
  - 以下 Azure 政府版中提供了 SQL 托管实例 (US Gov 德克萨斯州、US Gov 亚利桑那州) ，以及中国北部2和中国东部2。 它还在以下公共区域中提供：澳大利亚中部、澳大利亚中部2、巴西南部、法国南部、阿拉伯联合酋长国中部、阿拉伯联合酋长国北部、南非北部、南非西北部。

## <a name="known-issues"></a>已知问题

|问题  |发现日期  |状态  |解决日期  |
|---------|---------|---------|---------|
|[在从服务器信任组删除托管实例后，可以执行分布式事务](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Oct 2020|具有解决方法||
|[托管实例缩放操作后无法执行分布式事务](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Oct 2020|具有解决方法||
|Azure SQL 中的[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)和 `BACKUP` / `RESTORE` 托管实例中的语句无法使用 Azure AD 管理标识向 Azure 存储进行身份验证|09月2020|具有解决方法||
|[服务主体无法访问 Azure AD 和 AKV](#service-principal-cannot-access-azure-ad-and-akv)|2020 年 8 月|具有解决方法||
|[没有使用 CHECKSUM 的手动备份可能无法还原](#restoring-manual-backup-without-checksum-might-fail)|2020 年 5 月|已解决|2020 年 6 月|
|[在修改、禁用或启用现有作业后代理无响应](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|2020 年 5 月|已解决|2020 年 6 月|
|[资源组上的权限不应用于 SQL 托管实例](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|2020 年 2 月|具有解决方法||
|[通过门户对故障转移组进行手动故障转移的限制](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020 年 1 月|具有解决方法||
|[SQL 代理角色需要拥有对非 sysadmin 登录名的显式 EXECUTE 权限](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 12 月|具有解决方法||
|[重启代理进程可能会中断 SQL 代理作业](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 年 12 月|已解决|2020 年 3 月|
|[SSDT 不支持 Azure AD 登录名和用户](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|2019 年 11 月|无解决方法||
|[内存中 OLTP 内存限制不适用](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 10 月|具有解决方法||
|[尝试删除不为空的文件时，返回了错误的错误](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 年 10 月|具有解决方法||
|[更改服务层级和创建实例的操作会被正在进行的数据库还原操作阻止](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 年 9 月|具有解决方法||
|[故障转移后，可能需要重新配置“业务关键”服务层级上的 Resource Governor](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 年 9 月|具有解决方法||
|[升级服务层级后必须重新初始化跨数据库 Service Broker 对话](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|2019 年 8 月|具有解决方法||
|[不支持模拟 Azure AD 登录类型](#impersonation-of-azure-ad-login-types-is-not-supported)|2019 年 7 月|无解决方法||
|[sp_send_db_mail 中不支持 @query 参数](#-parameter-not-supported-in-sp_send_db_mail)|2019 年 4 月|无解决方法||
|[异地故障转移之后，必须重新配置事务复制](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 年 3 月|无解决方法||
|[在还原操作过程中使用临时数据库](#temporary-database-is-used-during-restore-operation)||具有解决方法||
|[将重新创建 TEMPDB 结构和内容](#tempdb-structure-and-content-is-re-created)||无解决方法||
|[小型数据库文件超出存储空间](#exceeding-storage-space-with-small-database-files)||具有解决方法||
|[显示 GUID 值而不是数据库名称](#guid-values-shown-instead-of-database-names)||具有解决方法||
|[不保留错误日志](#error-logs-arent-persisted)||无解决方法||
|[跨同一实例中的两个数据库的事务范围不受支持](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||具有解决方法|2020 年 3 月|
|[CLR 模块和链接的服务器有时无法引用本地 IP 地址](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||具有解决方法||
|从 Azure Blob 存储还原数据库后未使用 DBCC CHECKDB 验证数据库一致性。||已解决|2019 年 11 月|
|如果源数据库包含内存中 OLTP 对象，则从“业务关键”层级到“常规用途”层级的时间点数据库还原将不会成功。||已解决|2019 年 10 月|
|使用具有安全连接的外部（非 Azure）邮件服务器时出现数据库邮件功能问题||已解决|2019 年 10 月|
|SQL 托管实例不支持包含的数据库||已解决|2019 年 8 月|

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>在从服务器信任组删除托管实例后，可以执行分布式事务

[服务器信任组](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) 用于建立执行 [分布式事务](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview)的必备组件之间的信任关系。 从服务器信任组中删除托管实例或删除组后，仍可以执行分布式事务。 有一种解决方法，你可以应用来确保分布式事务已禁用并且是用户在托管实例上 [启动的手动故障转移](https://docs.microsoft.com/azure/azure-sql/managed-instance/user-initiated-failover) 。

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>托管实例缩放操作后无法执行分布式事务

托管实例缩放操作（包括变化的服务层或 Vcore 数）将在后端重置服务器信任组设置并禁用正在运行的 [分布式事务](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview)。 解决方法是在 Azure 门户上删除并创建新的 [服务器信任组](https://docs.microsoft.com/azure/azure-sql/managed-instance/server-trust-group-overview) 。

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT 和备份/还原语句无法使用托管标识访问 Azure 存储

Bulk insert 语句无法 `DATABASE SCOPED CREDENTIAL` 与托管标识一起使用来向 Azure 存储进行身份验证。 解决方法是切换到共享访问签名身份验证。 下面的示例将无法在 Azure SQL (数据库和托管实例) ：

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**解决方法**：使用 [共享访问签名向存储进行身份验证](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql?view=sql-server-ver15#f-importing-data-from-a-file-in-azure-blob-storage)。

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>服务主体无法访问 Azure AD 和 AKV

在某些情况下，用于访问 Azure AD 和 Azure Key Vault (AKV) 服务的服务主体可能存在问题。 此问题最终会对使用 Azure AD 身份验证和 SQL 托管实例的透明数据库加密 (TDE) 产生影响。 这可能是一个间歇性连接问题，或者无法运行诸如 CREATE LOGIN/USER FROM EXTERNAL PROVIDER 或 EXECUTE AS LOGIN/USER 之类的语句。 在某些情况下，在新的 Azure SQL 托管实例上使用客户托管密钥设置 TDE 也可能不起作用。

**解决方法**：若要防止托管实例在执行任何更新命令之前出现此问题，或在更新命令后遇到此问题 Azure 门户，请访问 sql 托管实例 [Active Directory 管理边栏选项卡](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#azure-portal)。 验证是否可以看到错误消息“托管实例需要服务主体才能访问 Azure Active Directory。 单击此处创建服务主体”。 如果看到此错误消息，请单击它，然后按照提供的分步说明操作，直到解决此错误为止。

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>没有使用 CHECKSUM 的手动备份可能无法还原

在某些情况下，没有使用 CHECKSUM 在托管实例上进行的数据库手动备份可能无法还原。 在这种情况下，请重试还原备份，直到成功为止。

**解决方法**：在启用了 CHECKSUM 的情况下在托管实例上手动备份数据库。

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>在修改、禁用或启用现有作业后代理无响应

在某些情况下，修改、禁用或启用现有作业可能会导致代理无响应。 此问题在被检测到后自动缓解，导致代理进程重启。

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>资源组上的权限不应用于 SQL 托管实例

将 SQL 托管实例参与者 Azure 角色应用于资源组 (RG) 时，该角色不应用于 SQL 托管实例，因此不起作用。

**解决方法**：在订阅级别为用户设置“SQL 托管实例参与者”角色。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>通过门户对故障转移组进行手动故障转移的限制

如果故障转移组跨不同 Azure 订阅或资源组中的实例，则无法从故障转移组中的主实例启动手动故障转移。

**解决方法**：通过门户从异地辅助实例启动故障转移。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 代理角色需要拥有对非 sysadmin 登录名的显式 EXECUTE 权限

如果将非 sysadmin 登录名添加到任何 [SQL 代理固定数据库角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)，则会出现以下问题：需要向主存储过程授予显式 EXECUTE 权限才能使这些登录名正常工作。 如果遇到此问题，将显示错误消息“在对象 <object_name> 中拒绝了 EXECUTE 权限(Microsoft SQL Server，错误:229)”。

**解决方法**：将登录名添加到 SQL 代理固定数据库角色（SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgentOperatorRole）后，对于添加到这些角色的每个登录名，请执行以下 T-SQL 脚本，向列出的存储过程显式授予 EXECUTE 权限。

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>重启代理进程可能会中断 SQL 代理作业

（2020 年 3 月已解决）SQL 代理在每次启动作业时都会新建一个会话，从而逐渐增加内存消耗。 为了避免达到内部内存限制，从而阻止已计划作业的执行，一旦代理的内存消耗量达到阈值，就会重启代理进程。 这可能会中断重启时正在运行的作业的执行。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>内存中 OLTP 内存限制不适用

在某些情况下，业务关键服务无法正确应用[内存优化对象的最大内存限制](../managed-instance/resource-limits.md#in-memory-oltp-available-space)。 SQL 托管实例可以让工作负荷使用更多的内存进行内存中 OLTP 操作，这可能影响实例的可用性和稳定性。 达到限制的内存中 OLTP 查询可能不会立即失败。 此问题即将得到解决。 使用较多内存中 OLTP 内存的查询在达到[限制](../managed-instance/resource-limits.md#in-memory-oltp-available-space)的情况下会更快地失败。

**解决方法**：使用 [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) [监视内存中 OLTP 存储使用情况](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-monitor-space)，确保工作负荷不会使用比提供的内存更多的内存。 提高基于 vCore 数的内存限制，或者优化工作负荷，让其使用较少的内存。
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>尝试删除不为空的文件时，返回了错误的错误

SQL Server 和 SQL 托管实例[不允许用户删除不为空的文件](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 如果尝试使用 `ALTER DATABASE REMOVE FILE` 语句删除非空数据文件，系统会立即返回 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 错误。 SQL 托管实例会继续尝试删除该文件，操作会在 30 分钟后失败并显示 `Internal server error`。

**解决方法**：使用 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 命令删除文件的内容。 如果这是文件组中的唯一文件，则需从与此文件组关联的表或分区中删除数据，然后才能收缩文件并选择将该数据加载到另一表/分区中。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>更改服务层级和创建实例的操作会被正在进行的数据库还原操作阻止

正在运行的 `RESTORE` 语句、数据迁移服务的迁移过程以及内置的时间点还原都会阻止对服务层的更新操作或者对现有实例的重设大小操作以及创建新实例的操作，直至还原过程完成为止。 

还原过程会阻止其运行时所在的子网的托管实例和实例池中的这些操作。 实例池中的实例不受影响。 服务层创建或更改操作不会失败或超时。还原过程完成或取消后，将继续执行这些操作。

**解决方法**：请等待还原过程完成，或者，如果创建或更新服务层级的操作的优先级更高，可取消还原过程。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>故障转移后，可能需要重新配置“业务关键”服务层级上的 Resource Governor

利用 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 功能，你可以限制分配给用户工作负荷的资源，在故障转移后可能会错误地分类某些用户工作负荷，或者在用户启动的服务层更改 (例如，更改 max vCore 或最大实例存储大小) 。

**解决方法**： `ALTER RESOURCE GOVERNOR RECONFIGURE` 如果使用 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)，则在启动实例时，将定期运行或作为 sql 代理作业的一部分执行 sql 任务。

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>升级服务层级后必须重新初始化跨数据库 Service Broker 对话

完成更改服务层级的操作后，跨数据库 Service Broker 对话会停止向其他数据库中的服务传递消息。 这些消息没有丢失，可以在发送方队列中找到它们。 在 SQL 托管实例中对 vCore 或实例存储大小进行任何更改都会导致 [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 视图中所有数据库的 `service_broke_guid` 值发生更改。 使用 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 语句创建的、引用其他数据库中的 Service Broker 的任何 `DIALOG` 将停止向目标服务传递消息。

**解决方法**：先停止使用跨数据库 Service Broker 对话的任何活动，再更新服务层级，然后重新初始化这些活动。 如果在更改服务层级后存在任何未传递的剩余消息，请从源队列中读取消息，然后将其重新发送到目标队列。

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>不支持模拟 Azure AD 登录类型

不支持使用以下 Azure Active Directory (Azure AD) 主体的 `EXECUTE AS USER` 或 `EXECUTE AS LOGIN` 进行模拟：
-   别名化的 Azure AD 用户。 在这种情况下，将返回以下错误：`15517`。
- 基于 Azure AD 应用程序或服务主体的 Azure AD 登录名和用户。 在这种情况下，将返回以下错误：`15517` 和 `15406`。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail 中不支持 @query 参数

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 过程中的 `@query` 参数不起作用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>异地故障转移之后，必须重新配置事务复制

如果对自动故障转移组中的数据库启用了事务复制，则 SQL 托管实例管理员必须清理旧的主节点上的所有发布内容，然后在故障转移到另一个区域后，在新的主节点上重新配置这些发布内容。 有关详细信息，请参阅[复制](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication)。

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 不支持 Azure AD 登录名和用户

SQL Server Data Tools 不完全支持 Azure AD 登录名和用户。

### <a name="temporary-database-is-used-during-restore-operation"></a>在还原操作过程中使用临时数据库

在 SQL 托管实例上还原某个数据库时，还原服务首先会使用所需的名称创建一个空数据库，以便在实例上分配该名称。 一段时间后，将会删除此数据库，并启动实际数据库的还原。 

处于“正在还原”状态的数据库将临时使用随机 GUID 值而不是名称。 还原过程完成后，临时名称将更改为 `RESTORE` 语句中指定的所需名称。 

在初始阶段，用户可以访问空数据库，甚至可以在此数据库中创建表或加载数据。 当还原服务启动第二个阶段时，将删除此临时数据库。

**解决方法**：在看到还原完成之前，请不要访问正在还原的数据库。

### <a name="tempdb-structure-and-content-is-re-created"></a>将重新创建 TEMPDB 结构和内容

`tempdb` 数据库始终拆分为 12 个数据文件，文件结构不可更改。 无法更改每个文件的最大大小，并且无法将新文件添加到 `tempdb`。 实例启动或故障转移时，`Tempdb` 始终会重新创建为空数据库；在 `tempdb` 中所做的任何更改不会保留。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE`、`ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 语句可能会失败，因为实例可能会达到 Azure 存储限制。

每个 SQL 托管实例的常规用途实例都为 Azure 高级磁盘空间保留了最多 35 TB 存储空间。 每个数据库文件放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁盘上未使用的空间不收费，但 Azure 高级磁盘大小总计不能超过 35 TB。 在某些情况下，由于内部碎片，总共不需要 8 TB 的托管实例可能会超过 35 TB 的 Azure 存储大小限制。

例如，SQL 托管实例的常规用途实例可将一个大小为 1.2 TB 的大文件放在 4 TB 的磁盘上。 它还可以将 248 个文件（每个大小为 1 GB）放在单独的 128 GB 磁盘上。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例演示在某些情况下，由于文件的具体分布，SQL 托管实例的实例可能会出乎意料地达到为附加的 Azure 高级磁盘预留的 35 TB 存储空间大小限制。

在此示例中，只要未添加新文件，现有数据库就会继续工作并且可以毫无问题地增长。 由于没有足够的空间用于新磁盘驱动器，因此无法创建或还原新数据库，即使所有数据库的总大小未达到实例大小限制也是如此。 这种情况下返回的错误并不明确。

可以使用系统视图[识别剩余文件的数目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)。 如果达到此限制，请尝试[使用 DBCC SHRINKFILE 语句来清空并删除一些较小的文件](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或切换到[没有此限制的业务关键层](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>显示 GUID 值而不是数据库名称

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为将来它们会被替换为实际的数据库名称。

**解决方法**：使用 sys.databases 视图通过物理数据库名称（以 GUID 数据库标识符的形式指定）解析实际数据库名称：

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>不保留错误日志

SQL 托管实例中可用的错误日志不会持久保留，并且它们的大小不包括在最大存储限制中。 在发生故障转移时可能会自动清除错误日志。 错误日志历史记录中可能存在差距，因为 SQL 托管实例在多个虚拟机上转移了多次。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>跨同一实例中的两个数据库的事务范围不受支持

（2020 年 3 月已解决）如果两个查询被发送到同一事务范围下相同实例内的两个数据库，那么 .Net 中的 `TransactionScope` 类就无法正常运行：

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**解决方法（自 2020 年 3 月起不再需要）** ：使用 [SqlConnection.ChangeDatabase(String)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) 在连接上下文中使用其他数据库，而非使用两个连接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模块和链接的服务器有时无法引用本地 IP 地址

放在 SQL 托管实例中的 CLR 模块以及引用当前实例的链接服务器或分布式查询有时可能无法解析本地实例的 IP。 此错误是暂时性问题。

**解决方法**：如果可能，请在 CLR 模块中使用上下文连接。

## <a name="updates"></a>更新

有关 SQL 数据库更新和改进的列表，请参阅 [Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。

有关所有 Azure 服务的更新和改进，请参阅 [服务更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>参与内容制作

若要参与 Azure SQL 文档制作，请参阅[文档参与者指南](https://docs.microsoft.com/contribute/)。
