---
title: CREATE TABLE AS SELECT (CTAS)
description: 用于开发解决方案的 SQL 分析中的 CREATE TABLE AS SELECT （CTAS）语句的说明和示例。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 09a543ac4b4f77f0c7b7efd2411b962fa9fa2769
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195900"
---
# <a name="create-table-as-select-ctas-in-sql-analytics"></a>SQL Analytics 中的 CREATE TABLE AS SELECT （CTAS）

本文说明用于开发解决方案的 SQL 分析中的 SELECT （CTAS） T-sql 语句 CREATE TABLE。 此外，本文还提供了代码示例。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) （CTAS）语句是最重要的 t-sql 功能之一。 CTAS 是一种并行操作，它基于 SELECT 语句的输出创建新表。 CTAS 是使用单个命令创建数据并将其插入到表中的最简单、最快捷的方法。

## <a name="selectinto-vs-ctas"></a>选择 .。。INTO 和 CTAS

CTAS 是更可自定义的[SELECT .。。INTO](/sql/t-sql/queries/select-into-clause-transact-sql)语句。

下面是一个简单的 SELECT .。。为

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

选择 .。。INTO 不允许在操作过程中更改分布方法或索引类型。 您可以使用 ROUND_ROBIN 的默认分发类型和聚集列存储索引的默认表结构创建 `[dbo].[FactInternetSales_new]`。

另一方面，通过 CTAS，您可以同时指定表数据的分布以及表结构类型。 将上一个示例转换为 CTAS：

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> 如果只是尝试更改 CTAS 操作中的索引，而源表是哈希分布，请保持相同的分布列和数据类型。 这可以避免在操作过程中发生跨分布数据移动，从而提高效率。

## <a name="use-ctas-to-copy-a-table"></a>使用 CTAS 复制表

CTAS 的最常见用途之一就是创建表的副本，以便更改 DDL。 假设您最初将表创建为 `ROUND_ROBIN`，现在希望将其更改为在列上分布的表。 CTAS 是更改分布列的方式。 还可以使用 CTAS 更改分区、索引或列类型。

假设您使用 `ROUND_ROBIN`的默认分布类型创建此表，而不是在 `CREATE TABLE`中指定分布列。

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
    CustomerPONumber nvarchar(25));
```

现在你想要创建此表的新副本，其中包含一个 `Clustered Columnstore Index`，因此你可以利用聚集列存储表的性能。 还需要将此表分布 `ProductKey`上，因为你要预测此列上的联接，并在 `ProductKey`的联接过程中避免数据移动。 最后，还需要在 `OrderDateKey`上添加分区，以便通过删除旧分区来快速删除旧数据。 下面是 CTAS 语句，它将旧表复制到新表中。

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

最后，可以重命名表，在新表中交换，然后删除旧表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>使用 CTAS 解决不支持的功能

还可以使用 CTAS 来解决下面列出的一些不支持的功能。 此方法通常可以证明有用，因为您不仅能符合您的代码，而且在 SQL 分析上运行速度会更快。 此性能是完全并行化设计的结果。 方案包括：

* UPDATE 中的 ANSI JOIN
* DELETE 中的 ANSI JOIN
* MERGE 语句

> [!TIP]
> 请尝试考虑 "CTAS first"。 使用 CTAS 解决问题通常是一种很好的方法，即使您要编写更多的数据也是如此。

## <a name="ansi-join-replacement-for-update-statements"></a>更新语句的 ANSI 联接替换

你可能会发现有一个复杂的更新。 此更新通过使用 ANSI 联接语法来执行 UPDATE 或 DELETE，将两个以上的表联接在一起。

假设必须更新此表：

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

原始查询看起来可能类似于以下示例：

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

SQL Analytics 在 `UPDATE` 语句的 `FROM` 子句中不支持 ANSI 联接，因此不能使用前面的示例而不进行修改。

可以使用 CTAS 和隐式联接的组合来替换前面的示例：

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>替换 Delete 语句的 ANSI Join

有时，删除数据的最佳方法是使用 CTAS，尤其是对于使用 ANSI 联接语法的 `DELETE` 语句。 这是因为 SQL Analytics 在 `DELETE` 语句的 `FROM` 子句中不支持 ANSI 联接。 选择要保留的数据，而不是删除数据。

下面是转换后的 `DELETE` 语句的示例：

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>替换 Merge 语句

您可以使用 CTAS 替换 merge 语句（至少在部分中）。 可以将 `INSERT` 和 `UPDATE` 组合成单个语句。 应将任何已删除的记录从 `SELECT` 语句中限制为从结果中忽略。

以下示例适用于 `UPSERT`：

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>显式声明数据类型和输出是否可为 null

迁移代码时，可能会发现，你可以通过这种类型的编码模式运行：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

您可能认为应该将此代码迁移到 CTAS，这是正确的。 但是，这里有一个隐藏问题。

以下代码不会产生相同的结果：

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

注意，“结果”列会沿用表达式的数据类型和为 Null 性值。 如果您不小心，则继续执行数据类型会导致值的细微差异。

尝试以下示例：

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

为结果存储的值不相同。 由于结果列中的持久化值用于其他表达式，错误变得更加重要。

![CTAS 结果的屏幕截图](media/sql-data-warehouse-develop-ctas/ctas-results.png)

这对于数据迁移非常重要。 尽管第二个查询可能更准确，但仍存在问题。 与源系统相比，数据会有所不同，这会在迁移中造成完整性问题。 这是一种“错误”答案实际上为正确答案的罕见情况！

由于隐式类型强制转换，我们看到两个结果之间的差异。 在第一个示例中，表定义了列定义。 插入行时，将发生隐式类型转换。 在第二个示例中，没有隐式类型转换，因为表达式定义了列的数据类型。

另请注意，第二个示例中的列已定义为可为 Null 的列，而在第一个示例中，该列未定义。 在第一个示例中创建表时，显式定义了列的为 null 性。 在第二个示例中，它留给了表达式，默认情况下，会导致 NULL 定义。

若要解决这些问题，必须在 CTAS 语句的 SELECT 部分中明确设置类型转换和可为 null 性。 不能在 "CREATE TABLE" 中设置这些属性。
下面的示例演示如何修复代码：

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

注意以下事项：

* 您可以使用 CAST 或 CONVERT。
* 使用 ISNULL 而不是合并来强制可为 Null。 请参阅以下说明。
* ISNULL 是最外层的函数。
* ISNULL 的第二部分是一个常量，0。

> [!NOTE]
> 若要正确设置可为 null 性，请务必使用 ISNULL 而不是合并。 合并不是确定性的函数，因此表达式的结果始终可为 Null。 ISNULL 则不同。 它是确定性的。 因此，当 ISNULL 函数的第二个部分是常量或文本时，结果值将为 NOT NULL。

确保计算的完整性对于表分区切换也很重要。 假设您已将此表定义为事实数据表：

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

但是，金额字段是计算的表达式。 它不是源数据的一部分。

若要创建分区数据集，可能需要使用以下代码：

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

查询将正常运行。 当你尝试执行分区切换时，会出现此问题。 表定义不匹配。 若要使表定义匹配，请修改 CTAS 以添加 `ISNULL` 函数以保留列的可为 null 属性。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

CTAS 是一种工程最佳实践，可以看到类型一致性并维护上的可为 null 属性。 它有助于维护计算的完整性，并确保可以进行分区切换。

CTAS 是 SQL Analytics 中最重要的语句之一。 请确保全面了解该语句。 请参阅[CTAS 文档](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

