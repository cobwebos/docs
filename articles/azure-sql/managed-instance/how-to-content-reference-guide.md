---
title: 配置 & 管理内容引用
titleSuffix: Azure SQL Managed Instance
description: 介绍如何配置和管理 Azure SQL 托管实例内容的参考指南。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 23ce3272d99401340d784f88dd537baa55774a63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708818"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL 托管实例内容参考
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍可帮助你管理和配置 Azure SQL 托管实例的各种指南、脚本和说明的内容引用。

## <a name="load-data"></a>加载数据

- [迁移到 AZURE sql 托管实例](migrate-to-instance-from-sql-server.md)：了解有关迁移到 azure sql 托管实例的建议迁移过程和工具。
- 将[TDE Cert 迁移到 AZURE SQL 托管实例](tde-certificate-migrate.md)：如果你的 SQL Server 数据库使用透明数据加密（TDE）进行保护，则需要迁移 SQL 托管实例可用来对要在 Azure 中还原的备份进行解密的证书。
- [从 BACPAC 导入 DB](../database/database-import.md)
- [将 DB 导出到 BACPAC](../database/database-export.md)
- [使用 BCP 加载数据](../load-from-csv-with-bcp.md)
- [用 Azure 数据工厂加载数据](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>网络配置

- [确定子网大小](vnet-subnet-determine-size.md)：由于部署 SQL 托管实例后无法调整子网大小，因此需要计算计划部署到子网中的托管实例的数量和类型所需的 IP 地址范围。 
- [创建新的 VNet 和子网](virtual-network-subnet-create-arm-template.md)：根据[网络要求](connectivity-architecture-overview.md#network-requirements)配置虚拟网络和子网。 
- [配置现有的 VNet 和子网](vnet-existing-add-subnet.md)：验证网络要求，并配置现有虚拟网络和子网以部署 SQL 托管实例。 
- [配置自定义 dns](custom-dns-configure.md)：通过 db 邮件配置文件的链接服务器，将自定义 dns 配置为授予对自定义域托管实例的外部资源访问权限。 
- [同步网络配置](azure-app-sync-network-configuration.md)：如果你在将[应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)后无法建立连接，请刷新网络配置计划。
- [查找管理终结点 IP 地址](management-endpoint-find-ip-address.md)：确定 SQL 托管实例出于管理目的而使用的公共终结点。 
- [验证内置防火墙保护](management-endpoint-verify-built-in-firewall.md)：验证 SQL 托管实例仅允许所需端口上的流量，以及其他内置防火墙规则。 
- [连接应用程序](connect-application-instance.md)：了解用于将应用程序连接到 SQL 托管实例的不同模式。

## <a name="feature-configuration"></a>功能配置

- [配置 Azure AD 身份验证](../database/authentication-aad-configure.md)
- [配置条件访问](../database/conditional-access-configure.md)
- [多重身份验证 Azure AD](../database/authentication-mfa-ssms-overview.md)
- [配置多重身份验证](../database/authentication-mfa-ssms-configure.md)
- [配置临时保留策略](../database/temporal-tables-retention-policy.md)
- [为 TDE 配置 BYOK](../database/transparent-data-encryption-byok-configure.md)
- [轮换 TDE BYOK 密钥](../database/transparent-data-encryption-byok-key-rotation.md)
- [删除 TDE 保护程序](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [配置内存中 OLTP](../in-memory-oltp-configure.md)
- [配置 Azure 自动化](../database/automation-manage.md)
- 通过[事务复制](replication-between-two-instances-configure-tutorial.md)，可以在托管实例之间复制数据，也可以从 SQL Server 本地复制到 SQL 托管实例，反之亦然。
- [配置威胁检测](threat-detection-configure.md)-[威胁检测](../database/threat-detection-overview.md)是一种内置的 Azure SQL 托管实例功能，可检测各种潜在攻击，例如 SQL 注入或从可疑位置访问。 
- 通过[创建警报](alerts-create.md)，你可以为监视的指标（例如 CPU 使用率、存储空间消耗、IOPS 以及 SQL 托管实例的其他指标）设置警报。 

## <a name="monitoring-and-tuning"></a>监视和优化

- [手动优化](../database/performance-guidance.md)
- [使用 DMV 监视性能](../database/monitoring-with-dmvs.md)
- [使用查询存储来监视性能](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [使用智能见解排查性能问题](../database/intelligent-insights-troubleshoot-performance.md)
- [使用智能见解诊断日志](../database/intelligent-insights-use-diagnostics-log.md)
- [监视内存中 OLTP 空间](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>扩展的事件

- [扩展的事件](../database/xevent-db-diff-from-svr.md)
- [将扩展事件存储到事件文件中](../database/xevent-code-event-file.md)
- [将扩展事件存储到环形缓冲区](../database/xevent-code-ring-buffer.md)

## <a name="develop-applications"></a>开发应用程序

- [连接](../database/connect-query-content-reference-guide.md#libraries)
- [使用 Spark 连接器](../../cosmos-db/spark-connector.md)
- [对应用程序进行身份验证](../database/application-authentication-get-client-id-keys.md)
- [使用批处理提高性能](../performance-improve-use-batching.md)
- [连接指南](../database/troubleshoot-common-connectivity-issues.md)
- [DNS 别名](../database/dns-alias-overview.md)
- [使用 PowerShell 设置 DNS 别名](../database/dns-alias-powershell-create.md)
- [端口 - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C 和 C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>设计应用程序

- [灾难恢复设计](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [弹性池设计](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [应用升级设计](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>设计多租户 SaaS 应用程序

- [SaaS 设计模式](../database/saas-tenancy-app-design-patterns.md)
- [SaaS 视频索引器](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 应用安全](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)



## <a name="next-steps"></a>后续步骤

通过[部署 SQL 托管实例](instance-create-quickstart.md)开始。
