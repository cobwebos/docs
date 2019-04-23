---
title: Azure SQL 数据库 - 读取副本中的查询 | Microsoft Docs
description: Azure SQL 数据库提供的功能进行负载平衡使用只读副本的名为读取横向扩展的容量的只读工作负荷。
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
ms.date: 04/19/2019
ms.openlocfilehash: cbcdcfd151951334246a4e85d9f521a15bb6269d
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006142"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>使用负载均衡只读查询工作负荷的只读副本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

作为的一部分[高可用性体系结构](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)，高级版、 业务关键或超大规模的服务层中每个数据库会自动预配的主副本和多个辅助副本。 为次要副本预配的计算大小与主要副本相同。 **读取横向扩展**功能允许你为负载平衡 SQL 数据库只读工作负荷使用的一个只读副本的容量而不共享读写副本。 这样，只读工作负荷将与主要的读写工作负荷相隔离，不会影响其性能。 此功能适用于包括逻辑上分离只读工作负荷，例如分析的应用程序。 它们无法获得性能优势，使用这些额外的容量，无需额外付费。

下图说明了其使用的是业务关键数据库。

![只读副本](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

默认情况下，新的高级、 业务关键和超大规模的数据库上启用读取横向扩展功能。 如果 SQL 连接字符串配置了`ApplicationIntent=ReadOnly`，应用程序将被重定向到该数据库的只读副本网关。 有关如何使用信息`ApplicationIntent`属性，请参阅[指定应用程序意向](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent)。

如果您想要确保应用程序连接到的主副本而不考虑`ApplicationIntent`设置 SQL 连接字符串中，您必须显式禁用读取的横向扩展或更改其配置时创建数据库。 例如，如果将数据库从标准版或常规用途层升级到 Premium、 业务关键或超大规模的层，并且想要确保所有连接都继续转到主副本，则禁用读取横向扩展。有关如何禁用它的详细信息，请参阅[启用和禁用读取横向扩展](#enable-and-disable-read-scale-out)。

> [!NOTE]
> 只读副本不支持查询数据存储、扩展事件、SQL Profiler 和审核功能。 

## <a name="data-consistency"></a>数据一致性

副本的优势之一是，它始终处于事务一致性状态，但在不同的时间点，不同的副本之间可能会有一些较小的延迟。 读取横向扩展支持会话级一致性。 这意味着，如果只读会话重新连接副本不可用而导致连接错误后，它可能会重定向到不是 100%与读写副本保持最新的副本。 同样，如果应用程序使用读写会话写入数据，并立即使用只读会话读取该数据，则最新的更新可能不会在副本中立即可见。 延迟是由异步事务日志重做操作导致的。

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

默认情况下，高级版、 业务关键和超大规模服务层上启用读取横向扩展。 不能在基本、 标准版或常规用途服务层中启用读取横向扩展。 使用 0 个副本配置的超大规模数据库上自动禁用读取横向扩展。 

可以禁用并重新启用读取横向扩展单一数据库和弹性池数据库在高级或业务关键服务层中使用以下方法。

> [!NOTE]
> 禁用读取横向扩展的功能用于向后兼容性。

### <a name="azure-portal"></a>Azure 门户

你可以管理上的读取 Sacle 扩展设置**配置**数据库边栏选项卡。 

### <a name="powershell"></a>PowerShell

在 Azure PowerShell 中管理读取横向扩展需要安装 Azure PowerShell 2016 年 12 月版或更高版本。 有关最新的 PowerShell 版本，请参阅 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

可以禁用或重新启用读取横向扩展在 Azure PowerShell 中通过调用[集 AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 并传入所需的值 –`Enabled`或`Disabled`-为`-ReadScale`参数。 

若要禁用读取的横向扩展在现有数据库 （在尖括号中的项替换为你的环境的正确值并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
若要禁用读取的横向扩展在新数据库 （在尖括号中的项替换为你的环境的正确值并删除尖括号）：

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

若要重新启用读取的横向扩展在现有数据库 （在尖括号中的项替换为你的环境的正确值并删除尖括号）：

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

若要创建一个数据库，读取横向扩展已禁用，或更改现有数据库的设置，使用以下方法使用`readScale`属性设置为`Enabled`或`Disabled`如以下示例请求。

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

如果您正在使用负载均衡只读工作负荷 （例如，作为故障转移组的成员） 的异地复制的数据库上读取横向扩展，，请确保主数据库和异地复制辅助数据库上启用该读取的横向扩展。 此配置可确保应用程序在故障转移后连接到新的主数据库时，相同的负载均衡体验能够继续。 如果要连接到启用了读取扩展的异地复制辅助数据库，则设置为 `ApplicationIntent=ReadOnly` 的会话将路由到其中一个副本，就像我们在主数据库上路由连接一样。  而未设为 `ApplicationIntent=ReadOnly` 的会话将路由到异地复制辅助数据库的主要副本，该副本也为只读。 因为异地复制的辅助数据库具有比主数据库的不同终结点，从历史上看以访问辅助副本时它不所需设置`ApplicationIntent=ReadOnly`。 为确保后向兼容性，`sys.geo_replication_links` DMV 显示`secondary_allow_connections=2`（允许的任何客户端连接）。

> [!NOTE]
> 不支持轮循机制或任何其他负载平衡之间的路由的辅助数据库的本地副本。

## <a name="next-steps"></a>后续步骤

- 有关 SQL 数据库的超大规模的产品/服务的信息，请参阅[超大规模服务层](./sql-database-service-tier-hyperscale.md)。
