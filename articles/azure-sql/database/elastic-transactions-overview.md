---
title: 跨云数据库的分布式事务
description: Azure SQL 数据库的弹性数据库事务的概述。
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
ms.openlocfilehash: 5c94234644fcefb70a40ba0b2c21e6e205be0e65
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829408"
---
# <a name="distributed-transactions-across-cloud-databases"></a>跨云数据库的分布式事务
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL 数据库的弹性数据库事务可让你在 SQL 数据库中运行跨多个数据库的事务。 SQL 数据库的弹性数据库事务适用于使用 ADO .NET 的 .NET 应用程序，并与使用[system.exception 类的](https://msdn.microsoft.com/library/system.transactions.aspx)熟悉的编程体验相集成。 若要获取该库，请参阅 [.NET Framework 4.6.1（Web 安装程序）](https://www.microsoft.com/download/details.aspx?id=49981)。

在本地，这种方案通常需要运行 Microsoft 分布式事务处理协调器（MSDTC）。 由于 MSDTC 不适用于 Azure 中的平台即服务应用程序，因此，协调分布式事务的功能现在已直接集成到 SQL 数据库中。 应用程序可以连接到 SQL 数据库中的任何数据库以启动分布式事务，其中一个数据库将以透明方式协调分布式事务，如下图所示。

  ![使用弹性数据库事务在 Azure SQL 数据库中执行分布式事务 ][1]

## <a name="common-scenarios"></a>常见方案

使用 SQL 数据库的弹性数据库事务，应用程序可以对存储在 SQL 数据库中多个不同数据库中的数据进行原子性更改。 预览版着重于 C# 和 .NET 的客户端开发体验。 已计划在将来添加使用 T-SQL 的服务器端体验。  
弹性数据库事务面向以下方案：

* Azure 中的多数据库应用程序：在此方案中，数据垂直分区到 SQL 数据库的多个数据库中，使不同种类的数据位于不同的数据库中。 某些操作需要更改数据，这些操作保存在两个或多个数据库中。 应用程序使用弹性数据库事务来协调数据库之间的更改并确保原子性。
* Azure 中的分片数据库应用程序：在此方案中，数据层使用[弹性数据库客户端库](elastic-database-client-library.md)或自我分片在 SQL 数据库中的多个数据库之间对数据进行水平分区。 常见的用例之一是在分片的多租户应用程序中，当更改涉及到多个租户时，需要执行原子更改。 例如，从一个租户转移到另一个租户，而两者位于不同的数据库。 第二种情况是精细的分片，以适应大型租户的容量需求，这又通常表示某些原子操作需要在用于同一租户的多个数据库之间延伸。 第三种方案是以原子更新来引用数据库之间复制的数据。 现在，可以使用预览版跨多个数据库协调这几个方面原子性事务操作。
  弹性数据库事务使用两阶段提交，确保跨数据库的事务原子性。 这适用于在单个事务中一次涉及少于100个数据库的事务。 不会强制实施这些限制，但当超过这些限制时，弹性数据库事务的性能和成功率就会下降。

## <a name="installation-and-migration"></a>安装和迁移

SQL 数据库中的弹性数据库事务的功能通过 .NET 库的更新提供 System.Data.dll 和 System.Transactions.dll。 DLL 确保必要时使用两阶段事务提交，以确保原子性。 若要使用弹性数据库事务来开始开发应用程序，请安装 [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) 或更高版本。 在旧版 .NET Framework 上运行时，事务无法升级为分布式事务，并会引发异常。

安装后，可以在 System. transaction 中使用分布式事务 Api，并连接到 SQL 数据库。 如果现有的 MSDTC 应用程序使用了这些 API，只需在安装 4.6.1 Framework 之后，以 .NET 4.6 为目标重建现有的应用程序。 如果项目面向 .NET 4.6，它们将自动使用新 Framework 版本中更新的 Dll，并且与 SQL 数据库的连接将立即成功。

请记住，弹性数据库事务不需要安装 MSDTC。 相反，弹性数据库事务直接由 SQL 数据库管理。 这大大简化了云方案，因为 MSDTC 的部署不需要使用分布式事务和 SQL 数据库。 第 4 部分更详细说明了如何将弹性数据库事务和所需的 .NET Framework 连同云应用程序一起部署到 Azure。

## <a name="development-experience"></a>开发体验

### <a name="multi-database-applications"></a>多数据库应用程序

以下示例代码使用熟悉的 .NET System.Transactions 编程体验。 TransactionScope 类在 .NET 中创建环境事务。 （"环境事务" 是位于当前线程中的事务。）在 TransactionScope 内打开的所有连接都参与该事务。 如果有不同的数据库参与，事务会自动提升为分布式事务。 通过设置完成范围来指示提交，即可控制事务的结果。

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

SQL 数据库的弹性数据库事务还支持协调分布式事务，在使用弹性数据库客户端库的 OpenConnectionForKey 方法时，可以为横向扩展的数据层打开连接。 假设需要保证事务一致性，使更改跨多个不同的分片键值。 与托管不同分片键值的分片的连接由 OpenConnectionForKey 来中转。 在一般情况下，可以连接到不同的分片，以确保事务保证需要分布式事务。
以下代码示例演示了此方法。 假设使用一个称为 shardmap 的变量代表来自弹性数据库客户端库的分片映射：

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="net-installation-for-azure-cloud-services"></a>适用于 Azure 云服务的 .NET 安装

Azure 为托管 .NET 应用程序提供了多个产品。 不同产品的比较可见于 [Azure 应用服务、云服务和虚拟机比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。 如果产品的来宾 OS 版本低于弹性事务所需的 .NET 4.6.1，需要将来宾 OS 升级到 4.6.1。

对于 Azure App Service，当前不支持升级到来宾操作系统。 对于 Azure 虚拟机，只需要登录到 VM 并运行最新的 .NET framework 安装程序即可。 对于 Azure 云服务，需要将更高版本的 .NET 安装包括到部署的启动任务中。 [在云服务角色上安装 .NET](../../cloud-services/cloud-services-dotnet-install-dotnet.md) 中说明了概念和步骤。  

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

## <a name="transactions-across-multiple-servers"></a>跨多个服务器的事务

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

在 Azure SQL 数据库中的不同服务器上支持弹性数据库事务。 当事务跨服务器边界时，参与的服务器首先需要进入相互通信关系。 一旦建立了通信关系，任意两个服务器中的任何数据库都可以与另一服务器的数据库参与弹性事务。 如果事务跨越两个以上的服务器，则需要为任意一对服务器设置通信关系。

使用以下 PowerShell cmdlet 来管理弹性数据库事务的跨服务器通信关系：

* **AzSqlServerCommunicationLink**：使用此 cmdlet 可在 Azure SQL 数据库中的两个服务器之间创建新的通信关系。 这种关系是对称的，这意味着这两台服务器可以在其他服务器之间启动事务。
* **AzSqlServerCommunicationLink**：使用此 cmdlet 检索现有通信关系及其属性。
* **AzSqlServerCommunicationLink**：使用此 cmdlet 可删除现有的通信关系。

## <a name="monitoring-transaction-status"></a>监视事务状态

在 SQL 数据库中使用动态管理视图（Dmv）来监视正在进行的弹性数据库事务的状态和进度。 与事务相关的所有 Dmv 都与 SQL Database 中的分布式事务相关。 可以在此处找到相应的 DMV 列表：[事务相关的动态管理视图和函数 (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx)。

这些 DMV 特别有用：

* **sys.dm\_tran\_active\_transactions**：列出当前正在使用的事务及其状态。 UOW（工作单位）列可以标识属于同一分布式事务的不同子事务。 同一分布式事务中的所有事务具有相同的 UOW 值。 有关详细信息，请参阅 [DMV 文档](https://msdn.microsoft.com/library/ms174302.aspx)。
* **sys.dm\_tran\_database\_transactions**：提供有关事务的其他信息，例如事务在日志中的位置。 有关详细信息，请参阅 [DMV 文档](https://msdn.microsoft.com/library/ms186957.aspx)。
* **sys.dm\_tran\_locks**：提供当前进行中事务所持有的锁的相关信息。 有关详细信息，请参阅 [DMV 文档](https://msdn.microsoft.com/library/ms190345.aspx)。

## <a name="limitations"></a>限制

以下限制当前适用于 SQL 数据库中的弹性数据库事务：

* 仅支持在 SQL 数据库中跨数据库的事务。 其他[X/OPEN XA](https://en.wikipedia.org/wiki/X/Open_XA)资源提供程序和 SQL 数据库之外的数据库无法参与弹性数据库事务。 这意味着，弹性数据库事务不能跨本地 SQL Server 和 Azure SQL 数据库进行延伸。 对于本地的分布式事务，请继续使用 MSDTC。
* 仅支持来自 .NET 应用程序的客户端协调事务。 目前已规划 T-SQL 的服务器端支持，例如 BEGIN DISTRIBUTED TRANSACTION，但尚未推出。
* 不支持跨 WCF 服务的事务。 例如，有一个执行事务的 WCF 服务方法。 事务范围内的调用会失败，并显示异常 [System.ServiceModel.ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)。

## <a name="next-steps"></a>后续步骤

如有疑问，请访问[Microsoft 问答&一个问题页面了解 SQL 数据库](https://docs.microsoft.com/answers/topics/azure-sql-database.html)。 对于功能请求，请将其添加到[SQL 数据库反馈论坛](https://feedback.azure.com/forums/217321-sql-database/)。

<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
 