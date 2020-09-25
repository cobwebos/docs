---
title: 配置和管理内容参考信息
description: 查找内容参考信息，了解如何配置和管理 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: fa79af1047862e8694fa6342942ffd0793bafcb6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334994"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>配置和管理内容参考信息 - Azure SQL 数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍可帮助你管理和配置 Azure SQL 数据库的各种指南、脚本和说明的内容参考信息。 

## <a name="load-data"></a>加载数据

- [迁移到 SQL 数据库](migrate-to-database-from-sql-server.md)
- 了解如何[在迁移后管理 SQL 数据库](manage-data-after-migrating-to-database.md)。
- [复制数据库](database-copy.md)
- [从 BACPAC 导入 DB](database-import.md)
- [将 DB 导出到 BACPAC](database-export.md)
- [使用 BCP 加载数据](../load-from-csv-with-bcp.md)
- [使用 ADF 加载数据](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>配置功能

- [配置 Azure Active Directory (Azure AD) 身份验证](authentication-aad-configure.md)
- [配置条件访问](conditional-access-configure.md)
- [多重 Azure AD 身份验证](authentication-mfa-ssms-overview.md)
- [配置多重身份验证](authentication-mfa-ssms-configure.md)
- [配置时态保留策略](temporal-tables-retention-policy.md)
- [为 TDE 配置 BYOK](transparent-data-encryption-byok-configure.md)
- [轮换 TDE BYOK 密钥](transparent-data-encryption-byok-key-rotation.md)
- [删除 TDE 保护器](transparent-data-encryption-byok-remove-tde-protector.md)
- [配置内存中 OLTP](../in-memory-oltp-configure.md)
- [配置 Azure 自动化](automation-manage.md)
- [配置事务性复制](replication-to-sql-database.md)以在数据库之间复制数据。
- [配置威胁检测](threat-detection-configure.md)来让 Azure SQL 数据库识别可疑活动，例如 SQL 注入或来自可疑位置的访问。
- [配置动态数据掩码](dynamic-data-masking-configure-portal.md)来保护敏感数据。
- 为数据库[配置备份保留](long-term-backup-retention-configure.md)以在 Azure Blob 存储上保留备份。 
- [配置异地复制](active-geo-replication-overview.md)以将数据库副本保留在另一个区域中。
- [为异地副本配置安全性](active-geo-replication-security-configure.md)。

## <a name="monitor-and-tune-your-database"></a>监视和优化数据库

- [手动优化](performance-guidance.md)
- [使用 DMV 监视性能](monitoring-with-dmvs.md)
- [使用查询存储监视性能](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [启用自动优化](automatic-tuning-enable.md)来让 Azure SQL 数据库优化工作负荷的性能。
- [启用自动优化的电子邮件通知](automatic-tuning-email-notifications-configure.md)以获取有关优化建议的信息。
- [应用性能建议](database-advisor-find-recommendations-portal.md)并优化数据库。
- [创建警报](alerts-insights-configure-portal.md)以从 Azure SQL 数据库获取通知。
- [排查连接问题](troubleshoot-common-errors-issues.md)：如果发现应用程序与数据库之间存在某些连接问题。 还可以[使用“资源运行状况”来排查连接问题](resource-health-to-troubleshoot-connectivity.md)。
- [使用智能见解排查性能问题](intelligent-insights-troubleshoot-performance.md)
- [管理文件空间](file-space-manage.md)来监视数据库中的存储使用情况。
- [使用智能见解诊断日志](intelligent-insights-use-diagnostics-log.md)
- [监视内存中 OLTP 空间](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>扩展的事件

- [扩展的事件](xevent-db-diff-from-svr.md)
- [将扩展的事件存储到事件文件](xevent-code-event-file.md)
- [将扩展的事件存储到环形缓冲区](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>查询分布式数据

- 跨多个数据库[查询垂直分区的数据](elastic-query-getting-started-vertical.md)。
- [跨横向扩展的数据层进行报告](elastic-query-horizontal-partitioning.md)。
- [在具有不同架构的表中进行查询](elastic-query-vertical-partitioning.md)。

### <a name="data-sync"></a>数据同步

- [SQL 数据同步](sql-data-sync-data-sql-server-sql-database.md)
- [Data Sync Agent](sql-data-sync-agent-overview.md)
- [复制架构更改](sql-data-sync-update-sync-schema.md)
- [使用 OMS 进行监视](sql-data-sync-monitor-sync.md)
- [数据同步最佳做法](sql-data-sync-best-practices.md)
- [数据同步故障排除](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>弹性数据库作业

- 使用 PowerShell [创建和管理](elastic-jobs-powershell-create.md)弹性数据库作业。
- 使用 Transact-SQL [创建和管理](elastic-jobs-tsql-create-manage.md)弹性数据库作业。
- [从旧的弹性作业进行迁移](elastic-jobs-migrate.md)。

## <a name="database-sharding"></a>数据库分片

- [升级弹性数据库客户端库](elastic-scale-upgrade-client-library.md)。
- [创建分片应用](elastic-scale-get-started.md)。
- [查询水平分片的数据](elastic-query-getting-started.md)。
- 运行[多分片查询](elastic-scale-multishard-querying.md)。
- [移动分片数据](elastic-scale-configure-deploy-split-and-merge.md)。
- 在数据库分片中[配置安全性](elastic-scale-split-merge-security-configuration.md)。
- 向当前的数据库分片集[添加分片](elastic-scale-add-a-shard.md)。
- [解决分片映射问题](elastic-database-recovery-manager.md)。
- [迁移分片 DB](elastic-convert-to-use-elastic-tools.md)。
- [创建计数器](elastic-database-perf-counters.md)。
- [使用实体框架](elastic-scale-use-entity-framework-applications-visual-studio.md)查询分片的数据。
- [使用 Dapper 框架](elastic-scale-working-with-dapper.md)查询分片的数据。

## <a name="develop-applications"></a>开发应用程序

- [连接](connect-query-content-reference-guide.md#libraries)
- [使用 Spark 连接器](spark-connector.md)
- [对应用进行身份验证](application-authentication-get-client-id-keys.md)
- [使用批处理提高性能](../performance-improve-use-batching.md)
- [连接指南](troubleshoot-common-connectivity-issues.md)
- [DNS 别名](dns-alias-overview.md)
- [设置 DNS 别名 PowerShell](dns-alias-powershell-create.md)
- [端口 - ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C 和 C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>设计应用程序

- [设计灾难恢复](designing-cloud-solutions-for-disaster-recovery.md)
- [设计弹性池](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [设计应用升级](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a> (SaaS) 应用程序设计多租户软件即服务

- [SaaS 设计模式](saas-tenancy-app-design-patterns.md)
- [SaaS 视频索引器](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 应用安全](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure SQL 托管实例操作指南](../managed-instance/how-to-content-reference-guide.md)
