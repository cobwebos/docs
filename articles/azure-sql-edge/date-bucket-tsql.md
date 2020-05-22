---
title: Date_Bucket (Transact-SQL) - Azure SQL Edge（预览版）
description: 了解如何在 Azure SQL Edge（预览版）中使用 Date_Bucket
keywords: Date_Bucket, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: dda292909b7b90579666cd83d460fc4051c9456c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595676"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

此函数从默认原始值 `1900-01-01 00:00:00.000` 返回对应于每个日期/时间段开始的日期/时间值。

有关所有 Transact-SQL 日期和时间数据类型及函数的概述，请参阅[日期和时间数据类型及函数 &#40;Transact-SQL&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/)。

[Transact-SQL 语法约定](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

`DATE_BUCKET` 使用默认原始日期值 `1900-01-01 00:00:00.000`即 1900 年 1 月 1 日星期一凌晨 12:00。

## <a name="syntax"></a>语法

```sql
DATE_BUCKET (datePart, number, date)
```

## <a name="arguments"></a>参数

*datePart*

与“number”参数一起使用的 date 的一部分。 例如： 年、月、分钟、秒等。

> [!NOTE]
> 对于 datepPart 参数，`DATE_BUCKET` 不接受用户定义的变量等效项。
  
|*datePart*|缩写形式|  
|---|---|
|day|dd, d |  
|week|wk, ww |  
|hour|**hh**|  
|minute|mi, n |  
|second|ss, s |  
|millisecond|ms|  

*数字*

一个整数，用于确定与 datePart 参数组合使用的存储桶的宽度。 这表示从原始时间开始的 dataPart 存储桶的宽度。 **`This argument cannot be a negative integer value`** ： 

*date*

可解析为下列值之一的表达式：

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

对于 date，`DATE_BUCKET` 将接受列表达式、表达式或用户定义的变量，前提是它们解析为上面提到的任何数据类型。

## <a name="return-type"></a>返回类型

此方法的返回值数据类型是动态的。 返回类型取决于为 `date` 提供的参数。 如果为 `date` 提供了有效的输入数据类型，则 `DATE_BUCKET` 返回相同的数据类型。 如果为 `date` 参数指定了字符串文本，则 `DATE_BUCKET` 会引发错误。

## <a name="return-values"></a>返回值

### <a name="understanding-the-output-from-date_bucket"></a>了解来自 `DATE_BUCKET` 的输出

`Date_Bucket` 返回与 datePart 和 number 参数相对应的最晚日期或时间值。 例如，在下面的表达式中，`Date_Bucket` 将返回输出值 `2020-04-13 00:00:00.0000000`，因为输出是基于从默认原始时间 `1900-01-01 00:00:00.000` 开始的一周存储桶计算得出的。 `2020-04-13 00:00:00.0000000` 值是从原始值 `1900-01-01 00:00:00.000` 开始的 6276 周。 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

对于下面的所有表达式，将返回相同的输出值 `2020-04-13 00:00:00.0000000`。 这是因为 `2020-04-13 00:00:00.0000000` 是从原始日期开始的 6276 周，6276 可被2、3、4 和 6 整除。

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

以下表达式的输出是从原始时间开始的 6275 周。

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

## <a name="datepart-argument"></a>datepart 参数

dayofyear、day 和 weekday 返回相同的值  。 每个 datepart 及其缩写都返回相同的值。
  
## <a name="number-argument"></a>number 参数

number 参数不能超出正 int 值的范围。 在以下语句中，number 的参数超出 int 的范围（超出 1）。 以下语句返回以下错误消息：“`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

如果 number 的负值传递到 `Date_Bucket` 函数，将返回以下错误。 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>date 参数  

`DATE_BUCKET` 返回与 `date` 参数的数据类型相对应的基值。 在下面的示例中，将返回具有 datetime2 数据类型的输出值。 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="remarks"></a>备注

在以下子句中使用 `DATE_BUCKET`：

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>示例

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. 从原始时间开始计算存储桶宽度为 1 的 Date_Bucket

其中每个语句都将从原始时间开始增加存储桶宽度为 1 的 date_bucket：

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

下面是结果集：

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. 使用表达式作为 number 和 date 形参的实参

以下示例使用不同类型的表达式作为 number 和 date 形参的实参 。 这些示例是使用“AdventureWorksDW2017”数据库生成的。
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>将用户定义的变量指定为 number 和 date  

此示例将用户定义的变量指定为 number 和 date 的参数 ：
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

下面是结果集：

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>将一列指定为 date

在下面的示例中，我们将计算基于每周日期存储桶分组的 OrderQuantity 的总和及 UnitPrice 的总和。
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

下面是结果集：
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>将标量系统函数指定为 date

此示例指定 `SYSDATETIME` 为 date。 返回的确切值取决于语句执行的日期和时间：
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

下面是结果集：

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>将标量子查询和标量函数指定为 number 和 date

此示例使用标量子查询 `MAX(OrderDate)` 作为 number 和 date 的参数 。 `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` 充当 number 形参的假实参，用来说明如何从值列表中选择 number 实参。
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>将数值表达式和标量系统函数指定为 number 和 date

此示例使用数值表达式 (-`(10/2))` 和标量系统函数 (`SYSDATETIME`) 作为 number 和 date 的参数 。
  
```sql
SELECT Date_Bucket(week,-(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>将聚合开窗函数指定为 number

此示例使用聚合开窗函数作为 number 的参数。
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 

## <a name="see-also"></a>另请参阅

[CAST 和 CONVERT (Transact-SQL)](/sql/t-sql/functions/cast-and-convert-transact-sql/)
