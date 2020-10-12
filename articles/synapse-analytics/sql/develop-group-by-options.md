---
title: 在 Synapse SQL 中使用 GROUP BY 选项
description: Synapse SQL 允许通过实施不同的 GROUP BY 选项来开发解决方案。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 0d9d28bacac02326ee781ca56309b7a72e921960
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289163"
---
# <a name="group-by-options-in-synapse-sql"></a>Synapse SQL 中的 GROUP BY 选项
Synapse SQL 允许通过实施不同的 GROUP BY 选项来开发解决方案。 

## <a name="what-does-group-by-do"></a>GROUP BY do

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) T-SQL 子句用于将数据聚合成摘要行集。

SQL 点播支持整组分组选项。 SQL 池支持的 GROUP BY 选项数量有限。

## <a name="group-by-options-supported-in-sql-pool"></a>SQL 池中支持的 GROUP BY 选项

GROUP BY 具有 SQL 池不支持的一些选项。 这些选项有解决方法，如下所示：

* 带 ROLLUP 的 GROUP BY
* GROUPING SETS
* 带 CUBE 的 GROUP BY

### <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 选项

此处最简单的选项是使用 UNION ALL 来执行汇总，而不是依赖显式语法。 结果应完全相同

下面的示例使用带有 ROLLUP 选项的 GROUP BY 语句：

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

### <a name="cube-options"></a>Cube 选项

可以使用 UNION ALL 方法创建 GROUP BY WITH CUBE。 问题在于，代码可能很快就会变得庞大且失控。 若要缓解此问题，可以使用这种更高级的方法。

第一步是定义“cube”，它定义我们想要创建的所有聚合级别。 请记下两个派生表的交叉联接，因为它会生成所有级别。 其余代码适用于格式设置。

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

下图显示了 [CREATE TABLE 为 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)的结果：

![按多维数据集分组](./media/develop-group-by-options/develop-group-by-cube.png)

第二步是指定用于存储临时结果的目标表：

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

第三步是遍历执行聚合的列的多维数据集。 此查询将针对 #Cube 临时表中的每一行运行一次。 结果存储在 #Results 临时表中：

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

最后，可以通过从 #Results 临时表进行读取来返回结果：

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

将代码拆分成不同的部分并生成循环构造以后，代码就会更易于管理和维护。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅 [开发概述](develop-overview.md)。
