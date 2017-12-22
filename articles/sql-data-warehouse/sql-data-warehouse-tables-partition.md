---
title: "对 SQL 数据仓库中的表进行分区 | Microsoft Docs"
description: "Azure SQL 数据仓库中的表分区入门。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a28cb1f8a2e48332b344566620dc49b29d9d3c99
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>对 SQL 数据仓库中的表进行分区
> [!div class="op_single_selector"]
> * [概述][Overview]
> * [数据类型][Data Types]
> * [分布][Distribute]
> * [索引][Index]
> * [分区][Partition]
> * [统计信息][Statistics]
> * [临时][Temporary]
> 
> 

所有 SQL 数据仓库表类型（包括聚集列存储、聚集索引和堆）都支持分区。  所有分布类型（包括哈希分布或轮循机制分布）也都支持分区。  可以通过分区将数据分成较小的数据组，而且在大多数情况下，分区是根据日期列来完成的。

## <a name="benefits-of-partitioning"></a>分区的好处
分区可能有利于数据维护和查询性能。  分区是对二者都有利还是只对其中之一有利取决于数据加载方式，以及是否可以将同一个列用于两种目的，因为只能根据一个列来进行分区。

### <a name="benefits-to-loads"></a>对加载的好处
在 SQL 数据仓库中分区的主要好处是通过分区删除、切换和合并来提高数据加载效率和性能。  大多数情况下，数据是根据日期列来分区的，而日期列与数据加载到数据库中的顺序密切相关。  使用分区来维护数据的最大好处之一是可以避免事务日志记录。  虽然直接插入、更新或删除数据可能是最直接的方法，但如果在加载过程中使用分区，则只需付出一点点思考和努力就可以大大改进性能。

可以使用分区切换来快速删除或替换表的一部分。  例如，销售事实表可能仅包含过去 36 个月的数据。  在每个月月底，便从表删除最旧月份的销售数据。  删除该数据时，可以使用 delete 语句删除最旧月份的数据。  但是，使用 delete 语句逐行删除大量数据可能需要极长的时间，同时还会有执行大型事务的风险，这些大型事务在出现错误时进行回退的时间会很长。  更理想的方式是删除最旧的数据分区。  如果在某种情况下删除各个行可能需要数小时，则删除整个分区可能只需数秒钟。

### <a name="benefits-to-queries"></a>对查询的好处
分区还可用来提高查询性能。  对分区数据应用筛选器的查询可以将扫描限制在合格的分区上。 此筛选方法可以避免全表扫描且仅扫描数据的一个较小子集。 引入聚集列存储索引以后，谓词消除的性能好处不再那么明显，但在某些情况下，可能会对查询有好处。  例如，如果使用销售日期字段将销售事实表分区成 36 个月，以销售日期进行筛选的查询便可以跳过对不符合筛选条件的分区的搜索。

## <a name="partition-sizing-guidance"></a>分区大小调整指南
虽然在某些情况下可以使用分区来改进性能，但如果在创建表时使用**过多**分区，则在某些情况下可能会降低性能。  对于聚集列存储表，尤其要考虑到这一点。  若要使数据分区有益于性能，务必了解使用数据分区的时机，以及要创建的分区的数目。  对于多少分区属于分区过多并没有简单的硬性规定，具体取决于数据，以及要同时加载到多少分区。  一个成功的分区方案通常只有数十到数百的分区，没有数千个。

在“聚集列存储”表上创建分区时，务请考虑每个分区可容纳的行数。  对于聚集列存储表来说，若要进行最合适的压缩并获得最佳性能，则每个分布和分区至少需要 1 百万行。  在创建分区之前，SQL 数据仓库已将每个表细分到 60 个分布式数据库中。  向表添加的任何分区都是基于在后台创建的分布。  根据此示例，如果销售事实表包含 36 个月的分区，并假设 SQL 数据仓库有 60 个分布区，则销售事实表每个月应包含 6000 万行，或者在填充所有月份时包含 21 亿行。  如果表包含的行数远少于每个分区行数的最小建议值，可考虑使用较少的分区，以增加每个分区的行数。  另请参阅[索引编制][Index]一文，其中包含的查询可以在 SQL 数据仓库上运行，用于评估群集列存储索引的质量。

## <a name="syntax-difference-from-sql-server"></a>与 SQL Server 的语法差异
SQL 数据仓库引入了分区定义的简化方法，这与 SQL Server 略有不同。  在 SQL 数据仓库中不像在 SQL Server 中一样使用分区函数和方案。  只需识别分区列和边界点。  尽管分区的语法可能与 SQL Server 稍有不同，但基本概念是相同的。  SQL Server 和 SQL 数据仓库支持一个表一个分区列，后者可以是范围分区。  若要了解有关分区的详细信息，请参阅[已分区表和已分区索引][Partitioned Tables and Indexes]。

下例为 SQL 数据仓库分区的 [CREATE TABLE][CREATE TABLE] 语句，根据 OrderDateKey 列对 FactInternetSales 表进行了分区：

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>从 SQL Server 迁移分区
要将 SQL Server 分区定义迁移到 SQL 数据仓库，只需执行以下操作即可：

* 消除 SQL Server [分区方案][partition scheme]。
* 将[分区函数][partition function]定义添加到 CREATE TABLE。

如果要从 SQL Server 实例迁移分区的表，则可使用以下 SQL 来算出每个分区中的行数。  请记住，如果在 SQL 数据仓库上使用相同的分区粒度，则每个分区的行数会下降到原来的 1/60。  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>工作负荷管理
需要纳入表分区决策的最后一项考虑事项是[工作负荷管理][workload management]。  在 SQL 数据仓库中，工作负荷管理主要是管理内存和并发。  在 SQL 数据仓库中，资源类控制在查询执行期间分配给每个分布的最大内存。  理想情况下，调整分区大小需考虑其他因素，例如在构建聚集列存储索引时的内存需求。  为聚集列存储索引分配更多内存对其有很大好处。  因此，需要确保重建分区索引不会耗尽内存。 从默认角色 (smallrc) 切换到其他某个角色（例如 largerc），即可增加查询能够使用的内存量。

查询资源调控器动态管理视图即可获取每个分布的内存分配信息。 事实上，内存授予小于以下各图。 不过，这在一定程度上提供了指导，可用于设置数据管理操作分区大小。  尽量避免将分区大小调整超过超大型资源类所提供的内存授予。 如果分区成长超过此数据，就冒着内存压力的风险，进而导致比较不理想的压缩。

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>分区切换
SQL 数据仓库支持分区拆分、合并和切换。 这些函数中，每个都是使用 [ALTER TABLE][ALTER TABLE] 语句执行的。

若要切换两个表间的分区，必须确定分区对齐其各自的边界，而且表定义匹配。 检查约束不适用于强制表中的值范围，源表必须包含与目标表相同的分区边界。 如果情况不是如此，则分区切换会失败，因为分区元数据不会同步。

### <a name="how-to-split-a-partition-that-contains-data"></a>如何拆分包含数据的分区
使用 `CTAS` 语句是拆分包含数据的分区的最有效方法。 如果分区表是群集列存储，则表分区必须为空才能拆分。

以下示例显示了每个分区包含一个行的分区列存储表：

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> 通过创建统计信息对象，我们可以确保表元数据更加准确。 如果我们省略了创建统计信息这一步，SQL 数据仓库将使用默认值。 有关统计信息的详细信息，请参阅[统计信息][statistics]。
> 
> 

然后，可以使用 `sys.partitions` 目录视图来查询行计数：

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

如果尝试拆分此表，会收到错误：

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

消息 35346，级别 15，状态 1，行 44: ALTER PARTITION 语句的 SPLIT 子句失败，因为分区不为空。  仅当表上存在列存储索引时，才可以拆分空分区。 请考虑在发出 ALTER PARTITION 语句前禁用列存储索引，并在 ALTER PARTITION 完成后重建列存储索引。

但是，我们可以使用 `CTAS` 创建新表以保存数据。

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

分区边界已对齐，因此允许切换。 这使源表有空白分区可供我们完成后续拆分。

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

接下来只需使用 `CTAS` 将数据对齐新的分区边界，并将数据切换回到主表

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

完成数据移动后，最好是刷新目标表上的统计信息，确保统计信息可在其各自的分区中准确反映数据的新分布：

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>表分区源代码管理
若要避免表定义在源代码管理系统中**失效**，可以考虑以下方法：

1. 将表创建为分区表，但不包含分区值

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. 在部署过程中 `SPLIT` 表：

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

使用这种方法时，源代码管理中的代码将保持静态，允许动态的分区边界值，并不断地与仓库一起演进。

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅有关[表概述][Overview]、[表数据类型][Data Types]、[分布表][Distribute]、[为表编制索引][Index]、[维护表统计信息][Statistics]和[临时表][Temporary]的文章。  有关最佳实践的详细信息，请参阅 [SQL 数据仓库最佳实践][SQL Data Warehouse Best Practices]。

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
