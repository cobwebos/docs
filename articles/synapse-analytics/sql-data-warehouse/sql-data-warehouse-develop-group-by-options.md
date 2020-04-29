---
title: 使用 group by 选项
description: 用于在 Synapse SQL 池中实现 group by 选项的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d8d4c6d47e33ca365415542c2da9779b4d7d1dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416200"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Synapse SQL 池中的 Group by 选项

在本文中，你将了解如何在 SQL 池中实现 group by 选项。

## <a name="what-does-group-by-do"></a>GROUP BY 的作用是什么？

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 子句用于将数据聚合成摘要行集。 "分组依据" 具有 SQL 池不支持的某些选项。 这些选项有解决方法，如下所示：

* 带 ROLLUP 的 GROUP BY
* GROUPING SETS
* 带 CUBE 的 GROUP BY

## <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 选项

此处最简单的选项是使用 UNION ALL 来执行汇总，而不是依赖显式语法。 结果完全相同。

下面的示例使用了具有 ROLLUP 选项的 GROUP BY 语句：

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

通过使用 ROLLUP，前面的示例请求以下聚合：

* 国家/地区和区域
* 国家/地区
* 总计

若要替换 ROLLUP 并返回相同的结果，可以使用 UNION ALL 并显式指定所需的聚合：

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

若要替换 GROUPING SETS，示例原则也适用。 只需要为希望查看的聚合级别创建 UNION ALL 部分。

## <a name="cube-options"></a>Cube 选项

可以使用 UNION ALL 方法创建 GROUP BY WITH CUBE。 问题在于，代码可能很快就会变得庞大且失控。 若要缓解此问题，可以使用这种更高级的方法。

使用上面的示例，第一步是定义 "cube"，它定义了要创建的所有聚合级别。

记下两个派生表的交叉联接，因为这样会生成所有级别。 其余代码用于设置格式：

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

下图显示了 CTAS 的结果：

![按多维数据集分组](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

第二步是指定目标表用于存储临时结果：

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

第三步是是循环访问执行聚合的列 cube。 查询将对 #Cube 临时表中的每一行运行一次。 结果存储在 #Results 临时表中：

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

最后，您可以通过读取 #Results 临时表来返回结果：

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

通过将代码分解到各个部分并生成循环构造，代码将变得更易于管理和维护。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。
