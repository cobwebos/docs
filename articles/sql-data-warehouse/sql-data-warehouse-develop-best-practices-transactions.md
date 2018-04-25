---
title: 优化 Azure SQL 数据仓库的事务 | Microsoft Docs
description: 了解如何在尽量降低长时间回退风险的情况下优化 Azure SQL 数据仓库中的事务性代码的性能。
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/19/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 59467c0cd93141cef56e1c9d2f36b0870a589712
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>优化 Azure SQL 数据仓库中的事务
了解如何在尽量降低长时间回退风险的情况下优化 Azure SQL 数据仓库中的事务性代码的性能。

## <a name="transactions-and-logging"></a>事务和日志记录
事务是关系数据库引擎的一个重要组成部分。 SQL 数据仓库在数据修改期间使用事务。 这些事务可以是显式或隐式。 单个 INSERT、UPDATE 和 DELETE 语句都是隐式事务的示例。 显式事务使用 BEGIN TRAN、COMMIT TRAN 或 ROLLBACK TRAN。 显式事务通常用于多个修改语句需要绑定在单个原子单元的时候。 

Azure SQL 数据仓库使用事务日志将更改提交到数据库。 每个分布区都具有其自己的事务日志。 事务日志写入都是自动的。 无需任何配置。 尽管此过程可保证写入，但它确在系统中引入一项开销。 编写事务性高效的代码，可以尽量减少这种影响。 事务性高效的代码大致分为两类。

* 尽可能使用最少日志记录构造
* 使用限定范围的批来处理数据，避免单数形式的长时运行事务
* 对于给定分区的大型修改，采用分区切换模式

## <a name="minimal-vs-full-logging"></a>最少日志记录与完整日志记录
完整记录的操作使用事务日志来跟踪每个行更改，而最少记录的操作只跟踪扩展分配和元数据更改。 因此，最少日志记录涉及只记录失败或显式请求 (ROLLBACK TRAN) 后回退事务所需的信息。 由于事务日志中跟踪的信息少得多，因此最少记录操作的执行性能优于同样大小的完整记录操作。 此外，事务日志中写入较少，因此生成的日志数据量也更少，并且 I/O 更高效。

事务安全限制仅适用于完整记录的操作。

> [!NOTE]
> 最少记录的操作可以参与显式事务。 分配结构中的所有更改都被跟踪，因此实现回滚最少记录的操作变得可能。 
> 
> 

## <a name="minimally-logged-operations"></a>最少记录的操作
以下操作可以实现最少记录：

* CREATE TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> 内部数据移动操作（例如 BROADCAST 和 SHUFFLE）不受事务安全限制影响。
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>带批量加载的最少日志记录
CTAS 和 INSERT...SELECT 都是批量加载操作。 但两者都受目标表定义影响，并且取决于加载方案。 下表说明了什么时候批量操作是完整记录的或最少记录的：  

| 主索引 | 加载方案 | 日志记录模式 |
| --- | --- | --- |
| 堆 |任意 |**最少** |
| 聚集索引 |空目标表 |**最少** |
| 聚集索引 |加载的行不与目标中现有页面重叠 |**最少** |
| 聚集索引 |加载的行与目标中现有页面重叠 |完整 |
| 聚集列存储索引 |批大小 >= 102,400/每分区对齐的分布区 |**最少** |
| 聚集列存储索引 |批大小 < 102,400/每分区对齐的分布区 |完整 |

值得注意的是，任何更新辅助或非聚集索引的写入都将始终是完整记录的操作。

> [!IMPORTANT]
> SQL 数据仓库具有 60 个分布区。 因此，假设所有行均匀分布且处于单个分区中，批在写入到聚集列存储索引时会需有 6,144,000 行（或更多）要按最少记录的方式记入日志。 如果对表进行分区且正插入的行跨越分区边界，则每个分区边界都需 6,144,000 行，假定数据分布很均匀。 每个分布区的每个分区各自必须超过 102,400 行的阈值，从而使插入以最少记录的方式记录到分布区中。
> 
> 

将数据加载到含聚集索引的非空表通常可以包含完整记录和最少记录的行的组合。 聚集索引是页面的平衡树 (b-tree)。 如果正写入的页面已包含其他事务中的行，则这些写入操作会被完整记录。 但如果该页面为空，则写入到该页面会按最少记录的方式记录。

## <a name="optimizing-deletes"></a>优化删除
DELETE 是一个完整记录的操作。  如果需要删除表或分区中的大量数据，`SELECT` 要保留的数据通常更有意义，其可作为最少记录的操作来运行。  若要选择数据，可使用 [CTAS](sql-data-warehouse-develop-ctas.md) 创建新表。  创建后，可通过 [RENAME](/sql/t-sql/statements/rename-transact-sql) 操作使用新创建的表将旧表交换出来。

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>优化更新
UPDATE 是一个完整记录的操作。  如果需要更新表或分区中的大量行，通常更有效的方法是使用最少记录的操作（如 [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)）来实现。

在下面的示例中，完整的表更新已转换为 CTAS，以便使最少日志记录成为可能。

在此示例中，我们回顾性地向表中的销售额添加折扣金额：

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> 重新创建大型表可以受益于使用 SQL 数据仓库工作负荷管理功能。 有关详细信息，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。
> 
> 

## <a name="optimizing-with-partition-switching"></a>使用分区切换进行优化
面对[表分区](sql-data-warehouse-tables-partition.md)内较大规模修改时，分区切换模式非常有用。 如果数据修改非常重要且跨越多个分区，则遍历分区可获得相同的结果。

执行分区切换的步骤如下所示：

1. 创建清空的分区
2. 采用 CTAS 执行“update”
3. 将现有数据转出到输出表
4. 转入新数据
5. 清理数据

但是，若要帮助确定要切换的分区，请创建以下帮助器过程。  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

此过程可最大程度地重复使用代码，并使分区切换示例更紧凑。

下面的代码演示上述步骤，以实现完整的分区切换例程。

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>使用小批量尽量减少日志记录
对于大型数据修改操作，将操作划分为区块或批次来界定工作单元很有效。

以下代码是可工作的示例。 批大小已设置为一个简单的数字来突显该方法。 实际中批大小会变得非常大。 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>暂停和缩放指南
借助 Azure SQL 数据仓库，可以根据需要[暂停、恢复和缩放](sql-data-warehouse-manage-compute-overview.md)数据仓库。 暂停或缩放 SQL 数据仓库时，一定要了解会立即终止任何正在处理的事务；导致回退所有未决事务。 如果工作负荷在暂停或缩放操作前已发出数据修改在长时间运行之后仍未完成的指示，则需要撤消此项工作。 此撤消操作可能会影响暂停或缩放 Azure SQL 数据仓库数据库所花费的时间。 

> [!IMPORTANT]
> `UPDATE` 和 `DELETE` 都是完整记录的操作，因此这些撤消/重做操作相比同等最少记录的操作可能要花费更长的时间。 
> 
> 

最佳方案是在暂停或缩放 SQL 数据仓库前就完成正提交的数据修改事务。 但是，此方案不一定始终可行。 若要降低长时间回退的风险，请考虑以下选项之一：

* 使用 [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 重新编写长时间运行的操作
* 将该操作分解为多个块；针对行的子集进行操作

## <a name="next-steps"></a>后续步骤
参阅 [SQL 数据仓库中的事务](sql-data-warehouse-develop-transactions.md)，以便详细了解隔离级别和事务限制。  有关其他最佳实践的概述，请参阅 [SQL 数据仓库最佳实践](sql-data-warehouse-best-practices.md)。

