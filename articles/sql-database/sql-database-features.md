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
ms.date: 10/23/2017
ms.author: carlrab
ms.openlocfilehash: c5853b7f4af02e096a0678de14abd5c0b3d7ab0c
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="azure-sql-database-features"></a>Azure SQL 数据库功能

Azure SQL 数据库与 SQL Server 共享通用基本代码，并在数据库级别支持大多数相同功能。 Azure SQL 数据库和 SQL Server 之间的主要功能差异存在于实例级别。 

我们会继续向 Azure SQL 数据库添加功能。 因此，我们鼓励访问我们的针对 Azure 的服务更新网页并使用其筛选器：

* 筛选为 [SQL 数据库服务](https://azure.microsoft.com/updates/?service=sql-database)。
* 筛选为针对 SQL 数据库功能的正式发布版本 [(GA) 公告](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability)。

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server 和 SQL 数据库功能支持

下面的表列出了 SQL Server 的主要功能，并说明了每个特定的功能是否受支持，同时提供了一个更详细介绍该功能的链接。 若要了解在迁移现有数据库解决方案时需考虑哪些 Transact-SQL 差异，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](sql-database-transact-sql-information.md)。


| SQL Server 功能 | **在 Azure SQL 数据库中受支持** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | 是 - 请参阅[证书存储](sql-database-always-encrypted.md)和[密钥保管库](sql-database-always-encrypted-azure-key-vault.md)|
| [AlwaysOn 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | 每个数据库均包含高可用性。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-geo-replication-overview.md)中对灾难恢复进行了讨论 |
| [附加数据库](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | 否 |
| [应用程序角色](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | 是 |
| [BACPAC 文件（导出）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | 是 - 请参阅 [SQL 数据库导出](sql-database-export.md) |
| [BACPAC 文件（导入）](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | 是 - 请参阅 [SQL 数据库导入](sql-database-import.md) |
| [备份命令](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | 否 - 请参阅[自动化的备份](sql-database-automated-backups.md) |
| [内置函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 |
| [更改数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | 否 |
| [更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | 是 |
| [排序规则语句](https://docs.microsoft.com/sql/t-sql/statements/collations) | 是 |
| [列存储索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | 是 - [仅限 Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [公共语言运行时 (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | 否 |
| [包含的数据库](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | 是 |
| [包含的用户](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | 是 |
| [流控制语言关键字](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | 是 |
| [跨数据库查询](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | 部分 - 请参阅[弹性查询](sql-database-elastic-query-overview.md) |
| [游标](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 是 | 
| [Data compression](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression)（数据压缩） | 是 |
| [数据库邮件](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | 否 |
| [数据库镜像](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | 否 |
| [数据库配置设置](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | 是 |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | 否 |
| [数据库快照](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | 否 |
| [数据类型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | 是 |  
| [DBCC 语句](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | 大多数 - 请参阅单个语句 |
| [DDL 语句](https://docs.microsoft.com/sql/t-sql/statements/statements) | 是 |
| [DDL 触发器](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | 仅数据库 |
| [分布式事务 - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | 否 - 请参阅[弹性事务](sql-database-elastic-transactions-overview.md) |
| [DML 语句](https://docs.microsoft.com/sql/t-sql/queries/queries) | 是 |
| [DML 触发器](https://docs.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers) | 大多数 - 请参阅单个语句 | 
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | 部分 - 请参阅单个 DMV |
| [事件通知](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | 否 - 请参阅[警报](sql-database-insights-alerts-portal.md) |
| [表达式](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |是 |
| [扩展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | 部分 - 请参阅 [SQL 数据库中的扩展事件](sql-database-xevent-db-diff-from-svr.md) |
| [扩展的存储过程](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | 否 |
| [文件和文件组](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | 仅限主文件组 |
| [文件流](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | 否 |
| [全文搜索](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | 不支持第三方断字符 |
| [函数](https://docs.microsoft.com/sql/t-sql/functions/functions) | 大多数 - 请参阅单个函数 |
| [图形处理](/sql/relational-databases/graphs/sql-graph-overview) | 是 |
| [内存中优化](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | 是 - [仅限 Premium Edition](sql-database-in-memory.md) |
| [JSON 数据支持](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | 是 |
| [语言元素](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | 大多数 - 请参阅单个元素 |  
| [链接服务器](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | 否 - 请参阅[弹性查询](sql-database-elastic-query-horizontal-partitioning.md) |
| [日志传送](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | 每个数据库均包含高可用性。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-geo-replication-overview.md)中对灾难恢复进行了讨论 |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | 否 |
| [批量导入中的最小日志记录](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | 否 |
| [修改系统数据](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | 否 |
| [联机索引操作](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | 是 |
| [运算符](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | 大多数 - 请参阅单个运算符 |
| [数据库时间点还原](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | 是 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | 否 |
| [基于策略的管理](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | 否 |
| [Predicates](https://docs.microsoft.com/sql/t-sql/queries/predicates)（谓词） | 是 |
| [R 服务](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | 预览版；请参阅[机器学习中的新增功能](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [资源调控器](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | 否 |
| [RESTORE 语句](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | 否 | 
| [从备份还原数据库](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | 仅从内置备份 - 请参阅 [SQL 数据库恢复](sql-database-recovery-using-backups.md) |
| [行级别安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | 是 |
| [语义搜索](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | 否 |
| [序列号](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | 是 |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | 否 |
| [服务器配置设置](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | 否 |
| [SET 语句](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | 大多数 - 请参阅单个语句 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | 是 |
| [SQL Server 代理](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | 否 - 请参阅[弹性作业](sql-database-elastic-jobs-getting-started.md) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | 请参阅 [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server 审核](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | 否 - 请参阅 [SQL 数据库审核](sql-database-auditing.md) |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | 请参阅 [Azure 数据工厂](https://azure.microsoft.com/services/data-factory/) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | 是 |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | 否 - 请参阅[扩展事件](sql-database-xevent-db-diff-from-svr.md) |
| [SQL Server 复制](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [仅限事务性和快照复制订户](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | 否 |
| [存储过程](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | 是 |
| [系统存储函数](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | 部分 - 请参阅单个函数 |
| [系统存储过程](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | 部分 - 请参阅单个存储过程 |
| [系统表](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | 部分 - 请参阅单个表 |
| [系统目录视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | 部分 - 请参阅单个视图 |
| [表分区](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | 是 - 仅限主文件组 |
| [临时表](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | 仅限本地和数据库范围的全局临时表 |
| [临时表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | 是 |
| [变量](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 是 | 
| [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | 是 |
| [Windows Server 故障转移群集](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | 每个数据库均包含高可用性。 [简要介绍如何使用 Azure SQL 数据库确保业务连续性](sql-database-geo-replication-overview.md)中对灾难恢复进行了讨论 |
| [XML 索引](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | 是 |

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 数据库服务的信息，请参阅[什么是 SQL 数据库？](sql-database-technical-overview.md)
- 有关 Transact-SQL 支持和差异的信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](sql-database-transact-sql-information.md)。
