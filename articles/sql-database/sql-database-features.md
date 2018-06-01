---
title: Azure SQL 数据库功能比较 | Microsoft Docs
description: 本文将 Azure SQL 数据库和托管实例相互之间以及与 SQL Server 之间的功能进行了比较。
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: article
ms.date: 05/15/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 8144f70737e5f78f568a8a6dd35ddc7d43ced0fd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210049"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>功能比较：Azure SQL 数据库与 SQL Server 

Azure SQL 数据库与 SQL Server 共享一个通用代码库。 Azure SQL 数据库支持的 SQL Server 功能取决于创建的 Azure SQL 数据库类型。 使用 Azure SQL 数据库，可以创建数据库作为[托管实例](sql-database-managed-instance.md)（目前处于公开预览状态）的一部分，也可以创建单一数据库或附属于弹性池的数据库。 

Microsoft 会继续向 Azure SQL 数据库添加功能。 访问针对 Azure 的服务更新网页，并使用以下筛选器获取最新更新：

* 筛选为 [SQL 数据库服务](https://azure.microsoft.com/updates/?service=sql-database)。
* 筛选为针对 SQL 数据库功能的正式发布版本 [(GA) 公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server 和 SQL 数据库功能支持

下表列出了 SQL Server 的主要功能，并说明该功能是否部分或完全受支持，同时提供更详细介绍该功能的链接。 

| **SQL 功能** | **在 Azure SQL 数据库中受支持** | **托管实例（预览版）** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md) | 是 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md) |
| [AlwaysOn 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [附加数据库](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 | 否 |
| [应用程序角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 是 | 是 |
|[审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [是](sql-database-auditing.md)| [是](sql-database-managed-instance-auditing.md) |
| [自动备份](sql-database-automated-backups.md) | 是 | 是 |
| [自动优化（强制执行计划）](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)| [是](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [自动优化（索引）](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [是](sql-database-automatic-tuning.md)| 否 |
| [BACPAC 文件（导出）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 请参阅 [SQL 数据库导出](sql-database-export.md) | 是 |
| [BACPAC 文件（导入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 请参阅 [SQL 数据库导入](sql-database-import.md) | 是 |
| [备份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否，仅系统启动的自动备份 - 请参阅[自动备份](sql-database-automated-backups.md) | 系统启动的自动备份和用户启动的仅复制备份 - 请参阅[备份差异](sql-database-managed-instance-transact-sql-information.md#backup) |
| [内置函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [更改数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 | 是 |
| [更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 是 |是 |
| [排序规则语句](https://docs.microsoft.com/sql/t-sql/statements/collations) | 是 | 是 |
| [列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [高级层、标准层 - S3 及以上层、常规用途层和业务关键层](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |是 |
| [公共语言运行时 (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 | 是 - 请参阅 [CLR 差异](sql-database-managed-instance-transact-sql-information.md#clr) |
| [包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 是 | 是 |
| [包含的用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 是 | 是 |
| [流控制语言关键字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 是 | 是 |
| [跨数据库查询](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-overview.md) | 是，外加[弹性查询](sql-database-elastic-query-overview.md) |
| [跨数据库事务](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 | 是 - 请参阅[链接的服务器差异](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#linked-servers) |
| [游标](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 是 |是 | 
| [Data compression](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)（数据压缩） | 是 |是 |
| [数据库邮件](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 | 是 |
| [数据迁移服务 (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | 是 | 是 |
| [数据库镜像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 | 否 |
| [数据库配置设置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 是 | 是 |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 | 否 |
| [数据库快照](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 | 否 |
| [数据类型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 是 |是 |
| [DBCC 语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [DBCC 差异](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL 语句](https://docs.microsoft.com/sql/t-sql/statements/statements) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [DDL 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 仅数据库 |  是 |
| [分布式分区视图](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | 否 | 是 |
| [分布式事务 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 请参阅[弹性事务](sql-database-elastic-transactions-overview.md) |  否 - 请参阅[弹性事务](sql-database-elastic-transactions-overview.md) |
| [DML 语句](https://docs.microsoft.com/sql/t-sql/queries/queries) | 是 | 是 |
| [DML 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | 大多数 - 请参阅单个语句 |  是 |
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 大多数 - 请参阅单个 DMV |  是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
|[动态数据屏蔽](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[是](sql-database-dynamic-data-masking-get-started.md)| [是](sql-database-dynamic-data-masking-get-started.md) |
| [弹性池](sql-database-elastic-pool.md) | 是 | 内置 - 单个托管实例可以有多个共享同一资源池的数据库 |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 请参阅[警报](sql-database-insights-alerts-portal.md) | 是 |
| [表达式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 | 是 |
| [扩展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分 - 请参阅 [SQL 数据库中的扩展事件](sql-database-xevent-db-diff-from-svr.md) | 是 - 请参阅[扩展事件的差异](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [扩展的存储过程](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 | 否 |
[文件和文件组](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 仅限主文件组 | 是 |
| [文件流](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 | 否 |
| [全文搜索](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  不支持第三方断字符 |不支持第三方断字符 |
| [函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [异地还原](sql-database-recovery-using-backups.md#geo-restore) | 是 | 否 - 可以还原定期进行的 COPY_ONLY 完整备份 - 请参阅[备份差异](sql-database-managed-instance-transact-sql-information.md#backup)和[还原差异](sql-database-managed-instance-transact-sql-information.md#restore-statement)。 |
| [异地复制](sql-database-geo-replication-overview.md) | 是 | 否 |
| [图形处理](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | 是 | 是 |
| [内存中优化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是 - [仅限高级层和业务关键层](sql-database-in-memory.md) | 否 |
| [JSON 数据支持](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [是](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) | [是](https://docs.microsoft.com/azure/sql-database/sql-database-json-features) |
| [语言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大多数 - 请参阅单个元素 |  是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [链接服务器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-horizontal-partitioning.md) | 仅限 SQL Server 和 SQL 数据库 |
| [日志传送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 | 否 |
| [批量导入中的最小日志记录](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否 | 否 |
| [修改系统数据](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 | 是 |
| [联机索引操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 是 | 是 |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|否|是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|是|是|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|否|是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|否|是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|是|是|
| [运算符](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大多数 - 请参阅单个运算符 |是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [分区](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 是 | 是 |
| [数据库时间点还原](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md#point-in-time-restore) | 是 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否 | 否 |
| [基于策略的管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 | 否 |
| [Predicates](https://docs.microsoft.com/sql/t-sql/queries/predicates)（谓词） | 是 | 是 |
| [R 服务](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 预览版；请参阅[机器学习中的新增功能](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | 否 |
| [资源调控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 | 是 |
| [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 是 - 请参阅[还原差异](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [从备份还原数据库](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 仅从自动备份 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md) | 从自动备份 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md)以及从完整备份 - 请参阅[备份差异](sql-database-managed-instance-transact-sql-information.md#backup) |
| [行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 是 | 是 |
| [语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 | 否 |
| [序列号](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 是 | 是 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 | 是 - 请参阅 [Service Broker 差异](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [服务器配置设置](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [SET 语句](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大多数 - 请参阅单个语句 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | 是 | 是 |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 是 | 是 |
| [SQL 数据同步](sql-database-get-started-sql-data-sync.md) | 是 | 否 |
| [SQL Operations Studio ](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | 是 | 是 |
| [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 请参阅[弹性作业](sql-database-elastic-jobs-getting-started.md) | 是 - 请参阅 [SQL Server 代理差异](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 否 - 请参阅 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | 否 - 请参阅 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 请参阅 [SQL 数据库审核](sql-database-auditing.md) | 是 - 请参阅[审核差异](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | 是 | 是 |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 是，使用 Azure 数据工厂 (ADF) 环境中的托管 SSIS ，其中程序包存储在由 Azure SQL 数据库承载的 SSISDB 中并在 [Azure SSIS Integration Runtime](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md) 上执行。 | 是，使用 Azure 数据工厂 (ADF) 环境中的托管 SSIS ，其中程序包存储在由托管实例承载的 SSISDB 中并在 [Azure SSIS Integration Runtime](../data-factory/tutorial-deploy-ssis-packages-azure-powershell.md) 上执行。 |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | 是 | 是 |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 请参阅[扩展事件](sql-database-xevent-db-diff-from-svr.md) | 是 |
| [SQL Server 复制](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [仅限事务性和快照复制订户](sql-database-cloud-migrate.md) | 否 |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 - 请参阅 [Power BI](https://docs.microsoft.com/power-bi/) | 否 - 请参阅 [Power BI](https://docs.microsoft.com/power-bi/) |
| [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 是 | 是 |
| [系统存储函数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 大多数 - 请参阅单个函数 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [系统存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 请参阅单个存储过程 | 是 - 请参阅[存储过程、函数和触发器差异](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [系统表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 请参阅单个表 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [系统目录视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 请参阅单个视图 | 是 - 请参阅 [T-SQL 差异](sql-database-managed-instance-transact-sql-information.md) |
| [临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | 本地和数据库范围的全局临时表 | 本地和实例范围的全局临时表 |
| [临时表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [是](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) | [是](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables) |
|威胁检测|  [是](sql-database-threat-detection.md)|[是](sql-database-managed-instance-threat-detection.md)|
| [跟踪标志](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | 否 | 否 |
| [变量](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 是 | 是 |
| [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 | 否，未处于公开预览状态 |
[VNet](../virtual-network/virtual-networks-overview.md) | 部分 - 请参阅 [VNET 终结点](sql-database-vnet-service-endpoint-rule-overview.md) | 是，仅限资源管理器模型 |
| [Windows Server 故障转移群集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 | 每个数据库均包含[高可用性](sql-database-high-availability.md)。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-business-continuity.md)中对灾难恢复进行了讨论 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 是 | 是 |

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
- 有关托管实例的信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
