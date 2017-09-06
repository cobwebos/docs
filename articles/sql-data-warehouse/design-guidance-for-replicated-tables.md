---
title: "复制表的设计指南 - Azure SQL 数据仓库 | Microsoft Docs"
description: "在 Azure SQL 数据仓库架构中设计复制表的建议。"
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/14/2017
ms.author: rortloff;barbkess
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 437a4f628a343312984d1fa2981df7fa01459e26
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---

# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>在 Azure SQL 数据仓库中使用复制表的设计指南
本文提供在 SQL 数据仓库架构中设计复制表的建议。 使用这些建议，可减少数据移动并降低查询复杂性，从而提高查询性能。

> [!NOTE]
> 复制表功能当前以公共预览版提供。 某些行为随时会变化。
> 

## <a name="prerequisites"></a>先决条件
本文假设读者熟悉 SQL 数据仓库中的数据分布和数据移动概念。  有关详细信息，请参阅[分布式数据](sql-data-warehouse-distributed-data.md)。 

在设计表的过程中，尽可能多地了解数据以及数据查询方式。  例如，考虑以下问题：

- 表有多大？   
- 表的刷新频率是多少？   
- 数据仓库中有事实数据表和维度表吗？   

## <a name="what-is-a-replicated-table"></a>什么是复制表？
复制表是表的完整副本，可在每个计算节点上进行访问。 复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 因为表有多个副本，因此在压缩的表大小小于 2 GB 时，复制表效果最佳。

下图显示了一个可在任意计算节点上访问的复制表。 在SQL 数据仓库中，复制表完整复制到每个计算节点上的分发数据库。 

![复制表](media/guidance-for-using-replicated-tables/replicated-table.png "复制表")  

复制表非常适合星型架构的小型维度表。 维度表的大小通常使其可以存储和维护多个副本。 维度用于存储变化缓慢的描述性数据，例如客户名称和地址以及产品详细信息。 由于数据变化缓慢，因此重新生成复制表的次数较少。 

在以下情况下，考虑使用复制表：

- 磁盘上的表小于 2 GB，无论行数。 若要确定表大小，可以使用 [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/en-us/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) 命令：`DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`。 
- 在联接中使用表，否则需要数据移动。 例如，当联接列不是相同的分布列时，哈希分布表上的联接需要数据移动。 如果其中一个哈希分布表较小，请考虑使用复制表。 轮循表上的联接需要数据移动。 在大多数情况下，我们建议使用复制表，而不是轮循表。 


在以下情况下，考虑将现有分布式表转换为复制表：

- 查询计划使用将数据广播到所有计算节点的数据移动操作。 BroadcastMoveOperation 成本高昂，并且会降低查询性能。 要查看查询计划中的数据移动操作，请使用 [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)。
 
在以下情况下，复制表可能无法实现最好的查询性能：

- 对表进行频繁的插入、更新和删除操作。 这些数据操作语言 (DML) 操作需要重新生成复制表。 经常重新生成可能会降低性能。
- 数据仓库缩放频繁。 缩放数据仓库会更改计算节点的数量，这会导致重新生成。
- 表中包含大量列，但数据操作通常只访问少量列。 在这种情况下，与复制表相比，对表进行哈希分布，然后在经常访问的列上创建索引可能更为高效。 当查询需要数据移动时，SQL 数据仓库仅移动请求列中的数据。 



## <a name="use-replicated-tables-with-simple-query-predicates"></a>对简单查询谓词使用复制表
在选择分布或复制表之前，请先考虑计划对表运行的查询类型。 只要有可能，

- 对包含简单查询谓词（例如等式或不等式）的查询使用复制表。
- 对包含复杂查询谓词（例如 LIKE 或 NOT LIKE）的查询使用分布式表。

在工作分布在所有计算节点上时，CPU 密集型查询的效果最好。 例如，对于在表的每一行上运行计算的查询，针对分布式表进行查询比针对复制表进行查询效果更好。 由于复制表完整存储在每个计算节点上，因此对复制表的 CPU 密集型查询会针对每个计算节点上的整个表运行。 额外的计算可能会降低查询性能。

例如，此查询包含一个复制谓词。  供应商为分布式表而不是复制表时，它的运行速度更快。 在此示例中，供应商可以是哈希分布表或轮循分布表。

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>将现有轮循表转换为复制表
如果已有轮循表，在其符合本文中概述的条件时，建议将其转换为复制表。 复制表提高了轮循表的性能，因为前者不需要移动数据。  循环表的联接总是需要移动数据。 

下面的示例使用 [CTAS](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 将 DimSalesTerritory 表更改为复制表。 无论 DimSalesTerritory 是哈希分布表还是轮询表，此示例都适用。

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>轮循表与复制表的查询性能示例 

复制表的联接不需要移动数据，因为整个表都存在于每个计算节点上。 如果维度表是轮循分布表，则联接会将维度表完整复制到每个计算节点上。 为了移动数据，查询计划包含一个名为 BroadcastMoveOperation 的操作。 此类数据移动操作会降低查询性能，可通过使用复制表避免此问题。 要查看查询计划步骤，请使用 [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) 系统目录视图。 

例如，在针对 AdventureWorks 架构的以下查询中，` FactInternetSales` 表是哈希分布表。 `DimDate` 和 `DimSalesTerritory` 表是较小的维度表。 此查询将返回 2004 财年北美地区的销售总额：
 
```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
将 `DimDate` 和 `DimSalesTerritory` 重新创建为轮循表。 结果，查询显示了以下查询计划，其中包含多个广播移动操作： 
 
![轮循查询计划](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

将 `DimDate` 和 `DimSalesTerritory` 重新创建为复制表，然后重新运行查询。 生成的查询计划要短很多，而且不移动任何广播。

![复制查询计划](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>修改复制表的性能注意事项
SQL 数据仓库通过维护表的主版本来实现复制表。 它将主版本复制到每个计算节点上的某个分发数据库。 发生更改时，SQL 数据仓库会首先更新主表。 然后需要在每个计算节点上重新生成表。 重新生成复制表包括将表复制到每个计算节点，然后重新生成索引。

执行以下操作后需要重新生成：
- 加载或修改数据
- 数据仓库缩放为不同的 DWU 设置
- 更新表定义

执行以下操作后不需要重新生成：
- 暂停操作
- 恢复操作

修改数据后不会立即重新生成。 而是在查询首次从表中进行选择时触发重新生成。  表的初始 Select 语句中包含重新生成复制表的步骤。  因为重新生成是在查询内完成的，所以对初始 Select 语句的影响可能会很大，具体取决于表的大小。  如果需要重新生成多个复制表，将按语句中的步骤连续重新生成每个副本。  为了在重新生成复制表期间保持数据一致性，会在表上使用排他锁。  在重新生成期间，该锁可防止对表的所有访问。 

### <a name="use-indexes-conservatively"></a>谨慎使用索引
标准索引适用于复制表。 SQL 数据仓库在重新生成的过程中重新生成每个复制表索引。 仅当重新生成索引带来的好处超出了相关成本时，才使用索引。  
 
### <a name="batch-data-loads"></a>批处理数据加载
将数据加载到复制表中时，请尝试通过批量加载最大限度减少重新生成。 在运行 Select 语句之前执行所有批量加载。

例如，此负载模式从 4 个源加载数据并调用 4 个重新生成。 

- 从源 1 加载。
- Select 语句触发重新生成 1。
- 从源 2 加载。
- Select 语句触发重新生成 2。
- 从源 3 加载。
- Select 语句触发重新生成 3。
- 从源 4 加载。
- Select 语句触发重新生成 4。

例如，此负载模式从 4 个源加载数据，但仅调用 1 个重新生成。

- 从源 1 加载。
- 从源 2 加载。
- 从源 3 加载。
- 从源 4 加载。
- Select 语句触发重新生成。


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>批量加载后重新生成复制表
为了确保查询执行时间一致，建议在批量加载后强制刷新复制表。 否则，第一个查询必须等待表刷新，包括重新生成索引。 受影响的复制表的大小和数量可显著影响性能。  

此查询使用 [sys.pdw_replicated_table_cache_state ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV列出已修改但未重新生成的复制表。

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
若要强制重新生成，请在上一个输出中的每个表上运行以下语句。 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>后续步骤 
若要创建复制表，请使用以下语句之一：

- [CREATE TABLE (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)（创建表（Azure SQL 数据仓库））
- [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)（CREATE TABLE AS SELECT（Azure SQL 数据仓库））

有关分布式表的概述，请参阅[分布式表](sql-data-warehouse-tables-distribute.md)。




