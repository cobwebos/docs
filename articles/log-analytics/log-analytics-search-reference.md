---
title: "Azure Log Analytics 搜索参考 | Microsoft 文档"
description: "Log Analytics 搜索参考介绍搜索语言并提供常规查询语法选项，可用于搜索数据和筛选表达式，以帮助缩小搜索范围。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc9c9b0a6292dab256997a86a6db16367fc48cd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-search-reference"></a>Log Analytics 搜索参考

>[!NOTE]
> 本文介绍 Log Analytics 中使用当前查询语言的日志搜索。  如果工作区已升级到[新的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则应参考[新语言的语言参考](https://go.microsoft.com/fwlink/?linkid=856079)。

以下有关搜索语言的参考部分介绍常规查询语法选项，可用于搜索数据和筛选表达式，以帮助缩小搜索范围。 另外还介绍了可用于对检索到的数据执行操作的命令。

在[搜索字段和 Facet 参考部分](#search-field-and-facet-reference)，可以了解在搜索中返回的字段，以及可以帮助深入了解类似数据类别的 Facet。

## <a name="general-query-syntax"></a>常规查询语法
常规查询的语法如下：

```
filterExpression | command1 | command2 …
```

筛选器表达式 (`filterExpression`) 定义查询的“where”条件。 命令应用于查询所返回的结果。 多个命令的情况下必须使用管道字符 ( | ) 进行分隔。

### <a name="general-syntax-examples"></a>常规语法示例
示例:

```
system
```

此查询将返回针对全文或术语搜索编制索引的任何字段中包含单词 *system* 的结果。

> [!NOTE]
> 并非所有字段都以这种方式编制索引，但通常最常见的文本字段（如说明和名称）会使用这种方式。
>
>

```
system error
```

此查询将返回包含单词 *system* 和 *error* 的结果。

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

此查询将返回包含单词 *system* 和 *error* 的结果。 然后，依次按照 ManagementGroupName 字段（升序）和 TimeGenerated 字段（降序）对结果进行排序。 并且，此查询仅获取前 10 个结果。

> [!IMPORTANT]
> 字符串和文本字段的所有字段名称和值都区分大小写。
>
>

## <a name="filter-expressions"></a>筛选表达式
以下小节说明筛选器表达式。

### <a name="string-literals"></a>字符串文本
字符串文本是分析器不识别为关键字或预定义的数据类型（例如，数字或日期）的任何字符串。

示例:

```
These all are string literals
```

此查询将搜索包含出现所有这五个单词的结果。 要执行复杂的字符串搜索，请将字符串文本用引号引起来。 例如：

```
"Windows Server"
```

这会仅返回包含 *Windows Server* 的完全匹配项的结果。

### <a name="numbers"></a>数字
对于数字字段，分析器支持十进制整数和浮点数语法。

示例:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>日期和时间
系统中的每个数据段都具有 *TimeGenerated* 属性，用于表示记录的原始日期和时间。 某些类型的数据可能包含更多日期和时间字段（例如 *LastModified*）。

Log Analytics 中的时间线**图表/时间**选择器显示一段时间内的结果分布（根据正在运行的当前查询）。 此结果基于 *TimeGenerated* 字段。 日期和时间字段具有特定的字符串格式，可在查询中用于将查询限制为特定的时间范围。 也可以使用语法来指代相对时间间隔（例如，“between 3 days ago and 2 hours ago”）。

下面是日期和时间语法的有效形式：

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


例如：

```
TimeGenerated:2013-10-01T12:20
```

前一个命令只返回 *TimeGenerated* 的值正好为“12:20 on October 1, 2013”的记录。

现在，分析器还支持助记日期/时间值。 （这可能也不会产生任何结果，因为数据不能如此快的通过系统传递。）

这些示例都是用于相对和绝对日期的基本构件。 后续三个部分以使用相对日期范围为例讲解如何在更高级的筛选器中使用这些字段。

### <a name="datetime-math"></a>日期/时间数学运算符
通过简单的日期/时间计算，使用日期/时间数学运算符来偏移或取整日期/时间值。

语法：

```
datetime/unit
```

```
datetime[+|-]count unit
```

| 运算符 | 说明 |
| --- | --- |
| / |将日期/时间取整为指定的单位。 例如，NOW/DAY 将当前日期/时间取整为当天的午夜。 |
| + 或 - |按指定的单位数偏移日期/时间。 例如，NOW+1HOUR 将当前日期/时间向前偏移一小时。 2013-10-01T12:00-10DAYS 将日期值向后偏移 10 天。 |

可以将日期/时间数学运算符链接在一起。 例如：

```
NOW+1HOUR-10MONTHS/MINUTE
```

下表列出了受支持的日期/时间单位。

| 日期/时间单位 | 说明 |
| --- | --- |
| YEAR、YEARS |取整为当前年份，或者按指定的年数偏移。 |
| MONTH、MONTHS |取整为当前月份，或者按指定的月数偏移。 |
| DAY、DAYS、DATE |取整为该月的当前日期，或者按指定的天数偏移。 |
| HOUR、HOURS |取整为当前小时，或者按指定的小时数偏移。 |
| MINUTE、MINUTES |取整为当前分钟，或者按指定的分钟数偏移。 |
| SECOND、SECONDS |取整为当前秒，或者按指定的秒数偏移。 |
| MILLISECOND、MILLISECONDS、MILLI、MILLIS |取整为当前毫秒，或者按指定的毫秒数偏移。 |

### <a name="field-facets"></a>字段分面
使用字段分面可以指定特定字段的搜索条件及其确切值。 这不同于为整个索引中的各个字词编写“自由文本”查询。 前面的几个示例中已使用此方法。 下面是更复杂的示例。

**语法**

```
field:value
```

```
field=value
```

**说明**

搜索特定值的字段。 值可以是字符串文本、数字或者日期和时间。

例如：

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**语法**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**说明**

提供比较。

例如：

```
TimeGenerated>NOW+2HOURS
```

**语法**

```
field:[from..to]
```

**说明**

提供范围方面。

例如：

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>IN
使用 **IN** 关键字可从值列表中选择。 根据所使用的语法，这可以是所提供的值的简单列表或聚合值的列表。

语法 1：

```
field IN {value1,value2,value3,...}
```

使用此语法可包括简单列表中的所有值。



示例:

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

语法 2：

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

使用此语法可以创建聚合。 然后，可将该聚合中的值列表提供给另一个用于查找包含这些值的事件的外部（主）搜索。 可以通过将内部搜索括在大括号内，并提供其结果作为使用 IN 运算符的外部搜索中字段的可能值来执行此操作。

内部查询示例：使用以下聚合查询查找*当前未安装安全更新程序的计算机*：

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

最后一个查询查找*当前未安装安全更新程序的计算机的所有 Windows 事件*，如下所示：

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contains
使用 **Contains** 关键字可以筛选具有包含指定字符串的字段的记录。 这种搜索区分大小写，仅适用于字符串字段，不能包含任何转义字符。

语法：

```
field:contains("string")
```

示例：

```
Type:contains("Event")
```

此查询返回类型中包含字符串“Event”的记录。 示例包括 **Event**、**SecurityEvent** 和 **ServiceFabricOperationEvent**。



### <a name="regular-expressions"></a>正则表达式
可以使用 **Regex** 关键字针对具有正则表达式的字段指定搜索条件。 有关可在正则表达式中使用的语法的完整说明，请参阅[使用正则表达式筛选 Log Analytics 中的日志搜索](log-analytics-log-searches-regex.md)。

语法：

```
field:Regex("Regular Expression")
```

示例：

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>逻辑运算符
查询语言支持逻辑运算符（*AND*、*OR* 和 *NOT*）及其各自的 C 样式别名（*&&*、*||* 和 *!*）。 可以使用括号来组合这些运算符。

示例:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

可以省略顶级筛选器参数的逻辑运算符。 在这种情况下，则会采用 AND 运算符。

| 筛选器表达式 | 等效于 |
| --- | --- |
| system error |system AND error |
| system "Windows Server" OR Severity:1 |system AND ("Windows Server" OR Severity:1) |

### <a name="wildcarding"></a>通配符
查询语言支持使用 (\*) 字符来表示查询中某个值的一个或多个字符。

示例：

 查找名称中含有“SQL”的所有计算机，例如“Redmond-SQL”。

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> 目前，不能在引号中使用通配符。 例如，消息 `"*This text*"` 认为 (\*) 用作文本 (\*) 字符。


## <a name="commands"></a>命令


命令应用于查询所返回的结果。 使用管道字符 ( | ) 将命令应用于检索到的结果。 多个命令的情况下必须使用管道字符进行分隔。

> [!NOTE]
> 与字段名称和数据不同，命令名称可以用大写或小写。
>
>

### <a name="sort"></a>排序
语法：

    sort field1 asc|desc, field2 asc|desc, …

按特定字段对结果进行排序。 用于按升序或降序对结果排序的 asc/desc 后缀是可选的。 如果省略后缀，则采用 *asc* 排列顺序。 对于 **TimeGenerated** 字段，则会采用 *desc* 排序顺序，因此默认首先返回最新结果。

### <a name="toplimit"></a>Top/Limit
语法：

    top number


    limit number
限制对前 N 个结果的响应。

示例：

    Type:Alert errors detected | top 10

返回前 10 个匹配结果。

### <a name="skip"></a>Skip
语法：

    skip number

跳过列出的结果数。

示例：

    Type:Alert errors detected | top 10 | skip 200

返回从第 200 个结果开始的前 10 个匹配结果。

### <a name="select"></a>选择
语法：

    select field1, field2, ...

限制所选字段的结果。

示例：

    Type:Alert errors detected | select Name, Severity

限制返回的结果字段为*名称*和*严重性*。

### <a name="measure"></a>度量
*measure* 命令用于将统计函数应用于原始搜索结果。 这非常适合用于获取数据的*分组依据*视图。 使用 measure 命令时，Log Analytics 搜索显示包含聚合结果的表。

**语法：**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



按 *groupField* 聚合结果，并使用 *aggregatedField* 计算聚合的度量值。

| 度量统计函数 | 说明 |
| --- | --- |
| *aggregateFunction* |聚合函数的名称（不区分大小写）。 支持以下聚合函数：COUNT、MAX、MIN、SUM、AVG、STDDEV、COUNTDISTINCT、PERCENTILE## 或 PCT##（## 是介于 1 和 99 之间的任意数字）。 |
| *aggregatedField* |待聚合的字段。 此字段对于 COUNT 聚合函数是可选的，但必须是现有数字字段 SUM、MAX、MIN、AVG STDDEV、PERCENTILE## 或 PCT##（## 是介于 1 和 99 之间的任意数字）。 aggregatedField 可以是 **Extend** 支持的任何函数。 |
| *fieldAlias* |用于已计算的聚合值（可选）别名。 如果未指定，字段名为 **AggregatedValue**。 |
| *groupField* |结果集分组所依据的字段名称。 |
| *间隔* |时间间隔，格式为：**nnnNAME**。 nnn 是正整数。 **NAME** 是间隔名称。 支持的间隔名称区分大小写，包括：MILLISECOND[S]、SECOND[S]、MINUTE[S]、HOUR[S]、DAY[S]、MONTH[S] 和 YEAR[S] |

间隔选项仅可在日期/时间组字段（如 *TimeGenerated* 和 *TimeCreated*）中使用。 目前，该服务不强制这一点，但是将没有日期/时间的字段传递给后端会导致运行时错误。 实现架构验证时，服务 API 拒绝将没有日期/时间的字段用于间隔聚合的查询。 当前的*度量*实现支持间隔分组用于任何聚合函数。

如果省略了 BY 子句，但指定了间隔（作为另一个语法），则默认采用 *TimeGenerated* 字段。

示例:

**示例 1**

    Type:Alert | measure count() as Count by ObjectId

按 *ObjectID* 对警报进行分组，并计算每组警报的数目。 聚合值以 *Count* 字段（别名）的形式返回。

**示例 2**

    Type:Alert | measure count() interval 1HOUR

使用 *TimeGenerated* 字段按 1 小时间隔对警报进行分组，并返回每个间隔中警报的数目。

**示例 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

与上面的示例相同，但包含聚合字段别名 (*AlertsPerHour*)。

**示例 4**

    * | measure count() by TimeCreated interval 5DAYS

使用 *TimeGenerated* 字段按 5 天间隔对结果进行分组，并返回每个间隔中的结果数。

**示例 5**

    Type:Alert | measure max(Severity) by WorkflowName

按工作负荷名称对警报进行分组，并返回每个工作流的警报严重性最大值。

**示例 6**

    Type:Alert | measure min(Severity) by WorkflowName

与上面的示例相同，但包含 *min* 聚合函数。

**示例 7**

    Type:Perf | measure avg(CounterValue) by Computer

按计算机对性能进行分组，并计算平均值 (avg)。

**示例 8**

    Type:Perf | measure sum(CounterValue) by Computer

与上面的示例相同，但使用 *sum*。

**示例 9**

    Type:Perf | measure stddev(CounterValue) by Computer

与上面的示例相同，但使用 *stddev*。

**示例 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

与上面的示例相同，但使用 *percentile70*。

**示例 11**

    Type:Perf | measure pct70(CounterValue) by Computer

与上面的示例相同，但使用 *pct70*。 请注意，*PCT##* 只是 *PERCENTILE##* 函数的别名。

**示例 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

依次按 Computer 和 CounterName 对性能进行分组，并计算平均值。

**示例 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

获取具有最大警报数目的前五个工作流。

**示例 14**

    * | measure countdistinct(Computer) by Type

统计每种类型唯一的计算机报告的数量。

**示例 15**

    * | measure countdistinct(Computer) Interval 1HOUR

统计每小时唯一的计算机报告的数量。

**示例 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

按计算机对处理器时间百分比进行分组，并返回每隔一小时的平均值。

**示例 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

按方法对 W3CIISLog 进行分组，并返回每隔 5 分钟的最大值。

**示例 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

按计算机对处理器时间百分比进行分组，并返回每一个小时的最小值、平均值、第 75 百分位和最大值。

**示例 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

依次按计算机和实例名称对处理器时间百分比进行分组，并返回每一个小时的最小值、平均值、第 75 百分位和最大值。

**示例 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

计算计算机上每个磁盘每分钟的磁盘写入最大次数。

### <a name="where"></a>其中
语法：

```
**where** AggregatedValue>20
```

只能在 *Measure* 命令后使用，以进一步筛选 *Measure* 聚合函数已生成的聚合结果。

示例:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>Dedup
语法：

    Dedup FieldName

返回针对给定字段的每个唯一值找到的第一个文档。

示例：

    Type=Event | Dedup EventID | sort TimeGenerated DESC

此示例针对每个 EventID 返回一个事件（最新的事件）。

### <a name="join"></a>Join
联接两个查询的结果，组成一个结果集。  支持下表所述的多种联接类型。

| 联接类型 | 说明 |
|:--|:--|
| 内部 | 只返回两个查询中值相匹配的记录。 |
| 外部 | 返回两个查询中的所有记录。  |
| 左侧  | 返回左侧查询中的所有记录，返回右侧查询中匹配的记录。 |


- 联接当前不支持包含 **IN** 关键字、**Measure** 命令或 **Extend** 命令的查询（如果它针对右侧查询中的字段）。
- 当前，一个联接中只能包含单个字段。
- 单个搜索不能包含多个联接。

**语法**

```
<left-query> | JOIN <join-type> <left-query-field-name> (<right-query>) <right-query-field-name>
```

**示例**

为说明不同的联接类型，请考虑联接一个收集自名为 MyBackup_CL 的自定义日志的数据类型，其中每台计算机都具有检测信号。  这些数据类型具有下列数据。

`Type = MyBackup_CL`

| TimeGenerated | 计算机 | LastBackupStatus |
|:---|:---|:---|
| 2017/4/20 凌晨 01:26:32.137 | srv01.contoso.com | 成功 |
| 2017/4/20 凌晨 02:13:12.381 | srv02.contoso.com | 成功 |
| 2017/4/20 凌晨 02:13:12.381 | srv03.contoso.com | 失败 |

`Type = Hearbeat`（仅显示字段的子集）

| TimeGenerated | 计算机 | ComputerIP |
|:---|:---|:---|
| 2017/4/21 中午 12:01:34.482 | srv01.contoso.com | 10.10.100.1 |
| 2017/4/21 中午 12:02:21.916 | srv02.contoso.com | 10.10.100.2 |
| 2017/4/21 中午 12:01:47.373 | srv04.contoso.com | 10.10.100.4 |

#### <a name="inner-join"></a>内部联接

`Type=MyBackup_CL | join inner Computer (Type=Heartbeat) Computer`

返回下列记录，其中计算机字段与两种数据类型都匹配。

| 计算机| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 2017/4/20 凌晨 01:26:32.137 | 成功 | 2017/4/21 中午 12:01:34.482 | 10.10.100.1 | 检测信号 |
| srv02.contoso.com | 2017/4/20 凌晨 02:13:12.381 | 成功 | 2017/4/21 中午 12:02:21.916 | 10.10.100.2 | 检测信号 |


#### <a name="outer-join"></a>外部联接

`Type=MyBackup_CL | join outer Computer (Type=Heartbeat) Computer`

为两种数据类型返回下列记录。

| 计算机| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 2017/4/20 凌晨 01:26:32.137 | 成功  | 2017/4/21 中午 12:01:34.482 | 10.10.100.1 | 检测信号 |
| srv02.contoso.com | 2017/4/20 凌晨 02:14:12.381 | 成功  | 2017/4/21 中午 12:02:21.916 | 10.10.100.2 | 检测信号 |
| srv03.contoso.com | 2017/4/20 凌晨 01:33:35.974 | 失败  | 2017/4/21 中午 12:01:47.373 | | |
| srv04.contoso.com |                           |          | 2017/4/21 中午 12:01:47.373 | 10.10.100.2 | 检测信号 |



#### <a name="left-join"></a>左侧联接

`Type=MyBackup_CL | join left Computer (Type=Heartbeat) Computer`

从 MyBackup_CL 返回下列记录，其中包含任何来自检测信号的匹配字段。

| 计算机| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| srv01.contoso.com | 2017/4/20 凌晨 01:26:32.137 | 成功 | 2017/4/21 中午 12:01:34.482 | 10.10.100.1 | 检测信号 |
| srv02.contoso.com | 2017/4/20 凌晨 02:13:12.381 | 成功 | 2017/4/21 中午 12:02:21.916 | 10.10.100.2 | 检测信号 |
| srv03.contoso.com | 2017/4/20 凌晨 02:13:12.381 | 失败 | | | |


### <a name="extend"></a>Extend
允许在查询中创建运行时字段。 请注意，不能同时使用运行时字段和 measure 命令来执行聚合。

**示例 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
显示 SQL 评估建议的加权建议评分。

**示例 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
显示以 KB 而不是以字节为单位的计数器值。

**示例 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
缩放 WireData TotalBytes 的值，使所有结果都介于 0 和 100 之间。

**示例 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
将小于 50% 的性能计数器值标记为 LOW，将其他值标记为 HIGH。

**示例 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
计算计算机上每个磁盘每分钟的磁盘写入最大次数。

**支持的函数**

| 函数 | 说明 | 语法示例 |
| --- | --- | --- |
| abs |返回指定的值或函数的绝对值。 |`abs(x)` <br> `abs(-5)` |
| acos |返回一个值或函数的反余弦值。 |`acos(x)` |
| and |当且仅当其所有操作数的计算结果为 true，才返回值为 true。 |`and(not(exists(popularity)),exists(price))` |
| asin |返回一个值或函数的反正弦值。 |`asin(x)` |
| atan |返回一个值或函数的反正切值。 |`atan(x)` |
| atan2 |返回由直角坐标 x,y 转换为极坐标所产生的角度。 |`atan2(x,y)` |
| cbrt |立方根。 |`cbrt(x)` |
| ceil |向上取整为整数。 |`ceil(x)`  <br> `ceil(5.6)` 返回 6 |
| cos |返回角度的余弦值。 |`cos(x)` |
| cosh |返回角度的双曲余弦值。 |`cosh(x)` |
| def |默认值的简写。 返回字段“field”的值。 如果该字段不存在，则返回指定的默认值并生成第一个值，其中 `exists()==true`。 |`def(rating,5)`。 此 def() 函数返回分级，或如果文档中未指定分级，则返回 5。 <br> `def(myfield, 1.0)` 等效于 `if(exists(myfield),myfield,1.0)`。 |
| deg |将弧度转换为度。 |`deg(x)` |
| div |`div(x,y)` x 除以 y。 |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist |返回 n 维空间中两个向量（点）之间的距离。 接受幂以及两个或更多个 ValueSource 实例，并计算两个向量之间的距离。 每个 ValueSource 必须是数字。 必须有偶数个传入的 ValueSource 实例，该方法假定前半部分表示第一个向量，后半部分表示第二个向量。 |`dist(2, x, y, 0, 0)` 计算每个文档的 (0,0) 和 (x,y) 之间的欧氏距离。 <br> `dist(1, x, y, 0, 0)` 计算每个文档的 (0,0) 和 (x,y) 之间的曼哈顿（出租车）距离。 <br> `dist(2,,x,y,z,0,0,0)` 每个文档的 (0,0,0) 和 (x,y,z) 之间的欧氏距离。<br>`dist(1,x,y,z,e,f,g)` (x,y,z) 和 (e,f,g) 之间的曼哈顿距离，其中每个字母是一个字段名称。 |
| exists |如果存在该字段的任何成员，则返回 TRUE。 |`exists(author)` 对于“author”字段中具有值的任何文档返回 TRUE。<br>`exists(query(price:5.00))`“price”与“5.00”匹配时，返回 TRUE。 |
| exp |返回上升到 x 次幂的欧拉数。 |`exp(x)` |
| floor |向下取整为整数。 |`floor(x)`  <br> `floor(5.6)` 返回 5 |
| hypo |返回没有中间上溢或下溢的 sqrt(sum(pow(x,2),pow(y,2)))。 |`hypo(x,y)`  <br> ` |
| if |启用条件函数查询。 在 `if(test,value1,value2)` 中，test 是或是指一个逻辑值或返回逻辑值（TRUE 或 FALSE）的表达式。 test 产生的值为 TRUE 时，`value1` 是函数返回的值。 test 产生的值为 FALSE 时，`value2` 是函数返回的值。 表达式可以是输出布尔值的任何函数。 它也可以是返回数字值的函数，值为 0 时会解释为 false 或字符串，值为空字符串时解释为 false。 |`if(termfreq(cat,'electronics'),popularity,42)` 此函数检查每个文档以查看它是否在 cat 字段中包含术语“electronics”。 如果包含，则返回 popularity 字段的值。 否则返回值 42。 |
| linear |实现 `m*x+c`，其中 m 和 c 是常量，x 是一个任意函数。 这等效于 `sum(product(m,x),c)`，但更为有效，因为它是作为单个函数实现的。 |`linear(x,m,c) linear(x,2,4)` 返回 `2*x+4` |
| ln |返回指定函数的自然对数。 |`ln(x)` |
| log |返回指定函数的底数为 10 的对数。 |`log(x)   log(sum(x,100))` |
| map |将最小值和最大值（含两者）范围内的输入函数 x 的任何值映射到指定的目标。 参数 min 和 max 必须为常量。 参数 target 和 default 可以是常量或函数。 如果 x 的值不在 min 和 max 之间，则返回 x 的值，或者如果指定为第 5 个参数，则返回默认值。 |`map(x,min,max,target) map(x,0,0,1)` 将所有 0 值更改为 1。 这可以用于处理默认 0 值。<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)` 将介于 0 和 100 之间的所有值更改为 1，并将所有其他值更改为 -1。<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))` 将介于 0 和 100 之间的所有值更改为 x+599，并将所有其他值更改为 text 字段中术语“solr”的出现次数。 |
| max |返回被指定为参数的多个嵌套函数或常量的最大数值：`max(x,y,...)`。 max 函数还可作为某个指定的常量用作另一个函数或字段的“下限”。  使用 `field(myfield,max)` 语法选择单个多值字段的最大值。 |`max(myfield,myotherfield,0)` |
| min |返回被指定为参数的多个嵌套函数或常量的最小数值：`min(x,y,...)`。 min 函数还可用于为使用常量的函数提供“上限”。 使用 `field(myfield,min)` 语法选择单个多值字段的最小值。 |`min(myfield,myotherfield,0)` |
| mod |计算函数 x 乘以函数 y 的模数。 |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| ms |返回其参数之间相差的毫秒数。 日期相对于 Unix 或 POSIX 时间 epoch，即 1970-01-01 00:00:00 UTC。 参数可能是已编制索引的 TrieDateField 的名称或基于常量日期的日期数学运算符或 NOW。 `ms()` 等效于 `ms(NOW)`，自 epoch 以来的毫秒数。 `ms(a)` 返回自参数表示的 epoch 以来的毫秒数。 `ms(a,b)` 返回 b 在 a 之前发生的毫秒数，即 `a - b`。 |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| not |包装函数逻辑上求反的值。 |`not(exists(author))` 仅当 `exists(author)` 为 false 时，返回值为 TRUE。 |
| 或 |逻辑或运算。 |`or(value1,value2)` value1 或 value2 为 true 时，返回值为 TRUE。 |
| pow |将指定的底数提升到指定的幂。 `pow(x,y)` 将 x 提升为 y 的幂。 |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)` 与 sqrt 相同。 |
| product |返回以逗号分隔的列表形式指定的多个值或函数的乘积。 `mul(...)` 也可用作此函数的别名。 |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |执行倒数函数 `recip(x,m,a,b)` 实现 `a/(m*x+b)`，其中 m、a、b 是常量，x 是任意复合函数。 当 a 和 b 相等且 x>=0 时，此函数的最大值为 1，并且值的大小随着 x 的增大而减小。 同时增大 a 和 b 的值会导致整个函数的运动呈现为曲线较为平缓的部分。 当 x 为 `rord(datefield)` 时，这些属性可以使此函数成为一个理想函数，用于助推更多最近的文档。 |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |将度转换为弧度。 |`rad(x)` |
| rint |取整为最接近的整数。 |`rint(x)`  <br> `rint(5.6)` 返回 6 |
| sin |返回角的正弦值。 |`sin(x)` |
| sinh |返回角的双曲正弦值。 |`sinh(x)` |
| scale |缩放函数 x 值，以便这些值介于指定的 minTarget 和 maxTarget（含两者）之间。 当前实现将遍历所有函数值，以获取最小值和最大值，以便其可以选取正确的缩放范围。 当前实现无法区分文档已被删除还是没有值的文档。 在这些情况下，它使用 0.0 值。 这意味着，如果值在正常情况下全部大于 0.0，则仍然有一个最终可以得到 0.0 作为映射来源的最小值。 在这些情况下，适当的 `map()` 函数可用作一种将 0.0 更改为实际范围中的某个值的解决方法，如下所示：`scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)` 缩放 x 的值，以便所有值将介于 1 和 2（含两者）之间。 |
| sqrt |返回指定值或函数的平方根。 |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |计算两个字符串之间的距离。 使用 Lucene 拼写检查器 StringDistance 接口，并且支持该包中所有可用的实现。 此外，允许应用程序通过 Solr 的资源加载功能插入其自身。 strdist 采用 `(string1, string2, distance measure)`。 距离度量的可能值有：<ul><li>jw：Jaro-Winkler</li><li>edit：Levenstein 或 Edit distance</li><li>ngram：NGramDistance（如果指定）也可以选择传入 ngram 大小。 默认值为 2。</li><li>FQN：用于实现 StringDistance 接口的完全限定的类名称。 必须具有无参数构造函数。</li></ul> |`strdist("SOLR",id,edit)` |
| sub |从 `sub(x,y)` 返回 x-y。 |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| sum |返回以逗号分隔的列表形式指定的多个值或函数的和。 `add(...)` 可用作此函数的别名。 |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |返回术语在该文档的字段中出现的次数。 |termfreq(text,'memory') |
| tan |返回角的正切值。 |`tan(x)` |
| tanh |返回角的双曲正切值。 |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>搜索字段和方面参考
使用日志搜索查找数据时，结果会显示各个字段和方面。 看到的一些信息可能不会显得非常具体。 可以使用以下信息帮助了解结果。

| 字段 | 搜索类型 | 说明 |
| --- | --- | --- |
| TenantId |全部 |用于对数据进行分区。 |
| TimeGenerated |全部 |用于驱动时间线、时间选择器（在搜索和其他屏幕中）。 它表示生成数据段的时间（通常在代理上）。 该时间以 ISO 格式表示，并且始终为 UTC。 在“类型”基于现有检测（即日志中的事件）的情况下，这通常是记录日志条目/行/记录的实际时间。 对于通过管理包或在云中生成的某些其他类型（例如建议或警报），时间表示不同的信息。 这是收集此新数据片段（具有某种配置的快照）的时间，或在其基础上生成建议/警报的时间。 |
| EventID |事件 |Windows 事件日志中的 EventID。 |
| EventLog |事件 |由 Windows 记录的事件的事件日志。 |
| EventLevelName |事件 |关键/警告/信息/成功 |
| EventLevel |事件 |严重/警告/信息/成功的数值（改用 EventLevelName 进行更简单/更具可读性的查询）。 |
| SourceSystem |全部 |数据的来源（就服务的“连接”模式而言）。 示例包括 Microsoft System Center Operations Manager 和 Azure 存储。 |
| ObjectName |PerfHourly |Windows 性能对象名称。 |
| InstanceName |PerfHourly |Windows 性能计数器实例名称。 |
| CounteName |PerfHourly |Windows 性能计数器名称。 |
| ObjectDisplayName |PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty |在 Operations Manager 中按性能收集规则确定的对象显示名称。 也可能是操作见解发现的对象显示名称，或针对其生成警报的对象显示名称。 |
| RootObjectName |PerfHourly、ConfigurationAlert、ConfigurationObject、ConfigurationObjectProperty |在 Operations Manager 中按性能收集规则确定的父级对象的父级（在双重托管关系）显示名称。 也可能是操作见解发现的对象显示名称，或针对其生成警报的对象显示名称。 |
| 计算机 |大多数类型 |数据所属的计算机名称。 |
| DeviceName |ProtectionStatus |数据所属的计算机名称（与“Computer”相同）。 |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |威胁状态级别是威胁状态的数字表示形式。 与 HTTP 响应代码类似，已在数字之间留出了间隙（这就是为什么没有威胁是 150，也不是 100 或 0），以便我们获得一些空间来添加新的状态。 对威胁状态和保护状态进行汇总时，目的是显示在所选的时间段内计算机所处于的最差状态。 数字设置不同状态的级别，以便可以查找具有最大数字的记录。 |
| ThreatStatus |ProtectionStatus |ThreatStatus 的说明，使用 ThreatStatusRank 进行一对一映射。 |
| TypeofProtection |ProtectionStatus |在计算机中检测到的反恶意软件产品：无、Microsoft 恶意软件删除工具、Forefront 等。 |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus、RequiredUpdate |此计算机的代理的 HealthService ID。 |
| HealthServiceId |大多数类型 |此计算机的代理的 HealthService ID。 |
| ManagementGroupName |大多数类型 |Operations Manager 连接的代理的管理组名称。 否则为 null/空白。 |
| ObjectType |ConfigurationObject |Log Analytics 配置评估发现的此对象的类型（如 Operations Manager 管理包的 'type'/class 中所示）。 |
| UpdateTitle |RequiredUpdate |发现未安装的更新的名称。 |
| PublishDate |RequiredUpdate |更新在 Microsoft 更新中发布的时间。 |
| 服务器 |RequiredUpdate |数据所属的计算机名称（与“Computer”相同）。 |
| 产品 |RequiredUpdate |更新应用于的产品。 |
| UpdateClassification |RequiredUpdate |更新的类型（例如，更新汇总或服务包）。 |
| KBID |RequiredUpdate |介绍此最佳做法或更新的知识库文章 ID。 |
| WorkflowName |ConfigurationAlert |生成警报的规则或监视器的名称。 |
| 严重性 |ConfigurationAlert |警报的严重性。 |
| Priority |ConfigurationAlert |警报的优先级。 |
| IsMonitorAlert |ConfigurationAlert |此警报是由监视器 (true) 还是由规则 (false) 生成的？ |
| AlertParameters |ConfigurationAlert |含有 Log Analytics 警报的参数的 XML。 |
| 上下文 |ConfigurationAlert |含有 Log Analytics 警报的“上下文”的 XML。 |
| 工作负载 |ConfigurationAlert |警报所涉及的技术或“工作负荷”。 |
| AdvisorWorkload |建议 |建议所涉及的技术或“工作负荷”。 |
| 说明 |ConfigurationAlert |警报描述（简短）。 |
| DaysSinceLastUpdate |UpdateAgent |此代理在多少天前（相对于此记录的“TimeGenerated”）安装的来自 Server Update Service (WSUS) 或 Microsoft 更新的任何更新？ |
| DaysSinceLastUpdateBucket |UpdateAgent |基于“时间存储桶”中的分类 DaysSinceLastUpdate，计算机上次安装来自 WSUS/Microsoft 更新的任何更新是多久之前。 |
| AutomaticUpdateEnabled |UpdateAgent |自动更新检查在此代理上是启用还是禁用？ |
| AutomaticUpdateValue |UpdateAgent |自动更新检查设为自动下载并安装、仅下载还是仅检查？ |
| WindowsUpdateAgentVersion |UpdateAgent |Microsoft 更新代理的版本号。 |
| WSUSServer |UpdateAgent |此更新代理针对的哪一个 WSUS 服务器？ |
| OSVersion |UpdateAgent |运行此更新代理的操作系统的版本。 |
| Name |Recommendation、ConfigurationObjectProperty |建议的名称/标题或来自 Log Analytics 配置评估的属性名称。 |
| 值 |ConfigurationObjectProperty |来自 Log Analytics 配置评估的属性值。 |
| KBLink |建议 |描述此最佳做法或更新的知识库文章的 URL。 |
| RecommendationStatus |建议 |建议将少数类型的记录更新，而不仅仅是添加到搜索索引。 此状态将更改建议是活动/打开还是 Log Analytics 检测到已得到解决。 |
| RenderedDescription |事件 |Windows 事件的呈现说明（通过填充参数重复使用的文本）。 |
| ParameterXml |事件 |含有 Windows 事件（如事件查看器中所示）的“数据”部分中的参数的 XML。 |
| EventData |事件 |含有 Windows 事件（如事件查看器中所示）的整个“数据”部分的 XML。 |
| 源 |事件 |已生成事件的事件日志源。 |
| EventCategory |事件 |直接来自 Windows 事件日志的事件类别。 |
| UserName |事件 |Windows 事件（通常为 NT AUTHORITY\LOCALSYSTEM）的用户名称。 |
| SampleValue |PerfHourly |性能计数器的每小时聚合的平均值。 |
| Min |PerfHourly |性能计数器每小时聚合的每小时间隔中的最小值。 |
| Max |PerfHourly |性能计数器每小时聚合的每小时间隔中的最大值。 |
| Percentile95 |PerfHourly |性能计数器每小时聚合的每小时间隔的第 95 百分位值。 |
| SampleCount |PerfHourly |多少“原始”性能计数器样本已用于生成此每小时聚合记录。 |
| 威胁 |ProtectionStatus |找到的恶意软件的名称。 |
| StorageAccount |W3CIISLog |从中读取日志的 Azure 存储帐户。 |
| AzureDeploymentID |W3CIISLog |日志所属的云服务的 Azure 部署 ID。 |
| 角色 |W3CIISLog |日志所属的 Azure 云服务的角色。 |
| RoleInstance |W3CIISLog |日志所属的 Azure 角色的 RoleInstance。 |
| sSiteName |W3CIISLog |日志所属的 IIS 网站（元数据库表示法）；原始日志中的 s-sitename 字段。 |
| sComputerName |W3CIISLog |原始日志中的 s-computername 字段。 |
| sIP |W3CIISLog |HTTP 请求发送到的服务器 IP 地址。 原始日志中的 s-ip 字段。 |
| csMethod |W3CIISLog |HTTP 请求中的客户端所使用的 HTTP 方法（例如 GET/POST）。 原始日志中的 cs-method 字段。 |
| cIP |W3CIISLog |HTTP 请求来自的客户端 IP 地址。 原始日志中的 c-ip 字段。 |
| csUserAgent |W3CIISLog |由客户端（浏览器或其他）声明的 HTTP 用户代理。 原始日志中的 cs-user-agent。 |
| scStatus |W3CIISLog |由服务器返回到客户端的 HTTP 状态代码（例如 200/403/500）。 原始日志中的 cs-status。 |
| TimeTaken |W3CIISLog |完成请求所需的时间（以毫秒计）。 原始日志中的 timetaken 字段。 |
| csUriStem |W3CIISLog |请求的相对 URI（没有主机地址，即 /search）。 原始日志中的 cs-uristem 字段。 |
| csUriQuery |W3CIISLog |URI 查询。 URI 查询仅对于动态网页（如 ASP 页面）是必需的，因此，此字段通常包含用于静态页的连字符。 |
| sPort |W3CIISLog |HTTP 请求发送到的服务器端口（并且自选取该端口后，IIS 会进行侦听）。 |
| csUserName |W3CIISLog |经过身份验证的用户名称，前提是请求已经过验证且不是匿名。 |
| csVersion |W3CIISLog |请求中使用的 HTTP 协议版本（例如 HTTP/1.1）。 |
| csCookie |W3CIISLog |Cookie 信息。 |
| csReferer |W3CIISLog |用户上次访问的站点。 此站点提供指向当前站点的链接。 |
| csHost |W3CIISLog |所请求的主机头（例如 www.mysite.com）。 |
| scSubStatus |W3CIISLog |子状态错误代码。 |
| scWin32Status |W3CIISLog |Windows 状态代码。 |
| csBytes |W3CIISLog |从客户端到服务器的请求中发送的字节数。 |
| scBytes |W3CIISLog |从服务器到客户端的响应中返回的字节数。 |
| ConfigChangeType |ConfigurationChange |更改的类型（例如 WindowsServices/软件）。 |
| ChangeCategory |ConfigurationChange |更改的类别（已修改/已添加/已删除）。 |
| SoftwareType |ConfigurationChange |软件的类型（更新/应用程序）。 |
| SoftwareName |ConfigurationChange |软件的名称（仅适用于软件更改）。 |
| 发布者 |ConfigurationChange |发布软件的供应商（仅适用于软件更改）。 |
| SvcChangeType |ConfigurationChange |在 Windows 服务上已应用的更改的类型（状态/启动类型/路径/服务帐户）。 仅适用于 Windows 服务更改。 |
| SvcDisplayName |ConfigurationChange |已更改的服务的显示名称。 |
| SvcName |ConfigurationChange |已更改的服务名称。 |
| SvcState |ConfigurationChange |服务的新（当前）状态。 |
| SvcPreviousState |ConfigurationChange |以前已知的服务状态（仅当服务状态更改后适用）。 |
| SvcStartupType |ConfigurationChange |服务启动类型。 |
| SvcPreviousStartupType |ConfigurationChange |以前的服务启动类型（仅当服务启动类型更改后适用）。 |
| SvcAccount |ConfigurationChange |服务帐户。 |
| SvcPreviousAccount |ConfigurationChange |以前的服务帐户（仅当服务帐户更改后适用）。 |
| SvcPath |ConfigurationChange |Windows 服务的可执行文件的路径。 |
| SvcPreviousPath |ConfigurationChange |Windows 服务的可执行文件的以前路径（仅当更改后适用）。 |
| SvcDescription |ConfigurationChange |服务说明。 |
| 上一步 |ConfigurationChange |此软件以前的状态（已安装/未安装/以前的版本）。 |
| Current |ConfigurationChange |此软件的最新状态（已安装/未安装/当前版本）。 |

## <a name="next-steps"></a>后续步骤
有关日志搜索的其他信息：

* 熟悉[日志搜索](log-analytics-log-searches.md)以查看解决方案收集的详细信息。
* 使用 [Log Analytics 中的自定义字段](log-analytics-custom-fields.md)扩展日志搜索。
