---
title: KQL 快速参考
description: 使用语法示例的有用 KQL 函数及其定义的列表。
author: yossi-karp
ms.author: v-yokarp
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: b53890afd10a3aee131ab3897d01e6b6fdf261a6
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429262"
---
# <a name="kql-quick-reference"></a>KQL 快速参考

本文介绍函数及其说明的列表，以帮助你开始使用 Kusto 查询语言。

| Operator/Function                               | 说明                           | 语法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**筛选器/搜索/条件**                      |**_通过筛选或搜索查找相关数据_** |                      |
| [where](/azure/kusto/query/whereoperator.md)                      | 针对特定谓词的筛选器           | `T | where Predicate`                         |
| [其中包含/](/azure/kusto/query/whereoperator.md)        | `Contains`：查找任何子字符串匹配项 <br> `Has`：查找特定词（更好的性能）  | `T | where col1 contains/has "[search term]"`|
| [search](/azure/kusto/query/searchoperator.md)                    | 在表中搜索所有列中的值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](/azure/kusto/query/takeoperator.md)                        | 返回指定的记录数。 用于测试查询<br>**_注意_** ： `_take`_ 和 `_limit`_ 是同义词。 | `T | take NumberOfRows` |
| [区分](/azure/kusto/query/casefunction.md)                        | 添加一个条件语句，类似于其他系统中的 if/then/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator.md)                | 生成一个表，其中包含输入表中所提供列的不同组合 | `distinct [ColumnName], [ColumnName]` |
| **日期/时间**                                   |**_使用日期和时间函数的操作_**               |                          |
|[小时](/azure/kusto/query/agofunction.md)                           | 返回相对于查询执行时间的时间偏移量。 例如，`ago(1h)` 是当前时钟读取之前的一小时。 | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction.md)  | 返回[各种日期格式](/azure/kusto/query/format-datetimefunction.md#supported-formats)的数据。 | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction.md)                          | 在时间范围内舍入所有值并将其分组 | `bin(value,roundTo)` |
| **创建/删除列**                   |**_在表中添加或删除列_** |                                                    |
| [出来](/azure/kusto/query/printoperator.md)                      | 输出包含一个或多个标量表达式的单个行 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](/azure/kusto/query/projectoperator.md)                  | 选择要按指定顺序包含的列 | `T | project ColumnName [= Expression] [, ...]` <br> 或 <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator.md)         | 选择要从输出中排除的列 | `T | project-away ColumnNameOrPattern [, ...]` |
| [extend](/azure/kusto/query/extendoperator.md)                    | 创建计算列并将其添加到结果集中 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序和聚合数据集**                 |**_通过在有意义的方法中对数据进行排序或分组来重新构建数据_**|                  |
| [sort](/azure/kusto/query/sortoperator.md)                        | 按一个或多个列按升序或降序对输入表中的行进行排序 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [返回页首](/azure/kusto/query/topoperator.md)                          | 当使用对数据集进行排序时，返回数据集的前 N 行 `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](/azure/kusto/query/summarizeoperator.md)              | 根据 `by` 组列对行进行分组，并计算每个组的聚合 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [计数](/azure/kusto/query/countoperator.md)                       | 对输入表中的记录进行计数（例如，T）<br>此运算符是 `summarize count() ` 的速记| `T | count` |
| [join](/azure/kusto/query/joinoperator.md)                        | 合并两个表中的行，通过匹配每个表中指定列的值形成一个新表。 支持完整的联接类型： `flouter`、`inner`、`innerunique`、`leftanti`、`leftantisemi`、`leftouter`、`leftsemi`、`rightanti`、`rightantisemi`、`rightouter`、`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](/azure/kusto/query/unionoperator.md)                      | 使用两个或多个表并返回其所有行 | `[T1] | union [T2], [T3], …` |
| [range](/azure/kusto/query/rangeoperator.md)                      | 使用算术序列值生成表 | `range columnName from start to stop step step` |
| **设置数据格式**                                 | **_以有用的方式重构要输出的数据_** | |
| [查找](/azure/kusto/query/lookupoperator.md)                    | 使用在维度表中查找的值扩展事实数据表的列 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv 展开](/azure/kusto/query/mvexpandoperator.md)               | 将动态数组转换为行（多值扩展） | `T | mv-expand Column` |
| [分析](/azure/kusto/query/parseoperator.md)                      | 计算字符串表达式并将其值分析为一个或多个计算列。 用于构造非结构化数据。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make 系列](/azure/kusto/query/make-seriesoperator.md)          | 沿指定轴创建系列的指定聚合值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [用于](/azure/kusto/query/letstatement.md)                         | 将名称绑定到可引用其绑定值的表达式。 值可以是 lambda 表达式，用于创建即席函数作为查询的一部分。 使用 `let` 可对结果类似于新表的表创建表达式。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **常规**                                     | **_其他操作和函数_** | |
| [invoke](/azure/kusto/query/invokeoperator.md)                    | 对作为输入的表运行函数。 | `T | invoke function([param1, param2])` |
| [评估 pluginName](/azure/kusto/query/evaluateoperator.md)     | 评估查询语言扩展插件（插件） | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **视觉**                               | **_以图形格式显示数据的操作_** | |
| [呈现](/azure/kusto/query/renderoperator.md) | 将结果呈现为图形输出 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
