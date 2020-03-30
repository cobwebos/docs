---
title: 在 Azure 数据工厂中旋转数据流转换功能
description: Azure 数据工厂中可用争用数据流函数概述
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e2517ec4a02a5d61fb3ce1d9ca9ffa2b5f4e8bf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74287026"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>在争论数据流中的转换函数

在 Azure 数据工厂中构建数据流允许您在云规模下进行无代码敏捷数据准备和争鸣。 旋转数据流与[联机电源查询](https://docs.microsoft.com/powerquery-m/power-query-m-reference)集成，并使 Power Query M 函数可用于通过火花执行进行数据争鸣。 

尽管在创作期间可用，但目前并非所有 Power Query M 函数都支持用于数据争用。 构建争用数据流时，如果不支持函数，系统将提示您出现以下错误消息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

下面是支持电源查询 M 函数的列表。

## <a name="column-management"></a>列管理

* 选择：[表.选择列](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 删除：[表.删除列](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 重命名：[表.重命名列](https://docs.microsoft.com/powerquery-m/table-renamecolumns)，[表.前缀列](https://docs.microsoft.com/powerquery-m/table-prefixcolumns)，[表.转换列名](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 重新排序：[表.重新排序列](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>行筛选

使用 M 函数[表.选择行](https://docs.microsoft.com/powerquery-m/table-selectrows)以筛选以下条件：

* 平等和不平等
* 数字、文本和日期比较（但不是日期时间）
* 数字信息，如[数字.甚至](https://docs.microsoft.com/powerquery-m/number-iseven)/[奇数](https://docs.microsoft.com/powerquery-m/number-iseven)
* 使用[文本包含、](https://docs.microsoft.com/powerquery-m/text-contains)包含 、[文本.使用 开头](https://docs.microsoft.com/powerquery-m/text-startswith)或[文本.结束](https://docs.microsoft.com/powerquery-m/text-endswith)
* 日期范围，包括所有"IsIn"[日期函数](https://docs.microsoft.com/powerquery-m/date-functions)） 
* 这些使用和，或或不条件的组合

## <a name="adding-and-transforming-columns"></a>添加和转换列

以下 M 函数添加或转换列：[表.addColumn，](https://docs.microsoft.com/powerquery-m/table-addcolumn)[表.转换列](https://docs.microsoft.com/powerquery-m/table-transformcolumns)，[表.替换值](https://docs.microsoft.com/powerquery-m/table-replacevalue)，[表.重复列](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn)。 下面是支持的转换函数。

* 数值算术
* 文本串联
* 日期和时间算术（算术运算符，[日期.Adddays，](https://docs.microsoft.com/powerquery-m/date-adddays)[日期.addmonths，](https://docs.microsoft.com/powerquery-m/date-addmonths)[日期.AddQuarters，](https://docs.microsoft.com/powerquery-m/date-addquarters)[日期.add周](https://docs.microsoft.com/powerquery-m/date-addweeks)，[日期.Add年](https://docs.microsoft.com/powerquery-m/date-addyears)）
* 持续时间可用于日期和时间算术，但在写入接收器之前必须转换为另一种类型（算术运算符[、#duration、](https://docs.microsoft.com/powerquery-m/sharpduration)[持续时间.天](https://docs.microsoft.com/powerquery-m/duration-days)、[持续时间.小时](https://docs.microsoft.com/powerquery-m/duration-hours)、[持续时间.分钟](https://docs.microsoft.com/powerquery-m/duration-minutes)、[持续时间.持续时间](https://docs.microsoft.com/powerquery-m/duration-seconds)[.总天数](https://docs.microsoft.com/powerquery-m/duration-totaldays)、[持续时间.总时间、持续时间.总时间、持续时间.总时间](https://docs.microsoft.com/powerquery-m/duration-totalhours)、[持续时间.总分钟](https://docs.microsoft.com/powerquery-m/duration-totalminutes)数[、持续时间.总秒数、持续时间。"](https://docs.microsoft.com/powerquery-m/duration-totalseconds)    
* 大多数标准、科学和三角测量数值函数（[操作](https://docs.microsoft.com/powerquery-m/number-functions#operations)、[舍入](https://docs.microsoft.com/powerquery-m/number-functions#rounding)和[三角测量](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry)下的所有函数 *，但*数字.因子、数字、排列和数字.组合除外）
* 替换（[替换器.替换文本](https://docs.microsoft.com/powerquery-m/replacer-replacetext)、[替换器.替换值](https://docs.microsoft.com/powerquery-m/replacer-replacevalue)、[文本.替换](https://docs.microsoft.com/powerquery-m/text-replace)、[文本.删除](https://docs.microsoft.com/powerquery-m/text-remove)）
* 位置文本提取（[文本.位置](https://docs.microsoft.com/powerquery-m/text-positionof)，[文本.长度](https://docs.microsoft.com/powerquery-m/text-length)，[文本.开始](https://docs.microsoft.com/powerquery-m/text-start)，[文本.结束](https://docs.microsoft.com/powerquery-m/text-end)，[文本.中间](https://docs.microsoft.com/powerquery-m/text-middle)，[文本.替换范围](https://docs.microsoft.com/powerquery-m/text-replacerange)，[文本.删除范围](https://docs.microsoft.com/powerquery-m/text-removerange)）
* 基本文本格式（[文本.下部](https://docs.microsoft.com/powerquery-m/text-lower)，[文本.上部](https://docs.microsoft.com/powerquery-m/text-upper)，[文本.修剪](https://docs.microsoft.com/powerquery-m/text-trim)/[开始](https://docs.microsoft.com/powerquery-m/text-trimstart)/[结束](https://docs.microsoft.com/powerquery-m/text-trimend)，[文本.PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[结束](https://docs.microsoft.com/powerquery-m/text-padend)，[文本.反向](https://docs.microsoft.com/powerquery-m/text-reverse)）
* 日期/时间函数（[日期.天](https://docs.microsoft.com/powerquery-m/date-day)，[日期.月](https://docs.microsoft.com/powerquery-m/date-month)，[日期.年](https://docs.microsoft.com/powerquery-m/date-year)[时间.小时](https://docs.microsoft.com/powerquery-m/time-hour)，[时间.分钟](https://docs.microsoft.com/powerquery-m/time-minute)，[时间.秒](https://docs.microsoft.com/powerquery-m/time-second)，[日期.星期一，](https://docs.microsoft.com/powerquery-m/date-dayofweek)[日期.日期，日期](https://docs.microsoft.com/powerquery-m/date-dayofyear).日期[.日期月](https://docs.microsoft.com/powerquery-m/date-daysinmonth)）
* 如果表达式（但分支必须具有匹配的类型）
* 行筛选器作为逻辑列
* 数字、文本、逻辑、日期和日期时间常量

<a name="mergingjoining-tables"></a>合并/联接表
----------------------
* 电源查询将生成嵌套联接（表.NestedJoin;用户还可以手动编写[表.AddJoinColumn）。](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)
    然后，用户必须将嵌套联接列扩展到非嵌套联接（表.ExpandTableColumn，在任何其他上下文中不受支持）。
* M 函数[Table.Join](https://docs.microsoft.com/powerquery-m/table-join)可以直接编写，以避免需要额外的扩展步骤，但用户必须确保联接的表之间没有重复的列名称
* 支持的联接类型：[Inner](https://docs.microsoft.com/powerquery-m/joinkind-inner)内[， 左外，](https://docs.microsoft.com/powerquery-m/joinkind-leftouter)[右外，](https://docs.microsoft.com/powerquery-m/joinkind-rightouter)[全外](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* [值.相等值](https://docs.microsoft.com/powerquery-m/value-equals)和[值.可空等值](https://docs.microsoft.com/powerquery-m/value-nullableequals)都支持作为关键相等性比较器

## <a name="group-by"></a>Group by

使用[表组](https://docs.microsoft.com/powerquery-m/table-group)聚合值。
* 必须与聚合函数一起使用
* 支持的聚合函数：[表.RowCount，](https://docs.microsoft.com/powerquery-m/table-rowcount)[列表.Sum，](https://docs.microsoft.com/powerquery-m/list-sum)[列表.计数](https://docs.microsoft.com/powerquery-m/list-count)，[列表.平均](https://docs.microsoft.com/powerquery-m/list-average)，[列表.Min，](https://docs.microsoft.com/powerquery-m/list-min)[列表.Max，](https://docs.microsoft.com/powerquery-m/list-max)[列表.标准偏差](https://docs.microsoft.com/powerquery-m/list-standarddeviation)，[列表.第一](https://docs.microsoft.com/powerquery-m/list-first)，[列表.最后](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>排序

使用[表排序](https://docs.microsoft.com/powerquery-m/table-sort)对值进行排序。

## <a name="reducing-rows"></a>减少行

保持和删除顶部，保持范围（相应的 M 函数，仅支持计数，而不是条件：[表.FirstN，](https://docs.microsoft.com/powerquery-m/table-firstn)[表.跳过](https://docs.microsoft.com/powerquery-m/table-skip)，[表.删除FirstN，](https://docs.microsoft.com/powerquery-m/table-removefirstn)[表.范围](https://docs.microsoft.com/powerquery-m/table-range)，[表.MinN，](https://docs.microsoft.com/powerquery-m/table-minn)[表.MaxN）](https://docs.microsoft.com/powerquery-m/table-maxn)

## <a name="known-unsupported-functions"></a>已知不支持的函数

| 函数 | 状态 |
| -- | -- |
| Table.PromoteHeaders | 不支持。 通过在数据集中设置"第一行为标题"，可以得出相同的结果。 |
| Table.CombineColumns | 这是一个不直接支持但可以通过添加连接两个给定列的新列来实现的常见方案。  例如，表.Addcolumn（删除电子邮件列，"名称"，每个 [名字] &""& [姓氏]） |
| Table.TransformColumnTypes | 在大多数情况下，这一点是支持的。 不支持以下方案：将字符串转换为货币类型、将字符串转换为时间类型、将字符串转换为百分比类型。 |
| Table.NestedJoin | 只需执行联接，就会导致验证错误。 必须展开列才能正常工作。 |
| Table.Distinct | 不支持删除重复行。 |
| Table.RemoveLastN | 不支持删除底部行。 |
| Table.RowCount | 不支持，但可以通过添加列来实现，该列的所有单元格都为空（可以使用条件列），然后在该列上按组使用。 表.组受支持。 | 
| 行级错误处理 | 当前不支持行级错误处理。 例如，要从列中筛选出非数值，一种方法是将文本列转换为数字。 每个无法转换的单元格都将处于错误状态，需要筛选。 在争论数据流时，这种情况是不可能的。 |
| Table.Transpose | 不支持 |
| Table.Pivot | 不支持 |

## <a name="next-steps"></a>后续步骤

了解如何[创建争鸣的数据流](wrangling-data-flow-tutorial.md)。