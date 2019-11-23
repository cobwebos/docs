---
title: Data Warehouse Units (DWUs) in Azure Synapse Analytics (formerly SQL DW)
description: Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7cd6a037f339f193f63cbe152f0ea9964679c231
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420496"
---
# <a name="data-warehouse-units-dwus"></a>Data Warehouse Units (DWUs)

Recommendations on choosing the ideal number of data warehouse units (DWUs) to optimize price and performance, and how to change the number of units.

## <a name="what-are-data-warehouse-units"></a>What are Data Warehouse Units

A [SQL pool](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) represents a collection of analytic resources that are being provisioned when using [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Analytic resources are defined as a combination of CPU, memory and IO. These three resources are bundled into units of compute scale called Data Warehouse Units (DWUs). DWU 表示抽象、规范化的计算资源和性能度量值。 A change to your service level alters the number of DWUs that are available to the system, which in turn adjusts the performance, and the cost, of your system.

For higher performance, you can increase the number of data warehouse units. For less performance, reduce data warehouse units. 存储和计算成本分别计费，因此更改数据仓库单位数不会影响存储成本。

Performance for data warehouse units is based on these workload metrics:

- How fast a standard data warehousing query can scan a large number of rows and then perform a complex aggregation. 这是一种 I/O 和 CPU 密集型操作。
- How fast the data warehouse can ingest data from Azure Storage Blobs or Azure Data Lake. 这是一种网络和 CPU 密集型操作。
- How fast the [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL command can copy a table. 此操作涉及从存储读取数据、将数据分配到设备的节点上，以及重新将数据写入到存储。 这是一种 CPU、IO 和网络密集型操作。

增加 DWU：

- 以线性方式更改系统对扫描、聚合和 CTAS 语句的性能
- 增加 PolyBase 加载操作的读取器和编写器数量
- 增加并发查询和并发槽的最大数量。

## <a name="service-level-objective"></a>服务级别目标

服务级别目标 (SLO) 是确定数据仓库的成本和性能级别的可伸缩性设置。 The service levels for Gen2 SQL pool are measured in data warehouse units (DWU), for example DW2000c.

In T-SQL, the SERVICE_OBJECTIVE setting determines the service level for your SQL pool.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>容量限制

每个 SQL Server（例如 myserver.database.windows.net）都有一个允许指定数据仓库单位数的[数据库事务单位 (DTU)](../sql-database/sql-database-what-is-a-dtu.md) 配额。 有关详细信息，请参阅[工作负荷管理容量限制](sql-data-warehouse-service-capacity-limits.md#workload-management)。

## <a name="how-many-data-warehouse-units-do-i-need"></a>How many data warehouse units do I need

合适的数据仓库单位数很大程度上取决于工作负荷及已加载到系统的数据量。

查找最适合工作负荷的 DWU 的步骤：

1. 首先选择一个较小的 DWU。
2. 在测试数据加载到系统中时，监视应用程序性能，将所选 DWU 数目与观测到的性能变化进行比较。
3. 确认峰值活动周期的其他要求。 Workloads that show significant peaks and troughs in activity may need to be scaled frequently.

SQL Analytics is a scale-out system that can provision vast amounts of compute and query sizeable quantities of data. 要查看其真正的缩放功能（尤其是针对较大的 DWU），建议在缩放的同时对数据集进行缩放，确保可向 CPU 提供足够的数据。 对于规模测试，建议至少使用 1 TB。

> [!NOTE]
>
> 此外，如果可以计算节点之间拆分工作，与多个并行化只会增加查询性能。 如果发现该缩放未更改性能，建议优化表设计和/或查询。 有关查询优化指南，请参阅[管理用户查询](sql-data-warehouse-overview-manage-user-queries.md)。

## <a name="permissions"></a>权限

更改数据仓库单位需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) 中所述的权限。

Azure 资源的内置角色（如 SQL DB 参与者和 SQL Server 参与者）可以更改 DWU 设置。

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

To change DWUs:

1. 打开 [Azure 门户](https://portal.azure.com)，打开数据库，并单击“缩放”。

2. 在“缩放”下，向左或向右移动滑块，以更改 DWU 设置。

3. 单击“保存”。 此时会显示确认消息。 单击“是”以确认或“否”以取消。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

To change the DWUs, use the [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet. The following example sets the service level objective to DW1000c for the database MySQLDW that is hosted on server MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

有关详细信息，请参阅[适用于 SQL 数据仓库的 PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

With T-SQL you can view the current DWU settings, change the settings, and check the progress.

若要更改 DWU，请执行以下操作：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 语句。 The following example sets the service level objective to DW1000c for the database MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API

若要更改 DWU，请使用[创建或更新数据库 REST API](/rest/api/sql/databases/createorupdate)。 The following example sets the service level objective to DW1000c for the database MySQLDW, which is hosted on server MyServer. 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

有关更多 REST API 示例，请参阅[适用于 SQL 数据仓库的 REST API](sql-data-warehouse-manage-compute-rest-api.md)。

## <a name="check-status-of-dwu-changes"></a>检查 DWU 更改的状态

DWU 更改可能需要几分钟才能完成。 如果要自动缩放，建议实现逻辑以确保某些操作在继续另一项操作前已完成。

检查通过不同终结点的数据库状态可允许你正确实现自动化。 门户在完成操作和数据库当前状态时提供通知，但不允许以编程方式检查状态。

不能使用 Azure 门户检查向外扩展操作的数据库状态。

检查 DWU 更改的状态：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。

1. 提交以下查询以检查数据库状态。

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. 提交以下查询以检查操作状态

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
This DMV returns information about various management operations on your SQL pool such as the operation and the state of the operation, which is either IN_PROGRESS or COMPLETED.

## <a name="the-scaling-workflow"></a>缩放工作流

When you start a scale operation, the system first kills all open sessions, rolling back any open transactions to ensure a consistent state. 对于缩放操作，缩放仅在此事务回退完成后才会发生。  

- For a scale-up operation, the system detaches all compute nodes, provisions the additional compute nodes, and then reattaches to the storage layer.
- For a scale-down operation, the system detaches all compute nodes and then reattaches only the needed nodes to the storage layer.

## <a name="next-steps"></a>后续步骤

若要了解有关如何管理性能的详细信息，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)和[内存和并发限制](memory-concurrency-limits.md)。
