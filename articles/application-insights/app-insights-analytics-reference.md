---
title: "Azure Application Insights 中的 Analytics 参考 | Microsoft Docs"
description: "Application Insights 的强大搜索工具 - Analytics 中的语句参考。 "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: eea324de-d5e5-4064-9933-beb3a97b350b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b850264ef2b89ad1679ae1e956a58cc849e63c84
ms.lasthandoff: 03/25/2017


---
# <a name="reference-for-analytics"></a>分析参考
[Analytics](app-insights-analytics.md) 是 [Application Insights](app-insights-overview.md) 的强大搜索功能。 这些页面介绍 Analytics 查询语言。

其他信息源：

* 键入时，Analytics 会提供很多参考资料。 只要开始输入查询，系统就会提示可能的完整形式。
* [此教程页面](app-insights-analytics-tour.md)提供语言功能的分步介绍。
* [SQL 用户的备忘单](https://aka.ms/sql-analytics)转换最常见的惯用语言。
* [针对模拟数据测试驱动 Analytics](https://analytics.applicationinsights.io/demo)（如果你自己的应用尚未将数据发送到 Application Insights）。
 

## <a name="index"></a>索引
**Let** [let](#let-clause) | [materialize](#materialize) 

**查询和运算符** [as](#as-operator) | [count](#count-operator) | [datatable](#datatable-operator) | [distinct](#distinct-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [find](#find-operator) | [getschema](#getschema-operator) | [join](#join-operator) | [limit](#limit-operator) | [make-series](#make-series-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render 指令](#render-directive) | [restrict 子句](#restrict-clause) | [sample](#sample-operator) | [sample-distinct](#sample-distinct-operator) | [sort](#sort-operator) | [summarize](#summarize-operator) | [table](#table-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator) 

**聚合** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**标量** [Boolean Literals](#boolean-literals) | [Boolean operators](#boolean-operators) | [Casts](#casts) | [Scalar comparisons](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**数字** [Arithmetic operators](#arithmetic-operators) | [Numeric literals](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [log](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**日期和时间** [Date and time expressions](#date-and-time-expressions) | [Date and time literals](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**字符串** [GUIDs](#guids) | [Obfuscated String Literals](#obfuscated-string-literals) | [String Literals](#string-literals) | [String comparisons](#string-comparisons) | [countof](#countof) | [extract](#extract) | [in, !in](#in) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**数组、对象和动态** [Array and object literals](#array-and-object-literals) | [Dynamic object functions](#dynamic-object-functions) | [Dynamic objects in let clauses](#dynamic-objects-in-let-clauses) | [JSON Path expressions](#json-path-expressions) | [Names](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [in, !in](#in) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)

## <a name="let"></a>Let
### <a name="let-clause"></a>let 子句
**表格 let - 命名表格**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**标量 let - 命名值**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda let - 命名函数**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ", 
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests 
    | summarize count() by bin(timestamp, 1h) 
    | project count_, pacificTime=us_date(timestamp-8h)

Let 子句将 [name](#names) 绑定到表格结果、标量值或函数。 子句是某个查询的前缀，限定范围则是该查询。 （Let 不允许对稍后将在会话中使用的内容命名。）

**语法**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* 类型：`bool`、`int`、`long`、`double`、`string`、`timespan`、`datetime`、`guid`、[`dynamic`](#dynamic-type)
* *plain_query*：不以 let 语句为前缀的查询。

**示例**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...

将表结果转换为标量并用于查询：

```
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

### <a name="materialize"></a>materialize

如果在下游多次使用了 let 子句的结果，则使用 materialize() 可以提高性能。 在执行查询时，Materialize() 将计算并缓存表格 let 子句的结果，确保不会多次执行该查询。

**语法**

    materialize(expression)

**参数**

* `expresion`：在执行查询期间要计算和缓存的表格表达式。

**提示**

* 如果 join/union 中的操作数包含可执行一次的通用子查询，请使用 materialize（参阅下面的示例）。
* 如果需要联接/联合分支，materialize 也很有用。
* Materialize 可为缓存的结果命名，只允许在 let 语句中使用。
* Materialze 的缓存大小限制为 5 GB。 这种限制是针对每个群集节点实施的，对所有查询通用。

**示例：self-join**


```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let materializedScope = pageViews
| summarize by name, Day = startofday(timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project name, Day1 = Day
| join kind = inner
(
    cachedResult
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

未缓存的版本使用结果 `scope` 两次：

```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let scope = pageViews
| summarize by name, Day = startofday(timestamp);
scope      // First use of this table.
| project name, Day1 = Day
| join kind = inner
(
    scope  // Second use can cause evaluation twice.
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

## <a name="queries-and-operators"></a>查询和运算符
针对遥测的查询由对源流的引用组成，后跟筛选器管道。 例如：

```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```

以管道字符 `|` 作为前缀的每个筛选器均是运算符的一个实例，并且带有某些参数。 运算符的输入是前一管道的结果表。 大多数情况下，任何参数均是输入列上的[标量表达式](#scalars)。 在少数情况下，参数是输入列的名称，有时参数是另一个表。 查询结果始终以表呈现，即使它仅有一列和一行。

查询可能包含单个换行符，但由空行结束。 这些查询的 `//` 和行尾之间可能包含注释。

查询可能以一个或多个 [let 子句](#let-clause)为前缀，用于定义可在查询中使用的标量、表或函数。

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> 以下查询示例使用 `T` 表示前一管道或源表。
> 
> 

### <a name="as-operator"></a>as 运算符

临时将名称绑定到输入表格表达式。

**语法**

    T | as name

**参数**

* *name：*表的临时名称

**说明**

* 如果想要在后面的子表达式中使用名称，请使用 [let](#let-clause) 而不是 *as*。
* 使用 *as* 可以指定表在 [union](#union-operator)、[find](#find-operator) 或 [search](#search-operator) 结果中显示的名称。

**示例**

```AIQL
range x from 1 to 10 step 1 | as T1
| union withsource=TableName (requests | take 10 | as T2)
```

### <a name="count-operator"></a>count 运算符
`count` 运算符返回输入记录集中的记录数（行数）。

**语法**

    T | count

**参数**

* *T*：待计算记录数的表格数据。

**返回**

此函数返回包含单个记录且列类型为 `long` 的表。 唯一单元格的值是 *T* 中的记录数。 

**示例**

```AIQL
requests | count
```

### <a name="datatable-operator"></a>datatable 运算符

指定表内联。 在查询本身中定义架构和值。

请注意，此运算符没有管道输入。

**语法**

    datatable ( ColumnName1 : ColumnType1 , ...) [ScalarValue1, ...]

* *ColumnName* 列的名称。
* *ColumnType* 一个[数据类型](#scalars)。 
* *ScalarValue* 相应类型的值。 值计数必须是列数的倍数。 

**返回**

包含指定值的表。

**示例**

```AIQL
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

### <a name="distinct-operator"></a>distinct 运算符

返回包含具有不同值组合的行集的表。 在选择在执行运算前投射到列子集。

**语法**

    T | distinct *              // All columns
    T | distinct Column1, ...   // Columns to project

**示例**

```AIQL
datatable (Supplier: string, Fruit: string, Price:int) 
["Contoso", "Grapes", 22,
"Fabrikam", "Apples", 14,
"Contoso", "Apples", 15,
"Fabrikam", "Grapes", 22]
| distinct Fruit, Price 
```


|水果|价格|
|---|---|
|葡萄|22|
|苹果|14|
|苹果|15|


### <a name="evaluate-operator"></a>evaluate 运算符
`evaluate` 是一种扩展机制，支持追加到查询的专用算法。

`evaluate` 必须是查询管道中的最后一个运算符（`render` 是最后一个运算符的情况除外）。 它不能出现在函数体中。

[evaluate autocluster](#evaluate-autocluster) | [evaluate basket](#evaluate-basket) | [evaluate diffpatterns](#evaluate-diffpatterns) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>evaluate autocluster
     T | evaluate autocluster()

AutoCluster 查找数据中离散属性（维度）的常见模式，并将原始查询的结果（无论是 100 行还是 10 万行）减少到数量较少的模式。 开发 AutoCluster 的目的是帮助分析故障（例如异常、崩溃），但也可能处理任何筛选的数据集。 

**语法**

    T | evaluate autocluster( arguments )

**返回**

AutoCluster 返回一组模式（通常较小），这些模式跨多个离散属性捕获具有共享常见值的数据部分。 每种模式均由结果中的一行表示。 

前两列是此模式从原始查询外所捕获行的数量和百分比。 其余列来自原始查询，其值是来自该列的特定值或表示变量值的“*”。 

请注意，这些模式并不是分离的：它们可能重叠，且通常不包括所有原始行。 某些行可能不属于任何模式。

**提示**

* 在输入管道中使用 `where` 和 `project` 可将数据缩减到仅剩所需数据。
* 找到所需行时，可通过将该行的特定值添加到 `where` 筛选器，来对其进行深入研究。

**参数（全部可选）**

* `output=all | values | minimal` 
  
    结果的格式。 结果中始终显示“计数”和“百分比”列。 
  
  * `all` - 输入中的所有列均为输出
  * `values` - 筛选出结果中仅含“*”的列
  * `minimal` - 还需筛选出原始查询中对于所有行都相同的列。 
* `min_percent=`*double*（默认值：1）
  
    生成行数的最小百分比。
  
    示例：`T | evaluate autocluster("min_percent=5.5")`
* `num_seeds=` *int*（默认值：25） 
  
    种子数决定算法初始本地搜索点的数量。 在某些情况下，通过在增加搜索空间和减缓查询之间进行权衡，增加种子数可增加结果的数量（或质量），具体取决于数据结构。 增加和减少 num_seeds 参数均会产生不良结果，因此，将其减少至 5 以下几乎无法提高性能，将其增加至 50 以上往往也不能生成其他模式。
  
    示例：`T | evaluate autocluster("num_seeds=50")`
* `size_weight=` *0<double<1*+（默认值：0.5）
  
    允许控制泛型（高覆盖率）和信息性（多共享的值）之间的平衡。 增加 size_weight 通常会减少模式数量，并且每个模式可能占更大百分比。 减少 size_weight 通常会产生更多特定模式，这些模式具有更多共享值和较小覆盖率百分比。 实质上，该公式是规范化泛型分数和信息性分数之间的加权几何平均值，且 size_weight 和 1-size_weight 为权重。 
  
    示例：`T | evaluate autocluster("size_weight=0.8")`
* `weight_column=` *column_name*
  
    根据指定权重考虑输入中的每一行（默认情况下每行具有权重“1”），权重列的常见用法是对已嵌入每一行的数据进行采样或存储/聚合。
  
    示例：`T | evaluate autocluster("weight_column=sample_Count")` 

#### <a name="evaluate-basket"></a>evaluate basket
     T | evaluate basket()

Basket 查找数据中离散属性（维度）的所有频繁模式，并返回原始查询中传递频率阈值的所有频繁模式。 Basket 能确保查找出数据中的所有频繁模式，但不能保证存在多项式运行时。 在行数方面，查询的运行时是线性的，但有些时候，在列数（维度）方面，它可能是指数的。 Basket 基于最初开发用于 basket 分析数据挖掘的 Apriori 算法。 

**返回**

事件的多个指定小数（默认为 0.05）中显示的所有模式。

**参数（全部可选）**

* `threshold=` *0.015<double<1*（默认值：0.05） 
  
    设置作为频繁行的最低比例（不会返回比例较低的模式）。
  
    示例：`T | evaluate basket("threshold=0.02")`
* `weight_column=` *column_name*
  
    根据指定权重考虑输入中的每一行（默认情况下每行具有权重“1”），权重列的常见用法是对已嵌入每一行的数据进行采样或存储/聚合。
  
    示例：T | evaluate basket("weight_column=sample_Count")
* `max_dims=` *1<int*（默认值：5）
  
    设置每个 basket 不相关维度的最大数量，默认情况下此数量不能减少查询运行时。
* `output=minimize` | `all` 
  
    结果的格式。 结果中始终显示“计数”和“百分比”列。
  
  * `minimize` - 筛选出结果中仅含“*”的列。
  * `all` - 输入中的所有列均为输出。

#### <a name="evaluate-diffpatterns"></a>evaluate diffpatterns
     requests | evaluate diffpatterns("split=success")

Diffpatterns 比较同一结构中的两个数据集，并查找区分两个数据集的离散属性（维度）的模式。 开发 diffpatterns 的目的是帮助分析故障（例如，比较给定时间范围内的故障和非故障），但也可查找出同一结构中任何两个数据集之间的差异。 

**语法**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**返回**

Diffpatterns 返回一组模式（通常较小），可捕获两个数据集中数据的不同部分（例如，某一模式可捕获第一个数据集中占较大百分比的行和第二个数据集中占较小百分比的行）。 每种模式均由结果中的一行表示。

前四列是每个数据集中的模式在原始查询外所捕获行的计数和百分比，第五行是两个数据集的差异（采用绝对百分比形式）。 其余列来自原始查询，其值是来自该列的特定值或表示变量值的 *。 

请注意，这些模式并不是截然不同的：它们可能重叠，且通常不包括所有原始行。 某些行可能不属于任何模式。

**提示**

* 在输入管道中使用 where 和 project 可将数据缩减到仅剩所需数据。
* 找到所需行时，可通过将该行的特定值添加到 where 筛选器，来对其进行深入研究。

**参数**

* `split=` 列名称（必需）
  
    列必须刚好具有两个值。 如有必要，请创建如下列：
  
    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`
* `target=` 字符串
  
    指示算法仅查找目标数据集中具有较高百分比的模式，目标必须是拆分列的两个值之一。
  
    `requests | evaluate diffpatterns("split=success", "target=false")`
* `threshold=` *0.015<double<1*（默认值：0.05） 
  
    设置两个集之间的最小模式（比例）差异。
  
    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`
* `output=minimize | all`
  
    结果的格式。 结果中始终显示“计数”和“百分比”列。 
  
  * `minimize` - 筛选出结果中仅含“*”的列
  * `all` - 输入中的所有列均为输出
* `weight_column=` *column_name*
  
    根据指定的权重考虑输入中的每一行（默认情况下每行具有权重“1”）。 权重列的常见用法是对已嵌入每一行的数据进行采样或存储/聚合。
  
    `requests | evaluate autocluster("weight_column=itemCount")`

#### <a name="evaluate-extractcolumns"></a>evaluate extractcolumns
     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns 用于使用多个简单列扩充表格，这些列基于其类型从（半）结构化的列中动态提取。 目前仅支持 json 列，包括 json 的动态和字符串序列化。

* `max_columns=` *int*（默认值：10） 
  
    新添加的列数是动态的，并且可能非常大（实际上它是所有 json 记录中的非重复键数），因此必须限制列数。 新列基于其频率按降序排列，最多可向表中添加 max_columns。
  
    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`
* `min_percent=` *double*（默认值：10.0） 
  
    限制新列的另一种方法是忽略频率低于 min_percent 的列。
  
    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`
* `add_prefix=` *bool*（默认值：true） 
  
    如果为 true，则添加复杂列的名称作为提取列名称的前缀。
* `prefix_delimiter=` *string*（默认值："_"） 
  
    如果 add_prefix=true，则此参数定义用于连接新列名称的分隔符。
  
    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`
* `keep_original=` *bool*（默认值：false） 
  
    如果为 true，则原始 (json) 列将在输出表中保留。
* `output=query | table` 
  
    结果的格式。 
  
  * `table` - 输出是用接收到的表减去指定的输入列加上已从输入列中提取的新列。
  * `query` - 输出是一个字符串，表示为获取表形式的结果而执行的查询。 

### <a name="extend-operator"></a>extend 运算符
     T | extend duration = stopTime - startTime

将一个或多个已计算列追加到表。 

**语法**

    T | extend ColumnName = Expression [, ...]

**参数**

* *T*：输入表。
* *ColumnName*：待添加列的名称。 [名称](#names)区分大小写，且可包含字母、数字或“_”字符。 使用 `['...']` 或 `["..."]` 引用包含其他字符的关键字或名称。
* 表达式：对现有列进行的计算。

**返回**

输入表的副本，包含指定的其他列。

**提示**

* 如果希望删除或重命名某些列，则改用 [`project`](#project-operator)。
* 不要仅仅为了获取在较长表达式中使用的较短名称而使用 `extend`。 `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 
  
    表的本机列索引已生成；新名称定义未编制索引的其他列，因此查询运行速度可能变慢。

**示例**

```AIQL
traces
| extend
    Age = now() - timestamp
```

### <a name="find-operator"></a>find 运算符

    find in (Table1, Table2, Table3) where id=="a string"
    find in (Table1, Table2, Table3) where id=="a string" project column1, column2
    find in (Table1, Table2, Table3) where * has "a string"
    find in (Table1, Table2, Table3) where appName in ("string 1", "string 2", "string 3")

在一组表中查找与谓词相匹配的行。

**语法**

    find in (Table1, ...) 
    where Predicate 
    [project Column1, ...]

**参数**

* Table1 表名称或查询。 它可以是 let 定义的表，但不能是函数。 使用表名的效果优于使用查询。
* 谓词 计算指定表中每一行的布尔表达式。
 * 可以使用“*”代替字符串比较中的列名称
* Column1`project` 选项可指定哪些列必须始终出现在输出中。 

**结果**

默认情况下，输出表包含：

* `source_` - 每行的源表标记。 如果想要指定显示在此列中的名称，请在每个 table 表达式的末尾使用 [as](#as-operator)。
* 谓词中显式提及的列
* 所有输入表共有的非空列。
* `pack_` - 包含其他列中的数据的属性包。

请注意，此格式可能随输入数据或谓词的更改而更改。 若要指定一组固定的列，请使用 `project`。

**示例**

获取所有请求和异常，来自可用性测试和机器人的请求和异常除外：

```AIQL

    find in (requests, exceptions) where isempty(operation_SyntheticSource)
```

查找来自 UK 的所有请求和异常，来自可用性测试和机器人的请求和异常除外：

```AIQL

    let requk = requests
    | where client_CountryOrRegion == "United Kingdom";
    let exuk = exceptions
    | where client_CountryOrRegion == "United Kingdom";
    find in (requk, exuk) where isempty(operation_SyntheticSource)
```

查找其字段包含术语“test”的最近遥测：

```AIQL

    find in (traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions) 
    where * has 'test' 
    | top 100 by timestamp desc
```

**性能提示**

* 向 `where` 谓词添加基于时间的术语。
* 使用 `let` 子句，而不是编写内联查询。

### <a name="getschema-operator"></a>getschema 运算符

   T | getschema
   
生成显示输入表的列名和类型的表。

```AIQL
requests
| project appId, appName, customDimensions, duration, iKey, itemCount, success, timestamp 
| getschema 
```

![getschema 的结果](./media/app-insights-analytics-reference/getschema.png)

### <a name="join-operator"></a>join 运算符
    Table1 | join (Table2) on CommonColumn

通过匹配指定列的值合并两个表的行。

**语法**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**参数**

* *Table1*- join 的“左侧”。
* *Table2* - join 的“右侧”。 它可能是输出表的嵌套查询表达式。
* *CommonColumn* - 在两个表中具有相同名称的列。
* *Kind* - 指定两个表中的行的匹配方式。

**返回**

具有以下内容的表：

* 一列，用于每个表中的每一列，包括匹配键。 如果存在名称冲突，将自动重命名右侧列。
* 一行，用于输入表之间的所有匹配项。 匹配项是从表中选出的一行，该表中的所有 `on` 字段值与其他表中的值相同。 
* 未指定 `Kind` 或 `= innerunique`
  
    左侧中仅有一行与 `on` 键的每个值匹配。 输出包含一行，用于此行与右侧行的每一个匹配项。
* `kind=inner`
  
     输出中存在一行，用于左侧和右侧匹配行的每个组合。
* `kind=leftouter`（或 `kind=rightouter` 或 `kind=fullouter`）
  
     除了内部匹配，还有一行用于左侧（和/或右侧）的每一行（即使没有匹配项）。 在这种情况下，不匹配的输出单元格包含 null。
* `kind=leftanti`
  
     返回左侧中与右侧不具有匹配项的所有记录。 结果表中只有左侧的列。 
* `kind=leftsemi`（或 `leftantisemi`）

    无论右表中是否存在匹配，左表中返回一行。 结果不包括右侧的数据。


**提示**

结果表大小限制为 64 MB。

为获得最佳性能：

* 先使用 `where` 和 `project` 减少输入表中的行数和列数，然后使用 `join`。 
* 如果某个表始终小于另一个表，则将其用作 join 的左侧（通过管道传送）。
* join 匹配的列必须具有相同名称。 如有必要，使用 project 运算符重命名其中一个表中的一列。



**示例**

从日志中获取扩展活动，某些条目在日志中标记活动的开始和结束时间。 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>limit 运算符
     T | limit 5

从输入表返回指定行数。 不保证返回哪些记录。 （若要返回特定的记录，请使用 [`top`](#top-operator)。）

**别名** `take`

**语法**

    T | limit NumberOfRows


**提示**

`Take` 是以交互方式工作时查看结果示例的简单和有效方法。 请注意，它不保证生成任何特定的行，或按任何特定顺序生成。

返回到客户端的行数有隐式限制（即使不使用 `take`）。 若要提升此限制，请使用 `notruncation` 客户端请求选项。

### <a name="make-series-operator"></a>make-series 运算符

执行聚合。 与 [summarize](#summarize-operator) 不同，每个组只有一个输出行。 在结果列中，每个组中的值打包为数组。 

**语法**

    T | 
    make-series [Column =] Aggregation default = DefaultValue [, ...] 
    on AxisColumn in range(start, stop, step) 
    by [Column =] GroupExpression [, ...]


**参数**

* *Column*：结果列的可选名称。 默认为派生自表达式的名称。
* *DefaultValue：*如果没有任何行包含特定的 AxisColumn 和 GroupExpression 值，则在结果中，将为数组的相应元素分配 DefaultValue。 
* *Aggregation：*使用 [aggregation 函数](#aggregations)的数字表达式。 
* *AxisColumn：*用作序列排序依据的列。 可将其视为时间线，但接受任何数字类型。
*start、stop、step：*定义每个行的 AxisColumn 值列表。 其他每个结果聚合列包含相同长度的数组。 
* *GroupExpression*：各列的表达式，提供一组非重复值。 每个 GroupExpression 值的输出中包含一行。 通常，它是已提供一组受限值的列名。 

**提示**

结果数组呈现在分析图表中，呈现方式与相应的 summarize 操作相同。

**示例**

```AIQL
requests
| make-series sum(itemCount) default=0, avg(duration) default=0
  on timestamp in range (ago(7d), now(), 1d)
  by client_City
```

![make-series 的结果](./media/app-insights-analytics-reference/make-series.png)

### <a name="mvexpand-operator"></a>mvexpand 运算符
    T | mvexpand listColumn 

扩展动态类型 (JSON) 单元格中的列表，以便每个条目都具有单独一行。 将复制扩展行中的所有其他单元格。 

（另请参阅执行相反函数的 [`summarize makelist`](#summarize-operator)）。

**示例**

假设输入表是：

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |[0,1,"k","v"] |

    mvexpand D

结果是：

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |0 |
| 2 |"world" |1 |
| 2 |"world" |"k" |
| 2 |"world" |"v" |

**语法**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**参数**

* *ColumnName*：在结果中，已命名列中的数组将扩展为多行。 
* *ArrayExpression*：生成数组的表达式。 如果使用此形式，则会添加新列并保留现有列。
* *Name*：新列的名称。
* *Typename*：将扩展表达式转换为特定类型
* *RowLimit*：从每个原始行生成的最大行数。 默认值为 128。

**返回**

用于已命名列或数组表达式中任何数组的每个值的多个行。

扩展列始终具有动态类型。 如需计算或聚合值，请使用转换，如 `todatetime()` 或 `toint()`。

支持两种模式的属性包扩展：

* `bagexpansion=bag`：将属性包扩展为单个条目属性包。 这是默认扩展。
* `bagexpansion=array`：将属性包扩展为双元素 `[`*key*`,`*value*`]` 数组结构，可允许统一访问键和值（以及对属性名称运行不同的计数聚合）。

**示例**

    exceptions | take 1
    | mvexpand details[0]

针对详细信息字段中的每一项将异常记录拆分成多行。

### <a name="parse-operator"></a>parse 运算符
    T | parse "I got 2 socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *

    T |  parse kind=regex "I got socks for my 63rd birthday"
    with "(I|She) got " present " for .*?" year:long *

从字符串中提取值。 可以使用简单或正则表达式匹配。

**语法**

    T | parse [kind=regex|relaxed] SourceText
        with [Match | Column [: Type [*]] ]  ...

**参数**

* `T`：输入表。
* `kind`： 
  * `simple`（默认）：`Match` 字符串为纯字符串。
  * `relaxed`：如果文本无法分析为列的类型，该列将设置为 null，且分析继续 
  * `regex`：`Match` 字符串属于正则表达式。
* `Text`：一列或可计算为或转换为字符串的其他表达式。
* *Match*：匹配字符串的下一部分并将其丢弃。
* *Column*：将字符串的下一部分分配给该列。 如果列不存在，则创建列。
* *Type*：将字符串的下一部分分析为指定类型，如 int、date、double。 

**返回**

输入表，根据“列”的列表扩展。

`with` 子句中的元素反过来与源文本匹配。 每个元素占用一部分源文本： 

* 文本字符串或正则表达式按匹配的长度移动匹配光标。
* 在正则表达式分析中，正则表达式可以使用最小化运算符“?”尽快移至以下匹配项。
* 包含类型的列名称将文本作为指定类型进行分析。 除非 kind=relaxed，否则不成功分析将验证是否匹配整个模式。
* 不含类型或含“字符串”类型的列名称会复制最小字符数以访问以下匹配项。
* “*”跳过最小字符数以访问以下匹配项。可在模式的开端和结尾或在类型后使用“*”，但不能在字符串后或字符串匹配项之间使用。

分析模式中的所有元素必须匹配正确；否则，无法生成任何结果。 此规则的例外是，当 kind=relaxed 时，如果分析类型化变量失败，分析的其余部分将继续。

**示例**

*Simple*：

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

| x | counter | 礼物 | 年龄 |
| --- | --- | --- | --- |
| 1 |2 |袜子 |63 |

*Relaxed*：

输入包含每个类型化列的正确匹配时，relaxed 分析生成与 simple 分析相同的结果。 但如果其中一个类型化列分析不正确，relaxed 分析会继续执行模式的剩下部分，而 simple 分析会停止且无法生成任何结果。

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


| x | 礼物 | 年龄 |
| --- | --- | --- |
| 1 |袜子 |63 |

*Regex*：

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 07:31, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 06:20:00 ) }" 
// Parse it:
| parse kind=regex s 
  with ".*?=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       @".*\)" *
| project-away x, s
```

| resource | 切片 | lock | 发布 | 上次发布 |
| --- | --- | --- | --- | --- |
| 计划程序 |16 |2016/02/17 07:31:00 |2016/02/17 08:41 |2016-02-17T06:20:00Z |

### <a name="project-operator"></a>project 运算符
    T | project cost=price*quantity, price

选择要包含、重命名或删除的列，然后插入新的计算列。 结果中的列顺序由参数的顺序指定。 只有在参数中指定的列才能包含在结果中：输入中的任何其他列都将删除。  （另请参阅 `extend`。）

**语法**

    T | project ColumnName [= Expression] [, ...]

**参数**

* *T*：输入表。
* *ColumnName*：要显示在输出中的列名称。 如果没有 *Expression*，则具有该名称的列必须显示在输入中。 [名称](#names)区分大小写，且可包含字母、数字或“_”字符。 使用 `['...']` 或 `["..."]` 引用包含其他字符的关键字或名称。
* *Expression*：引用输入列的可选标量表达式。 
  
    允许返回与输入中现有列具有相同名称的新计算列。

**返回**

一个表，其中包含名为参数的列和与输入表中相同数量的行。

**示例**

以下示例演示可使用 `project` 运算符执行的几种操作。 输入表 `T` 具有属于 `int` 类型的三列：`A`、`B` 和 `C`。 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>project-away 运算符
    T | project-away column1, column2, ...

排除指定的列。 结果包含所有输入列，自己命名的列除外。

### <a name="range-operator"></a>range 运算符
    range LastWeek from ago(7d) to now() step 1d

生成值的单列表。 请注意，该表不含管道输入。 

| LastWeek |
| --- |
| 2015-12-05 09:10:04.627 |
| 2015-12-06 09:10:04.627 |
| ... |
| 2015-12-12 09:10:04.627 |

**语法**

    range ColumnName from Start to Stop step Step

**参数**

* *ColumnName*：输出表中的单列名称。
* *Start*：输出中的最小值。
* *Stop*：输出中正生成的最大值（如果 *step* 跳过此值，则为最大值边界）。
* *Step*：两个连续值之间的差异。 

参数必须为数字、日期或时间跨度值。 它们不能引用任何表的列。 （如需基于输入表计算范围，请使用 [range*函数*](#range)，可能需配合使用 [mvexpand 运算符](#mvexpand-operator)。） 

**返回**

一个包含单个列 *ColumnName* 的表，其值包括 *Start*、*Start* + *Step*...直至 *Stop*。

**示例**  

```AIQL
range Steps from 1 to 8 step 3
```

一个包含单个列 `Steps` 的表，其类型是 `long`，其值是 `1`、`4` 和 `7`。

**示例**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

过去 7 天午夜时分的表。 bin (floor) 函数将每次时间缩减到当天的开始。

**示例**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

演示如何使用 `range` 运算符创建小型的临时维度表，然后使用该表在源数据不具有值的位置引入零。

### <a name="reduce-operator"></a>reduce 运算符
    exceptions | reduce by outerMessage

尝试将相似记录分成一组。 对于每个组，此运算符输出其认为最能准确描述该组的 `Pattern` 和该组中的记录 `Count`。

![](./media/app-insights-analytics-reference/reduce.png)

**语法**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**参数**

* *ColumnName*：待检查的列。 它必须是字符串类型。
* <seg>
  *Threshold*：范围 {0..1} 内的值。</seg> 默认值为 0.001。 对于大型输入，阈值应较小。 

**返回**

两个列，`Pattern` 和 `Count`。 在许多情况下，Pattern 是来自列的一个完整值。 在某些情况下，它可以识别常用术语，并用“*”替换变量部分。

例如，`reduce by city` 的结果可能包括： 

| 模式 | 计数 |
| --- | --- |
| San * |5182 |
| Saint * |2846 |
| Moscow |3726 |
| \* -on- \* |2730 |
| 巴黎 |27163 |

### <a name="render-directive"></a>render 指令
    T | render [ table | timechart  | barchart | piechart ]

render 指示表示层如何显示表。 它应是管道的最后一个元素。 这是在屏幕上使用控件的一种便捷替代方法，从而允许通过特定呈现方法保存查询。

### <a name="restrict-clause"></a>restrict 子句
指定可用于以下运算符的表名称集。 例如：

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sample-operator"></a>sample 运算符

从输入表返回均匀分布的随机行。


**语法**

    T | sample NumerOfRows

* *NumberOfRows*：示例中待返回的行数。

**提示**

若不需要均匀分布的示例，请使用 `Take`。


### <a name="sample-distinct-operator"></a>sample-distinct 运算符

返回单个列，该列包含所请求列的非重复值，且数目不超过指定数目。 当前不返回均匀分布的示例。

**语法**

    T | sample-distinct NumberOfValues of ColumnName

* *NumberOfValues* 所需的表的长度。
* *ColumnName* 所需的列。

**提示**

通过在 let 语句中使用 sample-distinct，之后使用 in 运算符进行筛选，可轻松实现总体抽样（参见示例）。
 
如果要获得最靠前的值，而非仅仅一个示例，可以使用 top-hitters 运算符。

如果要对数据行（而不是特定列的值）进行抽样，请参阅 [sample 运算符](#sample-operator)。

**示例**

进行总体抽样，并执行进一步的计算，了解汇总不会超过查询限制。

```AIQL
let sampleops = toscalar(requests | sample-distinct 10 of OperationName);
requests | where OperationName in (sampleops) | summarize total=count() by OperationName
```
### <a name="search-operator"></a>search 运算符

搜索多个表和列中的字符串。

**语法**

    search [kind=case_sensitive] [in (TableName, ...)] SearchToken

    T | search [kind=case_sensitive] SearchToken

    search [kind=case_sensitive] [in (TableName, ...)] SearchPredicate

    T | search [kind=case_sensitive] SearchPredicate

在任何表的任何列中查找给定标记字符串的匹配项。
 
* *TableName*：全局定义的（requests、exceptions 等）或者由 [let 子句](#let-clause)定义的表名。 可以使用通配符，例如 r*。
* *SearchToken：*一个标记字符串，必须全字匹配。 可以使用尾随通配符。 “Amster*”匹配“Amsterdam”，但是“Amster”不匹配。
* *SearchPredicate：*基于表中的列的布尔表达式。 可以使用“*”作为列名中的通配符。

**示例**

```AIQL
search "Amster*"  //All columns, all tables

search name has "home"  // one column

search * has "home"     // all columns

search in (requests, exceptions) "Amster*"  // two tables

requests | search "Amster*"

requests | search name has "home"

```




### <a name="sort-operator"></a>sort 运算符
    T | sort by country asc, price desc

按照一个或多个列的顺序对输入表的行排序。

**别名** `order`

**语法**

    T  | sort by Column [ asc | desc ] [ , ... ]

**参数**

* *T*：待排序的表输入。
* *Column*：作为排序依据的 *T* 的列。 值的类型必须是数字、日期、时间或字符串。
* `asc` 按升序（即由低到高）排列。 默认值是 `desc`，降序，由高到低。

**示例**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
表 Traces 中具有特定 `ActivityId` 的所有行，按时间戳排序。

### <a name="summarize-operator"></a>summarize 运算符
生成可聚合输入表内容的表。

    requests
    | summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

此表显示每个国家/地区中的数量、平均请求持续时间和几个城市。 输出中有一行用于每个不同的国家/地区。 输出列显示计数、平均持续时间、城市和国家/地区。 忽略所有其他输入列。

    T | summarize count() by price_range=bin(price, 10.0)

此表显示每个价格区间（[0,10.0]、[10.0,20.0] 等）内的项数。 此示例中有一列表示计数，还有一列表示价格范围。 忽略所有其他输入列。

**语法**

    T | summarize
         [  [ Column = ] Aggregation [ , ... ] ]
         [ by
            [ Column = ] GroupExpression [ , ... ] ]

**参数**

* *Column*：结果列的可选名称。 默认为派生自表达式的名称。 [名称](#names)区分大小写，且可包含字母、数字或“_”字符。 使用 `['...']` 或 `["..."]` 引用包含其他字符的关键字或名称。
* *Aggregation*：对聚合函数 `count()` 或 `avg()` 等的调用，其中列以参数命名。 请参阅[聚合](#aggregations)。
* *GroupExpression*：各列的表达式，提供一组非重复值。 它通常是已提供一组受限值的列名称，或是将数字或时间列用作参数的 `bin()`。 

如果不使用 `bin()` 提供数字或时间表达式，Analytics 会自动将其和 `1h` 的间隔应用于时间，或将 `1.0` 应用于数字。

如果未提供 *GroupExpression*，整个表会在单个输出行中进行汇总。

**返回**

输入行将排列成与 `by` 表达式具有相同值的组。 然后，对每个组计算指定的聚合函数，从而为每组生成行。 结果包含 `by` 列，还至少包含用于每个计算聚合的一列。 （某些聚合函数返回多个列。）

结果具有多个行，因为 `by` 值存在不同组合。 如果希望汇总数值的范围，请使用 `bin()` 将范围减少为离散值。

> [!NOTE]
> 尽管可为聚合和分组表达式提供任意表达式，但使用简单列名称或将 `bin()` 应用于数值列会更加高效。

### <a name="table-operator"></a>table 操作

    table('pageViews')

在参数字符串中命名的表。

**语法**

    table(tableName)

**参数**

* *tableName：*一个字符串。 表的名称，可以是静态的，也可以是 let 子句的结果。

**示例**

    table('requests');


    let size = (tableName: string) {
        table(tableName) | summarize sum(itemCount)
    };
    size('pageViews');



### <a name="take-operator"></a>take 运算符
[limit](#limit-operator) 的别名

### <a name="top-operator"></a>top 运算符
    T | top 5 by Name desc nulls first

返回按指定列排序的前 *N* 列。

**语法**

    T | top NumberOfRows by Sort_expression [ asc | desc ] [nulls first|nulls last] [, ... ]

**参数**

* *NumberOfRows*：待返回的 *T* 的行数。
* *Sort_expression*：作为行排序依据的表达式。 它通常只是列名称。 可指定多个 sort_expression。
* `asc` 或 `desc`（默认）可能会控制实际从范围“底部”还是“顶部”进行选择。
* `nulls first` 或 `nulls last` 控制 null 值的显示位置。 `First` 是 `asc` 的默认值，`last` 是 `desc` 的默认值。

**提示**

`top 5 by name` 表面上等效于 `sort by name | take 5`。 但前者运行速度更快，并且始终返回已排序的结果，而 `take` 则不能保证。

### <a name="top-nested-operator"></a>top-nested 运算符
    requests
    | top-nested 5 of name by count()
    , top-nested 3 of performanceBucket by count()
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart

产生分层结果，其中每个级别都是对上一级别的深化。 它可用于回答此类问题：“排行前 5 的请求是什么，以及对于每个请求，排行前 3 的性能存储桶是哪些，那么对于每个性能存储桶，发出这些请求的前 3 个国家/地区有哪些？”

**语法**

   T | top-nested N of COLUMN by AGGREGATION [, ...]

**参数**

* N:int - 待返回或待传递到下一级别的行数。 在具有三个级别的查询中，N 是 5、3、3，总行数则为 45。
* COLUMN - 待分组以进行聚合的列。 
* AGGREGATION - 待应用到行的每个组的[聚合函数](#aggregations)。 这些聚合结果确定要显示的顶级组。

### <a name="union-operator"></a>union 运算符
     Table1 | union Table2, Table3

获取两个或多个表，并返回表中的所有行。 

**语法**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**参数**

* *Table1*、*Table2*...
  * 表的名称，如 `requests`，或在 [let 子句](#let-clause)中定义的表；或是
  * 查询表达式，如 `(requests | where success=="True")`
  * 使用通配符指定的一组表。 例如，`e*` 形成一个联合，其中包括在之前的 let 子句（名称以“e”开头）中定义的所有表和“exceptions”表。
* `kind`： 
  * `inner` - 此结果包含所有输入表所共有列的子集。
  * `outer` - 此结果包含任何输入中出现的行。 未由输入行定义的单元格设置为 `null`。
* `withsource=`*ColumnName*：如果指定，输出将包含名为 *ColumnName* 的列，该列的值指示哪个源表提供了哪一行。 如果想要指定显示在此列中的名称，请在每个 table 表达式的末尾使用 [as](#as-operator)。

**返回**

一个表，行数与所有输入表中的行数相同，列数与输入中具有唯一列名称的列数相同。

无法保证行中的顺序。


**示例**

在过去 12 小时内生成了 `exceptions` 事件或 `traces` 事件的不同用户数。 在结果中，“SourceTable”列指示“exceptions”或“traces”：

```AIQL
    
    union withsource=SourceTable kind=outer exceptions, traces
    | where timestamp > ago(12h)
    | summarize dcount(user_Id) by SourceTable
```

此效率更高的版本可生成相同结果。 它可在创建联合之前筛选每个表：

```AIQL
    exceptions
    | where timestamp > ago(24h) | as exceptions
    | union withsource=SourceTable kind=outer (requests | where timestamp > ago(12h) | as traces)
    | summarize dcount(user_Id) by SourceTable 
```

使用 [as](#as-operator) 指定显示在源列中的名称。

#### <a name="forcing-an-order-of-results"></a>强制形成结果顺序

联合无法保证结果行中存在特定排序。
若要在每次运行查询后获取相同顺序，需将标记列附加到每个输入表：

    let r1 = (traces | count | extend tag = 'r1');
    let r2 = (requests | count| extend tag = 'r2');
    let r3 = (pageViews | count | extend tag = 'r3');
    r1 | union r2,r3 | sort by tag

#### <a name="see-also"></a>另请参阅

请考虑将 [join 运算符](#join-operator)作为替代。

### <a name="where-operator"></a>where 运算符
     requests | where resultCode=="200"

筛选表，获取满足谓词的行子集。

**别名** `filter`

**语法**

    T | where Predicate
    T | where * has Term

**参数**

* *T*：待筛选行的表格输入。
* *Predicate*：针对 *T* 的列的 `boolean` [表达式](#boolean)。对 *T* 中的每一行执行此计算。
* 术语 - 必须完全匹配列中的单词的字符串。

**返回**

对于 *T* 中的行，*Predicate* 为 `true`。

**提示**

获取最快性能：

* 在列名称和常量之间**采用简单比较**。 （“常量”指表中的常量 - `now()` 和 `ago()` 均可用，并且使用 [`let` 子句](#let-clause)分配标量值。）
  
    例如，使用 `where Timestamp >= ago(1d)` 而非 `where floor(Timestamp, 1d) == ago(1d)`。
* **简单项优先**：如果有多个子句与 `and` 联合，则将仅涉及一列的子句放在首位。 因此 `Timestamp > ago(1d) and OpId == EventId` 优于其他方法。

**示例**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

不超过 1 小时且来自“Kuskus”源的记录，包含具有相同值的两列。 

请注意，将两个列之间的比较放在最后，因为它不能利用索引和强制执行扫描。



## <a name="aggregations"></a>Aggregations
Aggregations 是用于合并在[汇总操作](#summarize-operator)中所创建组的值的函数。 例如，在此查询中，dcount() 是一个聚合函数：

    requests | summarize dcount(name) by success

### <a name="any"></a>任意
    any(Expression)

随机选择组的一行并返回指定表达式的值。

这非常有用，例如：某列含有大量相似值时（例如，“错误文本”列），需要根据复合组键的唯一值对该列执行一次采样。 

**示例**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>

### <a name="argmin-argmax"></a>argmin, argmax
    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

在最小化/最大化 *ExprToMaximize* 的组中查找行，并返回 *ExprToReturn* 的值（或使用 `*` 返回整个行）。

**提示**：传递列自动重命名。 若要确保使用的名称正确，请在将结果通过管道传递到其他运算符前，使用 `take 5` 检查结果。

**示例**

对于每个请求名称，显示最长请求出现的时间：

    requests | summarize argmax(duration, timestamp) by name

显示最长请求的所有详细信息，不只是时间戳：

    requests | summarize argmax(duration, *) by name


查找每个指标的最小值及其时间戳和其他数据：

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)

### <a name="avg"></a>平均值
    avg(Expression)

计算整组中 *Expression* 的平均值。

### <a name="buildschema"></a>buildschema
    buildschema(DynamicExpression)

返回允许 *DynamicExpression* 所有值的最小架构。 

参数列类型应为 `dynamic` - 数组或属性包。 

**示例**

    exceptions | summarize buildschema(details)

结果：

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

请注意，`indexer` 用来标记应使用数字索引的位置。 对于此架构，以下路径可能有效（假设这些示例索引在可用范围内）：

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**示例**

假定输入列具有三个动态值：

|  |
| --- |
| `{"x":1, "y":3.5}` |
| `{"x":"somevalue", "z":[1, 2, 3]}` |
| `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}` |

生成的架构可能为：

    {
      "x":["int", "string"],
      "y":["double", {"w": "string"}],
      "z":{"indexer": ["int", "string"]},
      "t":{"indexer": "string"}
    }

此架构表示：

* 根对象是具有四个属性（名为 x、y、z 和 t）的容器。
* “x”属性可能属于“int”类型或“string”类型。
* “y”属性可能属于“double”类型，或者另一个容器的“w”属性属于“string”类型。
* ``indexer`` 关键字指示“z”和“t”是数组。
* 数组“z”中的每个项属于 int 或 string。
* “t”是一个字符串数组。
* 每个属性均为隐式可选，并且任何数组均可能为空。

##### <a name="schema-model"></a>架构模型
返回架构的语法是：

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"indexer"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

它们等效于 TypeScript 类型批注的子集，作为动态值编码。 在 Typescript 中，示例架构应为：

    var someobject:
    {
      x?: (number | string),
      y?: (number | { w?: string}),
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string }
    }


### <a name="count"></a>count
    count([ Predicate ])

返回 *Predicate* 对其的计算结果为 `true` 的行数。 如果未指定 *Predicate*，则返回组中的记录总数。 

**性能提示**：使用 `summarize count(filter)` 而不使用 `where filter | summarize count()`

> [!NOTE]
> 避免使用 count() 查找请求数、异常数或已发生的其他事件数。 在[采样](app-insights-sampling.md)过程中，保留在 Application Insights 中的数据点数将少于原始事件数。 请改用 `summarize sum(itemCount)...`。 itemCount 属性反映每个保留数据点所表示的原始事件数。
> 
> 

### <a name="countif"></a>countif
    countif(Predicate)

返回 *Predicate* 对其的计算结果为 `true` 的行数。

**性能提示**：使用 `summarize countif(filter)` 而不使用 `where filter | summarize count()`

> [!NOTE]
> 避免使用 countif() 查找请求数、异常数或已发生的其他事件数。 在[采样](app-insights-sampling.md)过程中，数据点数将少于实际事件数。 请改用 `summarize sum(itemCount)...`。 itemCount 属性反映每个保留数据点所表示的原始事件数。
> 
> 

### <a name="dcount"></a>dcount
    dcount( Expression [ ,  Accuracy ])

返回对组中 *Expr* 的非重复值数的估计值。 （若要列出非重复值，请使用 [`makeset`](#makeset)。）

如果指定，*Accuracy* 可控制速度和准确性之间的平衡。

* `0` = 最低准确性和最快计算。
* `1` 为默认值，可平衡准确性和计算时间；错误率约 0.8%。
* `2` = 最高准确性和最慢计算；错误率约 0.4%。

**示例**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### <a name="dcountif"></a>dcountif
    dcountif( Expression, Predicate [ ,  Accuracy ])

返回对组中行的 *Expr* 非重复值数的估计值，此组的 *Predicate* 为 true。 （若要列出非重复值，请使用 [`makeset`](#makeset)。）

如果指定，*Accuracy* 可控制速度和准确性之间的平衡。

* `0` = 最低准确性和最快计算。
* `1` 为默认值，可平衡准确性和计算时间；错误率约 0.8%。
* `2` = 最高准确性和最慢计算；错误率约 0.4%。

**示例**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist
    makelist(Expr [ ,  MaxListSize ] )

返回组中 *Expr* 所有值的 `dynamic` (JSON) 数组。 

* *MaxListSize* 是对返回元素最大数目的可选整数限制（默认值是 *128*）。

### <a name="makeset"></a>makeset
    makeset(Expression [ , MaxSetSize ] )

返回 *Expr* 在组中所获取非重复值集的 `dynamic` (JSON) 数组。 （提示：若要只计算非重复值，请使用 [`dcount`](#dcount)。）

* *MaxSetSize* 是对返回元素最大数目的可选整数限制（默认值是 *128*）。

**示例**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

有关相对函数，另请参阅 [`mvexpand` 运算符](#mvexpand-operator)。

### <a name="max-min"></a>max, min
    max(Expr)

计算 *Expr* 的最大值。

    min(Expr)

计算 *Expr* 的最小值。

**提示**：这能提供该项本身的最大值或最小值 - 例如最高或最低价格。 但如果需要行中的其他列 - 例如，提供最低价格的供应商名称 - 则需使用 [argmin 或 argmax](#argmin-argmax)。

<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>

### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, percentiles, percentilew, percentilesw
    percentile(Expression, Percentile)

返回对组中指定百分位 *Expression* 的估计值。 准确性取决于百分位区域中的填充密度。

    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

与 `percentile()` 类似，但还可计算大量百分比值（比单独计算每个百分位更快）。

    percentilew(Expression, WeightExpression, Percentile)

加权百分位。 用于预聚合数据。  `WeightExpression` 是一个整数，指示每个聚合行表示的原始行数。

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

与 `percentilew()` 类似，但还可计算大量百分位值。

**示例**

对于每个请求名称，计算大于示例集 95% 和小于示例集 5% 的 `duration` 值：

    request 
    | summarize percentile(duration, 95)
      by name

省略“by...”可计算整个表。

同时计算不同请求名称的多个百分点值：

    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

结果显示，对于请求 /Events/Index，5% 请求在 2.44s 内响应，一半请求在 3.52s 内响应，5% 请求的响应超过 6.85s。

计算多个统计信息：

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>加权百分位数
在已预聚合数据的情况下，使用加权百分位函数。 

例如，假设应用每秒执行数千条操作，此时想知道操作的延迟情况。 简单的解决方案就是生成 Application Insights 请求或每个操作的自定义事件。 这将产生大量流量，尽管自适应采样可有效减少流量。 但还可以实施更好的解决方案：在应用中编写代码，并在将数据发送到 Application Insights 之前聚合数据。 系统将按固定间隔发送聚合汇总，因而可能将数据速率降低到每秒数点。

代码对延迟流进行测量（以毫秒为单位）。 例如：

     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

计算以下 bin 中的度量值：`{ 10, 20, 30, 40, 50, 100 }`

代码还将定期执行一系列 TrackEvent 调用，一个调用对应一个存储桶，并且每次调用均包含自定义度量值： 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Analytics 中将显示一个如下所示的事件组：

| `opCount` | `latency` | 含义 |
| --- | --- | --- |
| 8 |10 |= 10ms bin 中有 8 个操作 |
| 6 |20 |= 20ms bin 中有 6 个操作 |
| 3 |30 |= 30ms bin 中有 3 个操作 |
| 1 |40 |= 40ms bin 中有 1 个操作 |

若要获取事件延迟原始分布的准确反映，需使用 `percentilesw`：

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

结果与在原始测量值集中所用纯 `percentiles` 的结果相同。

> [!NOTE]
> 加权百分位数不适用于[采样数据](app-insights-sampling.md)，前者中每个样本行均表示原始行中的随机采样，而不是一个 bin。 纯百分位函数适用于采样数据。
> 
> 

#### <a name="estimation-error-in-percentiles"></a>百分位中的估算错误
聚合百分位使用 [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf) 提供近似值。 

重要事项： 

* 估算错误的界限因请求百分位的值而异。 最高准确性是 [0..100] 范围的两端，0 和 100 就是分布的最小和最大值。 越接近此范围的中间值，准确性越低。 中间值准确性最差，不超过 1%。 
* 错误边界通过级别观察而不是通过值。 假设 percentile(X, 50) 返回 Xm 的值。 此估算可保证 49%（最低）至 51%（最高）的 X 值小于 Xm。 对 Xm 和 X 的实际中间值之间的差异没有理论限制。

### <a name="stdev"></a>stdev
     stdev(Expr)

返回组中 *Expr* 的标准偏差。

### <a name="variance"></a>variance
    variance(Expr)

返回组中 *Expr* 的方差。

### <a name="sum"></a>sum
    sum(Expr)

返回组中 *Expr* 的总和。                      

## <a name="scalars"></a>标量
[转换](#casts) | [比较](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

支持的类型包括：

| 类型 | 其他名称 | 等效 .NET 类型 |
| --- | --- | --- |
| `bool` |`boolean` |`System.Boolean` |
| `datetime` |`date` |`System.DateTime` |
| `dynamic` | |`System.Object` |
| `guid` |`uuid`、`uniqueid` |`System.Guid` |
| `int` | |`System.Int32` |
| `long` | |`System.Int64` |
| `double` |`real` |`System.Double` |
| `string` | |`System.String` |
| `timespan` |`time` |`System.TimeSpan` |

### <a name="casts"></a>转换
可从一种类型转换为其他类型。 一般情况下，如果转换合理，则生效：

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

检查字符串是否可转换为特定类型：

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>标量比较
|  |  |
| --- | --- |
| `<` |小于 |
| `<=` |小于或等于 |
| `>` |大于 |
| `>=` |大于或等于 |
| `<>` |不等于 |
| `!=` |不等于 |
| `in` |右操作数是一个（动态）数组，且左操作数与右侧的一个元素相等。 |
| `!in` |右操作数是一个（动态）数组，且左操作数与右侧的任何元素均不相等。 |

### <a name="gettype"></a>gettype
**返回**

表示其单个参数的基础存储类型的字符串。 具有 `dynamic` 类型的值时，这非常有用：在这种情况下，`gettype()` 将显示值的编码方式。

**示例**

|  |  |
| --- | --- |
| `gettype("a")` |`"string" ` |
| `gettype(111)` |`"long" ` |
| `gettype(1==1)` |`"int8"` |
| `gettype(now())` |`"datetime" ` |
| `gettype(1s)` |`"timespan" ` |
| `gettype(parsejson('1'))` |`"int" ` |
| `gettype(parsejson(' "abc" '))` |`"string" ` |
| `gettype(parsejson(' {"abc":1} '))` |`"dictionary"` |
| `gettype(parsejson(' [1, 2, 3] '))` |`"array"` |
| `gettype(123.45)` |`"real" ` |
| `gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` |
| `gettype(parsejson(''))` |`"null"` |
| `gettype(1.2)==real` |`true` |

### <a name="hash"></a>hash
**语法**

    hash(source [, mod])

**参数**

* *source*：对其计算哈希的源标量。
* *mod*：要应用于哈希结果的取模值。

**返回**

给定标量的 xxhash（长）值，对给定 mod 值的取模（如果指定）。

**示例**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff
`iff()` 函数估算第一个参数 (predicate)，并根据 predicate是否为 `true` 或 `false` 来返回第二个或第三参数。 第二个和第三个参数必须属于同一类型。

**语法**

    iff(predicate, ifTrue, ifFalse)


**参数**

* *predicate*：计算结果为 `boolean` 值的表达式。
* *ifTrue*：如果 *predicate* 计算结果为 `true`，得以计算的表达式以及从函数返回的表达式值。
* *ifFalse*：如果 *predicate* 计算结果为 `false`，得以计算的表达式以及从函数返回的表达式值。

**返回**

如果 *predicate* 计算结果为 `true`，此函数返回 *ifTrue* 的值，否则返回 *ifFalse* 的值。

**示例**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>

### <a name="isnull-isnotnull-notnull"></a>isnull, isnotnull, notnull
    isnull(parsejson("")) == true

获取单个参数并指示其是否为 null。

**语法**

    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**返回**

true 或 false 具体取决于值是否为 null。

| x | isnull(x) |
| --- | --- |
| "" |false |
| "x" |false |
| parsejson("") |是 |
| parsejson("[]") |false |
| parsejson("{}") |false |

**示例**

    T | where isnotnull(PossiblyNull) | count

请注意，还可通过其他方法实现这种效果：

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar
计算查询或表达式并返回单个值形式的结果。 此函数适用于过渡计算；例如，计算事件总数，然后将此用作基准值。

**语法**

    toscalar(query)
    toscalar(scalar)

**返回**

计算参数。 如果参数是一个表，则返回第一行的第一列。 （较好的做法是将参数设置为仅包含一列和一行。）

**示例**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>布尔文本值
    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>布尔运算符
    and 
    or 

### <a name="convert-to-boolean"></a>转换为布尔值

如果具有的字符串 `aStringBoolean` 包含“true”或“false”值，则可将其转换为布尔值，如下所示：

    booleanResult = aStringBoolean =~ "true"



## <a name="numbers"></a>数字
[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>数值文字
|  |  |
| --- | --- |
| `42` |`long` |
| `42.0` |`real` |

### <a name="arithmetic-operators"></a>算术运算符
|  |  |
| --- | --- |
| + |添加 |
| - |减 |
| * |乘 |
| / |除 |
| % |取模 |
| `<` |小于 |
| `<=` |小于或等于 |
| `>` |大于 |
| `>=` |大于或等于 |
| `<>` |不等于 |
| `!=` |不等于 |

### <a name="abs"></a>abs
**语法**

    abs(x)

**参数**

* x - 整数、实数或时间跨度

**返回**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>

### <a name="bin-floor"></a>bin, floor
将值向下舍入到给定 bin 大小的整数倍。 常用于 [`summarize by`](#summarize-operator) 查询。 如果有一组离散值，它们将会分组成较小的特定值集。

别名 `floor`。

**语法**

     bin(value, roundTo)
     floor(value, roundTo)

**参数**

* *value*：数字、日期或时间跨度。 
* *roundTo*：“bin 大小”。 划分 *value* 的数字、日期或时间跨度。 

**返回**

最接近以下 *value* 的 *roundTo* 倍数。  

    (toint(value/roundTo)) * roundTo

**示例**

| 表达式 | 结果 |
| --- | --- |
| `bin(4.5, 1)` |`4.0` |
| `bin(time(16d), 7d)` |`14d` |
| `bin(datetime(1953-04-15 22:25:07), 1d)` |`datetime(1953-04-15)` |

以下表达式计算持续时间的直方图，其中存储桶大小为 1 秒：

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp
    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>floor
[`bin()`](#bin) 的别名。

### <a name="gamma"></a>gamma
[gamma 函数](https://en.wikipedia.org/wiki/Gamma_function)

**语法**

    gamma(x)

**参数**

* *x*：实数

对于正整数，则为 `gamma(x) == (x-1)!`，例如 `gamma(5) == 4 * 3 * 2 * 1`。

另请参阅 [loggamma](#loggamma)。

### <a name="log"></a>log
    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` 应为大于 0 的实数。 否则，将返回 null。

### <a name="loggamma"></a>loggamma
[gamma 函数](#gamma)绝对值的自然对数。

**语法**

    loggamma(x)

**参数**

* *x*：实数

### <a name="rand"></a>rand
随机数生成器。

* `rand()` - 介于 0.0 到 1.0 之间的实数
* `rand(n)` - 介于 0 到 n-1 之间的整数

### <a name="sqrt"></a>sqrt
平方根函数。  

**语法**

    sqrt(x)

**参数**

* *x*：大于等于 0 的实数。

**返回**

* 正数值，例如 `sqrt(x) * sqrt(x) == x`
* 如果参数为负或不能转换为 `real` 值，则为 `null`。 

### <a name="toint"></a>toint
    toint(100)        // cast from long
    toint(20.7) == 20 // nearest int below double
    toint(20.4) == 20 // nearest int below double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble
    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic


### <a name="tolong"></a>tolong
    tolong(20.7) == 20 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


## <a name="date-and-time"></a>日期和时间
[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>日期和时间文本
|  |  |
| --- | --- |
| **datetime** | |
| `datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")` |时间始终采用 UTC。 省略日期，提供当天时间。 |
| `now()` |当前时间。 |
| `now(`-*timespan*`)` |`now()-`*timespan* |
| `ago(`*timespan*`)` |`now()-`*timespan* |
| **timespan** | |
| `2d` |2 天 |
| `1.5h` |1.5 小时 |
| `30m` |30 分钟 |
| `10s` |10 秒 |
| `0.1s` |0.1 秒 |
| `100ms` |100 毫秒 |
| `10microsecond` | |
| `1tick` |100 纳秒 |
| `time("15 seconds")` | |
| `time("2")` |2 天 |
| `time("0.12:34:56.7")` |`0d+12h+34m+56.7s` |

### <a name="date-and-time-expressions"></a>日期和时间表达式
| 表达式 | 结果 |
| --- | --- |
| `datetime("2015-01-02") - datetime("2015-01-01")` |`1d` |
| `datetime("2015-01-01") + 1d` |`datetime("2015-01-02")` |
| `datetime("2015-01-01") - 1d` |`datetime("2014-12-31")` |
| `2h * 24` |`2d` |
| `2d` / `2h` |`24` |
| `datetime("2015-04-15T22:33") % 1d` |`timespan("22:33")` |
| `bin(datetime("2015-04-15T22:33"), 1d)` |`datetime("2015-04-15T00:00")` |
|  | |
| `<` |小于 |
| `<=` |小于或等于 |
| `>` |大于 |
| `>=` |大于或等于 |
| `<>` |不等于 |
| `!=` |不等于 |

### <a name="ago"></a>ago
从当前 UTC 时钟时间减去给定时间跨度。 与 `now()` 类似，此函数可在语句中多次使用，并且所有实例化引用的 UTC 时钟时间均相同。

**语法**

    ago(a_timespan)

**参数**

* *a_timespan*：待从当前 UTC 时钟时间 (`now()`) 减去的间隔。

**返回**

    now() - a_timespan

**示例**

过去一小时内具有时间戳的所有行：

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart
    datepart("Day", datetime(2015-12-14)) == 14

提取日期的指定部分用作整数。

**语法**

    datepart(part, datetime)

**参数**

* `part:String` - {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**返回**

表示指定部分的较长值。

### <a name="dayofmonth"></a>dayofmonth
    dayofmonth(datetime("2016-05-15")) == 15 

当天在当月中的序号。

**语法**

    dayofmonth(a_date)

**参数**

* `a_date`：`datetime`。

### <a name="dayofweek"></a>dayofweek
    dayofweek(datetime("2015-12-14")) == 1d  // Monday

自上个星期日以来的整数天数，用作 `timespan`。

**语法**

    dayofweek(a_date)

**参数**

* `a_date`：`datetime`。

**返回**

`timespan`从上个星期日的午夜开始，向下舍入为整数天数。

**示例**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear
    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

当天在当年中的序号。

**语法**

    dayofyear(a_date)

**参数**

* `a_date`：`datetime`。

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>

### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear
    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth
从日期时间获取月份号 (1-12)。

**示例**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear
从日期时间获取年份。

**示例**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>now
    now()
    now(-2d)

当前 UTC 时钟时间，偏移量取决于时间跨度（可选）。 此函数可在语句中多次使用，并且所有实例引用的时钟时间均相同。

**语法**

    now([offset])

**参数**

* *offset*：`timespan`，已添加到当前 UTC 时钟时间。 默认值：0。

**返回**

当前 UTC 时钟时间，用作 `datetime`。

    now() + offset

**示例**

确定 predicate 标识事件后的时间间隔：

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>

### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear
    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime
别名 `datetime()`。

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

检查字符串是否为有效日期：

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan
别名 `timespan()`。

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear
    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

整数结果表示采用 ISO 8601 标准的周数。 每周的第一天是星期日，每年的第一周是包含该年第一个星期四的周。 （因此，一年的最后几天可能包含下一年第 1 周内的几天，或者新一年的前几天可能包含上一年第 52 周或第 53 周内的几天。）

## <a name="string"></a>String
[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)

### <a name="string-literals"></a>字符串文本
规则与 JavaScript 中的规则相同。

可能用单引号或双引号字符括住字符串。 

反斜杠 (`\`) 用于转义 `\t` (tab)、`\n`（换行符）等字符和用引号字符括住的实例。

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>经过模糊处理的字符串文本
经过模糊处理的字符串文本是 Analytics 将在输出字符串（如跟踪）时进行模糊处理的字符串。 模糊处理过程用起始字符 (`*`) 替换所有经过模糊处理的字符。

若要形成经过模糊处理的字符串文本，请预置 `h` 或“H”。 例如：

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>字符串比较
| 运算符 | 说明 | 区分大小写 | 实际示例 |
| --- | --- | --- | --- |
| `==` |等于 |是 |`"aBc" == "aBc"` |
| `<>` `!=` |不等于 |是 |`"abc" <> "ABC"` |
| `=~` |等于 |否 |`"abc" =~ "ABC"` <br/>`boolAsString =~ "true"` |
| `!~` |不等于 |否 |`"aBc" !~ "xyz"` |
| `has` |右侧 (RHS) 是左侧 (LHS) 的整体 |否 |`"North America" has "america"` |
| `!has` |RHS 不是 LHS 的完整用语 |否 |`"North America" !has "amer"` |
| `hasprefix` |RHS 是 LHS 中某一字词的前缀 |否 |`"North America" hasprefix "ame"` |
| `!hasprefix` |RHS 不是 LHS 中任何字词的前缀 |否 |`"North America" !hasprefix "mer"` |
| `hassuffix` |RHS 是 LHS 中某一字词的后缀 |否 |`"North America" hassuffix "rth"` |
| `!hassuffix` |RHS 不是 LHS 中任何字词的后缀 |否 |`"North America" !hassuffix "mer"` |
| `contains` |RHS 作为 LHS 的子字符串 |否 |`"FabriKam" contains "BRik"` |
| `!contains` |RHS 不在 LHS 中 |否 |`"Fabrikam" !contains "xyz"` |
| `containscs` |RHS 作为 LHS 的子字符串 |是 |`"FabriKam" contains "Kam"` |
| `!containscs` |RHS 不在 LHS 中 |是 |`"Fabrikam" !contains "Kam"` |
| `startswith` |RHS 是 LHS 初始子字符串。 |否 |`"Fabrikam" startswith "fab"` |
| `!startswith` |RHS 不是 LHS 初始子字符串。 |否 |`"Fabrikam" !startswith "abr"` |
| `endswith` |RHS 是 LHS 终端子字符串。 |否 |`"Fabrikam" endswith "kam"` |
| `!endswith` |RHS 不是 LHS 终端子字符串。 |否 |`"Fabrikam" !endswith "ka"` |
| `matches regex` |LHS 包含 RHS 的匹配项 |是 |`"Fabrikam" matches regex "b.*k"` |
| [`in`](#in) |等于任一元素 |是 |`"abc" in ("123", "345", "abc")` |
| [`!in`](#in) |不等于任何元素 |是 |`"bc" !in ("123", "345", "abc")` |

如果正在测试是否存在整体用语，即符号或字母数字式字词（受非字母数字字符或开始或结束字段限制），则使用 `has` 或 `in`。 `has` 执行速度比 `contains`、`startswith` 或 `endswith` 更快。 这些查询的第一个查询运行速度更快：

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof
    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

计算字符串中子字符串的出现次数。 纯字符串匹配项可能重叠；正则表达式匹配项不会重叠。

**语法**

    countof(text, search [, kind])

**参数**

* *text*：字符串。
* *search*：将在 *text* 内部匹配的纯字符串或正则表达式。
* *kind*：`"normal"|"regex"` 默认为 `normal`。 

**返回**

搜索字符串可在容器中匹配的次数。 纯字符串匹配项可能重叠；正则表达式匹配项不会重叠。

**示例**

|  |  |
| --- | --- |
| `countof("aaa", "a")` |3 |
| `countof("aaaa", "aa")` |3 (不是 2!) |
| `countof("ababa", "ab", "normal")` |2 |
| `countof("ababa", "aba")` |2 |
| `countof("ababa", "aba", "regex")` |1 |
| `countof("abcabc", "a.c", "regex")` |2 |

### <a name="extract"></a>extract
    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

从文本字符串中获取[正则表达式](#regular-expressions)的匹配项。 （可选）然后它可将提取的子字符串转换为指明的类型。

**语法**

    extract(regex, captureGroup, text [, typeLiteral])

**参数**

* *regex*：[正则表达式](#regular-expressions)。
* *captureGroup*：指示待提取的捕获组的正 `int` 常数。 0 代表整个匹配项，1 代表正则表达式中第一个“(括号)”匹配的值，2 及以上数字代表后续括号。
* *text*：待搜索的 `string`。
* *typeLiteral*：可选类型文本（例如，`typeof(long)`）。 （如果支持）提取的子字符串将转换成此类型。 

**返回**

如果 *regex* 在 *text* 中查找匹配项：与指定捕获组 *captureGroup* 匹配的子字符串可转换为 *typeLiteral*（可选）。

如果没有匹配项，或类型转换失败：`null`。 

**示例**

示例字符串 `Trace` 用于搜索 `Duration` 的定义。 匹配项转换为 `real`，然后乘以时间常量 (`1s`)，以便 `Duration` 属于 `timespan` 类型。 在此示例中，此值等于 123.45 秒：

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

此示例等效于 `substring(Text, 2, 4)`：

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>



### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty
    isempty("") == true

如果参数为空字符串，则为 true，否则为 null。
另请参阅 [isnull](#isnull)。

**语法**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**返回**

指示参数是空字符串还是 isnull。

| x | isempty(x) |
| --- | --- |
| "" |是 |
| "x" |false |
| parsejson("") |是 |
| parsejson("[]") |false |
| parsejson("{}") |false |

**示例**

    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl
将 URL 拆分成几部分。

**语法**

    parseurl(urlstring)

**参数**

* *urlstring*：URL。

**返回**

包含字符串部分的对象。

**示例**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>replace
将所有正则表达式匹配项替换为其他字符串。

**语法**

    replace(regex, rewrite, text)

**参数**

* *regex*：搜索 *text* 的[正则表达式](https://github.com/google/re2/wiki/Syntax)。 可在“(括号)”中包含捕获组。 
* *rewrite*：由 *matchingRegex* 匹配的任何匹配项的替换正则表达式。 使用 `\0` 引用整个匹配项，`\1` 用于第一个捕获组，`\2` 等用于后续捕获组。
* *text*：字符串。

**返回**

使用 *rewrite* 计算结果替换 *regex* 的所有匹配项后的 *text*。 匹配项不重叠。

**示例**

此语句：

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

具有以下结果：

| x | str | 替换的内容 |
| --- | --- | --- |
| 1 |数值为 1.000000 |数值曾为：1.000000 |
| 2 |数值为 2.000000 |数值曾为：2.000000 |
| 3 |数值为 3.000000 |数值曾为：3.000000 |
| 4 |数值为 4.000000 |数值曾为：4.000000 |
| 5 |数值为 5.000000 |数值曾为：5.000000 |

### <a name="split"></a>split
    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

根据给定分隔符拆分给定字符串，并返回包含 conatined 子字符串的字符串数组。 （可选）可以返回特定子字符串（如果存在）。

**语法**

    split(source, delimiter [, requestedIndex])

**参数**

* *source*：根据给定分隔符拆分的源字符串。
* *delimiter*：用于拆分源字符串的分隔符。
* *requestedIndex*：可选的从零开始的索引 `int`。 （如果支持）返回的字符串数组将包含请求的子字符串（如果存在）。 

**返回**

包含给定源字符串的子字符串的字符串数组，其中的源字符串使用给定分隔符进行分隔。

**示例**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat
    strcat("hello", " ", "world")

1-16 个参数之间的连接，必须是字符串。

### <a name="strlen"></a>strlen
    strlen("hello") == 5

字符串的长度。

### <a name="substring"></a>substring
    substring("abcdefg", 1, 2) == "bc"

自给定索引开始提取给定源字符串中的子字符串。 （可选）可以指定请求子字符串的长度。

**语法**

    substring(source, startingIndex [, length])

**参数**

* *source*：从中提取子字符串的源字符串。
* *startingIndex*：请求子字符串的从零开始的起始字符位置。
* *length*：可用于在子字符串中指定请求字符数的可选参数。 

**返回**

给定字符串中的子字符串。 子字符串在 startingIndex（从零开始）字符位置开始，并持续到字符串或长度字符的末尾（如果指定）。

**示例**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower
    tolower("HELLO") == "hello"

将字符串转换为小写。

### <a name="toupper"></a>toupper
    toupper("hello") == "HELLO"

将字符串转换为大写。

### <a name="guids"></a>GUID
    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>数组、对象和动态
[文本](#dynamic-literals) | [转换](#casting-dynamic-objects) | [运算符](#operators) | [let 子句](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)

下面是对 Application Insights 异常进行查询的结果。 `details` 中的值是数组。

![](./media/app-insights-analytics-reference/310.png)

**索引：**与 JavaScript 中的索引数组和对象相同：

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* 但请使用 `arraylength` 和其他 Analytics 函数（不是“.length”！）

**转换** 某些情况需要转换从对象中提取的元素，因为其类型可能有所不同。 例如，`summarize...to` 需要特定类型：

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions
    | summarize count()
      by tostring(details[0].parsedStack[0].assembly)

**文本** 若要创建显式数组或属性包对象，需将其编写为 JSON 字符串并转换：

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand：**若要将对象的属性分成单独行，需使用 mvexpand：

    exceptions | take 1
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)

**treepath：**查找复杂对象中的所有路径：

    exceptions | take 1 | project timestamp, details
    | extend path = treepath(details)
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema：**用于查找允许表中表达式的所有值的最小架构：

    exceptions | summarize buildschema(details)

结果：

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer":
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

请注意，`indexer` 用来标记应使用数字索引的位置。 对于此架构，以下路径可能有效（假设这些示例索引在可用范围内）：

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>数组和对象文本
若要创建动态文本，请结合使用 `parsejson`（别名 `todynamic`）和 JSON 字符串参数：

* `parsejson('[43, 21, 65]')` - 数字数组
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` - 包含数字的动态类型的单个值
* `parsejson('"21"')` - 包含字符串的动态类型的单个值

请注意，与 JavaScript 不同，JSON 要求使用双引号 (`"`) 括住字符串。 因此，使用单引号 (`'`) 括住 JSON 编码的字符串文本通常更简单。

此示例创建一个动态值，然后使用其字段：

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>动态对象函数
|  |  |
| --- | --- |
| [*value* `in` *array*](#in) |*array* 包含 *value* |
| [*value* `!in` *array*](#in) |*array* 不包含 *value* |
| [`arraylength(`array`)`](#arraylength) |如果不是数组，则为 null |
| [`extractjson(`path,object`)`](#extractjson) |使用路径导航到对象。 |
| [`parsejson(`source`)`](#parsejson) |将 JSON 字符串转换为动态对象。 |
| [`range(`from,to,step`)`](#range) |值组成的数组 |
| [`mvexpand` listColumn](#mvexpand-operator) |为指定单元格列表中的每个值复制一行。 |
| [`summarize buildschema(`column`)`](#buildschema) |根据列内容推断类型架构 |
| [`summarize makelist(`column`)` ](#makelist) |平展行组并将列的值放入数组。 |
| [`summarize makeset(`column`)`](#makeset) |平展行组并将列的值放入数组，不进行复制。 |

### <a name="dynamic-objects-in-let-clauses"></a>let 子句中的动态对象
[let 子句](#let-clause) 将动态值作为字符串存储，因此这两个子句等效并且两者都需要 `parsejson` (或 `todynamic`) 才能使用：

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a


### <a name="in"></a>位于
    value in (listExpression)
    value !in (listExpression)

确定列表中是否存在等于该值的项。 区分大小写，其中值为字符串。

**参数**

* `value`：标量表达式。
* `listExpression`：标量表达式列表，或计算结果为列表的表达式。 

例如，将嵌套数组平展为单个列表（例如 `where x in (dynamic([1,[2,3]]))` 变为 `where x in (1,2,3)`）。  

**示例**

```AIQL
    requests | where client_City in ("London", "Paris", "Rome")
```

```AIQL
let cities = dynamic(['Dublin','Redmond','Amsterdam']);
requests | where client_City in (cities) 
|  summarize count() by client_City
```

计算列表：

```AIQL
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

将函数调用用作列表表达式：

```AIQL
let topCities =  (n:int) {toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City)) };
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```
 

### <a name="arraylength"></a>arraylength
动态数组中的元素数。

**语法**

    arraylength(array)

**参数**

* *array*：`dynamic` 值。

**返回**

*array* 中的元素数；如果 *array* 不是数组，则返回`null`。

**示例**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson
    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

使用路径表达式获取 JSON 文本外指定的元素。 （可选）将提取的字符串转换为特定类型。

**语法**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**返回**

此函数对 dataSource（内含有效的 JSON 字符串）执行 JsonPath 查询，从而可选择根据第三个参数将该值转换为其他类型。

**示例**

[方括号] 表示法和点表示法等效：

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**性能提示**

* 使用 `extractjson()` 之前应用 where 子句
* 请考虑改用与 [extract](#extract) 匹配的正则表达式匹配项。 如果从模板生成 JSON，运行速度则更快并且高效。
* 如果需要从 JSON 提取多个值，请使用 `parsejson()`。
* 考虑将列类型声明为动态，以便在引入时分析 JSON。

### <a name="json-path-expressions"></a>JSON 路径表达式
|  |  |
| --- | --- |
| `$` |根对象 |
| `@` |当前对象 |
| `[0]` |数组下标 |
| `.` 或 `[0]` |子 |

（目前未实现通配符、递归、联合或切片。）

### <a name="parsejson"></a>parsejson
将 `string` 解释为 [JSON 值](http://json.org/)) 并返回用作 `dynamic` 的值。 需要提取 JSON 复合对象的多个元素时，使用它比使用 `extractjson()` 更好。

**语法**

    parsejson(json)

**参数**

* *json*：JSON 文档。

**返回**

*json* 指定的 `dynamic` 类型对象。

**示例**

在以下示例中，如果 `context_custom_metrics` 是类似如下的 `string`： 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

则以下片段将检索对象中 `duration` 槽的值，并从中检索两个槽，`duration.value` 和  `duration.min`（分别为 `118.0` 和 `110.0`）。

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>range
`range()` 函数（切勿与 `range` 运算符混淆）生成包含一系列均等间距分布值的动态数组。

**语法**

    range(start, stop, step)

**参数**

* *start*：生成数组中第一个元素的值。 
* *stop*：生成数组中最后一个元素的值，或大于生成数组中的最后一个元素且在 *start* 中 *step* 的整数倍以内的最小值。
* *step*：数组中两个连续元素之间的差异。

**示例**

以下示例返回 `[1, 4, 7]`：

```AIQL
range(1, 8, 3)
```

以下示例返回包含 2015 年所有天数的数组：

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic
    todynamic('{"a":"a1", "b":["b1", "b2"]}')

将字符串转换为动态值。

### <a name="treepath"></a>treepath
    treepath(dynamic_object)

枚举可标识动态对象中所有遗留内容的路径表达式。 

**返回**

路径表达式的数组。

**示例**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

请注意“[0]”指示存在数组，但不指定特定路径使用的索引。

### <a name="zip"></a>zip
    zip(list1, list2, ...)

将一组列表合并成一个元组列表。

* `list1...`：值列表

**示例**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]


    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>名称
名称长度最多为 1024 个字符。 名称区分大小写，可包含字母、数字和下划线 (`_`)。 

使用 [' ... '] 或 [" ... "] 括住名称，以包括其他字符或将关键字用作名称。 例如：

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|  |  |
| --- | --- |
| ['path\\file\n\'x\''] |使用 \ 转义字符 |
| ["d-e.=/f#\n"] | |
| [@'path\file'] |没有文本转义符 - \ |
| [@"\now & then\"] | |
| [where] |使用语言关键字作为名称 |

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


