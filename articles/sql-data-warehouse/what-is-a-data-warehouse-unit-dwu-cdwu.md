---
title: "什么是 Azure SQL 数据仓库中的数据仓库单位（DWU、cDWU）？ | Microsoft Docs"
description: "Azure SQL 数据仓库中的性能横向扩展功能。 通过调整 DWU、cDWU 数目进行横向扩展，或者通过暂停和恢复计算资源来节省成本。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 93f0d21c7214487ffa0c2c5e27bd6e468920418c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>数据仓库单位 (DWU) 和计算数据仓库单位 (cDWU)
介绍 Azure SQL 数据仓库的数据仓库单位 (DWU) 和计算数据仓库单位 (cDWU)。 包括针对如何选择合适的数据仓库单位数以及如何更改其数目的建议。 

## <a name="what-are-data-warehouse-units"></a>什么是数据仓库单位？
借助 SQL 数据仓库，将 CPU、内存和 IO 捆绑到称为数据仓库单位 (DWU) 的计算规模单位中。 DWU 表示抽象、规范化的计算资源和性能度量值。 通过更改服务级别，可更改分配给系统的 DWU 数，这反过来又会调整系统的性能和成本。 

增加数据仓库单位数，则需支付的性能费用更高。 减少数据仓库单位数，则需支付的性能费用更低。 存储和计算成本分别计费，因此更改数据仓库单位数不会影响存储成本。

数据仓库单位性能基于这些数据仓库负载指标：

- 标准数据仓库查询扫描大量行并执行复杂聚合的速度有多快？ 这是一种 I/O 和 CPU 密集型操作。
- 数据仓库从 Azure 存储 Blob 或 Azure Data Lake 引入数据的速度有多快？ 这是一种网络和 CPU 密集型操作。 
- [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 命令复制表的速度有多快？ 此操作涉及从存储读取数据、将数据分配到设备的节点上，以及重新将数据写入到存储。 这是一种 CPU、IO 和网络密集型操作。

增加 DWU：
- 以线性方式更改系统对扫描、聚合和 CTAS 语句的性能
- 增加 PolyBase 加载操作的读取器和编写器数量
- 增加并发查询和并发槽的最大数量。

## <a name="performance-tiers-and-data-warehouse-units"></a>性能层和数据仓库单位

每个性能层用于其数据仓库单位测量的单位都略有不同。 当规模单位直接转换为计费时，这种差异会反映在发票上。

- 弹性优化性能层通过数据仓库单位 (DWU) 进行计量。
- 计算优化性能层通过计算数据仓库单位 (cDWU) 进行计量。 

DWU 和 cDWU 都支持增加或减少计算，以及在无需使用数据仓库时暂停计算。 这些操作均可按需进行。 计算优化性能层还会在计算节点上使用基于本地磁盘的缓存以提高性能。 缩放或暂停系统时，缓存将失效，因此在达到最佳性能前，缓存需要预热一段时间。  

增加数据库单位时，将以线性方式增加计算资源。 计算优化性能层可提供最佳查询性能和最大规模，但入门价格也更高。 它专为对性能有持续需求的企业而设计。 这些系统最大限度利用缓存。 

### <a name="capacity-limits"></a>容量限制
默认情况下，每个服务器（例如，myserver.database.windows.net）都具有一个配额，该配额限制该实例上的数据库大小和规模。 服务器可承载 SQL DW 和 SQL DB 数据库，但这些数据库均必须符合配额限制。 此配额通过数据库事务单位 (DTU) 进行计量，默认情况下，此配额设置为 54,000 以允许最多 6000 个 cDWU。 此配额仅仅只是安全限制。 可增加配额，具体方法为创建支持票证，并选择“配额”作为请求类型。 

要计算 DTU 要求，请将以下乘数应用到 DTU 计算：

| 性能层 | 计量单位 | DTU 乘数 | 示例                   |
|:----------------:|----------------:|---------------:|--------------------------:|
| 弹性       |  DWU            | 7.5            | DW6000 x 7.5 = 45,000 DTU |
| 计算          | cDWU            | 9              | DW6000 x 7.5 = 54,000 DTU |

在门户中查看 SQL Server 属性可了解当前 DTU 消耗量。

## <a name="how-many-data-warehouse-units-do-i-need"></a>我需要多少个数据仓库单位？
合适的数据仓库单位数很大程度上取决于工作负荷及已加载到系统的数据量。

查找最适合工作负荷的 DWU 的步骤：

1. 开发期间，首先使用弹性优化性能层选择较小的 DWU。  由于此阶段的问题在于功能验证，因此选择弹性优化性能层是合理的。 DW200 是一个好的起点。 
2. 在测试数据加载到系统中时，监视应用程序性能，将所选 DWU 数目与观测到的性能变化进行比较。
3. 确认峰值活动周期的其他要求。 如果工作负荷在活动中显示出重要的峰值和谷值，则有充分理由进行频繁缩放，然后选择弹性优化性能层。
4. 如果所需 DWU 超过 1000，则选择计算优化性能层，因为这可提供最佳性能。

SQL 数据仓库是一个向外扩展系统，可预配大量计算和查询大量数据。 要查看其真正的缩放功能（尤其是针对较大的 DWU），建议在缩放的同时对数据集进行缩放，确保可向 CPU 提供足够的数据。 对于规模测试，建议至少使用 1 TB。

> [!NOTE]
>
> 此外，如果可以计算节点之间拆分工作，与多个并行化只会增加查询性能。 如果发现该缩放未更改性能，建议优化表设计和/或查询。 对于查询优化指南，请参阅以下[性能](sql-data-warehouse-overview-manage-user-queries.md)文章。 

## <a name="permissions"></a>权限

更改数据仓库单位需要 [ALTER DATABASE][ALTER DATABASE] 中所述的权限。 

## <a name="view-current-dwu-settings"></a>查看当前的 DWU 设置

查看当前的 DWU 设置：

1. 在 Visual Studio 中打开“SQL Server 对象资源管理器”。
2. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
3. 从 sys.database_service_objectives 动态管理视图中选择。 下面是一个示例： 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>更改数据仓库单位

### <a name="azure-portal"></a>Azure 门户
更改 DWU 或 cDWU：

1. 打开 [Azure 门户](https://portal.azure.com)，打开数据库，并单击“缩放”。

2. 在“缩放”下，向左或向右移动滑块，以更改 DWU 设置。

3. 单击“保存” 。 此时会显示确认消息。 单击“是”以确认或“否”以取消。

### <a name="powershell"></a>PowerShell
要更改 DWU 或 cDWU，请使用 [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase] PowerShell cmdlet。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
使用 T-SQL 可查看当前的 DWU 或 cDWU 设置、更改设置和检查进度。 

更改 DWU 或 cDWU：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 使用 [ALTER DATABASE][ALTER DATABASE] TSQL 语句。 以下示例将数据库 MySQLDW 的服务级别目标设置为 DW1000。 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

要更改 DWU，请使用 [创建或更新数据库][Create or Update Database] REST API。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>检查 DWU 更改的状态

DWU 更改可能需要几分钟才能完成。 如果要自动缩放，建议实现逻辑以确保某些操作在继续另一项操作前已完成。 

检查通过不同终结点的数据库状态可允许你正确实现自动化。 门户在完成操作和数据库当前状态时提供通知，但不允许以编程方式检查状态。 

不能使用 Azure 门户检查向外扩展操作的数据库状态。

检查 DWU 更改的状态：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 提交以下查询以检查数据库状态。


```sql
SELECT    *
FROM      sys.databases
;
```

3. 提交以下查询以检查操作状态

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

此 DMV 返回针对 SQL 数据仓库的各种管理操作的相关信息，例如操作和操作状态（IN_PROGRESS 或 COMPLETED）。

## <a name="the-scaling-workflow"></a>缩放工作流

启动缩放操作时，系统首先终止所有打开的会话，回退所有打开的事务以确保状态一致。 对于缩放操作，缩放仅在此事务回退完成后才会发生。  

- 对于增加操作，系统会预配额外计算，然后重新附加到存储层。 
- 对于减少操作，不需要的节点会从存储中分离出来并重新附加到剩余节点。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解其他一些重要性能概念：

* [工作负荷和并发管理][Workload and concurrency management]
* [表设计概述][Table design overview]
* [表分发][Table distribution]
* [表索引][Table indexing]
* [表分区][Table partitioning]
* [表统计信息][Table statistics]
* [最佳做法][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./sql-data-warehouse-develop-concurrency.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
