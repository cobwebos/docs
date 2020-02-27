---
title: 连接源数据库时出现问题
titleSuffix: Azure Database Migration Service
description: 了解如何对与将 Azure 数据库迁移服务连接到源数据库相关的已知问题/错误进行故障排除。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 332f612e1ffe57fc4edd90b1fe4c6a5ea5a2904a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649169"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>排查连接到源数据库时的 DMS 错误

以下文章详细介绍了如何解决将 Azure 数据库迁移服务（DMS）连接到源数据库时可能会遇到的潜在问题。 下面的每个部分都与特定类型的源数据库相关，其中列出了可能会遇到的错误以及有关如何排查连接问题的信息的链接。

## <a name="sql-server"></a>SQL Server

下表提供了与连接到源 SQL Server 数据库相关的潜在问题以及如何解决这些问题。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 请验证实例名称是否正确，以及 SQL Server 是否配置为允许远程连接。<br> | 如果服务找不到源服务器，则会发生此错误。 若要解决此问题，请参阅在[使用动态端口或命名实例时，连接到源 SQL Server 的错误消息](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)。 |
| **错误 53** -SQL 连接失败。 （此外，对于错误代码1、2、5、53、233、258、1225、11001）<br><br> | 如果服务无法连接到源服务器，则会发生此错误。 若要解决此问题，请参阅以下资源，然后重试。 <br><br>  [解决连接问题的交互式用户指南](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [将 SQL Server 迁移到 Azure SQL 数据库的先决条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [将 SQL Server 迁移到 Azure SQL 数据库托管实例的先决条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **错误 18456** -登录失败。<br> | 如果服务无法使用提供的 T-sql 凭据连接到源数据库，则会发生此错误。 若要解决此问题，请验证输入的凭据。 你还可以引用此表下面的说明中列出的[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或故障排除文档，然后重试。 |
| 提供的 AccountName 值 "{0}" 格式错误。 AccountName 的预期格式为 DomainName\UserName<br> | 如果用户选择 Windows 身份验证，但提供的用户名格式无效，则会发生此错误。 若要解决此问题，请以正确的 Windows 身份验证格式提供用户名，或选择 " **SQL 身份验证**"。 |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

下表提供了与连接到源 AWS RDS MySQL 数据库相关的潜在问题以及如何解决这些问题。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| **错误 [2003]** [HY000]-连接失败。 错误 [HY000] [MySQL] [ODBC x. x （w）驱动程序] 无法连接到 "{server}" 上的 MySQL 服务器（10060） | 如果 MySQL ODBC 驱动程序无法连接到源服务器，则会发生此错误。 若要解决此问题，请参阅此表下面的说明中列出的疑难解答文档，然后重试。<br> |
| **错误 [2005]** [HY000]-连接失败。 错误 [HY000] [MySQL] [ODBC x. x （w）驱动程序] 未知 MySQL server 主机 "{server}" | 如果服务在 RDS 上找不到源主机，则会发生此错误。 此问题可能是由于列出的源不存在，也可能是 RDS 基础结构出现问题。 若要解决此问题，请参阅此表下面的说明中列出的疑难解答文档，然后重试。<br> |
| **错误 [1045]** [HY000]-连接失败。 错误 [HY000] [MySQL] [使用 ODBC x. x （w）驱动程序] 对用户 "{user}" @ "{server}" 的访问被拒绝（使用密码：是） | 如果 MySQL ODBC 驱动程序由于凭据无效而无法连接到源服务器，则会发生此错误。 验证输入的凭据。 如果此问题仍然存在，请验证源计算机是否具有正确的凭据。 可能需要在控制台中重置密码。 如果仍然遇到问题，请参阅此表下面的说明中列出的疑难解答文档，然后重试。<br> |
| **错误 [9002]** [HY000]-连接失败。 错误 [HY000] [MySQL] [ODBC x. x （w）驱动程序] 连接字符串可能不正确。 访问门户获得引用。| 如果连接因连接字符串问题而失败，则会发生此错误。 验证提供的连接字符串是否有效。 若要解决此问题，请参阅此表下面的说明中列出的疑难解答文档，然后重试。<br> |
| **二进制日志记录错误。变量 binlog_format 的值为 "{value}"。请将其更改为 "row"。** | 如果二进制日志记录中存在错误，则会发生此错误。变量 binlog_format 的值错误。 若要解决此问题，请将参数组中的 binlog_format 更改为 "ROW"，然后重新启动实例。 有关详细信息，请参阅[二进制日志记录选项和变量](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)或[AWS RDS MySQL 数据库日志文件文档](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)。<br> |

> [!NOTE]
> 有关与连接到源 AWS RDS MySQL 数据库相关的问题的详细信息，请参阅以下资源：
> * [Amazon RDS 连接问题的疑难解答](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何实现解决连接到我的 Amazon RDS 数据库实例时遇到的问题吗？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

下表提供了与连接到源 AWS RDS PostgreSQL 数据库相关的潜在问题以及如何解决这些问题。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| **错误 [101]** [08001]-连接失败。 错误 [08001] 超时。 | 如果 Postgres 驱动程序无法连接到源服务器，则会发生此错误。 若要解决此问题，请参阅此表下面的说明中列出的疑难解答文档，然后重试。 |
| **错误：参数 wal_level 具有值 "{value}"。请将其更改为 "逻辑"，以允许复制。** | 如果参数 wal_level 的值错误，则会发生此错误。 若要解决此问题，请将参数组中的 logical_replication 更改为1，然后重新启动实例。 有关详细信息，请参阅使用 DMS[在 AMAZON RDS 上](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)[迁移到 Azure PostgreSQL 的先决条件](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)。 |

> [!NOTE]
> 有关与连接到源 AWS RDS PostgreSQL 数据库相关的问题的详细信息，请参阅以下资源：
> * [Amazon RDS 连接问题的疑难解答](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何实现解决连接到我的 Amazon RDS 数据库实例时遇到的问题吗？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

下表提供了与连接到源 AWS RDS SQL Server 数据库相关的潜在问题以及如何解决这些问题。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| **错误 53** -SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问该服务器。 请验证实例名称是否正确，以及 SQL Server 是否配置为允许远程连接。 （提供程序：命名管道提供程序，错误： 40-无法打开到 SQL Server 的连接 | 如果服务无法连接到源服务器，则会发生此错误。 若要解决此问题，请参阅此表下面的说明中列出的疑难解答文档，然后重试。 |
| **错误 18456** -登录失败。 用户 "{user}" 的登录失败 | 如果服务无法通过提供的 T-sql 凭据连接到源数据库，则会发生此错误。 若要解决此问题，请验证输入的凭据。 你还可以引用此表下面的说明中列出的[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或故障排除文档，然后重试。 |
| **错误 87** -连接字符串无效。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 请验证实例名称是否正确，以及 SQL Server 是否配置为允许远程连接。 （提供程序： SQL 网络接口，错误： 25-连接字符串无效） | 如果由于连接字符串无效而导致服务无法连接到源服务器，则会发生此错误。 若要解决此问题，请验证提供的连接字符串。 如果问题仍然存在，请参阅此表下面的说明中列出的疑难解答文档，然后重试。 |
| **错误-服务器证书不受信任。** 已成功与服务器建立连接，但是在登录过程中发生错误。 （提供程序： SSL 提供程序，错误： 0-证书链由不受信任的颁发机构颁发。） | 如果使用的证书不受信任，则会发生此错误。 若要解决此问题，需要找到可以信任的证书，然后在服务器上启用它。 或者，可以在连接时选择 "信任证书" 选项。 仅在熟悉使用的证书并且信任证书时才执行此操作。 <br> 使用自签名证书加密的 SSL 连接不提供强大的安全性，它们容易受到中间人攻击。 不要在生产环境中或在连接到 internet 的服务器上使用自签名证书依赖于 SSL。 <br> 有关详细信息，请参阅[使用 SSL 与 MICROSOFT SQL SERVER DB 实例](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)或[教程：使用 DMS 将 RDS SQL Server 迁移到 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)。 |
| **错误 300** -用户没有所需的权限。 已拒绝对对象 "{SERVER}"、数据库 "{database}" 进行 VIEW SERVER STATE 权限 | 如果用户没有执行迁移的权限，则会发生此错误。 若要解决此问题，请参阅[GRANT 服务器权限-transact-sql](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017)或[教程：使用 DMS 将 RDS SQL Server 迁移到 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)了解更多详细信息。 |

> [!NOTE]
> 有关解决与源 AWS RDS SQL Server 相关的问题的详细信息，请参阅以下资源：
>
> * [解决 SQL Server 连接错误](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [如何实现解决连接到我的 Amazon RDS 数据库实例时遇到的问题吗？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>已知问题

* [联机迁移到 Azure SQL Database 时的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [联机迁移到 Azure Database for MySQL 的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [联机迁移到 Azure Database for PostgreSQL 的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 查看[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)一文。
* 查看[如何使用 Azure 门户在 Azure Database for MySQL 中配置服务器参数](https://docs.microsoft.com/azure/mysql/howto-server-parameters)一文。
* 查看[有关使用 Azure 数据库迁移服务的先决条件的文章概述](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 请参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
