---
title: "Azure SQL 数据库功能概述 | Microsoft 文档"
description: "此页面概述了 Azure SQL 数据库逻辑服务器和数据库，并且包括了功能支持矩阵和所列出的每项功能的链接。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Azure SQL 数据库功能

下面的表列出了 Azure SQL 数据库的主要功能以及 SQL Server 的等效功能，并说明了每个特定的功能是否受支持，同时提供了一个更详细介绍每个平台的该功能的链接。 若要了解在迁移现有数据库解决方案时需考虑哪些 Transact-SQL 差异，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](sql-database-transact-sql-information.md)。

我们会继续向 Azure SQL 数据库添加功能。 因此，我们鼓励访问我们的针对 Azure 的服务更新网页并使用其筛选器：

* 筛选为 [SQL 数据库服务](https://azure.microsoft.com/updates/?service=sql-database)。
* 筛选为针对 SQL 数据库功能的正式发布版本 [(GA) 公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

| **功能** | **SQL Server** | **Azure SQL 数据库** | 
| --- | :---: | :---: | 
| 活动异地复制 | 不支持 - 请参阅 [AlwaysOn 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [支持](sql-database-geo-replication-overview.md)
| 始终加密 | [支持](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 支持 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md)|
| AlwaysOn 可用性组 | [支持](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 不支持 - 请参阅[活动异地复制](sql-database-geo-replication-overview.md) |
| 附加数据库 | [支持](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 不支持 |
| 应用程序角色 | [支持](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [支持](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| 自动缩放 | 不支持 | 支持 - 请参阅[服务层](sql-database-service-tiers.md) |
| Azure Active Directory | 不支持 | [支持](sql-database-aad-authentication.md) |
| Azure 数据工厂 | [支持](../data-factory/data-factory-introduction.md) | [支持](../data-factory/data-factory-introduction.md) |
| 审核 | [支持](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [支持](sql-database-auditing.md) |
| BACPAC 文件（导出） | [支持](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [支持](sql-database-export.md) |
| BACPAC 文件（导入） | [支持](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [支持](sql-database-import.md) |
| 备份 | [支持](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 不支持 |
| 内置函数 | [支持](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅[单个函数] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| 更改数据捕获 | [支持](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 不支持 |
| 更改跟踪 | [支持](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [支持](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| 排序规则语句 | [支持](https://docs.microsoft.com/sql/t-sql/statements/collations) | [支持](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| 列存储索引 | [支持](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [仅限 Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| 公共语言运行时 (CLR) | [支持](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 不支持 |
| 包含的数据库 | [支持](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [支持](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| 包含的用户 | [支持](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [支持](sql-database-manage-logins.md#non-administrator-users) |
| 流控制语言关键字 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| 跨数据库查询 | [支持](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 部分 - 请参阅[弹性查询](sql-database-elastic-query-overview.md) |
| 游标 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| 数据压缩 | [支持](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [支持](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| 数据库备份 | [用户管理](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [SQL 数据库服务管理](sql-database-automated-backups.md) |
| 数据库邮件 | [支持](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 不支持 |
| 数据库镜像 | [支持](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 不支持 |
| 数据库配置设置 | [支持](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [支持](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 不支持 |
| 数据库快照 | [支持](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 不支持 |
| 数据类型 | [支持](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| DBCC 语句 | [支持](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大多数 - 请参阅[单个语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| DDL 语句 | [支持](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大多数 - 请参阅[单个语句](https://docs.microsoft.com/sql/t-sql/statements/statements)
| DDL 触发器 | [支持](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [仅限数据库](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| 分布式事务 | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 仅限有限的 SQL 数据库内方案 |
| DML 语句 | [支持](https://docs.microsoft.com/sql/t-sql/queries/queries) | 大多数 - 请参阅[单个语句](https://docs.microsoft.com/sql/t-sql/queries/queries) |
| DML 触发器 | [支持](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [支持](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV | [全部](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 部分 - 请参阅[单个 DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| 弹性池 | 不支持 | [支持](sql-database-elastic-pool.md) |
| 弹性作业 | 不支持 - 请参阅 [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [支持](sql-database-elastic-jobs-getting-started.md) | 
| 弹性查询 | 不支持 - 请参阅[跨数据库查询](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [支持](sql-database-elastic-query-overview.md) |
| 事件通知 | [支持](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [支持](sql-database-insights-alerts-portal.md) |
| 表达式 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| 扩展的事件 | [支持](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分 - 请参阅[单个事件](sql-database-xevent-db-diff-from-svr.md) |
| 扩展的存储过程 | [支持](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 不支持 |
| 文件和文件组 | [支持](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [仅限主文件组](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| 文件流 | [支持](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 不支持 |
| 全文搜索 | [支持](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [不支持第三方断字符](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| 函数 | [支持](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅[单个函数](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| 内存中优化 | [支持](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [仅限 Premium Edition](sql-database-in-memory.md) |
| 作业 | 请参阅 [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)（SQL Server 代理） | 请参阅[弹性作业](sql-database-elastic-jobs-getting-started.md) |
| JSON 数据支持 | [支持](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [支持](sql-database-json-features.md) |
| 语言元素 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大多数 - 请参阅[单个元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| 链接的服务器 | [支持](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 不支持 - 请参阅[弹性查询](sql-database-elastic-query-horizontal-partitioning.md) |
| 日志传送 | [支持](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 不支持 - 请参阅[活动异地复制](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [支持](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 不支持 |
| 批量导入中的最小日志记录 | [支持](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 不支持 |
| 修改系统数据 | [支持](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 不支持 |
| 联机索引操作 | [支持](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [支持 - 按服务层限制的事务大小](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| 运算符 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大多数 - 请参阅[单个运算符](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| 数据库时间点还原 | [支持](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [支持](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [支持](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 不支持
| 基于策略的管理 | [支持](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 不支持 |
| 谓词 | [支持](https://docs.microsoft.com/sql/t-sql/queries/predicates) | 大多数 - 请参阅[单个谓词](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| R 服务 | [支持](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| 资源调控器 | [支持](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 不支持 |
| RESTORE 语句 | [支持](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 不支持 | 
| 从备份还原数据库 | [支持](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [仅限从内置备份还原](sql-database-recovery-using-backups.md) |
| 行级安全性 | [支持](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [支持](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| 语义搜索 | [支持](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 不支持 |
| 序列号 | [支持](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [支持](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Service Broker | [支持](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 不支持 |
| 服务器配置设置 | [支持](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 不支持 - 请参阅[数据库配置选项](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| SET 语句 | [支持](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大多数 - 请参阅[单个语句](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| 空间 | [支持](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [支持](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server 代理 | [支持](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 不支持 - 请参阅[弹性作业](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [支持](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 不支持 - 请参阅 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [支持](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 不支持 - 请参阅 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [支持](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [支持](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [支持](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 不支持 - 请参阅[扩展的事件](sql-database-xevent-db-diff-from-svr.md) |
| SQL Server 复制 | [支持](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [仅限事务性和快照复制订户](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [支持](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 不支持 |
| 存储过程 | [支持](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [支持](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| 系统存储函数 | [支持](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 部分 - 请参阅[单个函数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| 系统存储过程 | [支持](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 请参阅[单个存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| 系统表 | [支持](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 请参阅[单个表](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| 系统目录视图 | [支持](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 请参阅[单个视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| 表分区 | [支持](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 支持 - [仅限主文件组](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| 临时表 | [本地和全局](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [仅限本地](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| 临时表 | [支持](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [支持](sql-database-temporal-tables.md) |
| 事务 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| 变量 | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| 透明数据加密 (TDE)  | [支持](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [支持](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server 故障转移群集 | [支持](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 不支持 - 请参阅[活动异地复制](sql-database-geo-replication-overview.md) |
| XML 索引 | [支持](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [支持](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
- 有关 Transact-SQL 支持和差异的信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](sql-database-transact-sql-information.md)。

