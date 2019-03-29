---
title: Azure SQL 数据库 - 读取副本中的查询 | Microsoft Docs
description: Azure SQL 数据库允许使用只读副本的容量（称为“读取横向扩展”）对只读的工作负荷进行负载均衡。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619885"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>使用只读副本进行负载均衡只读查询工作负荷

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库，但未来的所有开发都不适用于 Az.Sql 模块。 有关这些 cmdlet，请参阅[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 命令在 Az 模块和 AzureRm 模块中的参数是大体上相同的。

“读取扩展”允许使用一个只读副本的容量对 Azure SQL 数据库只读工作负荷进行负载均衡。

“高级”层（[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)）或“业务关键”层（[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)）中的每个数据库中已自动预配多个 Always ON 副本，以支持可用性 SLA。 下图对此进行了说明。

![只读副本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

使用与主副本相同的计算大小预配辅助副本。 “读取扩展”功能允许使用一个只读副本的容量而不是共享读写副本，对 SQL 数据库只读工作负载进行负载均衡。 这样，只读工作负荷将与主要的读写工作负荷相隔离，不会影响其性能。 该功能适用于其中包括逻辑上独立的只读工作负荷（例如分析）的应用程序，因此可以在不增加成本的情况下使用此额外容量来获得性能优势。

若要将读取横向扩展功能用于特定的数据库，必须在创建数据库时或者在之后通过更改其配置来显式启用此功能，可以采用以下方式执行此操作：使用 PowerShell 调用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 或 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) 命令，或者通过 Azure 资源管理器 REST API 使用[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)方法。

应用程序连接到该数据库为数据库启用读取横向扩展后，会将网关到读写副本或根据该数据库的只读副本引导`ApplicationIntent`中配置的属性应用程序的连接字符串。 有关 `ApplicationIntent` 属性的信息，请参阅[指定应用程序意向](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果禁用了“读取扩展”，或在不支持的服务层中设置了 ReadScale 属性，则所有连接都将定向到读写副本，而与 `ApplicationIntent` 属性无关。

> [!NOTE]
> 副本的只读副本上不支持查询数据存储、 扩展事件、 SQL Profiler 和审核功能。 
## <a name="data-consistency"></a>数据一致性

副本的优势之一是，它始终处于事务一致性状态，但在不同的时间点，不同的副本之间可能会有一些较小的延迟。 读取横向扩展支持会话级一致性。 这意味着，如果只读会话在由于副本不可用而出现连接错误后重新连接，可以使用读写副本将其重定向到并非完全处于最新状态的副本。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该，则可能最新的更新不会在副本上立即可见。 延迟被由于异步事务日志重做操作。

> [!NOTE]
> 区域中的复制延迟较低，且这种情况很少见。

## <a name="connect-to-a-read-only-replica"></a>连接到只读副本

为数据库启用读取横向扩展时，客户端提供的连接字符串中的 `ApplicationIntent` 选项会指示连接是要路由到写入副本还是只读副本。 具体而言，如果 `ApplicationIntent` 值为 `ReadWrite`（默认值），则连接将定向到数据库的读写副本。 这与现有行为相同。 如果 `ApplicationIntent` 值为 `ReadOnly`，则连接将路由到只读副本。

例如，以下连接字符串将客户端连接到只读副本（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

以下连接字符串之一将客户端连接到读写副本（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>验证连接到只读副本

可通过运行以下查询来验证是否连接到只读副本。 连接到只读副本时，它将返回 READ_ONLY。

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> 在任何给定时间，ReadOnly 会话只能访问一个 AlwaysON 副本。

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>监视和故障排除只读副本

当连接到只读副本，您可以访问性能指标使用`sys.dm_db_resource_stats`DMV。 若要访问查询计划统计信息，请使用`sys.dm_exec_query_stats`，`sys.dm_exec_query_plan`和`sys.dm_exec_sql_text`Dmv。

> [!NOTE]
> DMV`sys.resource_stats`逻辑 master 数据库中返回的主副本的 CPU 使用率和存储数据。


## <a name="enable-and-disable-read-scale-out"></a>启用和禁用读取扩展

默认情况下，[托管实例](sql-database-managed-instance.md)业务关键层中启用了读取扩展。 它在 [SQL 数据库服务器上放置的数据库](sql-database-servers.md)高级和业务关键层中应处于显式启用状态。 下面介绍了启用和禁用读取扩展的方法。

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell：启用和禁用读取扩展

在 Azure PowerShell 中管理读取横向扩展需要安装 Azure PowerShell 2016 年 12 月版或更高版本。 有关最新的 PowerShell 版本，请参阅 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

启用或禁用读取的横向扩展在 Azure PowerShell 中通过调用[集 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 并传入所需的值 –`Enabled`或`Disabled`-为`-ReadScale`参数。 或者，可以使用 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 来创建已启用读取横向扩展的新数据库。

例如，若要为现有数据库启用读取横向扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

若要为现有数据库禁用读取横向扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

若要创建已启用读取横向扩展的新数据库（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API：启用和禁用读取扩展

若要创建已启用读取横向扩展的数据库，或者要为现有数据库启用或禁用读取横向扩展，请按以下示例请求中所示，在 `readScale` 属性设置为 `Enabled` 或 `Disabled` 的情况下，创建或更新相应的数据库实体。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

有关详细信息，请参阅[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-tempdb-on-read-only-replica"></a>在只读副本上使用 TempDB

TempDB 数据库不会复制到只读副本。 每个副本具有其自己的副本创建时创建的 TempDB 数据库的版本。 它可确保 TempDB 可更新，并可以在查询执行期间修改。 如果使用 TempDB 对象取决于您的只读工作负荷，应作为查询脚本的一部分来创建这些对象。 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>结合使用读取扩展与异地复制的数据库

如果你使用读取的横向扩展负载均衡只读工作负荷 （例如，作为故障转移组的成员） 的异地复制的数据库，，请确保主数据库和异地复制辅助数据库上启用该读取的横向扩展。 此配置将确保相同的负载平衡体验你的应用程序故障转移后连接到新的主数据库时将继续。 如果要连接到启用了读取扩展的异地复制辅助数据库，则设置为 `ApplicationIntent=ReadOnly` 的会话将路由到其中一个副本，就像我们在主数据库上路由连接一样。  而未设为 `ApplicationIntent=ReadOnly` 的会话将路由到异地复制辅助数据库的主要副本，该副本也为只读。 由于异地复制辅助数据库的终结点与主数据库不同，因此之前访问辅助数据库不需要设置 `ApplicationIntent=ReadOnly`。 为确保后向兼容性，`sys.geo_replication_links` DMV 显示`secondary_allow_connections=2`（允许的任何客户端连接）。

> [!NOTE]
> 不支持在辅助数据库的本地副本之间执行轮循机制或任何其他负载均衡路由。

## <a name="next-steps"></a>后续步骤

- 有关使用 PowerShell 设置读取横向扩展的信息，请参阅 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 或 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet。
- 有关使用 REST API 设置读取横向扩展的信息，请参阅[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。
