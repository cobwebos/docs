---
title: 排查 Azure SQL 数据库的常见连接问题
description: 提供步骤来排查 Azure SQL 数据库连接问题，并解决其他的 Azure SQL 数据库或 Azure SQL 托管实例特定问题
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2020
ms.openlocfilehash: aa4bcee7a2eaf5e6ec11b9066ed6eca6b33bdba1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91284113"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>排查 Azure SQL 数据库和 Azure SQL 托管实例的连接问题和其他问题
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

与 Azure SQL 数据库或 Azure SQL 托管实例连接失败时，你会收到错误消息。 连接问题可能由以下原因导致：重新配置、防火墙设置、连接超时、登录信息不正确，或无法在[应用程序设计](develop-overview.md)过程中应用最佳做法和设计准则。 此外，如果达到了某些 Azure SQL 数据库或 SQL 托管实例资源的最大限制，则无法再连接。

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>暂时性故障错误消息（40197、40613 等）

Azure 基础结构能够在 SQL 数据库服务中出现大量工作负荷时动态地重新配置服务器。  此动态行为可能会导致客户端程序失去其与数据库或实例的连接。 此类错误情况称为 *暂时性故障*。 之所以会发生数据库重新配置事件是因为，有计划内事件（例如，软件升级）或计划外事件（例如，进程故障或负载均衡）。 大多数重新配置事件的生存期通常较短，应在最多 60 秒内完成。 但是，这些事件偶尔可能需要更长时间才能完成，例如当大型事务导致长时间运行的恢复时。 下表列出了在连接到 SQL 数据库时应用程序可能会收到的各种暂时性错误

### <a name="list-of-transient-fault-error-codes"></a>暂时性故障错误代码的列表

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| 4060 |16 |无法打开该登录请求的数据库“%.&#x2a;ls”。 登录失败。 有关详细信息，请参阅[错误 4000 到 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |该服务在处理你的请求时遇到错误。 请重试。 错误代码 %d。<br/><br/>当服务由于软件或硬件升级、硬件故障或任何其他故障转移问题而关闭时，将收到此错误。 错误 40197 的消息中嵌入的错误代码 (%d) 提供有关所发生的故障或故障转移类型的其他信息。 错误 40197 的消息中嵌入的错误代码的一些示例有 40020、40143、40166 和 40540。<br/><br/>重新连接会将你自动连接到数据库的正常运行副本。 应用程序必须捕获错误 40197、记录该消息中嵌入的错误代码 (%d) 以供进行故障排除，然后尝试重新连接到 SQL 数据库，直到资源可用且再次建立连接为止。 有关详细信息，请参阅[暂时性错误](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)。|
| 40501 |20 个 |服务当前正忙。 请在 10 秒钟后重试请求。 事件 ID：%ls。 代码：%d。 有关详细信息，请参阅： <br/>&bull; &nbsp;[逻辑 SQL Server 资源限制](resource-limits-logical-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。|
| 40613 |17 |数据库“%.&#x2a;ls”（在服务器“%.&#x2a;ls”上）当前不可用。 请稍后重试连接。 如果问题仍然存在，请与客户支持人员联系，并向其提供“%.&#x2a;ls”的会话跟踪 ID。<br/><br/> 如果已建立到数据库的现有专用管理员连接 (DAC)，则可能发生此错误。 有关详细信息，请参阅[暂时性错误](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults)。|
| 49918 |16 |无法处理请求。 没有足够的资源来处理请求。<br/><br/>服务当前正忙。 请稍后重试请求。 有关详细信息，请参阅： <br/>&bull; &nbsp;[逻辑 SQL Server 资源限制](resource-limits-logical-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。 |
| 49919 |16 |无法处理创建或更新请求。 订阅“%ld”有太多创建或更新操作正在进行。<br/><br/>服务正忙于为订阅或服务器处理多个创建或更新请求。 为了优化资源，当前阻止了请求。 请查询 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以了解挂起的操作。 请等到挂起的创建或更新请求完成后，或删除其中一个挂起的请求，再重试请求。 有关详细信息，请参阅： <br/>&bull; &nbsp;[逻辑 SQL Server 资源限制](resource-limits-logical-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。 |
| 49920 |16 |无法处理请求。 订阅“%ld”有太多操作正在进行。<br/><br/>服务正忙于为此订阅处理多个请求。 为了优化资源，当前阻止了请求。 请查询 [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) 以了解操作状态。 请等到挂起的请求完成，或删除其中一个挂起的请求，然后重试请求。 有关详细信息，请参阅： <br/>&bull; &nbsp;[逻辑 SQL Server 资源限制](resource-limits-logical-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。 |
| 4221 |16 |由于等待“HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING”的时间过长，登录以读取次要副本失败。 副本不可用于登录，因为回收副本时缺少正在进行中的事务的行版本。 可以通过回滚或提交主要副本上的活动事务来解决此问题。 通过避免在主要副本上长时间写入事务，可以将此状况的发生次数降到最低。 |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 查看 [Microsoft Azure 服务仪表板](https://azure.microsoft.com/status)以了解应用程序报告错误时出现的任何已知中断。
2. 连接到云服务的应用程序（如 Azure SQL 数据库）应期望定期重新配置事件并实施重试逻辑来处理这些错误，而不是将它们作为应用程序错误展现给用户。
3. 由于数据库即将达到其资源限制，因此错误看起来像是暂时性连接问题。 请参阅[资源限制](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached)。
4. 如果连接问题继续存在，或者应用程序发生错误的持续时间超过 60 秒或在特定的一天中看到错误多次发生，请通过在 [Azure 支持](https://azure.microsoft.com/support/options)网站上选择“**获取支持**”提出 Azure 支持请求。

#### <a name="implementing-retry-logic"></a>实现重试逻辑

强烈建议客户端程序包含重试逻辑，以便它可以提供一段时间来让暂时性故障纠正自身，并尝试重建连接。  我们建议在第一次重试前延迟 5 秒钟。 如果在少于 5 秒的延迟后重试，云服务有超载的风险。 对于后续的每次重试，延迟应以指数级增大，最大值为 60 秒。

有关重试逻辑的代码示例，请参阅：

- [Connect resiliently to SQL with ADO.NET（使用 ADO.NET 弹性连接到 SQL）](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [使用 PHP 弹性连接到 SQL](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

有关在应用程序中处理暂时性错误的其他信息，请查看[排查 SQL 数据库的暂时性连接错误](troubleshoot-common-connectivity-issues.md)

[连接池 (ADO.NET)](https://msdn.microsoft.com/library/8xx3tyca.aspx) 中提供了有关使用 ADO.NET 的客户端的*阻塞期*的说明。

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>与服务器建立连接时，出现网络相关或特定于实例的错误

如果应用程序无法连接到服务器，则会出现此问题。

若要解决此问题，请尝试执行[解决常见连接问题的步骤](#steps-to-fix-common-connection-issues)部分所述的步骤（按所述顺序执行）。

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>找不到或无法访问服务器/实例（错误 26、40、10053）

### <a name="error-26-error-locating-server-specified"></a>错误 26：查找指定的服务器时出错

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>错误 40：无法与服务器建立连接

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>错误 10053：在接收来自服务器的结果时发生传输级错误

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

如果应用程序无法连接到服务器，则会出现这些问题。

若要解决这些问题，请尝试执行[解决常见连接问题的步骤](#steps-to-fix-common-connection-issues)部分所述的步骤（按所述顺序执行）。

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>由于防火墙问题而无法连接到服务器

### <a name="error-40615-cannot-connect-to--servername-"></a>错误 40615：无法连接到 <服务器名称>

若要解决此问题，请[通过 Azure 门户在 SQL 数据库上配置防火墙设置](firewall-configure.md)。

### <a name="error-5-cannot-connect-to--servername-"></a>错误 5：无法连接到 <服务器名称>

若要解决此问题，请在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 1433。

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>无法登录到服务器（错误 18456、40531）

### <a name="login-failed-for-user--user-name-"></a>用户 '< 用户名>' 登录失败

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

若要解决此问题，请让服务管理员为你提供有效的用户名和密码。

服务管理员通常可使用以下步骤添加登录凭据：

1. 使用 SQL Server Management Studio (SSMS) 登录到服务器。
2. 运行以下 SQL 查询，以检查是否已禁用登录名：

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. 如果禁用了相应的名称，请使用以下语句来启用它：

   ```sql
   Alter login <User name> enable
   ```

4. 如果该 SQL 登录用户名不存在，请执行以下步骤创建该用户名：

   1. 在 SSMS 中，双击“安全性”将其展开。
   2. 右键单击“登录”，然后选择“新建登录名”。
   3. 在生成的带占位符的脚本中，编辑并运行以下 SQL 查询：

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. 双击“数据库”。
6. 选择要授予用户权限的数据库。
7. 双击“安全性”。
8. 右键单击“用户”，然后选择“新建用户”。
9. 在生成的带占位符的脚本中，编辑并运行以下 SQL 查询：

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > 也可使用 `sp_addrolemember` 将特定的用户映射到特定的数据库角色。

有关详细信息，请参阅[在 Azure SQL 数据库中管理数据库和登录名](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)。

## <a name="connection-timeout-expired-errors"></a>连接超时过期错误

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904)：连接超时过期

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904)：超时过期

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException：基础提供程序在打开时失败

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>无法连接到 <服务器名称>

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

这些异常可能是连接或查询问题造成的。 若要确认此错误是否由连接问题造成，请参阅[确认错误是否由连接问题造成](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)。

发生连接超时的原因是应用程序无法连接到服务器。 若要解决此问题，请尝试执行[解决常见连接问题的步骤](#steps-to-fix-common-connection-issues)部分所述的步骤（按所述顺序执行）。

## <a name="resource-governance-errors"></a>资源调控错误

### <a name="error-10928-resource-id-d"></a>错误10928：资源 ID：% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

若要解决此问题，请尝试以下方法之一：

- 检查是否存在长时间运行的查询。

  > [!NOTE]
  > 这是最简单的方法，但不一定能够解决问题。

1. 运行以下 SQL 查询来检查 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 视图，以确定是否存在任何阻塞的请求：

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 确定最前面的阻塞查询的**输入缓冲区**。
3. 优化最前面的阻塞查询。

   有关详细的故障排除过程，请参阅[我的查询是否在云中正常运行？](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)。

如果数据库在处理阻塞和长时间运行的查询时一直达到其限制，请考虑升级到具有更多资源 [版本](https://azure.microsoft.com/pricing/details/sql-database/)) 的版本。

有关动态管理视图的详细信息，请参阅[系统动态管理视图](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。

有关数据库限制的详细信息，请参阅  [服务器的 SQL 数据库资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-10929-resource-id-1"></a>错误10929：资源 ID：1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>错误40501：该服务当前正忙

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

这是一个引擎限制错误，表示超过了资源限制。

有关资源限制的详细信息，请参阅 [逻辑 SQL server 资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server)。

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>错误40544：数据库已达到其大小配额

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

当数据库达到其大小配额时会发生此错误。

以下步骤可帮助你解决此问题，或提供更多的选项：

1. 使用 Azure 门户中的仪表板检查数据库的当前大小。

   > [!NOTE]
   > 若要识别哪些表消耗了最多的空间，因此确定可能需要清理哪些表，请运行以下 SQL 查询：

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. 如果当前大小未超过所用版本支持的最大大小，你可以使用 ALTER DATABASE 来增大 MAXSIZE 设置。
3. 如果数据库已超过所用版本支持的最大大小，请尝试以下一个或多个步骤：

   - 执行一般的数据库清理活动。 例如，使用截断/删除操作清理不需要的数据，或者使用 SQL Server Integration Services (SSIS) 或批量复制程序 (bcp) 实用工具移出数据。
   - 请将数据分区或删除、删除索引或查阅文档以找到可能的解决方案。
   - 有关数据库缩放的信息，请参阅[缩放单一数据库资源](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale)和[缩放弹性池资源](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale)。

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>错误40549：会话已终止，因为你有长时间运行的事务

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

如果反复出现此错误，请尝试执行以下步骤来解决问题：

1. 检查 sys.dm_exec_requests 视图，以确定是否有任何打开的会话对 total_elapsed_time 列使用了较大的值。 运行以下 SQL 脚本来执行此项检查：

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. 确定长时间运行的查询的输入缓冲区。
3. 优化该查询。

还应考虑批处理查询。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance)。

有关详细的故障排除过程，请参阅[我的查询是否在云中正常运行？](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)。

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>错误40551：由于过度使用 TEMPDB，已终止该会话

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

若要解决此问题，请执行以下步骤：

1. 更改查询以减少临时表空间的用量。
2. 删除不再需要的临时对象。
3. 截断表，或删除未使用的表。

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>错误40552：由于过度使用事务日志空间，已终止该会话

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

若要解决此问题，请尝试执行以下方法：

- 此问题可能是插入、更新或删除操作造成的。
通过立即实施批处理或拆分为多个较小的事务，来尝试减少操作的行数。
- 此问题可能是索引重建操作造成的。 若要解决此问题，请确保表中受影响的行数 * (所更新字段的平均字节大小 + 80) < 2 GB。

  > [!NOTE]
  > 对于索引重建，所更新字段的平均大小应替换为平均索引大小。

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>错误40553：由于过度使用内存，已终止该会话

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

若要解决此问题，请尝试优化查询。

有关详细的故障排除过程，请参阅[我的查询是否在云中正常运行？](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)。

### <a name="table-of-additional-resource-governance-error-messages"></a>其他资源调控错误消息表

| 错误代码 | 严重性 | 说明 |
| ---:| ---:|:--- |
| 10928 |20 |资源 ID：%d。 数据库的 %s 限制是 %d 且已达到该限制。 有关详细信息，请参阅[单一数据库和共用数据库的 SQL 数据库资源限制](resource-limits-logical-server.md)。<br/><br/>资源 ID 指示已达到限制的资源。 对于工作线程，资源 ID = 1。 对于会话，资源 ID = 2。<br/><br/>有关此错误以及如何解决此错误的详细信息，请参阅： <br/>&bull; &nbsp;[逻辑 SQL Server 资源限制](resource-limits-logical-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。 |
| 10929 |20 |资源 ID：%d。 %s 最小保证为 %d，最大限制为 %d，数据库的当前使用率为 %d。 但是，服务器当前太忙，无法支持针对该数据库的数目大于 %d 的请求。 资源 ID 指示已达到限制的资源。 对于工作线程，资源 ID = 1。 对于会话，资源 ID = 2。 有关详细信息，请参阅： <br/>&bull; &nbsp;[逻辑 SQL Server 资源限制](resource-limits-logical-server.md)<br/>&bull; &nbsp;[单一数据库的基于 DTU 的限制](service-tiers-dtu.md)<br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。 <br/>否则，请稍后再试。 |
| 40544 |20 |数据库已达到大小配额。 请将数据分区或删除、删除索引或查阅文档以找到可能的解决方案。 有关数据库缩放的信息，请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。|
| 40549 |16 |由于您有长时间运行的事务，已终止会话。 请尝试缩短您的事务的运行时间。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](../performance-improve-use-batching.md)。|
| 40550 |16 |由于会话获取的锁过多，已终止该会话。 请尝试在单个事务中读取或修改更少的行。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](../performance-improve-use-batching.md)。|
| 40551 |16 |由于过度使用 `TEMPDB`，已终止该会话。 请尝试修改您的查询以减少使用临时表空间。<br/><br/>如果在使用临时对象，则通过在会话不再需要临时对象后删除这些临时对象，可以节省 `TEMPDB` 数据库中的空间。 若要详细了解如何在 SQL 数据库中使用 tempdb，请参阅 [SQL 数据库中的 Tempdb 数据库](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)。|
| 40552 |16 |由于过度使用事务日志空间，已终止该会话。 请尝试在单个事务中修改更少的行。 有关批处理的信息，请参阅[如何使用批处理来改善 SQL 数据库应用程序的性能](../performance-improve-use-batching.md)。<br/><br/>如果在使用 `bcp.exe` 实用程序或 `System.Data.SqlClient.SqlBulkCopy` 类执行大容量插入，则可尝试使用 `-b batchsize` 或 `BatchSize` 选项来限制在各事务中复制到服务器的行数。 如果正在使用 `ALTER INDEX` 语句重新生成索引，请尝试使用 `REBUILD WITH ONLINE = ON` 选项。 有关 vCore 购买模型的事务日志大小的信息，请参阅： <br/>&bull; &nbsp;[单一数据库的基于 vCore 的限制](resource-limits-vcore-single-databases.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)<br/>&bull; &nbsp;[Azure SQL 托管实例资源限制](../managed-instance/resource-limits.md)。|
| 40553 |16 |由于过度使用内存，已终止该会话。 请尝试修改您的查询以处理更少的行。<br/><br/>在 Transact-SQL 代码中减少 `ORDER BY` 和 `GROUP BY` 操作的数目可以降低查询的内存需求。 有关数据库缩放的信息，请参阅[缩放单一数据库资源](single-database-scale.md)和[缩放弹性池资源](elastic-pool-scale.md)。|

## <a name="elastic-pool-errors"></a>弹性池错误

以下错误与创建和使用弹性池有关：

| 错误代码 | 严重性 | 说明 | 纠正措施 |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |弹性池已达到其存储限制。 弹性池的存储使用不能超过 (%d) MB。 到达弹性池的存储限制时，尝试向数据库写入数据。 有关资源限制的信息，请参阅： <br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)。 <br/> |在可能的情况下，考虑增加弹性池的 DTU 数并/或将存储添加到弹性池，以便提高其存储限制、减少弹性池中各数据库使用的存储，或者从弹性池中删除数据库。 有关弹性池缩放的信息，请参阅[缩放弹性池资源](elastic-pool-scale.md)。|
| 10929 | 16 |%s 最小保证为 %d，最大限制为 %d，数据库的当前使用率为 %d。 但是，服务器当前太忙，无法支持针对该数据库的数目大于 %d 的请求。 有关资源限制的信息，请参阅： <br/>&bull; &nbsp;[弹性池的基于 DTU 的限制](resource-limits-dtu-elastic-pools.md)<br/>&bull; &nbsp;[弹性池的基于 vCore 的限制](resource-limits-vcore-elastic-pools.md)。 <br/> 否则，请稍后再试。 每个数据库的 DTU/vCore 最小值；每个数据库的 DTU/vCore 最大值。 弹性池中所有数据库上尝试的并发辅助进程（请求）总数超过池限制。 |在可能的情况下，考虑增加弹性池的 DTU 数或 vCores 数，以便提高其辅助角色限制，或者从弹性池中删除数据库。 |
| 40844 | 16 |弹性池中数据库“%ls”（位于服务器“%ls”上）是“%ls”版本的数据库，不能有连续的复制关系。  |空值 |
| 40857 | 16 |找不到服务器“%ls”的弹性池，弹性池名称:“%ls”。 指定的弹性池在指定的服务器中不存在。 | 提供有效的弹性池名称。 |
| 40858 | 16 |弹性池“%ls”已存在于服务器“%ls”中。 指定的弹性池已存在于指定的服务器中。 | 提供新弹性池名称。 |
| 40859 | 16 |弹性池不支持服务层级“%ls”。 进行弹性池预配时，不支持指定服务层级。 |提供正确的版本，或者将服务层级留空以使用默认服务层级。 |
| 40860 | 16 |弹性池“%ls”和服务目标“%ls”的组合无效。 仅当资源类型指定为“ElasticPool”时，才能一起指定弹性池和服务层级。 |指定正确的弹性池和服务层级组合。 |
| 40861 | 16 |数据库版本“%.*ls”必须与弹性池服务层级“%.* ls”相同。 数据库版本不同于弹性池服务层级。 |请勿指定不同于弹性池服务层级的数据库版本。  请注意，数据库版本不需要指定。 |
| 40862 | 16 |如果指定了弹性池服务目标，则必须指定弹性池名称。 弹性池服务目标没有唯一地标识弹性池。 |如果使用弹性池服务目标，则指定弹性池名称。 |
| 40864 | 16 |对于服务层级“%.*ls”来说，弹性池的 DTU 数必须至少为 (%d) 个 DTU。 尝试将弹性池的 DTU 数设置为最小限制以下。 |重新尝试将弹性池的 DTU 数至少设置为最小限制。 |
| 40865 | 16 |对于服务层级“%.*ls”来说，弹性池的 DTU 数不能超过 (%d) 个 DTU。 尝试将弹性池的 DTU 数设置为高出最大限制。 |重新尝试将弹性池的 DTU 数设置为不超过最大限制。 |
| 40867 | 16 |对于服务层级“%.*ls”来说，每个数据库的 DTU 最大值必须至少为 (%d)。 尝试将每个数据库的 DTU 最大值设置为低于支持的限制。 | 考虑使用支持所需设置的弹性池服务层级。 |
| 40868 | 16 |对于服务层级“%.*ls”来说，每个数据库的 DTU 最大值不能超过 (%d)。 尝试将每个数据库的 DTU 最大值设置为超出支持的限制。 | 考虑使用支持所需设置的弹性池服务层级。 |
| 40870 | 16 |对于服务层级“%.*ls”来说，每个数据库的 DTU 最小值不能超过 (%d)。 尝试将每个数据库的 DTU 最小值设置为超出支持的限制。 | 考虑使用支持所需设置的弹性池服务层级。 |
| 40873 | 16 |数据库数目 (%d) 和每个数据库的 DTU 最小值 (%d) 不能超过弹性池的 DTU 数 (%d)。 尝试指定弹性池中数据库的 DTU 最小值，该最小值超出弹性池的 DTU 数。 | 考虑增加弹性池的 DTU 数，或者降低每个数据库的 DTU 最小值，或者降低弹性池中数据库的数目。 |
| 40877 | 16 |除非弹性池不含任何数据库，否则不能将其删除。 弹性池包含一个或多个数据库，因此无法将其删除。 |删除弹性池中的数据库，以便删除弹性池。 |
| 40881 | 16 |弹性池“%.*ls”已达到其数据库计数限制。  对于 DTU 数为 (%d) 的弹性池，弹性池的数据库计数限制不能超出 (%d)。 达到弹性池的数据库计数限制时，尝试创建数据库或将其添加到弹性池。 | 在可能的情况下，考虑增加弹性池的 DTU 数，以便提高其数据库限制，或者从弹性池中删除数据库。 |
| 40889 | 16 |弹性池“%.*ls”的 DTU 数或存储限制不能降低，因为这样就无法为其数据库提供足够的存储空间。 尝试将弹性池的存储限制降低到其存储使用量以下。 | 考虑降低弹性池中各个数据库的存储使用量，或者从池中删除数据库以降低其 DTU 数或存储限制。 |
| 40891 | 16 |每个数据库的 DTU 最小值 (%d) 不能超过每个数据库的 DTU 最大值 (%d)。 尝试将每个数据库的 DTU 最小值设置为高于每个数据库的 DTU 最大值。 |确保每个数据库的 DTU 最小值不超过每个数据库的 DTU 最大值。 |
| TBD | 16 |弹性池中单个数据库的存储大小不能超过“%.*ls”服务层级弹性池所允许的最大大小。 数据库的最大大小超过弹性池服务层级允许的最大大小。 |将数据库的最大大小设置为处于弹性池服务层级允许的最大大小限制范围内。 |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>无法打开登录名请求的数据库 "master"。 登录失败

之所以出现此问题，是因为该帐户无权访问 master 数据库。 但默认情况下，SQL Server Management Studio (SSMS) 会尝试连接到 master 数据库。

若要解决此问题，请执行以下步骤：

1. 在 SSMS 的登录屏幕上选择“选项”，然后选择“连接属性”。 
2. 在“连接到数据库”字段中，输入用户的默认数据库名称作为默认登录数据库，然后选择“连接”。 

   ![连接属性](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>确认错误是否由连接问题造成

若要确认某个错误是否由连接问题造成，请查看帧的堆栈跟踪，其中会显示打开连接的调用，如下所示（请注意对 **SqlConnection** 类的引用）：

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

如果异常是由查询问题触发的，则你会看到如下所示的调用堆栈（请注意对 **SqlCommand** 类的引用）。 对于这种情况，请[优化查询](https://docs.microsoft.com/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud)。

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

有关优化性能的更多指导，请参阅以下资源：

- [如何维护 Azure SQL 索引和统计信息](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [手动优化 Azure SQL 数据库中的查询性能](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
- [使用动态管理视图监视 Azure SQL 数据库的性能](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
- [在 Azure SQL 数据库中操作 Query Store](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>解决常见连接问题的步骤

1. 确保已在应用程序服务器上启用 TCP/IP 作为客户端协议。 有关详细信息，请参阅[配置客户端协议](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols)。 在未安装 SQL 工具的应用程序服务器上，运行 cliconfg.exe（SQL Server 客户端网络实用工具）来验证是否已启用 TCP/IP。
2. 检查应用程序的连接字符串，确保配置正确。 例如，确保连接字符串指定了正确的端口 (1433) 和完全限定的服务器名称。
请参阅[获取连接信息](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information)。
3. 尝试增大连接超时值。 我们建议至少使用 30 秒连接超时。
4. 使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)、UDL 文件、ping 或 telnet 来测试应用程序服务器与 Azure SQL 数据库之间的连接。 有关详细信息，请参阅[排查连接问题](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)和[连接问题的诊断](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics)。

   > [!NOTE]
   > 作为故障排除步骤，还可以在不同的客户端计算机上测试连接。

5. 最佳做法是确保实施重试逻辑。 有关重试逻辑的详细信息，请参阅[排查 SQL 数据库的暂时性故障和连接错误](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues)。

如果这些步骤无法解决问题，请尝试收集更多的数据，然后联系支持人员。 如果应用程序是云服务，请启用日志记录。 此步骤会返回发生失败时的 UTC 时间戳。 此外，SQL 数据库会返回跟踪 ID。 [Microsoft 客户支持服务](https://azure.microsoft.com/support/options/) 可使用此信息。

有关如何启用日志记录的详细信息，请参阅[为 Azure 应用服务中的应用启用诊断日志记录](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/)。

## <a name="next-steps"></a>后续步骤

- [Azure SQL 数据库连接体系结构](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
- [Azure SQL 数据库和 Azure Synapse Analytics 网络访问控制](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)
