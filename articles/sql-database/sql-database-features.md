---
title: Azure SQL 数据库功能比较 | Microsoft Docs
description: 本文比较了不同风格的 Azure SQL 数据库中可用的 SQL Server 功能。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, sstein
ms.date: 05/10/2019
ms.openlocfilehash: a8d36e48558432edfaa242b9db13c59adacf5619
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876358"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>功能比较：Azure SQL 数据库与 SQL Server

Azure SQL 数据库与 SQL Server 共享一个通用代码库。 Azure SQL 数据库支持的 SQL Server 功能取决于创建的 Azure SQL 数据库类型。 使用 Azure SQL 数据库，可以作为[托管实例](sql-database-managed-instance.md)、单一数据库或弹性池的一部分创建一个数据库。

Microsoft 会继续向 Azure SQL 数据库添加功能。 访问针对 Azure 的服务更新网页，并使用以下筛选器获取最新更新：

- 筛选为 [SQL 数据库服务](https://azure.microsoft.com/updates/?service=sql-database)。
- 筛选为针对 SQL 数据库功能的正式发布版本 [(GA) 公告](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

如果需要更多有关差异的详细信息, 可以在[单个数据库和弹性池](sql-database-transact-sql-information.md)的单独页面或[托管实例](sql-database-managed-instance-transact-sql-information.md)中找到它们。

## <a name="sql-features"></a>SQL 功能

下表列出了 SQL Server 的主要功能, 并提供了托管实例或单一数据库和弹性池中是否部分或完全支持该功能的相关信息, 并提供了指向有关此功能的详细信息的链接。

| **SQL 功能** | **单一数据库和弹性池** | **托管实例** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md) | 是 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 | 每个数据库都包含[高可用性](sql-database-high-availability.md), 并且[不能由用户进行管理](sql-database-managed-instance-transact-sql-information.md#always-on-availability)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [附加数据库](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [应用程序角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 是 | 是 |
| [审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)| [是](sql-database-managed-instance-auditing.md), 但有一些[差异](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [自动备份](sql-database-automated-backups.md) | 是的。 完整备份每 7 天一次，差异备份每 12 小时一次，日志备份每 5-10 分钟一次。 | 是的。 完整备份每 7 天一次，差异备份每 12 小时一次，日志备份每 5-10 分钟一次。 |
| [自动优化（强制执行计划）](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)| [是](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [自动优化（索引）](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)| 否 |
| [备份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否，仅系统启动的自动备份 - 请参阅[自动备份](sql-database-automated-backups.md) | 是, 用户启动的仅复制备份到 Azure Blob 存储 (用户无法启动自动系统备份)-请参阅[备份差异](sql-database-managed-instance-transact-sql-information.md#backup) |
| [内置函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) | 
| [BULK INSERT 语句](https://docs.microsoft.com/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server) | 是，但是只能从充当源的 Azure Blob 存储进行。 | 是的, 但只需从 Azure Blob 存储作为源-请参阅[不同之处](sql-database-managed-instance-transact-sql-information.md#bulk-insert--openrowset)。 |
| [证书和非对称密钥](https://docs.microsoft.com/sql/relational-databases/security/sql-server-certificates-and-asymmetric-keys) | 是，无法访问文件系统完成 `BACKUP` 和 `CREATE` 操作。 | 是, 无需访问文件系统进行`BACKUP`和`CREATE`操作-请参阅[证书差异](sql-database-managed-instance-transact-sql-information.md#certificates)。 | 
| [变更数据捕获-CDC](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | 是 |
| [更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 是 |是 |
| [排序规则 - 数据库](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | 是 | 是 |
| [排序规则 - 服务器/实例](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | 不能, 始终使用默认`SQL_Latin1_General_CP1_CI_AS`的逻辑服务器排序规则。 | 是, 可以在[创建实例](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)时设置, 稍后不能更新。 |
| [列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [高级层、标准层 - S3 及以上层、常规用途层和业务关键层](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |是 |
| [公共语言运行时-CLR](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是, 但在语句中`CREATE ASSEMBLY`没有对文件系统的访问权限-请参阅[CLR 差异](sql-database-managed-instance-transact-sql-information.md#clr) |
| [包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 是 | 是 |
| [包含的用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 是 | 是 |
| [流控制语言关键字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 是 | 是 |
| [凭据](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/credentials-database-engine) | 是，但是仅限[数据库范围的凭据](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 | 是, 但仅支持 Azure Key Vault `SHARED ACCESS SIGNATURE`和, 请参阅[详细信息](sql-database-managed-instance-transact-sql-information.md#credential) |
| [跨数据库/三部分名称查询](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-overview.md) | 是，外加[弹性查询](sql-database-elastic-query-overview.md) |
| [跨数据库事务](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | 是, 在实例中。 有关跨实例查询, 请参阅[链接服务器差异](sql-database-managed-instance-transact-sql-information.md#linked-servers)。 |
| [游标](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 是 |是 |
| [Data compression](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)（数据压缩） | 是 |是 |
| [数据库邮件-DbMail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 | 是 |
| [数据库镜像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | [否](sql-database-managed-instance-transact-sql-information.md#database-mirroring) |
| [数据库配置设置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 是 | 是 |
| [数据库快照](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [数据类型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 是 |是 |
| [DBCC 语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [DBCC 差异](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL 语句](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [DDL 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 仅数据库 |  是 |
| [分布式分区视图](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | 是 |
| [分布式事务 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 请参阅[弹性事务](sql-database-elastic-transactions-overview.md) |  否-请参阅[链接服务器差异](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML 语句](https://docs.microsoft.com/sql/t-sql/queries/queries) | 是 | 是 |
| [DML 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 大多数 - 请参阅单个语句 |  是 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大多数 - 请参阅单个 DMV |  是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[是](sql-database-dynamic-data-masking-get-started.md)| [是](sql-database-dynamic-data-masking-get-started.md) |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 请参阅[警报](sql-database-insights-alerts-portal.md) | 否 |
| [表达式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 | 是 |
| [扩展事件 (XEvent)](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分 - 请参阅 [SQL 数据库中的扩展事件](sql-database-xevent-db-diff-from-svr.md) | 是 - 请参阅[扩展事件的差异](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [扩展的存储过程](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
| [文件和文件组](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 仅限主文件组 | 是的。 文件路径是自动分配的, 不能在`ALTER DATABASE ADD FILE` [语句](sql-database-managed-instance-transact-sql-information.md#alter-database-statement)中指定文件位置。  |
| [文件流](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 | [否](sql-database-managed-instance-transact-sql-information.md#filestream-and-filetable) |
| [全文搜索 (FT 数)](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  是，但不支持第三方断字符 | 是, 但[不支持第三方断字符](sql-database-managed-instance-transact-sql-information.md#full-text-semantic-search) |
| [函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [图形处理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | 是 | 是 |
| [内存中优化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是-[高级和业务关键层仅](sql-database-in-memory.md)对非持久性内存中对象 (如表类型) 的支持有限 | 是 - [仅限业务关键层](sql-database-managed-instance.md) |
| [JSON 数据支持](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [是](sql-database-json-features.md) | [是](sql-database-json-features.md) |
| [语言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大多数 - 请参阅单个元素 |  是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [链接服务器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-horizontal-partitioning.md) | 是的。 仅适用于没有分布式事务的[SQL Server 和 SQL 数据库](sql-database-managed-instance-transact-sql-information.md#linked-servers)。 |
| 从文件 (CSV, Excel) 读取的[链接服务器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine)| 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)作为 CSV 格式的替代方法。 | 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)作为 CSV 格式的替代方法。 在[托管实例反馈项](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)上跟踪此请求|
| [日志传送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 | 作为 DMS 迁移过程的一部分内置内置。 不可用作高可用性解决方案, 因为每个数据库都附带了其他[高可用性](sql-database-high-availability.md)方法, 因此不建议使用日志传送作为 HA 替代方法。 灾难恢复在[AZURE SQL 数据库的业务连续性概述](sql-database-business-continuity.md)中进行了介绍。 不能作为数据库之间的复制机制, 使用[业务关键层](sql-database-service-tier-business-critical.md)上的辅助副本、[自动故障转移组](sql-database-auto-failover-group.md)或[事务复制](sql-database-managed-instance-transactional-replication.md)作为替代项。 |
| [登录名和用户](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) | 是，但是 `CREATE` 和 `ALTER` 登录语句不提供所有选项（没有 Windows 和服务器级别 Azure Active Directory 登录名）。 `EXECUTE AS LOGIN`不支持-改为`EXECUTE AS USER`使用。  | 是, 但有一些[差异](sql-database-managed-instance-transact-sql-information.md#logins-and-users)。 不支持 Windows 登录名, 并且应将其替换为 Azure Active Directory 登录名。 |
| [批量导入中的最小日志记录](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否, 仅支持完整恢复模式。 | 否, 仅支持完整恢复模式。 |
| [修改系统数据](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 | 是 |
| [OLE 自动化](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | 否 | 否 |
| [联机索引操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 是 | 是 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|否|是, 仅适用于其他 Azure SQL 数据库和 SQL Server。 请参阅[t-sql 差异](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|是|是|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|否|是, 仅适用于其他 Azure SQL 数据库和 SQL Server。 请参阅[t-sql 差异](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|是，只是为了从 Azure Blob 存储导入。 |是, 仅适用于其他 Azure SQL 数据库和 SQL Server, 并从 Azure Blob 存储中导入。 请参阅[t-sql 差异](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|是|是|
| [运算符](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大多数 - 请参阅单个运算符 |是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [分区](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 是 | 是 |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否。 可以使用`OPENROWSET`函数查询放置在 Azure Blob 存储上的文件中的数据。 | 否。 可以使用`OPENROWSET`函数查询放置在 Azure Blob 存储上的文件中的数据。 |
| [Predicates](https://docs.microsoft.com/sql/t-sql/queries/predicates)（谓词） | 是 | 是 |
| [查询通知](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | 否 | 是 |
| [R 服务](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 是，[处于公开预览状态](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 否 |
| [恢复模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | 仅支持保证高可用性的完全恢复。 简单和大容量日志恢复模式不可用。 | 仅支持保证高可用性的完全恢复。 简单和大容量日志恢复模式不可用。 | 
| [资源调控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 | 是 |
| [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是, 对于放置`FROM URL`在 Azure Blob 存储上的备份文件, 有必需的选项。 请参阅[还原差异](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [从备份还原数据库](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 仅从自动备份 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md) | 从自动备份-请参阅[SQL 数据库恢复](sql-database-recovery-using-backups.md)和来自 Azure Blob 存储的完整备份-请参阅[备份差异](sql-database-managed-instance-transact-sql-information.md#backup) |
| [将数据库还原到 SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 否。 使用 BACPAC 或 BCP 而不是本机还原。 | 不能, 因为托管实例中使用的 SQL Server 数据库引擎的版本高于在本地使用的任何 SQL Server RTM 版本。 请改用 BACPAC、BCP 或事务复制。 |
| [行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 是 | 是 |
| [语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [序列号](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 是 | 是 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是, 但仅在实例内。 请参阅[Service Broker 差异](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [服务器配置设置](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [SET 语句](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)|
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 是 | 是 |
| [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 请参阅[弹性作业](elastic-jobs-overview.md) | 是 - 请参阅 [SQL Server 代理差异](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 请参阅 [SQL 数据库审核](sql-database-auditing.md) | 是 - 请参阅[审核差异](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 是 | 是 |
| [系统存储函数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [系统存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 请参阅单个存储过程 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-and-triggers) |
| [系统表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 请参阅单个表 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [系统目录视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 请参阅单个视图 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [TempDB](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database) | 是的。 [每个数据库的32GB 大小 (每个核心](sql-database-vcore-resource-limits-single-databases.md))。 | 是的。 [整个 GP 层的每个 vCore 的24GB 大小, 并受 BC 层上的实例大小限制](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)  |
| [临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本地和数据库范围的全局临时表 | 本地和实例范围的全局临时表 |
| [临时表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [是](sql-database-temporal-tables.md) | [是](sql-database-temporal-tables.md) |
| 时区选择 | 否 | [是](sql-database-managed-instance-timezone.md)的, 必须在创建托管实例时配置。 |
| 威胁检测|  [是](sql-database-threat-detection.md)|[是](sql-database-managed-instance-threat-detection.md)|
| [跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 否 | 是, 但仅限有限的全局跟踪标志集。 请参阅[DBCC 差异](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [事务复制](sql-database-managed-instance-transactional-replication.md) | 是,[仅限事务复制和快照复制订阅服务器](sql-database-single-database-migrate.md) | 是, 在[公共预览版](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)中。 请参阅[此处](sql-database-managed-instance-transact-sql-information.md#replication)的限制。 |
| [变量](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 是 | 是 |
| [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 - 仅限“常规用途”和“业务关键”服务层级| [是](transparent-data-encryption-azure-sql.md) |
| [Windows Server 故障转移群集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 否。 每个数据库都附带了提供[高可用性](sql-database-high-availability.md)的其他技术。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 | 否。 每个数据库都附带了提供[高可用性](sql-database-high-availability.md)的其他技术。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 是 | 是 |

## <a name="platform-capabilities"></a>平台功能

Azure 平台提供了许多 PaaS 功能, 这些功能添加为标准数据库功能的附加值。 有许多可用于 Azure SQL 数据库服务的外部服务。 

| **平台功能** | **单一数据库和弹性池** | **托管实例** |
| --- | --- | --- |
| [活动异地复制](sql-database-active-geo-replication.md) | 是 - 除超大规模之外的所有服务层级 | 否, 请参阅[自动故障转移组 (预览)](sql-database-auto-failover-group.md)作为替代方法 |
| [自动故障转移组](sql-database-auto-failover-group.md) | 是 - 除超大规模之外的所有服务层级 | 是，[处于公开预览状态](sql-database-auto-failover-group.md)|
| 自动缩放 | 是, 在[无服务器模型](sql-database-serverless.md)中 | 不需要, 你需要选择 "保留计算和存储"。 |
| [Azure Active Directory (AAD) 身份验证](sql-database-aad-authentication.md) | 是的。 仅限 AAD 用户。 | 是的。 包括服务器级别 AAD 登录名。 |
| [Azure 资源运行状况](/azure/service-health/resource-health-overview) | 是 | 否 |
| 备份保留 | 是的。 默认值为7天, 最长35天。 | 是的。 默认值为7天, 最长35天。 |
| [数据迁移服务 (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | 是 | 是 |
| 文件系统访问 | 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage)作为替代方法, 访问和加载 Azure Blob 存储中的数据。 | 否。 使用[BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)或[OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql#i-accessing-data-from-a-file-stored-on-azure-blob-storage)作为替代方法, 访问和加载 Azure Blob 存储中的数据。 |
| [异地还原](sql-database-recovery-using-backups.md#geo-restore) | 是 - 除超大规模之外的所有服务层级 | 是-使用[Azure PowerShell](https://medium.com/azure-sqldb-managed-instance/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa)。 |
| [超大规模体系结构](sql-database-service-tier-hyperscale.md) | 是 | 否 |
| [长期备份保留-从左向右](sql-database-long-term-retention.md) | 是, 保持自动备份最多10年。 | 还不可以。 使用`COPY_ONLY` [手动备份](sql-database-managed-instance-transact-sql-information.md#backup)作为临时解决方法。 |
| 暂停/继续 | 是, 在[无服务器模型](sql-database-serverless.md)中 | 否 | 
| [基于策略的管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| 公用 IP 地址 | 是的。 访问权限可以使用防火墙或服务终结点来限制。  | 是的。 需要显式启用端口 3342, 且必须在 NSG 规则中启用。 如果需要, 可以禁用公共 IP。 有关更多详细信息, 请参阅[公共终结点](sql-database-managed-instance-public-endpoint-securely.md)。 | 
| [数据库时间点还原](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是-除超大规模以外的所有服务层-请参阅[SQL 数据库恢复](sql-database-recovery-using-backups.md#point-in-time-restore) | 是 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md#point-in-time-restore) |
| 资源池 | 是, 作为[弹性池](sql-database-elastic-pool.md) | 否。 单个托管 mnstance 可以有多个共享同一资源池的数据库。 托管实例不能共享资源。 |
| 增加或减少 (联机) | 是的, 你可以更改 DTU 或保留的 Vcore 或最大停机时间, 但停机时间最短。 | 是的, 你可以更改保留的 Vcore 或最大的存储, 但停机时间最短。 |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | 是 | 是 |
| [SQL 数据同步](sql-database-get-started-sql-data-sync.md) | 是 | 否 |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 否，[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 是一项单独的 Azure 云服务。 | 否，[Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) 是一项单独的 Azure 云服务。 |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 是，使用 Azure 数据工厂 (ADF) 环境中的托管 SSIS ，其中程序包存储在由 Azure SQL 数据库承载的 SSISDB 中并在 Azure SSIS 集成运行时 (IR) 上执行，请参阅[在 ADF 中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 <br/><br/>若要比较 SQL 数据库服务器和托管实例中的 SSIS 功能，请参阅[比较 Azure SQL 数据库单一数据库/弹性池和托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 | 是，使用 Azure 数据工厂 (ADF) 环境中的托管 SSIS ，其中程序包存储在由托管实例承载的 SSISDB 中并在 Azure SSIS 集成运行时 (IR) 上执行，请参阅[在 ADF 中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 <br/><br/>若要比较 SQL 数据库和托管实例中的 SSIS 功能，请参阅[比较 Azure SQL 数据库单一数据库/弹性池和托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 请参阅 [Power BI](https://docs.microsoft.com/power-bi/) | 否 - 请参阅 [Power BI](https://docs.microsoft.com/power-bi/) |
| [查询性能见解 (QPI)](sql-database-query-performance.md) | 是 | 否。 使用 SQL Server Management Studio 和 Azure Data Studio 中的内置报表。 |
| [VNet](../virtual-network/virtual-networks-overview.md) | Partial, 它使用[VNet 终结点](sql-database-vnet-service-endpoint-rule-overview.md)启用受限访问 | 是的, 托管实例注入到客户的 VNet 中。 请参阅[子网](sql-database-managed-instance-transact-sql-information.md#subnet)和[VNet](sql-database-managed-instance-transact-sql-information.md#vnet) |
| VNet 服务终结点 | [是](sql-database-vnet-service-endpoint-rule-overview.md) | 否 |

## <a name="tools"></a>工具
Azure SQL 数据库支持可帮助你管理数据的各种数据工具。

| **工具** | **单一数据库和弹性池** | **托管实例** |
| --- | --- | --- |
| Azure 门户 | 是 | 是 |
| Azure CLI | 是 | 是|
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | 是 | 是 |
| Azure Powershell | 是 | 是 |
| [BACPAC 文件（导出）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 请参阅 [SQL 数据库导出](sql-database-export.md) | 是 - 请参阅 [SQL 数据库导出](sql-database-export.md) |
| [BACPAC 文件（导入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 请参阅 [SQL 数据库导入](sql-database-import.md) | 是 - 请参阅 [SQL 数据库导入](sql-database-import.md) |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [是](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) | 是[版本 150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 | 是 |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 是 | 是[版本18.0 和更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 请参阅[扩展事件](sql-database-xevent-db-diff-from-svr.md) | 是 |
| [System Center Operations Manager-SCOM](https://docs.microsoft.com/system-center/scom/welcome) | [是](https://www.microsoft.com/download/details.aspx?id=38829) | 否 |

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
- 有关托管实例的信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
