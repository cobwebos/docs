---
title: "对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数 | Microsoft 文档"
description: "了解如何使用 U-SQL 开窗函数。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: a5e14b32-d5eb-4f4b-9258-e257359f9988
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data`
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 55d19a00198f1943a8196d31399c617397b4e5d2
ms.contentlocale: zh-cn
ms.lasthandoff: 05/05/2017


---
# <a name="using-u-sql-window-functions-for-azure-data-lake-analytics-jobs"></a>对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数
ISO/ANSI SQL 标准于 2003 年引入开窗函数。 U-SQL 采用 ANSI SQL 标准定义的开窗函数的子集。

窗口函数用于在名为窗口的行集中执行运算。 窗口由 OVER 子句定义。 开窗函数解决一些关键方案，效率极高。

开窗函数分类： 

* [报表聚合函数](#reporting-aggregation-functions)，例如 SUM 或 AVG
* [排名函数](#ranking-functions)，例如 DENSE_RANK、ROW_NUMBER、NTILE 和 RANK
* [分析函数](#analytic-functions)（例如累积分布、百分位），不使用自联接访问上一行中的数据（在同一结果集中）

## <a name="sample-datasets"></a>示例数据集
本教程使用两个数据集：

### <a name="the-querylog-sample-dataset"></a>QueryLog 示例数据集
  
QueryLog 表示人员在搜索引擎中搜索的内容的列表。 每个查询日志包括：
  
* 查询 - 用户搜索的内容
* 延迟 - 查询返回到用户的速度（以毫秒为单位）
* 纵向 - 用户感兴趣的内容类型（Web 链接、图像、视频）  
 
```
@querylog = 
    SELECT * FROM ( VALUES
        ("Banana"  , 300, "Image" ),
        ("Cherry"  , 300, "Image" ),
        ("Durian"  , 500, "Image" ),
        ("Apple"   , 100, "Web"   ),
        ("Fig"     , 200, "Web"   ),
        ("Papaya"  , 200, "Web"   ),
        ("Avocado" , 300, "Web"   ),
        ("Cherry"  , 400, "Web"   ),
        ("Durian"  , 500, "Web"   ) )
    AS T(Query,Latency,Vertical);
```

## <a name="the-employees-sample-dataset"></a>员工示例数据集
  
员工数据集包含以下字段：
  
* EmpID - 员工 ID
* EmpName - 员工名称
* DeptName - 部门名称 
* DeptID - 部门 ID
* Salary - 员工薪金

```
@employees = 
    SELECT * FROM ( VALUES
        (1, "Noah",   "Engineering", 100, 10000),
        (2, "Sophia", "Engineering", 100, 20000),
        (3, "Liam",   "Engineering", 100, 30000),
        (4, "Emma",   "HR",          200, 10000),
        (5, "Jacob",  "HR",          200, 10000),
        (6, "Olivia", "HR",          200, 10000),
        (7, "Mason",  "Executive",   300, 50000),
        (8, "Ava",    "Marketing",   400, 15000),
        (9, "Ethan",  "Marketing",   400, 10000) )
    AS T(EmpID, EmpName, DeptName, DeptID, Salary);
```  

## <a name="compare-window-functions-to-grouping"></a>比较开窗函数与 Grouping
开窗和分组概念相关。 理解此关系非常有用。

### <a name="use-aggregation-and-grouping"></a>使用聚合和分组
下面的查询使用聚合计算所有员工的总薪资：

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

其结果是一个具有单列的单行。 $165000 是整个表中的薪资值的总和。 

| TotalSalary |
| --- |
| 165000 |


下面的语句使用 GROUP BY 子句来计算每个部门的总薪资：

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

其结果是：

| DeptName | SalaryByDept |
| --- | --- |
| 工程 |60000 |
| HR |30000 |
| Executive |50000 |
| Marketing |25000 |

SalaryByDept 列的总计是 $165000，与上一脚本中的数量一致。

这两种情况下，输出行数都比输入行数少：

* 不使用 GROUP BY，则聚合会将所有行折叠到一个单行中。 
* 使用 GROUP BY，会出现 N 个输出行，其中，N 是出现在此数据中的非重复值的数目。  在本例中，输出了四行。

### <a name="use-a-window-function"></a>使用开窗函数
以下示例中的 OVER 子句为空，因此该窗口包括所有行。 此示例中的 SUM 应用到其前的 OVER 子句中。

可将此查询读为“所有行窗口内的薪资总计”。

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

不同于 GROUP BY，此时输出行数与输入行数相同： 

| EmpName | TotalAllDepts |
| --- | --- |
| Noah |165000 |
| Sophia |165000 |
| Liam |165000 |
| Emma |165000 |
| Jacob |165000 |
| Olivia |165000 |
| Mason |165000 |
| Ava |165000 |
| Ethan |165000 |

值 165000（所有薪资总计）会置于每个输出行中。 此总计源自所有行的“开窗”，因此其包含所有的薪资。 

下一示例演示如何优化“开窗”以列出所有员工、部门和部门总薪资。 添加 PARTITION BY 到 OVER 字句中。

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

其结果是：

| EmpName | DeptName | SalaryByDep |
| --- | --- | --- |
| Noah |工程 |60000 |
| Sophia |工程 |60000 |
| Liam |工程 |60000 |
| Mason |Executive |50000 |
| Emma |HR |30000 |
| Jacob |HR |30000 |
| Olivia |HR |30000 |
| Ava |Marketing |25000 |
| Ethan |Marketing |25000 |

同样，此处输出行数与输入行数相同。 但每行具有相应部门的总薪资。

## <a name="reporting-aggregation-functions"></a>报表聚合函数
开窗函数还支持以下聚合函数：

* COUNT
* SUM
* 最小值
* MAX
* 平均值
* STDEV
* VAR

语法：

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

注意： 

* 默认情况下，除 COUNT 外的聚合函数会忽略空值。
* 聚合函数与 OVER 字句一起指定时，OVER 字句中不允许 ORDER BY 字句。

### <a name="use-sum"></a>使用 SUM
下面的示例按部门将总薪资添加到每个输入行中：

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

输出如下：

| EmpID | EmpName | DeptName | DeptID | Salary | TotalByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |工程 |100 |10000 |60000 |
| 2 |Sophia |工程 |100 |20000 |60000 |
| 3 |Liam |工程 |100 |30000 |60000 |
| 7 |Mason |Executive |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |30000 |
| 5 |Jacob |HR |200 |10000 |30000 |
| 6 |Olivia |HR |200 |10000 |30000 |
| 8 |Ava |Marketing |400 |15000 |25000 |
| 9 |Ethan |Marketing |400 |10000 |25000 |

### <a name="use-count"></a>使用 COUNT
下面的示例将额外字段添加到每个行中，以便显示每个部门中的员工总数。

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

结果：

| EmpID | EmpName | DeptName | DeptID | Salary | CountByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |工程 |100 |10000 |3 |
| 2 |Sophia |工程 |100 |20000 |3 |
| 3 |Liam |工程 |100 |30000 |3 |
| 7 |Mason |Executive |300 |50000 |1 |
| 4 |Emma |HR |200 |10000 |3 |
| 5 |Jacob |HR |200 |10000 |3 |
| 6 |Olivia |HR |200 |10000 |3 |
| 8 |Ava |Marketing |400 |15000 |2 |
| 9 |Ethan |Marketing |400 |10000 |2 |

### <a name="use-min-and-max"></a>使用 MIN 和 MAX
下面的示例将额外字段添加到每个行中，以便显示每个部门的最低薪资：

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

结果：

| EmpID | EmpName | DeptName | DeptID | Salary | MinSalary |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |工程 |100 |10000 |10000 |
| 2 |Sophia |工程 |100 |20000 |10000 |
| 3 |Liam |工程 |100 |30000 |10000 |
| 7 |Mason |Executive |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |10000 |
| 5 |Jacob |HR |200 |10000 |10000 |
| 6 |Olivia |HR |200 |10000 |10000 |
| 8 |Ava |Marketing |400 |15000 |10000 |
| 9 |Ethan |Marketing |400 |10000 |10000 |

## <a name="ranking-functions"></a>排名函数
排名函数返回 PARTITION BY 和 OVER 字句定义的每个分区中每行的排名值（LONG 类型）。 OVER 字句中的 ORDER BY 控制排名的顺序。

以下支持排名函数：

* RANK
* DENSE_RANK 
* NTILE
* ROW_NUMBER

**语法：**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

* 排名函数中 ORDER BY 为可选字句。 如果未指定 ORDER BY，则 U-SQL 将基于它读取记录的顺序分配值，从而为 ROW_NUMBER、RANK 或 DENSE_RANK 生成非确定性的值。
* NTILE 需要一个求值结果为正整数值的表达式。 该数值指定每个分区必须分成的组的数目。 此标识符仅用于 NTILE 排名函数。 

有关 OVER 子句的详细信息，请参阅 [U-SQL 参考](http://go.microsoft.com/fwlink/p/?LinkId=691348)。

ROW_NUMBER、RANK 和 DENSE_RANK 都会分配数字到开窗中的行。 与单独覆盖不同，查看他们如何响应同一输入会更加直观。

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

请注意 OVER 子句完全相同。 结果：

| 查询 | 延迟：INT | 垂直 | RowNumber | Rank | DenseRank |
| --- | --- | --- | --- | --- | --- |
| 香蕉 |300 |映像 |1 |1 |1 |
| 樱桃 |300 |映像 |2 |1 |1 |
| 榴莲 |500 |映像 |3 |3 |2 |
| 苹果 |100 |Web |1 |1 |1 |
| 无花果 |200 |Web |2 |2 |2 |
| 番木瓜 |200 |Web |3 |2 |2 |
| 无花果 |300 |Web |4 |4 |3 |
| 樱桃 |400 |Web |5 |5 |4 |
| 榴莲 |500 |Web |6 |6 |5 |

### <a name="rownumber"></a>ROW_NUMBER
每个窗口（垂直，映像或 Web）中，行号按延迟排序，依次递增 1.  

![U-SQL 开窗函数 ROW_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### <a name="rank"></a>RANK
与 ROW_NUMBER() 不同，RANK() 会使用 ORDER BY 字句中为窗口指定的延迟值。

由于延迟的前两个值相同，因此 RANK 会以 (1, 1, 3) 开始。 由于延迟值已移动至 500，因此下一个值是 3。 关键点是即使给予重复数值相同的排名，RANK 号也将跳至下一 ROW_NUMBER 值。 Web 垂直中会对序列 (2, 2, 4) 重复此模式。

![U-SQL 开窗函数 RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### <a name="denserank"></a>DENSE_RANK
DENSE_RANK 就像 RANK 一样，不同的是，它不会跳到下一 ROW_NUMBER。 DENSE_RANK 会转到序列中的下一编号。 请注意本示例中的序列 (1, 1, 2) 和 (2, 2, 3)。

![U-SQL 开窗函数 DENSE_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### <a name="remarks"></a>备注
* 如果未指定 ORDER BY，则排名函数将无序地应用于行集，从而导致非确定性的行为。
* 必须满足以下条件，才能保证每次执行时使用 ROW_NUMBER 的查询返回的行排序完全相同。
  
  * 已分区的列的值唯一。
  * ORDER BY 列的值唯一。
  * 分区列和 ORDER BY 列值的组合唯一。

### <a name="ntile"></a>NTILE
NTILE 将已排序分区中的行分发到指定数目的组。 会对组从 1 开始进行编号。 

下面的示例按延迟的顺序将每个分区（垂直）中的行集拆分为 4 个组，并返回每个行的组编号。 

映像垂直具有三个行，因此它具有三个组。 

Web 垂直具有六个行。  两个额外的行将分配给前两个组。 这就是组 1 和组 2 中存在两行，而组 3 和组 4 中只存在一行的原因。  

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

结果：

| 查询 | 延迟 | 垂直 | Quartile |
| --- | --- | --- | --- |
| 香蕉 |300 |映像 |1 |
| 樱桃 |300 |映像 |2 |
| 榴莲 |500 |映像 |3 |
| 苹果 |100 |Web |1 |
| 无花果 |200 |Web |1 |
| 番木瓜 |200 |Web |2 |
| 无花果 |300 |Web |2 |
| 樱桃 |400 |Web |3 |
| 榴莲 |500 |Web |4 |

NTILE 采用一个参数（“numgroups”）。 Numgroups 是一个正整数或较长的常数表达式，其指定每个分区必须分成的组的数量。 

* 如果分区中的行数可被 numgroups 整除，则组具有相同大小。 
* 如果分区中的行数不可被 numgroups 整除，则组的大小略有不同。 较大组按 OVER 字句指定的顺序位于较小组之前。 

例如：

    100 rows divided into 4 groups: 
    [ 25, 25, 25, 25 ]

    102 rows divided into 4 groups: 
    [ 26, 26, 25, 25 ]

### <a name="top-n-records-per-partition-via-rank-denserank-or-rownumber"></a>每个分区中通过 RANK、DENSE_RANK 或 ROW_NUMBER 的前 N 个记录
许多用户想要仅选择每个组的前 n 行，而这使用传统的 GROUP BY 无法实现。 

排名函数部分的开头已列出过下面的示例。 此示例不会显示每个分区的前 N 个记录：

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

结果：

| 查询 | 延迟 | 垂直 | Rank | DenseRank | RowNumber |
| --- | --- | --- | --- | --- | --- |
| 香蕉 |300 |映像 |1 |1 |1 |
| 樱桃 |300 |映像 |1 |1 |2 |
| 榴莲 |500 |映像 |3 |2 |3 |
| 苹果 |100 |Web |1 |1 |1 |
| 无花果 |200 |Web |2 |2 |2 |
| 番木瓜 |200 |Web |2 |2 |3 |
| 无花果 |300 |Web |4 |3 |4 |
| 樱桃 |400 |Web |5 |4 |5 |
| 榴莲 |500 |Web |6 |5 |6 |

### <a name="top-n-with-dense-rank"></a>TOP N with DENSE RANK
以下示例返回每个组的前三条记录，每个分区中行的顺序排名编号中无间隔。

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

结果：

| 查询 | 延迟 | 垂直 | DenseRank |
| --- | --- | --- | --- |
| 香蕉 |300 |映像 |1 |
| 樱桃 |300 |映像 |1 |
| 榴莲 |500 |映像 |2 |
| 苹果 |100 |Web |1 |
| 无花果 |200 |Web |2 |
| 番木瓜 |200 |Web |2 |
| 无花果 |300 |Web |3 |

### <a name="top-n-with-rank"></a>TOP N with RANK
    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

结果：    

| 查询 | 延迟 | 垂直 | 排名 |
| --- | --- | --- | --- |
| 香蕉 |300 |映像 |1 |
| 樱桃 |300 |映像 |1 |
| 榴莲 |500 |映像 |3 |
| 苹果 |100 |Web |1 |
| 无花果 |200 |Web |2 |
| 番木瓜 |200 |Web |2 |

### <a name="top-n-with-rownumber"></a>TOP N with ROW_NUMBER
    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

结果：   

| 查询 | 延迟 | 垂直 | RowNumber |
| --- | --- | --- | --- |
| 香蕉 |300 |映像 |1 |
| 樱桃 |300 |映像 |2 |
| 榴莲 |500 |映像 |3 |
| 苹果 |100 |Web |1 |
| 无花果 |200 |Web |2 |
| 番木瓜 |200 |Web |3 |

### <a name="assign-globally-unique-row-number"></a>分配全局唯一行号
给每行分配一个全局唯一行号通常有用。 比起使用化简器，排名函数更方便且更高效。

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## <a name="analytic-functions"></a>解析函数
解析函数用于了解开窗中值的分布。 使用解析函数最常见的方案是百分位计算。

**支持的解析开窗函数**

* CUME_DIST 
* PERCENT_RANK
* PERCENTILE_CONT
* PERCENTILE_DISC

### <a name="cumedist"></a>CUME_DIST

CUME_DIST 计算一组值中指定值的相对位置。 CUME_DIST 会计算查询的百分比，这些要计算的查询的延迟小于或等于同一垂直中的当前查询延迟。 

对于行 R，假定按升序排序，CUME_DIST 是行数，其值小于或等于 R 值，使其除以分区中计算的行数。 

CUME_DIST 会返回 0 < x <= 1范围之中的数字。

**语法：**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

下面的示例使用 CUME_DIST 函数来计算垂直中每个查询的延迟时间百分位数。 

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

结果：

| 查询 | 延迟 | 垂直 | CumeDist |
| --- | --- | --- | --- |
| 榴莲 |500 |映像 |1 |
| 香蕉 |300 |映像 |0.666666666666667 |
| 樱桃 |300 |映像 |0.666666666666667 |
| 榴莲 |500 |Web |1 |
| 樱桃 |400 |Web |0.833333333333333 |
| 无花果 |300 |Web |0.666666666666667 |
| 无花果 |200 |Web |0.5 |
| 番木瓜 |200 |Web |0.5 |
| 苹果 |100 |Web |0.166666666666667 |

分区键是“Web”的分区中有六行

* 有六行的值等于或小于 500，因此 CUME_DIST 等于 6/6=1
* 有五行的值等于或小于 400，因此 CUME_DIST 等于 5/6=0.83
* 有四行的值等于或小于 300，因此 CUME_DIST 等于 4/6=0.66
* 有三行的值等于或小于 200，因此 CUME_DIST 等于 3/6=0.5。 有两行延迟值相同。
* 有一行的值等于或小于 100，因此 CUME_DIST 等于 1/6=0.16。 

**使用注意事项：**

* Tie 值始终会计算为相同的累积分布值。
* NULL 值被视为最低的可能值。
* 若要计算 CUME_DIST，需使用 ORDER BY 子句。
* CUME_DIST 类似于 PERCENT_RANK 函数

注意：如果 SELECT 语句后面没有 OUTPUT，则不允许使用 ORDER BY 子句。

### <a name="percentrank"></a>PERCENT_RANK
PERCENT_RANK 计算一组行内的行的相对排名。 PERCENT_RANK 用于计算行集或分区内值的相对位置。 PERCENT_RANK 返回的值的范围大于 0 且小于或等于 1。 不同于 CUME_DIST，PERCENT_RANK 第一行始终为 0。

语法：

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**说明**

* 任何集中的第一行的 PERCENT_RANK 都为 0。
* NULL 值被视为最低的可能值。
* PERCENT_RANK 需要 ORDER BY 子句。
* CUME_DIST 类似于 PERCENT_RANK 函数 

下面的示例使用 PERCENT_RANK 函数来计算垂直中每个查询的延迟时间百分位数。 

垂直指定 PARTITION BY 字句对结果集中的行进行分区。 OVER 子句中的 ORDER BY 子句对每个分区中的行进行排序。 

PERCENT_RANK 函数返回的值将垂直内的查询延迟排名表现为百分比。 

    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

结果：

| 查询 | 延迟：INT | 垂直 | PercentRank |
| --- | --- | --- | --- |
| 香蕉 |300 |映像 |0 |
| 樱桃 |300 |映像 |0 |
| 榴莲 |500 |映像 |1 |
| 苹果 |100 |Web |0 |
| 无花果 |200 |Web |0.2 |
| 番木瓜 |200 |Web |0.2 |
| 无花果 |300 |Web |0.6 |
| 樱桃 |400 |Web |0.8 |
| 榴莲 |500 |Web |1 |

### <a name="percentilecont--percentiledisc"></a>PERCENTILE_CONT 和 PERCENTILE_DISC
这两个函数基于列值的连续或离散分布来计算百分位数。

**语法：**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal** - 要计算的百分位数。 该值范围必须介于 0.0 和 1.0 之间。

    WITHIN GROUP (ORDER BY <identifier> [ ASC | DESC ])

指定一组数值来排序和计算百分位数。 仅允许一个列标识符。 该表达式计算结果必须为数值类型。 不允许其他数据类型。 默认排序顺序为升序。

    OVER ([ PARTITION BY <identifier,>…[n] ] )

基于应用百分位数函数的分区键将输入行集分为分区。 有关详细信息，请参阅本文档的 RANKING 部分。
注意：会忽略数据集中的所有空值。

**PERCENTILE_CONT** 根据列值的连续分布来计算百分位数。 结果会被插入，可能不等于该列中任何特定值。 

**PERCENTILE_DISC** 根据列值的离散分布来计算百分位数。 结果等于该列中的一个特定值。 也就是说，与 PERCENTILE_CONT 相比，PERCENTILE_DISC 始终会返回一个实际（原始输入）值。

可在下面的示例中看到这两种函数的工作原理，该示例尝试在每个垂直内找到延迟中值（百分位数=0.50）

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 

        FROM @querylog;

结果：

| 查询 | 延迟：INT | 垂直 | PercentileCont50 | PercentilDisc50 |
| --- | --- | --- | --- | --- |
| 香蕉 |300 |映像 |300 |300 |
| 樱桃 |300 |映像 |300 |300 |
| 榴莲 |500 |映像 |300 |300 |
| 苹果 |100 |Web |250 |200 |
| 无花果 |200 |Web |250 |200 |
| 番木瓜 |200 |Web |250 |200 |
| 无花果 |300 |Web |250 |200 |
| 樱桃 |400 |Web |250 |200 |
| 榴莲 |500 |Web |250 |200 |

对于 PERCENTILE_CONT，由于可插入值，因此即使 web 垂直中无任何查询具有 250 的延迟，此 web 的中值也是 250。 

PERCENTILE_DISC 不会插入值，因此 Web 的中值是 200（输出行中的一个实际值）。

## <a name="see-also"></a>另请参阅
* [使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Azure Data Lake Analytics 交互式教程](data-lake-analytics-use-interactive-tutorials.md)
* [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)



