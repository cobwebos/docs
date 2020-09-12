---
title: 副本上的读取查询
description: Azure SQL 允许将只读副本的容量用于读取工作负荷（称为“读取扩展”）。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 09/03/2020
ms.openlocfilehash: 2e7c931d6d99187b4ee7985be19374048c226312
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442184"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>使用只读副本卸载只读的查询工作负荷
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

作为 [高可用性体系结构](high-availability-sla.md#premium-and-business-critical-service-tier-availability)的一部分，"高级" 和 "业务关键服务" 层中的每个单一数据库、弹性池数据库和托管实例都将自动配有一个主读写副本和多个辅助只读副本。 为次要副本预配的计算大小与主要副本相同。 “读取扩展”功能允许使用一个只读副本的计算容量而不是在读写副本上运行它们来卸载只读工作负荷。 这样，一些只读工作负荷可与读写工作负荷相隔离，并且不会影响其性能。 该功能适用于包含逻辑隔离的只读工作负荷（例如分析）的应用程序。 在“高级”和“业务关键”服务层级中，应用程序可以使用此额外的容量获得性能优势，而无需额外付费。

如果至少创建了一个次要副本，则“超大规模”服务层级还会提供“读取扩展”功能。 如果只读工作负荷需要的资源多于一个次要副本上的可用资源，则可使用多个次要副本对该只读工作负荷进行负载均衡。

“基本”、“标准”和“常规用途”服务层级的高可用性体系结构不包含任何副本。 读取扩展功能在这些服务层级中不可用。

下图演示了该功能。

![只读副本](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

新的“高级”、“业务关键”和“超大规模”数据库中默认已启用读取扩展功能。 对于超大规模，默认情况下，为新数据库创建一个次要副本。 

> [!NOTE]
> 托管实例的“业务关键”服务层级中始终启用读取扩展功能。

如果在 SQL 连接字符串中配置为 `ApplicationIntent=ReadOnly`，则将应用程序重定向到该数据库或托管实例的只读副本。 有关如何使用 `ApplicationIntent` 属性的信息，请参阅[指定应用程序意向](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果你希望确保应用程序始终连接到主要副本，而不管 SQL 连接字符串中的 `ApplicationIntent` 设置如何，则必须在创建数据库或更改其配置时显式禁用读取扩展。 例如，如果将数据库从“标准”或“常规用途”层级升级到“高级”、“业务关键”或“超大规模”层级，并想要确保所有连接继续定向到主要副本，请禁用读取扩展。有关如何禁用读取扩展的详细信息，请参阅[启用和禁用读取扩展](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 只读副本不支持查询存储和 SQL Profiler 功能。 

## <a name="data-consistency"></a>数据一致性

副本的优势之一是，它始终处于事务一致性状态，但在不同的时间点，不同的副本之间可能会有一些较小的延迟。 读取横向扩展支持会话级一致性。 这意味着，如果只读会话在由于副本不可用而出现连接错误后重新连接，可以使用读写副本将其重定向到并非完全处于最新状态的副本。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该数据，则最新的更新可能不会在副本中立即可见。 延迟是由异步事务日志恢复操作导致的。

> [!NOTE]
> 区域中的复制延迟较低，且这种情况很少见。 若要监视复制延迟，请参阅[对只读副本进行监视和故障排除](#monitoring-and-troubleshooting-read-only-replicas)。

## <a name="connect-to-a-read-only-replica"></a>连接到只读副本

为数据库启用读取横向扩展时，客户端提供的连接字符串中的 `ApplicationIntent` 选项会指示连接是要路由到写入副本还是只读副本。 具体而言，如果 `ApplicationIntent` 值为 `ReadWrite`（默认值），则连接会定向到读写副本。 这与连接字符串中未包括 `ApplicationIntent` 时的行为相同。 如果 `ApplicationIntent` 值为 `ReadOnly`，则连接将路由到只读副本。

例如，以下连接字符串将客户端连接到只读副本（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

以下连接字符串之一将客户端连接到读写副本（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>验证连接到只读副本

可通过在数据库上下文中运行以下查询来验证是否连接到只读副本。 连接到只读副本时，它将返回 READ_ONLY。

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> 在“高级”和“业务关键”服务层级中，只有一个只读副本随时可供访问。 “超大规模”层级支持多个只读副本。

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>对只读副本进行监视和故障排除

当连接到只读副本时，动态管理视图 (DMV) 反映副本的状态，并可对其进行查询以进行监视和故障排除。 数据库引擎提供了多个视图来公开各种监视数据。 

常用视图如下：

| 名称 | 目的 |
|:---|:---|
|[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| 提供最近一小时的资源利用率指标，包括相对于服务目标限制的 CPU、数据 IO 和日志写入使用率。|
|[sys.dm_os_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| 提供数据库引擎实例的聚合等待统计信息。 |
|[sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| 提供副本运行状况状态和同步统计信息。 重做队列大小和重做速率用作只读副本上数据延迟的指示。 |
|[sys.dm_os_performance_counters](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| 提供数据库引擎性能计数器。|
|[sys.dm_exec_query_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| 提供每个查询的执行统计信息，例如执行次数、已用 CPU 时间等。|
|[sys.dm_exec_query_plan()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| 提供缓存的查询计划。 |
|[sys.dm_exec_sql_text()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| 提供缓存的查询计划的查询文本。|
|[sys.dm_exec_query_profiles](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 提供正执行查询时的实时查询进度。|
|[sys.dm_exec_query_plan_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| 提供最近的已知实际执行计划，包括查询的运行时统计信息。|
|[sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| 提供所有数据库文件的存储 IOPS、吞吐量和延迟统计信息。 |

> [!NOTE]
> 逻辑 master 数据库中的 `sys.resource_stats` 和 `sys.elastic_pool_resource_stats` DMV 返回主要副本的资源利用率数据。

### <a name="monitoring-read-only-replicas-with-extended-events"></a>监视具有扩展事件的只读副本

连接到只读副本时，无法创建扩展事件会话。 但是在 Azure SQL 数据库中，主要副本上创建和更改的数据库范围内[扩展事件](xevent-db-diff-from-svr.md)会话的定义将复制到只读副本（包括异地副本），并捕获只读副本上的事件。

可独立于主要副本来启动和停止基于主要副本中会话定义的只读副本上的扩展事件会话。 主要副本上删除扩展事件会话时，也会在所有只读副本上删除它。

### <a name="transaction-isolation-level-on-read-only-replicas"></a>只读副本上的事务隔离级别

可始终将只读副本上运行的查询映射到[快照](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)事务隔离级别。 快照隔离使用行版本控制，以避免在读取器阻止写入器的情况下发生阻止。

极少数情况下，如果快照隔离事务访问已在其他并发事务中修改的对象元数据，则可能会收到错误 [3961](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)，“数据库 "%.*ls" 中的快照隔离事务失败，因为自此事务启动后，该语句所访问的对象已由其他并发事务中的 DDL 语句修改。 禁用它是因为元数据未进行版本控制。 如果与快照隔离混合，对元数据进行并发更新可能导致不一致。”

### <a name="long-running-queries-on-read-only-replicas"></a>只读副本上的长时间运行的查询

只读副本上运行的查询需要访问查询中引用的对象的元数据（表、索引、统计信息等）极少数情况下，如果在主要副本上修改元数据对象，而查询持有对只读副本上同一对象的锁定，则该查询可以[阻止](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK)将主要副本的更改应用到只读副本的过程。 如果此类查询长时间运行，会导致只读副本与主要副本明显不同步。 

如果只读副本上长时间运行的查询导致此类阻止，则自动终止它，并且会话收到错误 1219，“由于高优先级 DDL 操作，会话已断开连接”。

> [!NOTE]
> 如果针对只读副本运行查询时收到错误 3961 或错误 1219，则重试查询。

> [!TIP]
> 在“高级”和“业务关键”服务层级中，当连接到只读副本时，[sys.dm_database_replica_states](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV 中的 `redo_queue_size` 和 `redo_rate` 列都可用于监视数据同步过程，作为只读副本上数据延迟的指示。
> 

## <a name="enable-and-disable-read-scale-out"></a>启用和禁用读取扩展

“高级”、“业务关键”和“超大规模”服务层级中默认已启用读取扩展。 无法在“基本”、“标准”或“常规用途”服务层级中启用读取扩展。 “读取扩展”在配置了 0 个副本的“超大规模”数据库上自动禁用。

可以使用以下方法，对“高级”或“业务关键”服务层级中的单一数据库和弹性池数据库禁用和重新启用读取扩展。

> [!NOTE]
> 对于单一数据库和弹性池数据库，禁用读取扩展的功能是出于后向兼容性而提供的。 无法在“业务关键”托管实例上禁用读取扩展。

### <a name="azure-portal"></a>Azure 门户

可以在“配置”数据库边栏选项卡上管理“读取扩展”设置。

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> 仍然支持 PowerShell Azure 资源管理器模块，但是所有未来的开发都是针对 Az.Sql 模块。 Azure 资源管理器模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 Azure 资源管理器模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

在 Azure PowerShell 中管理读取横向扩展需要安装 Azure PowerShell 2016 年 12 月版或更高版本。 有关最新的 PowerShell 版本，请参阅 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

在 Azure PowerShell 中调用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet，并为 `-ReadScale` 参数传入所需的值（`Enabled` 或 `Disabled`），即可禁用或重新启用读取扩展。

若要对现有数据库禁用读取扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

若要对新数据库禁用读取扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

若要对现有数据库重新启用读取扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

若要创建已禁用读取扩展的数据库，或更改现有数据库的设置，请在将 `readScale` 属性设置为 `Enabled` 或 `Disabled` 的情况下使用以下方法，如以下示例请求所示。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

有关详细信息，请参阅[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>在只读副本上使用 `tempdb` 数据库

主要副本上的 `tempdb` 数据库不会复制到只读副本。 每个副本具有自身的 `tempdb` 数据库，该数据库是创建该副本时创建的。 这确保 `tempdb` 可更新，并可以在执行查询期间进行修改。 如果只读工作负荷依赖于使用 `tempdb` 对象，则应创建这些对象作为查询脚本的一部分。

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>结合使用读取扩展与异地复制的数据库

异地复制的辅助数据库具有与主数据库相同的高可用性体系结构。 如果要连接到启用了读取扩展的异地复制辅助数据库，则设置为 `ApplicationIntent=ReadOnly` 的会话将路由到其中一个高可用性副本，就像在主要的可写入数据库上路由它们一样。 而未设为 `ApplicationIntent=ReadOnly` 的会话将路由到异地复制辅助数据库的主要副本，该副本也为只读。 

通过这种方式，创建异地副本将为读写主数据库另外提供两个只读副本，总计三个只读副本。 其他每个异地副本提供另一对只读副本。 可在任何 Azure 区域（包括主数据库的区域）创建异地副本。

> [!NOTE]
> 异地复制的辅助数据库的副本之间没有自动轮循机制或任何其他负载均衡路由。

## <a name="next-steps"></a>后续步骤

- 若要了解 SQL 数据库的“超大规模”套餐，请参阅[“超大规模”服务层级](service-tier-hyperscale.md)。
