---
title: 连接源数据库时出现问题
titleSuffix: Azure Database Migration Service
description: 了解如何排查将 Azure 数据库迁移服务连接到源数据库时出现的常见已知问题/错误。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 2db941edef93b1e836e82753a6d6016adb977e65
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322591"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>排查连接到源数据库时出现的 DMS 错误

以下文章详细介绍了如何解决将 Azure 数据库迁移服务 (DMS) 连接到源数据库时可能会遇到的问题。 以下每个部分与特定类型的源数据库相关，列出了可能会遇到的错误，以及有关如何排查连接问题的信息的链接。

## <a name="sql-server"></a>SQL Server

下表提供了连接到源 SQL Server 数据库时可能会出现的问题及其解决方法。

| 错误         | 原因和故障排除详细信息 |
| ------------- | ------------- |
| SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称是否正确，以及 SQL Server 是否已配置为允许远程连接。<br> | 如果服务找不到源服务器，则会发生此错误。 若要解决该问题，请参阅[使用动态端口或命名实例连接到源 SQL Server 时出错](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)。 |
| **错误 53** - SQL 连接失败。 （同样适用于错误代码 1、2、5、53、233、258、1225、11001）<br><br> | 如果服务无法连接到源服务器，则会发生此错误。 若要解决该问题，请参考以下资源，然后重试。 <br><br>  [有关排查连接问题的交互式用户指南](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [将 SQL Server 迁移到 Azure SQL 数据库的先决条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [将 SQL Server 迁移到 Azure SQL 托管实例的先决条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **错误 18456** - 登录失败。<br> | 如果服务无法使用提供的 T-SQL 凭据连接到源数据库，则会发生此错误。 若要解决该问题，请检查输入的凭据。 还可以参考 [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 或此表下面的注释中列出的故障排除文档，然后重试。 |
| 提供了格式不当的 AccountName 值“{0}”。 AccountName 的所需格式为“域名\用户名”<br> | 如果用户选择了 Windows 身份验证但提供了格式无效的用户名，则会发生此错误。 若要解决该问题，请为 Windows 身份验证提供正确格式的用户名，或选择“SQL 身份验证”。**** |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

下表提供了连接到源 AWS RDS MySQL 数据库时可能会出现的问题及其解决方法。

| 错误         | 原因和故障排除详细信息 |
| ------------- | ------------- |
| **错误 [2003]** [HY000] - 连接失败。 错误 [HY000] [MySQL][ODBC x.x(w) 驱动程序] 无法连接到 '{server}' 上的 MySQL 服务器 (10060) | 如果 MySQL ODBC 驱动程序无法连接到源服务器，则会发生此错误。 若要解决该问题，请参考此表下面的注释中列出的故障排除文档，然后重试。<br> |
| **错误 [2005]** [HY000] - 连接失败。 错误 [HY000] [MySQL][ODBC x.x(w) 驱动程序] 未知的 MySQL 服务器主机 '{server}' | 如果服务在 RDS 上找不到源主机，则会发生此错误。 此问题的原因可能是列出的源不存在，或者 RDS 基础结构有问题。 若要解决该问题，请参考此表下面的注释中列出的故障排除文档，然后重试。<br> |
| **错误 [1045]** [HY000] - 连接失败。 错误 [HY000] [MySQL][ODBC x.x(w) 驱动程序] 拒绝 '{server}' 上的用户 '{user}' 访问(使用密码:是) | 如果由于凭据无效，导致 MySQL ODBC 驱动程序无法连接到源服务器，则会发生此错误。 请检查输入的凭据。 如果该问题持续出现，请检查源计算机是否具有正确的凭据。 可能需要在控制台中重置密码。 如果依然遇到该问题，请参考此表下面的注释中列出的故障排除文档，然后重试。<br> |
| **错误 [9002]** [HY000] - 连接失败。 错误 [HY000] [MySQL][ODBC x.x(w) 驱动程序] 连接字符串可能不正确。 访问门户以获得参考信息。| 如果连接字符串问题导致连接失败，则会发生此错误。 请检查连接字符串是否有效。 若要解决该问题，请参考此表下面的注释中列出的故障排除文档，然后重试。<br> |
| **二进制日志记录出错。变量 binlog_format 的值为 '{value}'。请将其更改为 'row'。** | 如果二进制日志记录存在错误，则会发生此错误。变量 binlog_format 的值不正确。 若要解决该问题，请将参数组中的 binlog_format 更改为 'ROW'，然后重新启动实例。 有关详细信息，请参阅[二进制日志记录选项和变量](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)或 [AWS RDS MySQL 数据库日志文件文档](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)。<br> |

> [!NOTE]
> 有关排查连接到源 AWS RDS MySQL 数据库时出现的问题的详细信息，请参阅以下资源：
> * [排查 Amazon RDS 连接问题](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解决连接到 Amazon RDS 数据库实例时出现的问题？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

下表提供了连接到源 AWS RDS PostgreSQL 数据库时可能会出现的问题及其解决方法。

| 错误         | 原因和故障排除详细信息 |
| ------------- | ------------- |
| **错误 [101]** [08001] - 连接失败。 错误 [08001] 已超时。 | 如果 Postgres 驱动程序无法连接到源服务器，则会发生此错误。 若要解决该问题，请参考此表下面的注释中列出的故障排除文档，然后重试。 |
| **错误：参数 wal_level 的值为 '{value}'。请将其更改为 'logical' 以允许复制。** | 如果参数 wal_level 的值不正确，则会发生此错误。 若要解决该问题，请将参数组中的 rds.logical_replication 更改为 1，然后重新启动实例。 有关详细信息, 请参阅[使用 DMS 迁移到 Azure PostgreSQL 的先决条件](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)或 [Amazon RDS 上的 PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)。 |

> [!NOTE]
> 有关排查连接到源 AWS RDS PostgreSQL 数据库时出现的问题的详细信息，请参阅以下资源：
> * [排查 Amazon RDS 连接问题](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解决连接到 Amazon RDS 数据库实例时出现的问题？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

下表提供了连接到源 AWS RDS SQL Server 数据库时可能会出现的问题及其解决方法。

| 错误         | 原因和故障排除详细信息 |
| ------------- | ------------- |
| **错误 53** - SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问该服务器。 验证实例名称是否正确，以及 SQL Server 是否已配置为允许远程连接。 （提供程序：命名管道提供程序，错误：40 - 无法与 SQL Server 建立连接） | 如果服务无法连接到源服务器，则会发生此错误。 若要解决该问题，请参考此表下面的注释中列出的故障排除文档，然后重试。 |
| **错误 18456** - 登录失败。 用户 '{user}' 登录失败 | 如果服务无法使用提供的 T-SQL 凭据连接到源数据库，则会发生此错误。 若要解决该问题，请检查输入的凭据。 还可以参考 [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 或此表下面的注释中列出的故障排除文档，然后重试。 |
| **错误 87** - 连接字符串无效。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称是否正确，以及 SQL Server 是否已配置为允许远程连接。 （提供程序：SQL 网络接口，错误：25 - 连接字符串无效） | 如果连接字符串无效，导致服务无法连接到源服务器，则会发生此错误。 若要解决该问题，请检查提供的连接字符串。 如果问题持续出现，请参考此表下面的注释中列出的故障排除文档，然后重试。 |
| **错误 - 服务器证书不受信任。** 已成功与服务器建立连接，但在登录过程中发生错误。 （提供程序：SSL 提供程序，错误：0 - 证书链是不受信任的颁发机构颁发的。） | 如果使用的证书不受信任，则会发生此错误。 若要解决该问题，需要找到可信的证书，然后在服务器上启用它。 或者，可以在连接时选择“信任证书”选项。 请仅在你熟悉所用的证书并且信任它时，才执行此操作。 <br> 使用自签名证书加密的 TLS 连接不提供强安全性 -- 它们容易受到中间人攻击。 请勿在生产环境中或在连接到 Internet 的服务器上依赖使用自签名证书的 TLS。 <br> 有关详细信息，请参阅[对 Microsoft SQL Server 数据库实例使用 SSL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) 或[教程：使用 DMS 将 RDS SQL Server 迁移到 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)。 |
| **错误 300** - 用户没有所需的权限。 已拒绝对对象 '{server}'，数据库 '{database}' 的 VIEW SERVER STATE 权限 | 如果用户无权执行迁移，则会发生此错误。 若要解决该问题，请参考[授予服务器权限 - Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) 或[教程：使用 DMS 将 RDS SQL Server 迁移到 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) 了解更多详细信息。 |

> [!NOTE]
> 有关排查连接到源 AWS RDS SQL Server 时出现的问题的详细信息，请参阅以下资源：
>
> * [解决 SQL Server 连接错误](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [如何解决连接到 Amazon RDS 数据库实例时出现的问题？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>已知问题

* [联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [在联机迁移到 Azure Database for MySQL 时存在的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [在联机迁移到 Azure Database for PostgreSQL 时存在的已知问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 参阅[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)一文。
* 查看[如何使用 Azure 门户在 Azure Database for MySQL 中配置服务器参数](https://docs.microsoft.com/azure/mysql/howto-server-parameters)一文。
* 查看[使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)一文。
* 参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
