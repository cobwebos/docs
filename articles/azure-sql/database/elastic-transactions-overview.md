---
title: '跨云数据库的分布式事务 (预览) '
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例的弹性数据库事务。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7e5dd5d8ddf8df507cebaaeba4a544f58250a891
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91975193"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>跨云数据库的分布式事务 (预览) 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL 数据库和 Azure SQL 托管实例的弹性数据库事务可让你运行跨多个数据库的事务。 弹性数据库事务适用于使用 ADO.NET 的 .NET 应用程序，并与使用 [system.web](https://msdn.microsoft.com/library/system.transactions.aspx) 类的熟悉的编程体验相集成。 若要获取该库，请参阅 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981)（Web 安装程序）。
除此之外，对于 Azure SQL 托管实例分布式事务可在 [transact-sql](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)中使用。

在本地，这种方案通常需要运行 Microsoft 分布式事务处理协调器 (MSDTC)。 由于 MSDTC 不适用于 Azure 中的平台即服务应用程序，因此，协调分布式事务的功能现在已直接集成到 SQL 数据库或托管实例中。 应用程序可以连接到任何数据库以启动分布式事务，其中一个数据库或服务器将以透明方式协调分布式事务，如下图所示。

在本文档中，术语 "分布式事务" 和 "弹性数据库事务" 被视为同义词，将使用 interchangably。

  ![使用弹性数据库事务在 Azure SQL 数据库中执行分布式事务 ][1]

## <a name="common-scenarios"></a>常见方案

弹性数据库事务可让应用程序对多个不同数据库中存储的数据进行原子性更改。 预览版着重于 C# 和 .NET 的客户端开发体验。 服务器端体验 (用 Transact-sql 编写的代码或服务器端脚本) 使用 [transact-sql](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 仅适用于托管实例，适用于 sql 数据库，它是稍后计划的。
> [!IMPORTANT]
> 在预览版中，目前不支持在 Azure SQL 数据库和 Azure SQL 托管实例之间运行弹性数据库事务。 弹性数据库事务只能跨越一组 SQL 数据库或一组托管实例。

弹性数据库事务面向以下方案：

* Azure 中的多数据库应用程序：在此方案中，数据垂直分区到 SQL 数据库中的几个数据库或托管实例，以使不同类型的数据驻留在不同的数据库中。 某些操作需要更改两个或更多数据库中保存的数据。 应用程序使用弹性数据库事务来协调数据库之间的更改并确保原子性。
* Azure 中的分片数据库应用程序：在此方案中，数据层使用 [弹性数据库客户端库](elastic-database-client-library.md) 或分片将数据水平分区到 SQL database 中的多个数据库或托管实例。 常见的用例之一是在分片的多租户应用程序中，当更改涉及到多个租户时，需要执行原子更改。 例如，从一个租户转移到另一个租户，而两者位于不同的数据库。 第二种方案是以细致分片来适应大租户的容量需求，这又通常表示某些原子操作需要扩展到用于同一租户的多个数据库。 第三种方案是以原子更新来引用数据库之间复制的数据。 现在，可以使用预览版跨多个数据库协调这几个方面原子性事务操作。
  弹性数据库事务使用两阶段提交，确保跨数据库的事务原子性。 如果单个事务一次涉及到的数据库少于 100 个，则适合采用此方案。 这些限制不是强制施加的，但是如果超出这些限制，弹性数据库事务的性能和成功率很有可能会下降。

## <a name="installation-and-migration"></a>安装和迁移

弹性数据库事务的功能通过 .NET 库的更新提供 System.Data.dll 和 System.Transactions.dll。 DLL 确保必要时使用两阶段事务提交，以确保原子性。 若要使用弹性数据库事务来开始开发应用程序，请安装 [.NET 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 或更高版本。 在旧版 .NET Framework 上运行时，事务无法升级为分布式事务，并会引发异常。

安装后，可以在 System. transaction 中使用分布式事务 Api，连接到 SQL 数据库并托管实例。 如果现有的 MSDTC 应用程序使用了这些 API，只需在安装 4.6.1 Framework 之后，以 .NET 4.6 为目标重建现有的应用程序。 如果你的项目面向 .NET 4.6，它们会自动将新的 Framework 版本和分布式事务 API 调用中的已更新 Dll 结合到 SQL 数据库的连接，否则托管实例将会成功。

请记住，弹性数据库事务不需要安装 MSDTC。 相反，弹性数据库事务由和在服务中直接管理。 这大大简化了云方案，因为 MSDTC 的部署不需要使用分布式事务和 SQL 数据库或托管实例。 第 4 部分更详细说明了如何将弹性数据库事务和所需的 .NET Framework 连同云应用程序一起部署到 Azure。

## <a name="net-installation-for-azure-cloud-services"></a>适用于 Azure 云服务的 .NET 安装

Azure 为托管 .NET 应用程序提供了多个产品。 不同产品的比较可见于 [Azure 应用服务、云服务和虚拟机比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。 如果产品的来宾 OS 版本低于弹性事务所需的 .NET 4.6.1，需要将来宾 OS 升级到 4.6.1。

对于 Azure 应用服务，当前不支持升级到来宾 OS。 对于 Azure 虚拟机，只需要登录到 VM 并运行最新的 .NET framework 安装程序即可。 对于 Azure 云服务，需要将更高版本的 .NET 安装包括到部署的启动任务中。 [在云服务角色上安装 .NET](../../cloud-services/cloud-services-dotnet-install-dotnet.md) 中说明了概念和步骤。  

请注意，与 .NET 4.6 的安装程序相比，.NET 4.6.1 的安装程序在 Azure 云服务上执行引导过程时，可能需要更多的临时存储空间。 为了确保安装成功，需要在 ServiceDefinition.csdef 文件中启动任务的 LocalResources 部分和环境设置中，增加 Azure 云服务的临时存储，如以下示例所示：

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET 开发体验

### <a name="multi-database-applications"></a>多数据库应用程序

以下示例代码使用熟悉的 .NET System.Transactions 编程体验。 TransactionScope 类在 .NET 中创建环境事务。  ("环境事务" 是位于当前线程中的一个。 ) TransactionScope 中打开的所有连接都参与该事务。 如果有不同的数据库参与，事务自动提升为分布式事务。 通过设置完成范围来指示提交，即可控制事务的结果。

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>分片数据库应用程序

针对 SQL 数据库和托管实例的弹性数据库事务还支持协调分布式事务，在使用弹性数据库客户端库的 OpenConnectionForKey 方法时，可以为横向扩展的数据层打开连接。 假设需要保证事务一致性，使更改跨多个不同的分片键值。 与托管不同分片键值的分片的连接由 OpenConnectionForKey 来中转。 在一般情况下，可以连接到不同的分片，以确保事务保证需要分布式事务。
以下代码示例演示了此方法。 假设使用一个称为 shardmap 的变量代表来自弹性数据库客户端库的分片映射：

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-sql 开发体验

使用 T-sql 的服务器端分布式事务仅适用于 Azure SQL 托管实例。 只能在属于同一 [服务器信任组](https://aka.ms/mitrusted-groups)的托管实例之间执行分布式事务。 在这种情况下，托管实例需要使用 [链接服务器](https://docs.microsoft.com/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) 相互引用。

下面的示例 Transact-sql 代码使用 [BEGIN DISTRIBUTED transaction](https://docs.microsoft.com/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) 启动分布式事务。

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='managedinstance02', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='sql-managed-instance-02.48ea8fd5ac90.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'managedinstance02', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>结合 .NET 和 Transact-sql 开发体验

使用 system.exception 类的 .NET 应用程序可以将 TransactionScope 类与 Transact-sql 语句 BEGIN DISTRIBUTED TRANSACTION 组合在一起。 在 TransactionScope 内，执行 BEGIN DITRIBUTED TRANSACTION 的内部事务将显式提升为分布式事务。 此外，当在 TransactionScope 内打开第二个 SqlConnecton 时，它将被隐式提升为分布式事务。 在分布式事务启动之后，所有后续事务请求（无论是来自 .NET 还是 Transact-sql）都将加入父分布式事务。 因此，BEGIN 语句启动的所有嵌套事务作用域将在同一事务中结束，COMMIT/ROLLBACK 语句将对总体结果产生以下影响：
 * COMMIT 语句不会对 BEGIN 语句启动的事务范围产生任何影响，也就是说，在完成之前将不会提交任何结果 ( 在 TransactionScope 对象上调用 # A1 方法。 如果 TransactionScope 对象在完成前被销毁，则将回滚在该范围内完成的所有更改。
 * ROLLBACK 语句将导致回滚整个 TransactionScope。 在 TransactionScope 中登记新事务的任何尝试都会失败，并尝试调用 TransactionScope 对象上 ( # A1。

下面是一个示例，其中使用 Transact-sql 将事务显式升级到分布式事务。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

下面的示例显示了在 TransactionScope 中启动第二个 SqlConnecton 后，将隐式 proted 到分布式事务的事务。

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>针对 Azure SQL 数据库跨多个服务器的事务

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

Azure SQL 数据库中支持跨不同服务器的弹性数据库事务。 当事务跨越服务器边界时，参与的服务器首先需要进入相互通信关系。 一旦建立了通信关系，任意两个服务器中的任何数据库都可以与另一服务器的数据库参与弹性事务。 当事务跨越两个以上的服务器时，任意服务器对之间的通信关系需要准备就绪。

使用以下 PowerShell cmdlet 来管理弹性数据库事务的跨服务器通信关系：

* **New-AzSqlServerCommunicationLink**：使用此 cmdlet 在 Azure SQL 数据库中的两个服务器之间创建新的通信关系。 这种关系是对称的，这意味着一台服务器可使用另一台服务器启动事务。
* **Get-AzSqlServerCommunicationLink**：使用此 cmdlet 来检索现有通信关系及其属性。
* **Remove-AzSqlServerCommunicationLink**：使用此 cmdlet 来删除现有通信关系。

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>针对 Azure SQL 托管实例的多个服务器的事务

Azure SQL 托管实例中的不同服务器支持分布式事务。 当事务跨托管实例边界时，参与的实例首先需要进入相互的安全和通信关系。 这是通过设置 [服务器信任组](https://aka.ms/mitrusted-groups) 来完成的，可以在 Azure 门户上完成此操作。

  ![Azure 门户上的服务器信任组][3]

下图显示了具有可通过 .NET 或 Transact-sql 执行分布式事务的托管实例的服务器信任组。

  ![使用弹性事务的 Azure SQL 托管实例分布式事务][2]

## <a name="monitoring-transaction-status"></a>监视事务状态

使用动态管理视图 (Dmv) 来监视正在进行的弹性数据库事务的状态和进度。 与事务相关的所有 Dmv 都与 SQL 数据库和托管实例中的分布式事务相关。 可以在此处找到相应的 DMV 列表：[与事务相关的动态管理视图和函数 (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)。

这些 DMV 特别有用：

* **sys.dm\_tran\_active\_transactions**：列出当前正在使用的事务及其状态。 UOW（工作单位）列可以标识属于同一分布式事务的不同子事务。 同一分布式事务中的所有事务具有相同的 UOW 值。 有关详细信息，请参阅 [DMV 文档](https://msdn.microsoft.com/library/ms174302.aspx)。
* **sys.dm\_tran\_database\_transactions**：提供有关事务的其他信息，例如事务在日志中的位置。 有关详细信息，请参阅 [DMV 文档](https://msdn.microsoft.com/library/ms186957.aspx)。
* **sys.dm\_tran\_locks**：提供当前进行中事务所持有的锁的相关信息。 有关详细信息，请参阅 [DMV 文档](https://msdn.microsoft.com/library/ms190345.aspx)。

## <a name="limitations"></a>限制

SQL 数据库中的弹性数据库事务当前存在以下限制：

* 仅支持 SQL 数据库中跨数据库的事务。 其他 [X/Open XA](https://en.wikipedia.org/wiki/X/Open_XA) 资源提供程序和除 SQL 数据库以外的数据库无法参与弹性数据库事务。 这意味着，弹性数据库事务无法扩展到本地 SQL Server 和 Azure SQL 数据库。 对于本地的分布式事务，请继续使用 MSDTC。
* 仅支持来自 .NET 应用程序的客户端协调事务。 目前已规划 T-SQL 的服务器端支持，例如 BEGIN DISTRIBUTED TRANSACTION，但尚未推出。
* 不支持跨 WCF 服务的事务。 例如，有一个执行事务的 WCF 服务方法。 事务范围内的调用将失败，并显示异常 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)。

以下限制当前适用于托管实例中的分布式事务：

* 仅支持在托管实例中跨数据库的事务。 其他 [X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA) 资源提供程序和 Azure SQL 以外的数据库托管实例无法参与分布式事务。 这意味着，分布式事务不能在本地 SQL Server 和 Azure SQL 托管实例上伸展。 对于本地的分布式事务，请继续使用 MSDTC。
* 不支持跨 WCF 服务的事务。 例如，有一个执行事务的 WCF 服务方法。 事务范围内的调用将失败，并显示异常 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)。
* 为了参与分布式事务，Azure SQL 托管实例必须是 [服务器信任组](https://aka.ms/mitrusted-groups) 的一部分。
* [服务器信任组](https://aka.ms/mitrusted-groups)的限制会影响分布式事务。
* 参与分布式事务的托管实例需要具有通过专用终结点的连接 (使用部署) 的虚拟网络中的专用 IP 地址，并且需要使用专用 Fqdn 相互引用。 依赖于 Transact-sql 的客户端应用程序可以使用私有或公共终结点来对服务器信任组中的所有实例运行事务。 下图对此限制进行了说明。
  ![私有终结点连接限制][4]
## <a name="next-steps"></a>后续步骤

* 如有疑问，请访问 [Microsoft 问答&一个问题页面了解 SQL 数据库](https://docs.microsoft.com/answers/topics/azure-sql-database.html)。
* 对于功能请求，请将它们添加到 [SQL 数据库反馈论坛](https://feedback.azure.com/forums/217321-sql-database/) 或 [托管实例论坛](https://feedback.azure.com/forums/915676-sql-managed-instance)。



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/sql-mi-private-endpoint-limitation.png
 
