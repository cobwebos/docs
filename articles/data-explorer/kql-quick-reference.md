---
title: KQL 快速参考
description: 有用的 KQL 函数及其定义的列表，包含语法示例。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139073"
---
# <a name="kql-quick-reference"></a>KQL 快速参考

本文将列出函数及其说明列表，以帮助您开始使用 Kusto 查询语言。

| 操作员/功能                               | 描述                           | 语法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**过滤器/搜索/条件**                      |**_通过筛选或搜索查找相关数据_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | 特定谓词上的筛选器           | `T | where Predicate`                         |
| [其中包含/有](/azure/kusto/query/whereoperator)        | `Contains`： 查找任何子字符串匹配项 <br> `Has`：查找特定单词（性能更好）  | `T | where col1 contains/has "[search term]"`|
| [搜索](/azure/kusto/query/searchoperator)                    | 搜索表中的所有列以搜索值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [采取](/azure/kusto/query/takeoperator)                        | 返回指定的记录数。 用于测试查询<br>**_注意_** `_take`：`_limit`和 * 是同义词。 | `T | take NumberOfRows` |
| [情况 下](/azure/kusto/query/casefunction)                        | 添加条件语句，类似于其他系统中的 if/然后/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | 生成具有输入表提供的列的不同组合的表 | `distinct [ColumnName], [ColumnName]` |
| **日期/时间**                                   |**_使用日期和时间函数的操作_**               |                          |
|[前](/azure/kusto/query/agofunction)                           | 返回相对于查询执行时间的时间偏移量。 例如，`ago(1h)`是当前时钟读数前一小时。 | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | 返回[各种日期格式](/azure/kusto/query/format-datetimefunction#supported-formats)的数据。 | `format_datetime(datetime , format)` |
| [bin](/azure/kusto/query/binfunction)                          | 在时间范围内舍入所有值并对其进行分组 | `bin(value,roundTo)` |
| **创建/删除列**                   |**_添加或删除表中的列_** |                                                    |
| [打印](/azure/kusto/query/printoperator)                      | 使用一个或多个标量表达式输出单个行 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [项目](/azure/kusto/query/projectoperator)                  | 选择要按指定顺序包括的列 | `T | project ColumnName [= Expression] [, ...]` <br> 或 <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](/azure/kusto/query/projectawayoperator)         | 选择要从输出中排除的列 | `T | project-away ColumnNameOrPattern [, ...]` |
| [扩展](/azure/kusto/query/extendoperator)                    | 创建计算列并将其添加到结果集 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序和聚合数据集**                 |**_通过以有意义的方式对数据进行排序或分组来重组数据_**|                  |
| [排序](/azure/kusto/query/sortoperator)                        | 按升序或降序按一个或多个列对输入表的行进行排序 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [返回页首](/azure/kusto/query/topoperator)                          | 使用数据集排序时返回数据集的前 N 行`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [总结](/azure/kusto/query/summarizeoperator)              | 根据组列对行进行`by`分组，并计算每个组的聚合 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | 计算输入表中的记录（例如，T）<br>此运算符是速记`summarize count() `| `T | count` |
| [加入](/azure/kusto/query/joinoperator)                        | 通过匹配每个表中指定列的值，合并两个表的行以形成新表。 支持各种`flouter`联接类型： 、 `inner`、 `innerunique`、 `leftanti` `leftantisemi`、 `leftouter` `leftsemi`、 `rightanti` `rightantisemi`、 `rightouter`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [联盟](/azure/kusto/query/unionoperator)                      | 获取两个或多个表并返回其所有行 | `[T1] | union [T2], [T3], …` |
| [范围](/azure/kusto/query/rangeoperator)                      | 生成具有算术值序列的表 | `range columnName from start to stop step step` |
| **设置数据格式**                                 | **_以有用的方式重组数据以输出_** | |
| [查找](/azure/kusto/query/lookupoperator)                    | 在维度表中使用拾值扩展事实数据表的列 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv 扩展](/azure/kusto/query/mvexpandoperator)               | 将动态数组转换为行（多值扩展） | `T | mv-expand Column` |
| [解析](/azure/kusto/query/parseoperator)                      | 计算字符串表达式并将其值分析为一个或多个计算列。 用于构建非结构化数据。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | 沿指定轴创建一系列指定的聚合值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [让](/azure/kusto/query/letstatement)                         | 将名称绑定到可以引用其绑定值的表达式。 值可以是 lambda 表达式，以创建作为查询的一部分的临时函数。 用于`let`在结果看起来像新表的表上创建表达式。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **常规**                                     | **_杂项操作和职能_** | |
| [invoke](/azure/kusto/query/invokeoperator)                    | 在作为输入接收的表上运行函数。 | `T | invoke function([param1, param2])` |
| [评估插件名称](/azure/kusto/query/evaluateoperator)     | 评估查询语言扩展（插件） | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **可视 化**                               | **_以图形格式显示数据的操作_** | |
| [呈现](/azure/kusto/query/renderoperator) | 渲染结果为图形输出 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
