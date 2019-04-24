---
title: Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS) | Microsoft Docs
description: 说明和 Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS) 语句的开发解决方案的示例。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: c8e9f3ccdfaee64f75443f6a4eb89a3df7c48b0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403694"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS)

本文介绍有关在开发解决方案的 Azure SQL 数据仓库中的 CREATE TABLE AS SELECT (CTAS) T-SQL 语句。 本文还提供代码示例。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) 语句是可用的最重要的 T-SQL 功能之一。 CTAS 是创建一个新表，基于 SELECT 语句的输出的并行操作。 CTAS 是创建并将数据插入表中使用单个命令的最简单且最快方式。

## <a name="selectinto-vs-ctas"></a>选择...到 vs。CTAS

CTAS 是更容易自定义版本的[选择...到](/sql/t-sql/queries/select-into-clause-transact-sql)语句。

下面是 SELECT 的一个简单的示例...到：

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

选择...为不允许您在操作过程中更改分布方法或索引类型。 创建`[dbo].[FactInternetSales_new]`使用 ROUND_ROBIN 的默认分布类型和聚集列存储索引的默认表结构。

使用 CTAS，另一方面，您可以指定这两个分布的表数据，以及表结构类型。 若要将转换为 CTAS 前面的示例：

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
> 如果你只想要更改在 CTAS 操作中，索引并且源表经过哈希分布，维护相同的分布列和数据类型。 这将在操作时，这更加有效避免跨分发数据移动。

## <a name="use-ctas-to-copy-a-table"></a>使用 CTAS 复制表

可能 CTAS 的最常见用途之一创建表的副本，以更改 DDL。 让我们假设你最初将表创建为`ROUND_ROBIN`，并且现在想要将其更改为某一列上分布的表。 CTAS 是如何将更改分布列。 CTAS 还可用于更改分区、 索引或列类型。

让我们假设您创建此表使用的默认分布类型`ROUND_ROBIN`，没有指定分布列中的`CREATE TABLE`。

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

现在你想要使用创建此表的新副本`Clustered Columnstore Index`，因此您可以利用聚集列存储表的性能。 你还想要在分布此表`ProductKey`，因为正在预测联接此列，并且想要避免在联接期间的数据移动上`ProductKey`。 最后，你还想要添加分区上`OrderDateKey`，因此您可以通过删除旧分区中快速删除旧数据。 下面是将旧表复制到新表的 CTAS 声明。

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

最后，您可以重命名表的位置，以交换新表中，然后删除旧表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>使用 CTAS 解决不受支持的功能

CTAS 还可用于解决不支持下面列出的功能数。 此方法通常可以证明很有用，因为不仅将你的代码是合规的而且它通常会更快地在 SQL 数据仓库。 此性能是其完全并行化设计的结果。 方案包括：

* UPDATE 中的 ANSI JOIN
* DELETE 中的 ANSI JOIN
* MERGE 语句

> [!TIP]
> 试着考虑"CTAS 第一个。" 使用 CTAS 解决问题通常是一个不错的方法，即使您因此编写更多的数据。

## <a name="ansi-join-replacement-for-update-statements"></a>替换 Update 语句的 ANSI Join

您可能会发现具有复杂更新。 更新通过使用 ANSI 联接语法来执行 UPDATE 或 DELETE 一起联接两个以上的表。

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

SQL 数据仓库不支持中的 ANSI 联接`FROM`子句`UPDATE`语句，因此你无法使用前面的示例，而无需修改它。

可以使用 CTAS 和隐式联接的组合来替换前面的示例：

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

删除数据的最佳方法是使用 CTAS，特别是对于有时`DELETE`语句使用 ANSI 联接语法。 这是因为 SQL 数据仓库不支持中的 ANSI 联接`FROM`子句`DELETE`语句。 而不是删除数据，选择你想要保留的数据。

以下是转换后的示例`DELETE`语句：

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

您可以替换 merge 语句，至少在部分中，通过使用 CTAS。 你可以组合`INSERT`和`UPDATE`成一条语句。 任何已删除的记录应该限制`SELECT`要从结果中忽略的语句。

以下示例适用于`UPSERT`:

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

当迁移代码时，可能会遇到这种类型的编码模式：

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

您可能认为应将此代码迁移到 CTAS，那您就对正确。 但是，没有一个隐含的问题。

下面的代码不会产生相同的结果：

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

请注意，列“result”沿用表达式的数据类型和可为 null 的值。 执行数据转发类型可能会导致值存在细微差异如果您不小心。

尝试运行此示例：

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

为结果存储的值不相同。 在其他表达式中使用结果列中保留的值时，该错误会更大。

![屏幕截图的 CTAS 结果](media/sql-data-warehouse-develop-ctas/ctas-results.png)

这是重要的数据迁移。 即使第二个查询可能更为准确，没有问题。 则数据将是不同的与源系统相比，这会引出迁移完整性问题。 这是“错误”答案其实是正确答案的极少见情况之一！

我们可以看到两个结果之间存在差异的原因是由于隐式类型转换。 在第一个示例中，此表定义的列定义。 插入行时，会发生隐式类型转换。 在第二个示例中，是不进行隐式类型转换过程，因为表达式定义列的数据类型。

另请注意第二个示例中的列已被定义为可以为 Null 的列，而第一个示例中没有。 第一个示例中，创建表时显式定义列的为 null 性。 在第二个示例中，它是表达式，并且默认情况下会导致 NULL 定义。

若要解决这些问题，必须在 CTAS 语句的 SELECT 部分中显式设置类型转换和为 null 性。 在创建表中，不能设置这些属性。
下面的示例演示如何修复此代码：

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

注意以下事项：

* 可以使用强制转换或转换。
* 使用 ISNULL、 不是 COALESCE 强制为 null 性。 请参阅以下说明。
* ISNULL 是最外面的函数。
* ISNULL 的第二部分是常量，0。

> [!NOTE]
> 若要正确设置为 null 性，务必使用 ISNULL 而不是 COALESCE。 COALESCE 不是确定性函数，并因此表达式的结果将始终为可以为 Null。 ISNULL 则不同。 它是确定的。 因此，当 ISNULL 函数的第二部分是常量或文本，生成的值将为 NOT NULL。

确保计算的完整性也很重要的表分区切换。 假设你有此表定义为事实数据表：

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

但是，金额字段是一个计算的表达式。 它不是源数据的一部分。

若要创建已分区数据集，您可能想要使用以下代码：

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

该查询将完美运行。 当您尝试执行分区切换时出现问题。 表定义不匹配。 若要使表定义匹配，修改要添加在 CTAS`ISNULL`函数以保留列的为 null 性属性。

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

您可以看到类型一致性和维持 CTAS 上的为 null 性属性是工程的最佳做法。 它有助于保持计算的完整性，并还确保，分区切换有可能。

CTAS 是 SQL 数据仓库中最重要的语句之一。 请确保全面了解该语句。 请参阅[CTAS 文档](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

