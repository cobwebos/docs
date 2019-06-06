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
ms.date: 06/03/2019
ms.openlocfilehash: 1b452fb0bac91429793f8d55e439c36c70784722
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492718"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>使用只读副本对只读的查询工作负荷进行负载均衡

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

作为[高可用性体系结构](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)的一部分，“高级”、“业务关键”或“超大规模”服务层级中的每个数据库会自动预配有一个主要副本和多个次要副本。 为次要副本预配的计算大小与主要副本相同。 **读取扩展**功能允许使用一个只读副本的容量而不是共享读写副本，对 SQL 数据库只读工作负载进行负载均衡。 这样，只读工作负荷将与主要的读写工作负荷相隔离，不会影响其性能。 该功能适用于包含逻辑隔离的只读工作负荷（例如分析）的应用程序。 这些应用程序可以在不增大成本的情况下使用此额外容量来获得性能优势。

下图演示了使用“业务关键”数据库的应用程序。

![只读副本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

新的“高级”、“业务关键”和“超大规模”数据库中默认已启用读取扩展功能。 如果在 SQL 连接字符串中配置了 `ApplicationIntent=ReadOnly`，则网关会将应用程序重定向到该数据库的只读副本。 有关如何使用 `ApplicationIntent` 属性的信息，请参阅[指定应用程序意向](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果你希望确保应用程序始终连接到主要副本，而不管 SQL 连接字符串中的 `ApplicationIntent` 设置如何，则必须在创建数据库或更改其配置时显式禁用读取扩展。 例如，如果将数据库从“标准”或“常规用途”层级升级到“高级”、“业务关键”或“超大规模”层级，并想要确保所有连接继续定向到主要副本，请禁用读取扩展。有关如何禁用读取扩展的详细信息，请参阅[启用和禁用读取扩展](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 只读副本不支持查询数据存储、扩展事件、SQL Profiler 和审核功能。 

## <a name="data-consistency"></a>数据一致性

副本的优势之一是，它始终处于事务一致性状态，但在不同的时间点，不同的副本之间可能会有一些较小的延迟。 读取横向扩展支持会话级一致性。 这意味着，如果只读会话在由于副本不可用而出现连接错误后重新连接，可以使用读写副本将其重定向到并非完全处于最新状态的副本。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该数据，则最新的更新可能不会在副本中立即可见。 延迟是由异步事务日志重做操作导致的。

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

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>对只读副本进行监视和故障排除

连接到只读副本后，可以使用 `sys.dm_db_resource_stats` DMV 访问性能指标。 若要访问查询计划统计信息，请使用 `sys.dm_exec_query_stats`、`sys.dm_exec_query_plan` 和 `sys.dm_exec_sql_text` DMV。

> [!NOTE]
> 逻辑 master 数据库中的 DMV `sys.resource_stats` 返回主要副本的 CPU 使用率和存储数据。


## <a name="enable-and-disable-read-scale-out"></a>启用和禁用读取扩展

“高级”、“业务关键”和“超大规模”服务层级中默认已启用读取扩展。 无法在“基本”、“标准”或“常规用途”服务层级中启用读取扩展。 “读取扩展”在配置了 0 个副本的“超大规模”数据库上自动禁用。 

可以使用以下方法，对“高级”或“业务关键”服务层级中的单一数据库和弹性池数据库禁用和重新启用读取扩展。

> [!NOTE]
> 禁用读取扩展的功能是出于后向兼容性而提供的。

### <a name="azure-portal"></a>Azure 门户

可以在“配置数据库”边栏选项卡上管理“读取扩展”设置。  

### <a name="powershell"></a>PowerShell

在 Azure PowerShell 中管理读取横向扩展需要安装 Azure PowerShell 2016 年 12 月版或更高版本。 有关最新的 PowerShell 版本，请参阅 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

可以禁用或重新启用读取横向扩展在 Azure PowerShell 中通过调用[集 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 并传入所需的值 –`Enabled`或`Disabled`-为`-ReadScale`参数。 

若要对现有数据库禁用读取扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
若要对新数据库禁用读取扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

若要对现有数据库重新启用读取扩展（请将尖括号中的项替换为环境的正确值，并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

若要创建已禁用读取扩展的数据库，或更改现有数据库的设置，请在将 `readScale` 属性设置为 `Enabled` 或 `Disabled` 的情况下使用以下方法，如以下示例请求所示。

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

有关详细信息，请参阅[数据库 - 创建或更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)。

## <a name="using-tempdb-on-read-only-replica"></a>对只读副本使用 TempDB

TempDB 数据库不会复制到只读副本。 每个副本具有自身的 TempDB 数据库版本，该版本是创建该副本时创建的。 系统确保 TempDB 可更新，并可以在执行查询期间进行修改。 如果只读工作负荷依赖于使用 TempDB 对象，则应创建这些对象作为查询脚本的一部分。 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>结合使用读取扩展与异地复制的数据库

如果你正在使用读取扩展在异地复制数据库（例如，作为故障转移组成员的数据库）上对只读工作负荷进行负载均衡操作，请确保主数据库和异地复制辅助数据库上都启用了读取扩展。 此配置可确保应用程序在故障转移后连接到新的主数据库时，相同的负载均衡体验能够继续。 如果要连接到启用了读取扩展的异地复制辅助数据库，则设置为 `ApplicationIntent=ReadOnly` 的会话将路由到其中一个副本，就像我们在主数据库上路由连接一样。  而未设为 `ApplicationIntent=ReadOnly` 的会话将路由到异地复制辅助数据库的主要副本，该副本也为只读。 由于异地复制辅助数据库的终结点与主数据库不同，因此之前访问辅助数据库不需要设置 `ApplicationIntent=ReadOnly`。 为确保后向兼容性，`sys.geo_replication_links` DMV 显示`secondary_allow_connections=2`（允许的任何客户端连接）。

> [!NOTE]
> 不支持在辅助数据库的本地副本之间执行轮循机制或任何其他负载均衡路由。

## <a name="next-steps"></a>后续步骤

- 若要了解 SQL 数据库的“超大规模”套餐，请参阅[“超大规模”服务层级](./sql-database-service-tier-hyperscale.md)。
