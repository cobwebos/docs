---
title: 发行说明
description: 了解 Azure SQL 数据库服务和 Azure SQL 数据库文档中的新功能和改进
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: sstein
ms.openlocfilehash: 2d89320b4e5237017b51d19495c60c03ce6288f7
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838478"
---
# <a name="sql-database-release-notes"></a>SQL 数据库发行说明

本文列出了当前以公共预览版提供的 SQL 数据库功能。 有关 SQL 数据库的更新和改进，请参阅[Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。 有关对其他 Azure 服务的更新和改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="features-in-public-preview"></a>处于公共预览版的功能

### <a name="single-database"></a>[单一数据库](#tab/single-database)

| 功能 | 详细信息 |
| ---| --- |
| 新的 Fsv2 系列和 M 系列硬件代| 有关信息，请参阅[硬件代](sql-database-service-tiers-vcore.md#hardware-generations)。|
| 通过单一数据库和弹性池加速的数据库恢复 | 有关信息，请参阅[加速的数据库恢复](sql-database-accelerated-database-recovery.md)。|
|估计非重复计数|有关信息，请参阅[估计非重复计数](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)。|
|行存储上的批处理模式（在兼容性级别 150 下）|有关信息，请参阅[行存储上的批处理模式](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)。|
| 数据发现和分类  |有关信息，请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](sql-database-data-discovery-and-classification.md)。|
| 弹性数据库作业 | 有关信息，请参阅[创建、配置和管理弹性作业](elastic-jobs-overview.md)。 |
| 弹性查询 | 有关信息，请参阅[弹性查询概述](sql-database-elastic-query-overview.md)。 |
| 弹性事务 | [跨云数据库的分布式事务](sql-database-elastic-transactions-overview.md)。 |
|内存授予反馈（行模式）（在兼容性级别 150 下）|有关信息，请参阅[内存授予反馈（行模式）](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)。|
| Azure 门户中的查询编辑器 |有关信息，请参阅[使用 Azure 门户的 SQL 查询编辑器进行连接并查询数据](sql-database-connect-query-portal.md)。|
| 带有单一数据库和弹性池的 R services/机器学习 |有关信息，请参阅[AZURE SQL 数据库中的机器学习服务](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)。|
|SQL Analytics|有关信息，请参阅[Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)。|
|表变量延迟编译（在兼容性级别 150 下）|有关信息，请参阅[表变量延迟的编译](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)。|
| &nbsp; |

### <a name="managed-instance"></a>[托管实例](#tab/managed-instance)

| 功能 | 详细信息 |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">实例池</a> | 将较小的 SQL 实例迁移到云的方便且经济高效的方式。 |
| <a href="https://aka.ms/managed-instance-aadlogins">实例级 Azure AD 服务器主体（登录名）</a> | 使用 <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN FROM EXTERNAL PROVIDER</a> 语句创建服务器级登录名。 |
| [事务复制](sql-database-managed-instance-transactional-replication.md) | 将表中的更改复制到托管实例、单一数据库或 SQL Server 实例上放置的其他数据库中，或者在其他托管实例或 SQL Server 实例中的某些行发生更改时更新表。 有关信息，请参阅[在 Azure SQL 数据库托管实例数据库中配置复制](replication-with-sql-database-managed-instance.md)。 |
| 威胁检测 |有关信息，请参阅[在 Azure SQL 数据库托管实例中配置威胁检测](sql-database-managed-instance-threat-detection.md)。|
| 长期备份保留 | 有关详细信息，请参阅在[AZURE SQL 数据库托管实例中配置长期备份保留](sql-database-managed-instance-long-term-backup-retention-configure.md)，当前处于有限的公共预览版中。 | 

---

## <a name="managed-instance---new-features-and-known-issues"></a>托管实例 - 新功能和已知问题

### <a name="managed-instance-h2-2019-updates"></a>托管实例 H2 2019 更新

- [服务辅助子网配置](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/)：一种用来管理子网配置的安全而方便的方法。使用此方法，你可以控制数据流量，而托管实例可确保管理流量不断流
- [自带密钥 (BYOK) 的透明数据加密 (TDE)](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) 为静态数据保护实现了自带密钥 (BYOK) 方案，并使组织能够在密钥管理和数据管理方面实现职责分离。
- 使用[自动故障转移组](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/)可以将主实例中的所有数据库复制到另一个区域中的辅助实例。
- 使用[全局跟踪标志](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/)配置托管实例行为。

### <a name="managed-instance-h1-2019-updates"></a>托管实例 H1 2019 更新

2019 年上半年在托管实例部署模型中启用了以下功能：
  - 支持具有 <a href="https://aka.ms/sql-mi-visual-studio-subscribers">Visual Studio 订阅者的 Azure 每月额度</a>和增加的[区域限制](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)的订阅。
  - 支持 <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 和 SharePoint 2019 </a> 以及 <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - 使用所选<a href="https://aka.ms/managed-instance-collation">服务器级排序规则</a>和<a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">时区</a>创建实例。
  - 托管实例现在使用<a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">内置防火墙</a>进行保护。
  - 配置实例以使用[公共终结点](sql-database-managed-instance-public-endpoint-configure.md)、[代理覆盖](sql-database-connectivity-architecture.md#connection-policy)连接以获得更好的网络性能，<a href="https://aka.ms/four-cores-sql-mi-update">Gen5 硬件代次上有 4 个 vCore</a> 或<a href="https://aka.ms/managed-instance-configurable-backup-retention">将备份保留期配置为最多 35 天</a>以便进行时间点还原。 长期[备份保留期](sql-database-long-term-retention.md#managed-instance-support)（最多10年）目前处于有限的公共预览版中。  
  - 利用新功能，可以<a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">使用 PowerShell 将数据库异地还原到另一个数据中心</a>、[重命名数据库](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/)、[删除虚拟群集](sql-database-managed-instance-delete-virtual-cluster.md)。
  - 新的内置[实例参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor)使职责分离 (SoD) 遵从安全原则并符合企业标准。
  - 托管实例在以下 Azure 政府版区域中提供（US Gov 德克萨斯州、US Gov 亚利桑那州）以及中国北部2和中国东部2。 它还在以下公共区域中提供：澳大利亚中部、澳大利亚中部2、巴西南部、法国南部、阿拉伯联合酋长国中部、阿拉伯联合酋长国北部、南非北部、南非西北部。

### <a name="known-issues"></a>已知问题

|问题  |发现日期  |状态  |解决日期  |
|---------|---------|---------|---------|
|[在修改、禁用或启用现有作业时，代理无响应](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|可能为2020|自动缓解| |
|[资源组上的权限不应用于托管实例](#permissions-on-resource-group-not-applied-to-managed-instance)|2020 年 2 月|具有解决方法| |
|[通过门户对故障转移组进行手动故障转移的限制](#limitation-of-manual-failover-via-portal-for-failover-groups)|2020 年 1 月|具有解决方法| |
|[SQL 代理角色需要拥有对非 sysadmin 登录名的显式 EXECUTE 权限](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 12 月|具有解决方法| |
|[重启代理进程可能会中断 SQL 代理作业](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|2019 年 12 月|已解决|2020 年 3 月|
|[SSDT 不支持 AAD 登录名和用户](#aad-logins-and-users-are-not-supported-in-ssdt)|2019 年 11 月|无解决方法| |
|[内存中 OLTP 内存限制不适用](#in-memory-oltp-memory-limits-are-not-applied)|2019 年 10 月|具有解决方法| |
|[尝试删除不为空的文件时，返回了错误的错误](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|2019 年 10 月|具有解决方法| |
|[更改服务层级和创建实例的操作会被正在进行的数据库还原操作阻止](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|2019 年 9 月|具有解决方法| |
|[故障转移后，可能需要重新配置“业务关键”服务层级上的 Resource Governor](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|2019 年 9 月|具有解决方法| |
|[升级服务层级后必须重新初始化跨数据库 Service Broker 对话](#cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade)|2019 年 8 月|具有解决方法| |
|[不支持模拟 Azure AD 登录类型](#impersonification-of-azure-ad-login-types-is-not-supported)|2019 年 7 月|无解决方法| |
|[sp_send_db_mail 中不支持 @query 参数](#-parameter-not-supported-in-sp_send_db_mail)|2019 年 4 月|无解决方法| |
|[异地故障转移之后，必须重新配置事务复制](#transactional-replication-must-be-reconfigured-after-geo-failover)|2019 年 3 月|无解决方法| |
|[在还原操作过程中使用临时数据库](#temporary-database-is-used-during-restore-operation)||具有解决方法| |
|[将重新创建 TEMPDB 结构和内容](#tempdb-structure-and-content-is-re-created)| |无解决方法| |
|[小型数据库文件超出存储空间](#exceeding-storage-space-with-small-database-files)| |具有解决方法| |
|[显示 GUID 值而不是数据库名称](#guid-values-shown-instead-of-database-names) ||具有解决方法| |
|[不保留错误日志](#error-logs-arent-persisted)||无解决方法| |
|[CLR 模块和链接的服务器有时无法引用本地 IP 地址](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)| |具有解决方法| |
|[跨同一实例中的两个数据库的事务范围不受支持](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)| |已解决|2020 年 3 月|
|从 Azure Blob 存储还原数据库后未使用 DBCC CHECKDB 验证数据库一致性。| |已解决|2019 年 11 月|
|如果源数据库包含内存中 OLTP 对象，则从“业务关键”层级到“常规用途”层级的时间点数据库还原将不会成功。| |已解决|2019 年 10 月|
|使用具有安全连接的外部（非 Azure）邮件服务器时出现数据库邮件功能问题| |已解决|2019 年 10 月|
|托管实例不支持包含的数据库| |已解决|2019 年 8 月|

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>在修改、禁用或启用现有作业时，代理无响应

在某些情况下，修改现有作业、禁用或启用它可能会导致代理停止响应。 在检测导致重新启动代理进程时，会自动缓解此问题。

### <a name="permissions-on-resource-group-not-applied-to-managed-instance"></a>资源组上的权限不应用于托管实例

将托管实例参与者 RBAC 角色应用于资源组 (RG) 时，该角色不应用于托管实例，因此不起作用。

**解决方法**：在订阅级别为用户设置“托管实例参与者”角色。

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>通过门户对故障转移组进行手动故障转移的限制

如果故障转移组跨不同 Azure 订阅或资源组中的实例，则无法从故障转移组中的主实例启动手动故障转移。

**解决方法**：通过门户从异地辅助实例启动故障转移。

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL 代理角色需要拥有对非 sysadmin 登录名的显式 EXECUTE 权限

如果将非 sysadmin 登录名添加到任何 [SQL 代理固定数据库角色](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles)，则会出现以下问题：需要向主存储过程授予显式 EXECUTE 权限才能使这些登录名正常工作。 如果遇到此问题，将显示错误消息“在对象 <object_name> 中拒绝了 EXECUTE 权限(Microsoft SQL Server，错误:229)”。

**解决方法**：将登录名添加到任一 SQL 代理固定数据库角色之后：添加到这些角色的每个登录名的 SQLAgentUserRole、SQLAgentReaderRole 或 SQLAgentOperatorRole 将执行以下 T-SQL 脚本，以向列出的存储过程显式授予 EXECUTE 权限。

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

**（2020年3月解决）** SQL 代理在每次启动作业时都会创建一个新会话，这逐渐增加了内存消耗。 为了避免达到内部内存限制，从而阻止已计划作业的执行，一旦代理的内存消耗量达到阈值，就会重启代理进程。 这可能会中断重启时正在运行的作业的执行。

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>内存中 OLTP 内存限制不适用

在某些情况下，业务关键型服务层级不会正确应用[内存优化对象的最大内存限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)。 托管实例可以让工作负荷使用更多的内存进行内存中 OLTP 操作，这可能影响实例的可用性和稳定性。 达到限制的内存中 OLTP 查询可能不会立即失败。 此问题即将得到解决。 使用较多内存中 OLTP 内存的查询在达到[限制](sql-database-managed-instance-resource-limits.md#in-memory-oltp-available-space)的情况下会更快地失败。

**解决方法：** 使用 [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) [监视内存中 OLTP 存储使用情况](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)，确保工作负荷不会使用比提供的内存更多的内存。 提高基于 vCore 数的内存限制，或者优化工作负荷，让其使用较少的内存。

### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>尝试删除不为空的文件时，返回了错误的错误

SQL Server/托管实例[不允许用户删除不为空的文件](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites)。 如果尝试使用 `ALTER DATABASE REMOVE FILE` 语句删除非空数据文件，系统会立即返回 `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` 错误。 托管实例会继续尝试删除该文件，操作会在 30 分钟后失败并显示`Internal server error`。

**解决方法**：使用 `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` 命令删除文件的内容。 如果这是文件组中的唯一文件，则需从与此文件组关联的表或分区中删除数据，然后才能收缩文件并选择将该数据加载到另一表/分区中。

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>更改服务层级和创建实例的操作会被正在进行的数据库还原操作阻止

正在运行的 `RESTORE` 语句、数据迁移服务的迁移过程以及内置的时间点还原都会阻止对服务层的更新操作或者对现有实例的重设大小操作以及创建新实例的操作，直至还原过程完成为止。 还原过程会阻止其运行时所在的子网的托管实例和实例池中的这些操作。 实例池中的实例不受影响。 创建或更改服务层级的操作不会失败或超时 - 一旦还原过程完成或取消，它们就会继续。

**解决方法**：请等待还原过程完成，或者，如果创建或更新服务层级的操作的优先级更高，可取消还原过程。

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>故障转移后，可能需要重新配置“业务关键”服务层级上的 Resource Governor

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 功能可让你限制分配给用户工作负荷的资源。在故障转移或者完成用户发起的服务层级更改（例如，更改最大 vCore 数或最大实例存储大小）后，Resource Governor 可能会错误地分类某些用户工作负荷。

**解决方法**： `ALTER RESOURCE GOVERNOR RECONFIGURE`如果你使用的是[Resource Governor](/sql/relational-databases/resource-governor/resource-governor)，则会定期运行，或者作为在启动实例时执行 sql 任务的 sql 代理作业的一部分。

### <a name="cross-database-service-broker-dialogs-must-be-re-initialized-after-service-tier-upgrade"></a>升级服务层级后必须重新初始化跨数据库 Service Broker 对话

完成更改服务层级的操作后，跨数据库 Service Broker 对话会停止向其他数据库中的服务传递消息。 消息并未**丢失**，可在发送方队列中找到它们。 在托管实例中对 vCore 或实例存储大小进行任何更改都会导致 [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) 视图中所有数据库的 `service_broke_guid` 值发生更改。 使用 [BEGIN DIALOG](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) 语句创建的、引用其他数据库中的 Service Broker 的任何 `DIALOG` 将停止向目标服务传递消息。

**解决方法：** 先停止使用跨数据库 Service Broker 对话的任何活动，再更新服务层级，然后重新初始化这些活动。 如果在更改服务层级后存在任何未传递的剩余消息，请从源队列中读取消息，然后将其重新发送到目标队列。

### <a name="impersonification-of-azure-ad-login-types-is-not-supported"></a>不支持模拟 Azure AD 登录类型

不支持使用以下 AAD 主体的 `EXECUTE AS USER` 或 `EXECUTE AS LOGIN` 进行模拟：
-    带别名的 AAD 的用户。 在这种情况下，将返回以下错误：`15517`。
- 基于 AAD 应用程序或服务主体的 AAD 登录名和用户。 在这种情况下，将返回以下错误：`15517` 和 `15406`。

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>sp_send_db_mail 中不支持 @query 参数

[sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 过程中的 `@query` 参数不起作用。

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>异地故障转移之后，必须重新配置事务复制

如果对自动故障转移组中的数据库启用了事务复制，则托管实例管理员必须清理旧的主节点上的所有发布内容，然后在故障转移到另一个区域后，在新的主节点上重新配置这些发布内容。 有关更多详细信息，请参阅[复制](sql-database-managed-instance-transact-sql-information.md#replication)。

### <a name="aad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 不支持 AAD 登录名和用户

SQL Server Data Tools 不完全支持 Azure Active Directory 登录名和用户。

### <a name="temporary-database-is-used-during-restore-operation"></a>在还原操作过程中使用临时数据库

在托管实例上还原某个数据库时，还原服务首先会使用所需的名称创建一个空数据库，以便在实例上分配该名称。 一段时间后，将会删除此数据库，并启动实际数据库的还原。 处于“正在还原”状态的数据库将临时使用随机 GUID 值而不是名称。  还原过程完成后，临时名称将更改为 `RESTORE` 语句中指定的所需名称。 在初始阶段，用户可以访问空数据库，甚至可以在此数据库中创建表或加载数据。 当还原服务启动第二个阶段时，将删除此临时数据库。

**解决方法**：在看到还原完成之前，请不要访问正在还原的数据库。

### <a name="tempdb-structure-and-content-is-re-created"></a>将重新创建 TEMPDB 结构和内容

`tempdb` 数据库始终拆分为 12 个数据文件，文件结构不可更改。 无法更改每个文件的最大大小，并且无法将新文件添加到 `tempdb`。 实例启动或故障转移时，`Tempdb` 始终会重新创建为空数据库；在 `tempdb` 中所做的任何更改不会保留。

### <a name="exceeding-storage-space-with-small-database-files"></a>小型数据库文件超出存储空间

`CREATE DATABASE`、`ALTER DATABASE ADD FILE` 和 `RESTORE DATABASE` 语句可能会失败，因为实例可能会达到 Azure 存储限制。

每个“常规用途”托管实例都为 Azure 高级磁盘空间保留了最多 35 TB 存储空间。 每个数据库文件放置在单独的物理磁盘上。 磁盘大小可以为 128 GB、256 GB、512 GB、1 TB 或 4 TB。 磁盘上未使用的空间不收费，但 Azure 高级磁盘大小总计不能超过 35 TB。 在某些情况下，由于内部碎片，总共不需要 8 TB 的托管实例可能会超过 35 TB 的 Azure 存储大小限制。

例如，“常规用途”托管实例可将一个大小为 1.2 TB 的大文件放在 4 TB 的磁盘上。 它还可以将 248 个文件（每个大小为 1 GB）放在单独的 128 GB 磁盘上。 在本示例中：

- 分配的磁盘存储总大小为 1 x 4 TB + 248 x 128 GB = 35 TB。
- 实例上的数据库的总预留空间为 1 x 1.2 TB + 248 x 1 GB = 1.4 TB。

此示例演示在某些情况下，由于文件的具体分布，托管实例可能会出乎意料地达到为附加的 Azure 高级磁盘预留的 35 TB 存储空间大小限制。

在此示例中，只要未添加新文件，现有数据库就会继续工作并且可以毫无问题地增长。 由于没有足够的空间用于新磁盘驱动器，因此无法创建或还原新数据库，即使所有数据库的总大小未达到实例大小限制也是如此。 这种情况下返回的错误并不明确。

可以使用系统视图[识别剩余文件的数目](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1)。 如果即将达到此限制，请尝试[使用 DBCC SHRINKFILE 语句清空并删除一些小型文件](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file)，或者切换到[没有此限制的“业务关键”层级](/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)。

### <a name="guid-values-shown-instead-of-database-names"></a>显示 GUID 值而不是数据库名称

多个系统视图、性能计数器、错误消息、XEvent 和错误日志条目显示了 GUID 数据库标识符而非实际的数据库名称。 不要依赖这些 GUID 标识符，因为它们在将来会被替换为实际的数据库名称。

**解决方法**：使用 sys.databases 视图通过物理数据库名称（以 GUID 数据库标识符的形式指定）解析实际数据库名称。

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>不保留错误日志

托管实例中可用的错误日志不会持久保留，并且它们的大小不包括在最大存储限制中。 在发生故障转移时可能会自动清除错误日志。 错误日志历史记录中可能存在差距，因为托管实例在多个虚拟机上转移了多次。

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>跨同一实例中的两个数据库的事务范围不受支持

**（2020年3月解决）** 如果`TransactionScope`两个查询发送到相同事务范围下同一实例中的两个数据库，则 .net 中的类不起作用：

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

**解决方法（自2020年3月起不需要）：** 使用[ChangeDatabase （String）](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)在连接上下文中使用其他数据库，而不是使用两个连接。

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR 模块和链接的服务器有时无法引用本地 IP 地址

放置在托管实例中的 CLR 模块，以及引用当前实例的链接服务器或分布式查询，有时可能无法解析本地实例的 IP。 此错误是暂时性问题。

**解决方法：** 如果可能，请在 CLR 模块中使用上下文连接。

## <a name="updates"></a>更新

有关 SQL 数据库更新和改进的列表，请参阅[Sql 数据库服务更新](https://azure.microsoft.com/updates/?product=sql-database)。

有关所有 Azure 服务的更新和改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="contribute-to-content"></a>参与内容制作

若要参与 Azure SQL 数据库文档制作，请参阅[文档参与者指南](https://docs.microsoft.com/contribute/)。
