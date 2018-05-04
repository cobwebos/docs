---
title: 使用 Azure SQ 数据仓库中的 Group By 选项 | Microsoft Docs
description: 有关在开发解决方案时实现 Azure SQL 数据仓库中的 Group By 选项的技巧。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="group-by-options-in-sql-data-warehouse"></a>SQL 数据仓库中的 Group By 选项
有关在开发解决方案时实现 Azure SQL 数据仓库中的 Group By 选项的技巧。

## <a name="what-does-group-by-do"></a>GROUP BY 的作用是什么？

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL 子句用于将数据聚合成摘要行集。 GROUP BY 具有 SQL 数据仓库不支持的一些选项。 这些选项都有解决方法。

这些选项包括：

* 带 ROLLUP 的 GROUP BY
* GROUPING SETS
* 带 CUBE 的 GROUP BY

## <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 选项
此处最简单的选项是改为使用 UNION ALL 来执行汇总，而不是依赖显式语法。 结果应完全相同

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
可以使用 UNION ALL 方法创建 GROUP BY WITH CUBE。 问题在于，代码可能很快就会变得庞大且失控。 若要避免此情况，可以使用这种更高级的方法。

使用上述示例。

第一步是定义“cube”，它定义我们想要创建的所有聚合级别。 请务必记下两个派生表的 CROSS JOIN。 这样就会生成所有级别。 剩余代码确实可以设置格式。

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

下面显示了 CTAS 的结果：

![按多维数据集分组](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

第三步是是循环访问执行聚合的列 cube。 查询将为 #Cube 临时表中的每个行运行一次，并将结果存储在 #Results 临时表中

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

最后，可以通过简单地从 #Results 临时表进行读取来返回结果

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

通过将代码拆分成不同的部分并生成循环构造，代码将更好管理和维护。

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

