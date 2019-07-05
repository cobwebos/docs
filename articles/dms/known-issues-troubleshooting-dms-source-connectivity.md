---
title: 有关已知问题/与连接到源数据库的 Azure 数据库迁移服务相关联的错误故障排除文章 |Microsoft Docs
description: 了解有关如何解决与连接到源数据库的 Azure 数据库迁移服务关联的已知的问题/错误。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: a4ebd1d4c85631cc6ebc1f5787e7545b78d62b5e
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462845"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>连接到源数据库时，DMS 错误进行故障排除

以下文章提供了详细介绍了如何解决连接到您的源数据库的 Azure 数据库迁移服务 (DMS) 时可能遇到的潜在问题。 下面各部分列出与特定类型的源数据库，列出该错误可能会遇到以及详细信息和有关如何排除连接故障的信息的链接。

## <a name="sql-server"></a>SQL Server

与连接到源 SQL Server 数据库相关联的潜在问题和如何解决它们提供下表中。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称正确，以及该 SQL Server 配置为允许远程连接。<br> | 如果服务找不到源服务器，会发生此错误。 若要解决此问题，请参阅文章[错误时使用动态端口连接到源 SQL Server 实例或命名实例](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)。 |
| **错误 53** -SQL 连接失败。 (同样，对于错误代码 1、 2、 5、 53、 233、 258、 1225，11001)<br><br> | 如果该服务无法连接到源服务器，会发生此错误。 若要解决此问题，请参阅以下资源，并再试一次。 <br><br>  [若要排查连接问题的交互式用户指南](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [将 SQL Server 迁移到 Azure SQL 数据库的先决条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [将 SQL Server 迁移到 Azure SQL 数据库托管实例的先决条件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **错误 18456** -登录失败。<br> | 如果该服务无法连接到源数据库使用提供的 T-SQL 的凭据，将发生此错误。 若要解决此问题，请验证输入的凭据。 此外可以指[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)表，或在这下面的说明中列出的故障排除文档，然后重试。 |
| 格式不正确的 AccountName 值{0}提供。 AccountName 的预期的格式是域名 \ 用户名<br> | 如果用户选择 Windows 身份验证，但提供了无效的格式中的用户名，则会发生此错误。 若要解决此问题，请提供格式正确的用户名为 Windows 身份验证或选择**SQL 身份验证**。 |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

连接到源 AWS RDS MySQL 数据库关联的潜在问题和如何解决它们提供下表中。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| **错误 [2003]** [HY000] 的连接失败。 错误 [HY000] [MySQL] [ODBC x.x(w) 驱动程序] 不能连接到 MySQL 服务器上 {server} (10060) | 如果 MySQL ODBC 驱动程序无法连接到源服务器，会发生此错误。 若要解决此问题，请参阅此表下面的说明中列出的故障排除文档并再试一次。<br> |
| **错误 [2005]** [HY000] 的连接失败。 错误 [HY000] [MySQL] [ODBC x.x(w) 驱动程序] 未知 MySQL 服务器主机 {server} | 如果服务找不到源主机上 rds。，发生此错误 此问题可能是因为列出的源不存在，或者与 RDS 基础结构问题。 若要解决此问题，请参阅此表下面的说明中列出的故障排除文档并再试一次。<br> |
| **错误 [1045]** [HY000] 的连接失败。 错误 [HY000] [MySQL] [ODBC x.x(w) 驱动程序] 访问被拒绝的用户 {user}'@'{server} (使用密码：是) | 如果 MySQL ODBC 驱动程序无法连接到源服务器由于凭据无效，则会发生此错误。 验证你输入的凭据。 如果问题仍然存在，请验证该源计算机有正确的凭据。 您可能需要重置在控制台中的密码。 如果仍遇到问题，请参阅此表下面的说明中列出的故障排除文档并再试一次。<br> |
| **错误 [9002]** [HY000] 的连接失败。 错误 [HY000] [MySQL] [ODBC x.x(w) 驱动程序] 的连接字符串可能不正确。 访问门户的引用。| 如果连接由于连接字符串的问题而失败的原因，会发生此错误。 验证提供的连接字符串有效。 若要解决此问题，请参阅此表下面的说明中列出的故障排除文档并再试一次。<br> |
| **二进制日志记录时出错。变量 binlog_format 的值 {value}。请将其更改为行。** | 如果二进制日志记录; 中没有错误，发生此错误变量 binlog_format 具有错误的值。 若要解决此问题，将参数组中的 binlog_format 更改为行，然后重新启动实例。 有关详细信息，请参阅[二进制日志记录选项和变量](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)或[AWS RDS MySQL 数据库日志文件文档](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)。<br> |

> [!NOTE]
> 有关故障排除与连接到源 AWS RDS MySQL 数据库相关的问题的详细信息，请参阅以下资源：
> * [Amazon RDS 连接问题的疑难解答](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解决连接到我 Amazon RDS 的数据库实例的问题？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

连接到源 AWS RDS PostgreSQL 数据库关联的潜在问题和如何解决它们提供下表中。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| **错误 [101]** [08001] 的连接失败。 错误 [08001] 超时时间已到。 | 如果语言 Postgres 驱动程序无法连接到源服务器，会发生此错误。 若要解决此问题，请参阅此表下面的说明中列出的故障排除文档并再试一次。 |
| **错误：参数 wal_level 具有值 {value}。请将其更改为 'logical' 以允许复制。** | 如果参数 wal_level 具有错误的值，则会发生此错误。 若要解决此问题，将参数组中的 rds.logical_replication 更改为 1，然后重新启动实例。 有关详细信息，请参阅[的必备组件迁移到 Azure PostgreSQL 使用 DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)或[Amazon RDS 上的 PostgreSQL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)。 |

> [!NOTE]
> 有关故障排除与连接到源 AWS RDS PostgreSQL 数据库相关的问题的详细信息，请参阅以下资源：
> * [Amazon RDS 连接问题的疑难解答](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解决连接到我 Amazon RDS 的数据库实例的问题？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

与连接到源 AWS RDS SQL Server 数据库相关联的潜在问题和如何解决它们提供下表中。

| 错误         | 原因和疑难解答详细信息 |
| ------------- | ------------- |
| **错误 53** -SQL 连接失败。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 服务器找不到或不是可访问。 验证实例名称正确，以及该 SQL Server 配置为允许远程连接。 （提供程序：命名管道提供程序，错误：40-无法打开到 SQL Server 连接 | 如果该服务无法连接到源服务器，会发生此错误。 若要解决此问题，请参阅此表下面的说明中列出的故障排除文档并再试一次。 |
| **错误 18456** -登录失败。 用户 {user} 失败的登录名 | 如果服务无法使用提供的 T-SQL 的凭据连接到源数据库，会发生此错误。 若要解决此问题，请验证输入的凭据。 此外可以指[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或在这下面的说明中列出的故障排除文档表，然后重试。 |
| **错误 87** -连接字符串无效。 建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。 验证实例名称正确，以及该 SQL Server 配置为允许远程连接。 （提供程序：SQL 网络接口，错误：25 的连接字符串不是有效) | 如果服务由于无效的连接字符串而无法连接到源服务器，会发生此错误。 若要解决此问题，验证提供连接字符串。 如果问题仍然存在，请参阅此表下面的说明中列出的故障排除文档并再试一次。 |
| **错误-不受信任的服务器证书。** 已成功与服务器建立连接，但然后在登录过程时出错。 （提供程序：SSL 提供程序，错误：0-证书链由颁发不受信任的颁发机构。） | 如果使用的证书不受信任，则会发生此错误。 若要解决此问题，需要查找可为受信任，，然后在服务器上启用它的证书。 或者，可以选择连接时信任证书选项。 只有在您熟悉使用的证书并信任它，请执行此操作。 <br> 使用自签名的证书进行加密的 SSL 连接不提供强安全性--它们容易受到人为干预攻击。 不要依赖于使用生产环境中的自签名的证书的 SSL 或连接到 internet 的服务器上。 <br> 有关详细信息，请参阅[通过 Microsoft SQL Server 数据库实例使用 SSL](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)或[教程：RDS SQL Server 迁移到 Azure 中使用 DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)。 |
| **错误 300** -用户没有所需的权限。 VIEW SERVER STATE 权限拒绝了对对象 {server} 数据库 {数据库} | 如果用户没有权限来执行迁移，则会发生此错误。 若要解决此问题，请参阅[GRANT 服务器权限的 Transact SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017)或[教程：RDS SQL Server 迁移到 Azure 中使用 DMS](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)的更多详细信息。 |

> [!NOTE]
> 有关故障排除与连接到源 AWS RDS SQL Server 相关的问题的详细信息，请参阅以下资源：
>
> * [解决与 SQL Server 的连接错误](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [如何解决连接到我 Amazon RDS 的数据库实例的问题？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>已知问题

* [联机迁移到 Azure SQL 数据库的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [在线迁移到 Azure Database for MySQL 的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [在线迁移到 Azure Database for PostgreSQL 的已知的问题/迁移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>后续步骤

* 查看文章[Azure 数据库迁移服务 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 查看文章[如何配置服务器参数中 Azure Database for MySQL 通过使用 Azure 门户](https://docs.microsoft.com/azure/mysql/howto-server-parameters)。
* 查看文章[使用 Azure 数据库迁移服务的先决条件概述](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 请参阅[有关使用 Azure 数据库迁移服务的常见问题解答](https://docs.microsoft.com/azure/dms/faq)。
