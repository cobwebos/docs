---
title: Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS) | Microsoft Docs
description: 有关使用 Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS) 语句开发解决方案的介绍和示例。
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 91de474cc0610099b4264cc6d0dfbd26e8df0618
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851451"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS)

本文介绍如何使用 Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS) T-SQL 语句来开发解决方案。 本文还会提供代码示例。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) 语句是所提供的最重要的 T-SQL 功能之一。 CTAS 是根据 SELECT 语句的输出创建新表的并行化操作。 CTAS 是在表中创建和插入数据的最简单快速方法，只需使用一条命令。

## <a name="selectinto-vs-ctas"></a>SELECT..INTO 与CTAS

CTAS 是 [SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql) 语句的自定义程度更高的版本。

下面是一个简单的 SELECT..INTO 示例：

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECT...INTO 不允许在操作过程中更改分布方法或索引类型。 使用默认分布类型 ROUND_ROBIN 以及默认表结构 CLUSTERED COLUMNSTORE INDEX 创建 `[dbo].[FactInternetSales_new]`。

另一方面，使用 CTAS 可以指定表数据的分布方式以及表结构类型。 将以上示例转换为 CTAS：

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> 如果只是想要尝试更改 CTAS 操作中的索引并且源表经过哈希分布，请保留相同的分布列和数据类型。 这会避免操作期间的交叉分布数据移动，从而更加高效。

## <a name="use-ctas-to-copy-a-table"></a>使用 CTAS 复制表

CTAS 最常见的用途之一就是创建表副本，以便可以更改 DDL。 假设你最初将表创建为 `ROUND_ROBIN`，现在想要将其更改在列中分布的表。 可以使用 CTAS 来更改分布列。 还可以使用 CTAS 来更改分区、索引或列类型。

假设你默认分布类型 `ROUND_ROBIN` 创建了此表，且未在 `CREATE TABLE` 中指定分布列。

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

现在想要创建此表的新副本并包含 `Clustered Columnstore Index`，以便可以使用群集列存储表的性能。 你还想在 `ProductKey` 上分布此表（因为预期此列会发生联接）并在联接 `ProductKey` 期间避免数据移动。 最后，你还希望在 `OrderDateKey` 上添加分区，以便通过删除旧分区来快速删除旧数据。 以下是可将旧表复制到新表的 CTAS 语句。

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

最后，可以重命名表以切换到新表，并删除旧表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>使用 CTAS 解决不支持的功能

还可以使用 CTAS 来解决下列多种不支持的功能。 经过证实，此方法往往可以带来帮助，因为代码不但能够兼容，而且通常可以在 SQL 数据仓库中更快速运行。 这种性能提升是完全并行化设计的结果。 方案包括：

* UPDATE 中的 ANSI JOIN
* DELETE 中的 ANSI JOIN
* MERGE 语句

> [!TIP]
> 试着考虑"CTAS 第一个。" 一般情况下，最好是使用 CTAS 来解决问题，即使因此要写入更多数据。

## <a name="ansi-join-replacement-for-update-statements"></a>替换 Update 语句的 ANSI Join

你可能有一个复杂的更新。 该更新使用 ANSI 联接语法来执行 UPDATE 或 DELETE，以将两个以上的表联接在一起。

假设必须更新此表：

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

原始查询看起来可能类似于以下示例：

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

SQL 数据仓库不支持在 `UPDATE` 语句的 `FROM` 子句中使用 ANSI Join，因此，只有在修改上述示例之后才能使用它。

可以使用 CTAS 和隐式联接的组合来替换上述示例：

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>替换 Delete 语句的 ANSI Join

有时，删除数据的最佳方法是使用 CTAS，特别是对于使用 ANSI Join 语法的 `DELETE` 语句。 这是因为，SQL 数据仓库不支持在 `DELETE` 语句的 `FROM` 子句中使用 ANSI Join。 无需删除数据，可以选择要保留的数据。

下面是转换后的 `DELETE` 语句示例：

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>替换 Merge 语句

使用 CTAS 至少可以部分替换 merge 语句。 可以将 `INSERT` 和 `UPDATE` 合并成单个语句。 任何已删除的记录将在 `SELECT` 语句中受到限制，以便从结果中省略。

以下示例适用于 `UPSERT`：

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>显式声明数据类型和输出是否可为 null

迁移代码时，可能会遇到这种类型的编码模式：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

你可能认为应该将此代码迁移到 CTAS，这是对的。 但是，这里有一个隐含的问题。

以下代码不会生成相同的结果：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

请注意，列“result”沿用表达式的数据类型和可为 null 的值。 传递数据类型可能会导致值存在细微的差异。

尝试运行以下示例：

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

为结果存储的值不相同。 因为结果列中保留的值用于其他表达式，错误变得更加严重。

![CTAS 结果的屏幕截图](media/sql-data-warehouse-develop-ctas/ctas-results.png)

这对于数据迁移非常重要。 尽管第二个查询看起来更准确，但仍有一个问题。 与源系统相比，此数据有所不同，会在迁移中造成完整性问题。 这是“错误”答案其实是正确答案的极少见情况之一！

这两个结果之间存在差异的原因隐式类型转换。 在第一个示例中，表定义了列定义。 插入行后，会发生隐式类型转换。 在第二个示例中，没有隐式类型转换，因为表达式定义了列的数据类型。

请注意，第二个示例中的列已定义为可为 Null 的列，而在第一个示例中还没有定义。 在第一个示例中创建表时，尚未显式定义列可为 null。 在第二个示例中，它已留给了表达式，默认情况下，这会导致 NULL 定义。

若要解决这些问题，必须在 CTAS 语句的 SELECT 部分中明确设置类型转换和可为 null 属性。 无法在“CREATE TABLE”中设置这些属性。
以下示例演示如何修复代码：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

注意以下事项：

* 可以使用 CAST 或 CONVERT。
* 使用 ISNULL 而不是 COALESCE 来强制可为 NULL 性。 请参阅以下注释。
* ISNULL 是最外层的函数。
* ISNULL 的第二个部分是常量，即 0。

> [!NOTE]
> 若要正确设置可为 null 属性，必须使用 ISNULL 而不是 COALESCE。 COALESCE 不是确定性的函数，因此表达式的结果始终可为 Null。 ISNULL 则不同。 它是确定性的。 因此当 ISNULL 函数的第二个部分是常量或文本时，结果值将是 NOT NULL。

确保计算的完整性对于表分区切换而言也很重要。 假设已将此表定义为事实表：

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

但是，数量字段是计算的表达式。 它不是源数据的一部分。

若要创建分区数据集，可能需要使用以下代码：

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

该查询会顺利运行。 但是，尝试执行分区切换时，会出现问题。 表定义不匹配。 若要使表定义匹配，请修改 CTAS，以添加一个 `ISNULL` 函数用于保留列的可为 null 性属性。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

可以看出，保持类型一致性并维护 CTAS 上的可为 null 属性是工程最佳做法。 这有助于维护计算的完整性，而且还可确保分区切换能够实现。

CTAS 是 SQL 数据仓库中最重要的语句之一。 请确保全面了解该语句。 请参阅 [CTAS 文档](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

