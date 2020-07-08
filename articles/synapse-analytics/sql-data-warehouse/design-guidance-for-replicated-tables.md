---
title: 复制表设计指南
description: 有关在 Synapse SQL 池中设计复制表的建议
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8328750849f5466c8754499694a41615776ff3da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201695"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql-pool"></a>有关在 Synapse SQL 池中使用复制表的设计指南

本文提供了有关在 Synapse SQL 池架构中设计复制表的建议。 可以使用这些建议通过减少数据移动和降低查询复杂性来提高查询性能。

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>先决条件

本文假设读者熟悉 SQL 池中的数据分布和数据移动概念。  有关详细信息，请参阅[体系结构](massively-parallel-processing-mpp-architecture.md)一文。

作为表设计的一部分，请尽可能多地去了解你的数据及其查询方式。例如，请考虑以下问题：

- 表有多大？
- 表的刷新频率是多少？
- SQL 池数据库中是否包含事实数据表和维度表？

## <a name="what-is-a-replicated-table"></a>什么是复制的表？

复制的表具有可在每个计算节点上访问的完整表副本。 复制表后，在执行联接或聚合前将无需在计算节点之间传输数据。 由于表具有多个副本，因此当表压缩后的大小小于 2 GB 时，复制的表性能最佳。  2 GB 不是硬性限制。  如果数据为静态数据，不会更改，则可复制更大的表。

下图显示了可在每个计算节点上访问的复制表。 在 SQL 池中，复制表完整复制到每个计算节点上的分发数据库。

![复制表](./media/design-guidance-for-replicated-tables/replicated-table.png "复制表")  

复制表非常适合星型架构中的维度表。 维度表通常联接到事实数据表，后者的分发不同于维度表。  通常情况下，维度的大小让存储并维护多个副本变得可行。 维度存储着不常更改的描述性数据，例如，客户名称和地址以及产品详细信息。 该数据的缓变本性使复制的表不会经历太多的维护。

在下列情况下，请考虑使用复制的表：

- 磁盘上的表大小小于 2 GB，无论有多少行。 若要查明表的大小，可以使用 [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 命令：`DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`。
- 表用于不采用复制的表时将要求移动数据的联接中。 连接未分布在同一列上的表（如将哈希分布式表连接到轮循机制表）时，需要进行数据移动才能完成此查询。  如果其中一个表较小，请考虑使用复制表。 大多数情况下，我们建议使用复制的表而非循环表。 若要查看查询计划中的数据移动操作，请使用 [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  BroadcastMoveOperation 是典型的数据移动操作，可通过使用复制的表来消除。  

在下列情况下，复制的表可能不会产生最佳查询性能：

- 表具有频繁的插入、更新和删除操作。这些数据操作语言 (DML) 操作要求重新生成复制表。频繁地重新生成会导致性能降低。
- SQL 池数据库会频繁缩放。 缩放 SQL 池数据库会更改计算节点数，这会重新生成复制表。
- 表具有大量列，但数据操作通常仅访问少量的列。 在这种情况下，与复制整个表相比，将表分发，然后对经常访问的列创建索引可能更为高效。 当查询需要进行数据移动时，SQL 池仅移动所请求列中的数据。

## <a name="use-replicated-tables-with-simple-query-predicates"></a>将复制的表与简单的查询谓词一起使用

在选择分发或复制表之前，请考虑计划对表运行的查询类型。 只要可能，

- 对于具有简单查询谓词（例如等于或不等于）的查询，请使用复制的表。
- 对于具有复杂查询谓词（例如 LIKE 或 NOT LIKE）的查询，请使用分布式表。

当工作分布在所有计算节点中时，CPU 密集型查询的性能最佳。 例如，对表的每个行运行计算的查询在分布式表上的性能要好于在复制的表上的性能。 由于复制的表完整存储在每个计算节点上，因此，针对复制的表的 CPU 密集型查询将针对每个计算节点上的整个表运行。 此额外的计算会降低查询性能。

例如，以下查询具有复杂谓词。  当数据位于分布式表而非复制的表中时，它运行更快。 在此示例中，数据可以是循环分布式的。

```sql

SELECT EnglishProductName
FROM DimProduct
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>将现有的循环表转换为复制的表

如果已经具有循环表，如果它们满足本文中列出的条件，建议将其转换为复制的表。 与循环表相比，复制的表可以提高性能，因为它们不要求移动数据。  循环表始终要求为联接移动数据。

此示例使用 [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 将 DimSalesTerritory 表更改为复制的表。 无论 DimSalesTerritory 是哈希分布式表还是循环表，此示例都是可行的。

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]
WITH
  (
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE')

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>循环表与复制的表的查询性能对比示例

复制的表不要求为联接移动任何数据，因为整个表已存在于每个计算节点上。 如果维度表是循环分布式的，则联接会将维度表整个复制到每个计算节点。 为了移动数据，查询计划包含了一个名为 BroadcastMoveOperation 的操作。 此类数据移动操作会降低查询性能，使用复制的表可以避免。 若要查看查询计划步骤，请使用 [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 系统目录视图。  

例如，在针对 AdventureWorks 架构的以下查询中，`FactInternetSales` 表是哈希分布式的。 `DimDate` 和 `DimSalesTerritory` 表是较小的维度表。 此查询返回 2004 会计年度在北美的总销售额：

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

我们已将 `DimDate` 和 `DimSalesTerritory` 重新创建为循环表。 因此，查询显示了以下查询计划，该计划具有多个广播移动操作：

![轮循查询计划](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg)

我们将 `DimDate` 和 `DimSalesTerritory` 重新创建为复制的表，然后再次运行了查询。 得到的查询计划短了很多，而且没有任何广播移动。

![复制查询计划](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg)

## <a name="performance-considerations-for-modifying-replicated-tables"></a>修改复制的表时的性能注意事项

SQL 池通过维护表的主版本来实现复制表。 它将主版本复制到每个计算节点上的第一个分发数据库。 发生更改时，会先更新主版本，然后重新生成每个计算节点上的表。 重新生成复制表包括将表复制到每个计算节点，然后生成索引。  例如，DW2000c 上的复制表有 5 个数据副本。  每个计算节点上均存在主控副本和完整副本。  所有数据均存储在分发数据库中。 SQL 池使用此模型来支持更快的数据修改语句和灵活的缩放操作。

在以下情况之后，针对复制的表的第一个查询触发异步重新生成：

- 加载或修改了数据
- Synapse SQL 实例缩放到了其他级别
- 更新了表定义

发生下列情况后，不需要重新生成：

- 暂停操作
- 恢复操作

在修改数据后，重新生成不会立即进行。 重新生成将在查询首次从表中选择数据时触发。  触发重新生成的查询将立即从表的主版本读取，同时将数据异步复制到每个计算节点。 在数据完成复制之前，后续查询将继续使用表的主版本。  如果强制执行其他重新生成操作的复制表发生任何活动，则数据复制将失效，并且下一个 select 语句将触发再次复制数据操作。

### <a name="use-indexes-conservatively"></a>谨慎使用索引

标准索引做法适用于复制的表。 SQL 池在重新生成的过程中重新生成每个复制表索引。 只有当提升性能比重新生成索引的成本更重要时，才应使用索引。

### <a name="batch-data-load"></a>批量数据加载

向复制的表中加载数据时，请尝试通过批量处理加载来尽量减少重新生成。 请在运行 select 语句之前执行所有批量加载。

例如，以下加载模式从四个源加载数据并调用四个重新生成。

        Load from source 1.
- Select 语句触发重新生成 1。
        从源 2 进行加载。
- Select 语句触发重新生成 2。
- 从源 3 进行加载。
- Select 语句触发重新生成 3。
- 从源 4 进行加载。
- Select 语句触发重新生成 4。

例如，以下加载模式从四个源加载数据，但仅调用一个重新生成。

- 从源 1 进行加载。
- 从源 2 进行加载。
- 从源 3 进行加载。
- 从源 4 进行加载。
- Select 语句触发重新生成。

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>在批量加载后重新生成复制的表

为了确保查询执行时间一致，建议在批量加载后强制生成复制表。 否则，第一个查询仍将使用数据移动来完成查询。

以下查询使用 [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) DMV 列出已修改但未重新生成的复制的表。

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

若要触发重新生成，请在上一个输出中的每个表上运行以下语句。

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>后续步骤

若要创建复制的表，请使用下列语句之一：

- [CREATE TABLE（SQL 池）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT（SQL 池）](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

有关分布式表的概述，请参阅[分布式表](sql-data-warehouse-tables-distribute.md)。
