---
title: Azure SQL 数据库文件空间管理 | Microsoft Docs
description: 本页介绍如何管理 Azure SQL 数据库的文件空间，并提供代码示例来演示如何确定是否需要收缩数据库，以及如何执行数据库收缩操作。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416000"
---
# <a name="manage-file-space-in-azure-sql-database"></a>管理 Azure SQL 数据库中的文件空间

本文介绍 Azure SQL 数据库中不同类型的存储空间，以及当客户需要管理分配给数据库和弹性池的文件空间时可以执行的步骤。

## <a name="overview"></a>概述

在 Azure SQL 数据库中，Azure 门户和以下 API 显示的存储大小指标用于度量数据库与弹性池使用的数据页数：
- 基于 Azure 资源管理器的指标 API，包括 PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric)
- T-SQL：[sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL：[sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL：[sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

在某些工作负荷模式下，数据库底层数据文件中分配的空间会超过数据文件中使用的数据页数。 如果使用的空间不断增大，以致后续删除了数据，则可能会出现这种情况。 删除数据后，分配的文件空间不会自动回收。 在这种情况下，为数据库或池分配的空间可能超过针对数据库设置的受支持最大限制，因此，会阻止数据增长或性能层更改，即使数据库实际使用的空间小于最大空间限制。 为缓解问题，可能需要收缩数据库，以减少数据库中已分配的但未使用的空间。

SQL 数据库服务不会自动收缩数据库文件来回收未使用的分配空间，因为这可能会影响数据库的性能。 但是，可以遵循[回收未使用的分配空间](#reclaim-unused-allocated-space)中所述的步骤，在选定的时间收缩数据库中的文件。 

> [!NOTE]
> 与数据文件不同，SQL 数据库服务会自动收缩日志文件，因为该操作不会影响数据库的性能。

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>了解数据库存储空间的类型

若要管理文件空间，需要了解与单一数据库和弹性池的数据库存储相关的以下术语。

|存储空间术语|定义|注释|
|---|---|---|
|**已用数据空间**|用于存储数据库数据的空间量，以 8 KB 页面数为单位。|通常，此已用空间会在执行插入操作时增大，在执行删除操作时减小。 在某些情况下，已用空间不会在执行插入或删除操作时发生变化，具体取决于该操作涉及的数据数量和模式，以及是否有任何碎片。 例如，从每个数据页中删除一行不一定会减小已用空间。|
|**分配的空间**|可用于存储数据库数据的格式化文件空间量|分配的空间会自动增长，但执行删除操作后永远不会减小。 此行为可确保将来的插入操作速度更快，因为不需要重新设置空间的格式。|
|**已分配但未使用的空间**|分配给数据库的未使用数据文件空间量。|此数量是已分配空间量与未使用空间量之差，表示可以通过收缩数据库文件回收的最大空间量。|
|**最大大小**|可由数据库使用的最大数据空间量。|分配的数据空间在增长后不能超过数据最大大小。|
||||

下图演示了存储空间类型之间的关系。

![存储空间类型和关系](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>查询数据库的存储空间信息

若要确定单个数据库是否存在可以回收的已分配但未使用的数据空间，请使用以下查询：

### <a name="database-data-space-used"></a>已用的数据库数据空间
修改以下查询，返回已用的数据库数据空间量（以 MB 为单位）。

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>已分配的，以及已分配但未使用的数据库数据空间量
修改以下查询，返回已分配的，以及已分配但未使用的数据库数据空间量。

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>数据库最大大小
修改以下查询，返回数据库最大大小（以字节为单位）。

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>查询弹性池的存储空间信息

若要确定某个弹性池以及每个入池数据库中是否存在可以回收的已分配但未使用的数据空间，请使用以下查询：

### <a name="elastic-pool-data-space-used"></a>已用的弹性池数据空间
修改以下查询，返回已用的弹性池数据空间量（以 MB 为单位）。

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>已分配的，以及已分配但未使用的弹性池数据空间量

修改以下 PowerShell 脚本来返回一个表，其中列出了为弹性池中每个数据库分配的总空间，以及已分配但未使用的空间。 该表中数据库的排序顺序为：已分配但未使用空间量最大的数据库排在最前，已分配但未使用空间量最小的数据库排在最后。  

将查询结果（确定分配给池中每个数据库的空间）相加，可以确定分配的弹性池空间。 分配的弹性池空间不应超过弹性池最大大小。  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

以下屏幕截图显示了脚本输出的示例：

![已分配的，以及已分配但未使用的弹性池空间示例](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>弹性池最大大小

使用以下 T-SQL 查询返回弹性数据库最大大小（以 MB 为单位）。

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>回收已分配但未使用的空间

确定有可回收的已分配但未使用的空间后，使用以下命令来收缩已分配的数据库空间。 

> [!IMPORTANT]
> 对于弹性池中的数据库，应该先收缩已分配但未使用空间量最大的数据库，以便最快地回收文件空间。  

使用以下命令收缩指定数据库中的所有数据文件：

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

有关此命令的详细信息，请参阅 [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)。

> [!IMPORTANT] 
> 收缩数据库数据文件后，请考虑重新生成数据库索引，因为索引可能碎片化，失去其性能优化效力。 如果出现这种情况，则应重新生成索引。 有关碎片和重新生成索引的详细信息，请参阅[重新组织和重新生成索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)。

## <a name="next-steps"></a>后续步骤

- 有关最大数据库大小的信息，请参阅：
  - [适用于单一数据库的 Azure SQL 数据库基于 vCore 的购买模型限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [使用基于 DTU 的购买模型的单一数据库的资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [适用于弹性池的 Azure SQL 数据库基于 vCore 的购买模型限制](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [使用基于 DTU 的购买模型的弹性池的资源限制](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- 有关 `SHRINKDATABASE` 命令的详细信息，请参阅 [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql)。 
- 有关碎片和重新生成索引的详细信息，请参阅[重新组织和重新生成索引](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes)。