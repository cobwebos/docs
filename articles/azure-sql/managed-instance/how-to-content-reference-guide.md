---
title: 配置和管理内容参考信息
titleSuffix: Azure SQL Managed Instance
description: 可通过内容参考信息了解如何配置和管理 Azure SQL 托管实例。
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
ms.openlocfilehash: de8d1b7aba7763799228c831af61bf83a79937bb
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167940"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Azure SQL 托管实例内容参考信息
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍可帮助你管理和配置 Azure SQL 托管实例的各种指南、脚本和说明的内容参考信息。

## <a name="load-data"></a>加载数据

- [迁移到 Azure SQL 托管实例](migrate-to-instance-from-sql-server.md)：了解迁移到 Azure SQL 托管实例时建议使用的迁移流程和工具。
- [将 TDE 证书迁移到 Azure SQL 托管实例](tde-certificate-migrate.md)：如果 SQL Server 数据库受透明数据加密 (TDE) 保护，则需迁移证书，以便 SQL 托管实例能够使用该证书来解密需要在 Azure 中还原的备份。
- [从 BACPAC 导入 DB](../database/database-import.md)
- [将 DB 导出到 BACPAC](../database/database-export.md)
- [使用 BCP 加载数据](../load-from-csv-with-bcp.md)
- [使用 Azure 数据工厂加载数据](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>网络配置

- [确定子网大小](vnet-subnet-determine-size.md)：由于在部署 SQL 托管实例之后无法调整子网的大小，因此需要计算计划部署到子网的托管实例的数量和类型所需的 IP 地址范围。 
- [创建新的 VNet 和子网](virtual-network-subnet-create-arm-template.md)：根据[网络要求](connectivity-architecture-overview.md#network-requirements)配置虚拟网络和子网。 
- [配置现有 VNet 和子网](vnet-existing-add-subnet.md)：验证网络要求，并配置现有虚拟网络和子网以部署 SQL 托管实例。 
- [配置自定义 DNS](custom-dns-configure.md)：通过链接的 db 邮件配置文件服务器，将自定义 DNS 配置为：向外部资源授予从 Azure SQL 托管实例到自定义域的访问权限。 
- [同步网络配置](azure-app-sync-network-configuration.md)：如果在[将应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)之后无法建立连接，请刷新网络配置计划。
- [查找管理终结点 IP 地址](management-endpoint-find-ip-address.md)：确定 SQL 托管实例出于管理目的使用的公共终结点。 
- [验证内置防火墙保护](management-endpoint-verify-built-in-firewall.md)：验证 SQL 托管实例仅允许所需端口上的流量，以及其他内置防火墙规则。 
- [连接应用程序](connect-application-instance.md)：了解如何通过不同的模式将应用程序连接到 Azure SQL 托管实例。

## <a name="feature-configuration"></a>功能配置

- [配置 Azure AD 身份验证](../database/authentication-aad-configure.md)
- [配置条件访问](../database/conditional-access-configure.md)
- [多重 Azure AD 身份验证](../database/authentication-mfa-ssms-overview.md)
- [配置多重身份验证](../database/authentication-mfa-ssms-configure.md)
- [配置临时保留策略](../database/temporal-tables-retention-policy.md)
- [为 TDE 配置 BYOK](../database/transparent-data-encryption-byok-configure.md)
- [轮换 TDE BYOK 密钥](../database/transparent-data-encryption-byok-key-rotation.md)
- [删除 TDE 保护程序](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [配置内存中 OLTP](../in-memory-oltp-configure.md)
- [配置 Azure 自动化](../database/automation-manage.md)
- 可以使用[事务复制](replication-between-two-instances-configure-tutorial.md)在托管实例之间复制数据，或者将数据从 SQL Server 本地复制到 SQL 托管实例，反之亦然。
- [配置威胁检测](threat-detection-configure.md)-[威胁检测](../database/threat-detection-overview.md)是一种内置的 Azure SQL 托管实例功能，可检测各种潜在攻击，例如 SQL 注入或从可疑位置访问。 
- 通过[创建警报](alerts-create.md)，你可以对 SQL 托管实例的监视指标（例如 CPU 使用率、存储空间消耗和 IOPS 等）设置警报。 

## <a name="monitoring-and-tuning"></a>监视和优化

- [手动优化](../database/performance-guidance.md)
- [使用 DMV 监视性能](../database/monitoring-with-dmvs.md)
- [使用查询存储监视性能](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [使用智能见解排查性能问题](../database/intelligent-insights-troubleshoot-performance.md)
- [使用智能见解诊断日志](../database/intelligent-insights-use-diagnostics-log.md)
- [监视内存中 OLTP 空间](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>扩展的事件

- [扩展的事件](../database/xevent-db-diff-from-svr.md)
- [将扩展的事件存储到事件文件](../database/xevent-code-event-file.md)
- [将扩展的事件存储到环形缓冲区](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>警报

- [为托管实例创建警报](alerts-create.md)

## <a name="operations"></a>操作

- [SQL 托管实例上用户启动的手动故障转移](user-initiated-failover.md)

## <a name="develop-applications"></a>开发应用程序

- [连接](../database/connect-query-content-reference-guide.md#libraries)
- [使用 Spark 连接器](../../cosmos-db/spark-connector.md)
- [对应用进行身份验证](../database/application-authentication-get-client-id-keys.md)
- [使用批处理提高性能](../performance-improve-use-batching.md)
- [连接指南](../database/troubleshoot-common-connectivity-issues.md)
- [DNS 别名](../database/dns-alias-overview.md)
- [使用 PowerShell 设置 DNS 别名](../database/dns-alias-powershell-create.md)
- [端口 - ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C 和 C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>设计应用程序

- [设计灾难恢复](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [设计弹性池](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [设计应用升级](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>设计多租户 SaaS 应用程序

- [SaaS 设计模式](../database/saas-tenancy-app-design-patterns.md)
- [SaaS 视频索引器](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [SaaS 应用安全](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>后续步骤

从[部署 SQL 托管实例](instance-create-quickstart.md)开始。
