---
title: 如何配置 Azure SQL 数据库
description: 了解如何配置和管理 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: ccdeb883dc9cf2cba499e45e25ff4706bb120463
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689877"
---
# <a name="how-to-use-azure-sql-database"></a>如何使用 Azure SQL 数据库

在本部分中，你可以找到可帮助你管理和配置 Azure SQL 数据库的各种指南、脚本和说明。 还可以找到具体的[单一数据库](sql-database-howto-single-database.md)和[托管实例](sql-database-howto-managed-instance.md)操作指南。

## <a name="load-data"></a>加载数据

- [在 Azure 中复制单一数据库或共用数据库](sql-database-copy.md)
- [从 BACPAC 导入 DB](sql-database-import.md)
- [将 DB 导出到 BACPAC](sql-database-export.md)
- [使用 BCP 加载数据](sql-database-load-from-csv-with-bcp.md)
- [使用 ADF 加载数据](../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>数据同步

- [SQL 数据同步](sql-database-sync-data.md)
- [Data Sync Agent](sql-database-data-sync-agent.md)
- [复制架构更改](sql-database-update-sync-schema.md)
- [使用 OMS 进行监视](sql-database-sync-monitor-oms.md)
- [数据同步最佳做法](sql-database-best-practices-data-sync.md)
- [数据同步故障排除](sql-database-troubleshoot-data-sync.md)

## <a name="monitoring-and-tuning"></a>监视和优化

- [手动优化](sql-database-performance-guidance.md)
- [使用 DMV 监视性能](sql-database-monitoring-with-dmvs.md)
- [使用查询存储监视性能](sql-database-operate-query-store.md)
- [使用智能见解排查性能问题](sql-database-intelligent-insights-troubleshoot-performance.md)
- [使用智能见解诊断日志](sql-database-intelligent-insights-use-diagnostics-log.md)
- [监视内存中 OLTP 空间](sql-database-in-memory-oltp-monitoring.md)

### <a name="extended-events"></a>扩展的事件

- [扩展的事件](sql-database-xevent-db-diff-from-svr.md)
- [将扩展的事件存储到事件文件](sql-database-xevent-code-event-file.md)
- [将扩展的事件存储到环形缓冲区](sql-database-xevent-code-ring-buffer.md)

## <a name="configure-features"></a>配置功能

- [配置 Azure AD 身份验证](sql-database-aad-authentication-configure.md)
- [配置条件访问](sql-database-conditional-access.md)
- [多重 AAD 身份验证](sql-database-ssms-mfa-authentication.md)
- [配置多重身份验证](sql-database-ssms-mfa-authentication-configure.md)
- [配置时态保留策略](sql-database-temporal-tables-retention-policy.md)
- [为 TDE 配置 BYOK](transparent-data-encryption-byok-azure-sql-configure.md)
- [轮换 TDE BYOK 密钥](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- [删除 TDE 保护器](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
- [配置内存中 OLTP](sql-database-in-memory-oltp-migration.md)
- [配置 Azure 自动化](sql-database-manage-automation.md)

## <a name="develop-applications"></a>开发应用程序

- [连接](sql-database-libraries.md)
- [使用 Spark 连接器](sql-database-spark-connector.md)
- [对应用进行身份验证](sql-database-client-id-keys.md)
- [错误消息](sql-database-develop-error-messages.md)
- [使用批处理提高性能](sql-database-use-batching-to-improve-performance.md)
- [连接指南](sql-database-connectivity-issues.md)
- [DNS 别名](dns-alias-overview.md)
- [设置 DNS 别名 PowerShell](dns-alias-powershell.md)
- [端口 - ADO.NET](sql-database-develop-direct-route-ports-adonet-v12.md)
- [C 和 C ++](sql-database-develop-cplusplus-simple.md)
- [Excel](sql-database-connect-excel.md)

## <a name="design-applications"></a>设计应用程序

- [设计灾难恢复](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [设计弹性池](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [设计应用升级](sql-database-manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>设计多租户 SaaS 应用程序

- [SaaS 设计模式](saas-tenancy-app-design-patterns.md)
- [SaaS 视频索引器](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 应用安全](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>后续步骤

- 详细了解[托管实例操作指南](sql-database-howto-managed-instance.md)。
- 详细了解[单一数据库操作指南](sql-database-howto-single-database.md)。
