---
title: 如何配置 Azure SQL 数据库 | Microsoft Docs
description: 了解如何配置和管理 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: b4dd21324591075d7625a82fbbb661c4a8e84b1d
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439534"
---
# <a name="how-to-use-azure-sql-database"></a>如何使用 Azure SQL 数据库

在本部分中，你可以找到可帮助你管理和配置 Azure SQL 数据库的各种指南、脚本和说明。 还可以找到适用于[单一数据库](sql-database-howto-single-database.md)和[托管实例](sql-database-howto-managed-instance.md)的特定操作指南。

## <a name="load-data"></a>加载数据

- [在 Azure 中复制单一数据库](https://docs.microsoft.com/azure/sql-database/sql-database-copy)
- [从 BACPAC 导入 DB](https://docs.microsoft.com/azure/sql-database/sql-database-import)
- [将 DB 导出到 BACPAC](https://docs.microsoft.com/azure/sql-database/sql-database-export)
- [使用 BCP 加载数据](https://docs.microsoft.com/azure/sql-database/sql-database-load-from-csv-with-bcp)
- [使用 ADF 加载数据](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database?toc=/azure/sql-database/toc.json)

### <a name="data-sync"></a>数据同步

- [SQL 数据同步](https://docs.microsoft.com/azure/sql-database/sql-database-sync-data)
- [数据同步代理](https://docs.microsoft.com/azure/sql-database/sql-database-data-sync-agent)
- [复制架构更改](https://docs.microsoft.com/azure/sql-database/sql-database-update-sync-schema)
- [使用 OMS 进行监视](https://docs.microsoft.com/azure/sql-database/sql-database-sync-monitor-oms)
- [数据同步最佳做法](https://docs.microsoft.com/azure/sql-database/sql-database-best-practices-data-sync)
- [数据同步故障排除](https://docs.microsoft.com/azure/sql-database/sql-database-troubleshoot-data-sync)

## <a name="monitoring-and-tuning"></a>监视和优化

-  [手动优化](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [使用 DMV 监视性能](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [使用查询存储监视性能](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)
- [使用智能见解排查性能问题](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance)
- [使用智能见解诊断日志](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-use-diagnostics-log)
- [监视内存中 OLTP 空间](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-monitoring)

### <a name="extended-events"></a>扩展的事件

- [扩展的事件](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-db-diff-from-svr)
- [将扩展的事件存储到事件文件](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-event-file)
- [将扩展的事件存储到环形缓冲区](https://docs.microsoft.com/azure/sql-database/sql-database-xevent-code-ring-buffer)

## <a name="configure-features"></a>配置功能

- [配置 Azure AD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)
- [配置条件访问](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- [多重 AAD 身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication)
- [配置多重身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-ssms-mfa-authentication-configure)
- [配置时态保留策略](https://docs.microsoft.com/azure/sql-database/sql-database-temporal-tables-retention-policy)
- [为 TDE 配置 BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-configure)
- [轮换 TDE BYOK 密钥](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-key-rotation)
- [删除 TDE 保护器](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)
- [配置内存中 OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory-oltp-migration)
- [配置 Azure 自动化](https://docs.microsoft.com/azure/sql-database/sql-database-manage-automation)

## <a name="develop-applications"></a>开发应用程序

- [连接](https://docs.microsoft.com/azure/sql-database/sql-database-libraries)
- [使用 Spark 连接器](https://docs.microsoft.com/azure/sql-database/sql-database-spark-connector)
- [对应用进行身份验证](https://docs.microsoft.com/azure/sql-database/sql-database-client-id-keys)
- [错误消息](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages)
- [使用批处理提高性能](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)
- [连接指南](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)
- [DNS 别名](https://docs.microsoft.com/azure/sql-database/dns-alias-overview)
- [设置 DNS 别名 PowerShell](https://docs.microsoft.com/azure/sql-database/dns-alias-powershell)
- [端口 - ADO.NET](https://docs.microsoft.com/azure/sql-database/sql-database-develop-direct-route-ports-adonet-v12)
- [C 和 C ++](https://docs.microsoft.com/azure/sql-database/sql-database-develop-cplusplus-simple)
- [Excel](https://docs.microsoft.com/azure/sql-database/sql-database-connect-excel)

## <a name="design-applications"></a>设计应用程序

- [设计灾难恢复](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery)
- [设计弹性池](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool)
- [设计应用升级](https://docs.microsoft.com/azure/sql-database/sql-database-manage-application-rolling-upgrade)

### <a name="design-multi-tenant-saas-applications"></a>设计多租户 SaaS 应用程序

- [SaaS 设计模式](https://docs.microsoft.com/azure/sql-database/saas-tenancy-app-design-patterns)
- [SaaS 视频索引器](https://docs.microsoft.com/azure/sql-database/saas-tenancy-video-index-wingtip-brk3120-20171011)
- [SaaS 应用安全](https://docs.microsoft.com/azure/sql-database/saas-tenancy-elastic-tools-multi-tenant-row-level-security)

## <a name="next-steps"></a>后续步骤
- 详细了解[托管实例中的操作指南](sql-database-howto-managed-instance.md)。
- 详细了解[单一数据库中的操作指南](sql-database-howto-single-database.md)。
