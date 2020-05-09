---
title: Azure 数据工厂中的整理数据流转换函数
description: Azure 数据工厂中的可用整理数据流函数概述
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 0a0947a5e2b57f728023b0f923428814b3e439ec
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626677"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>整理数据流中的转换函数

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

通过 Azure 数据工厂中的整理数据流，可以在云规模进行无代码的敏捷数据准备和整理。 整理数据流与[Power Query Online](https://docs.microsoft.com/powerquery-m/power-query-m-reference)集成，使 Power Query M 功能可用于通过 spark 执行的数据整理。 

当前并非所有 Power Query M 函数都支持数据整理，但在创作期间可用。 生成整理数据流时，如果函数不受支持，系统将提示你输入以下错误消息：

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

下面是受支持的 Power Query M 函数的列表。

## <a name="column-management"></a>列管理

* 选择： [SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* 删除： [table.removecolumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* 重命名： [RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns)、 [PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns)、 [TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* 重新排序： [ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>行筛选

使用 M 函数[SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows)根据以下条件进行筛选：

* 相等和不相等
* 数值、文本和日期比较（但不是 DateTime）
* 数字信息，如[IsEven](https://docs.microsoft.com/powerquery-m/number-iseven)/[奇数](https://docs.microsoft.com/powerquery-m/number-iseven)
* 使用 Text 的文本[包含](https://docs.microsoft.com/powerquery-m/text-contains)、 [StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)或[EndsWith。](https://docs.microsoft.com/powerquery-m/text-endswith)
* 包括所有 "IsIn'[日期" 函数](https://docs.microsoft.com/powerquery-m/date-functions)的日期范围 
* 使用 and、or 或 not 条件的组合

## <a name="adding-and-transforming-columns"></a>添加和转换列

以下 M 函数添加或转换列： [AddColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn)、 [table.transformcolumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns)、 [ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue)、 [DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn)。 下面是受支持的转换函数。

* 数值算术
* 文本连接
* Date andTime 算术运算（算术运算符、 [AddDays](https://docs.microsoft.com/powerquery-m/date-adddays)、 [AddMonths](https://docs.microsoft.com/powerquery-m/date-addmonths)、 [AddQuarters](https://docs.microsoft.com/powerquery-m/date-addquarters) [、AddWeeks、](https://docs.microsoft.com/powerquery-m/date-addweeks) [AddYears](https://docs.microsoft.com/powerquery-m/date-addyears)）
* 持续时间可用于日期和时间算法，但必须先转换为另一种类型，然后才能写入接收器（算术运算符[、#duration](https://docs.microsoft.com/powerquery-m/sharpduration)、 [duration](https://docs.microsoft.com/powerquery-m/duration-days)、duration [、duration、](https://docs.microsoft.com/powerquery-m/duration-hours)Duration [、](https://docs.microsoft.com/powerquery-m/duration-minutes) [TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays) [、](https://docs.microsoft.com/powerquery-m/duration-seconds) [TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours)、 [totaldvrduration.totalminutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes)、 [duration. timespan.totalseconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds)）    
* 大多数标准、科学和三角数字函数（*除 Number 之外*的所有函数，包括运算、[舍入](https://docs.microsoft.com/powerquery-m/number-functions#rounding)和[三角](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry)[运算](https://docs.microsoft.com/powerquery-m/number-functions#operations)。阶乘、数字和组合）
* [Replace](https://docs.microsoft.com/powerquery-m/text-replace)（[替换器，ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext)，[替换器](https://docs.microsoft.com/powerquery-m/replacer-replacevalue) [，text）](https://docs.microsoft.com/powerquery-m/text-remove)
* 位置文本提取（[PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof)、Text、 [Length](https://docs.microsoft.com/powerquery-m/text-length)、 [text](https://docs.microsoft.com/powerquery-m/text-start) [、text、text.](https://docs.microsoft.com/powerquery-m/text-end)RemoveRange、 [ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange)、 [Text.Middle](https://docs.microsoft.com/powerquery-m/text-middle) [Text.RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange)）
* 基本文本格式（[文本. 较低](https://docs.microsoft.com/powerquery-m/text-lower)、[文本](https://docs.microsoft.com/powerquery-m/text-upper)、文本[、文本。剪裁](https://docs.microsoft.com/powerquery-m/text-trim)/[开始](https://docs.microsoft.com/powerquery-m/text-trimstart)/[结束](https://docs.microsoft.com/powerquery-m/text-trimend)、 [PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[结束](https://docs.microsoft.com/powerquery-m/text-padend)、[文本反转](https://docs.microsoft.com/powerquery-m/text-reverse)）
* 日期/时间函数（[日期. 日](https://docs.microsoft.com/powerquery-m/date-day)、[月](https://docs.microsoft.com/powerquery-m/date-month)、日、[年](https://docs.microsoft.com/powerquery-m/date-year)、月[、日](https://docs.microsoft.com/powerquery-m/time-minute) [、](https://docs.microsoft.com/powerquery-m/time-second)[日、日](https://docs.microsoft.com/powerquery-m/time-hour)、 [DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek)、 [DayOfYear](https://docs.microsoft.com/powerquery-m/date-dayofyear)、 [DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth)）
* If 表达式（但分支必须具有匹配的类型）
* 作为逻辑列的行筛选器
* Number、text、logical、date 和 datetime 常量

<a name="mergingjoining-tables"></a>合并/联接表
----------------------
* Power Query 将生成嵌套联接（NestedJoin; 用户也可以手动写入[AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)）。
    然后，用户必须将嵌套的联接列展开为非嵌套联接（Table.expandtablecolumn，在任何其他上下文中不受支持）。
* 可以直接写入 M 函数[表](https://docs.microsoft.com/powerquery-m/table-join)，以避免需要额外的扩展步骤，但用户必须确保联接的表中没有重复的列名
* 支持的联接类型：[内部](https://docs.microsoft.com/powerquery-m/joinkind-inner)、 [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter)、 [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter)、 [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* 同时[Value.Equals](https://docs.microsoft.com/powerquery-m/value-equals)支持 NullableEquals 和值等[。](https://docs.microsoft.com/powerquery-m/value-nullableequals)

## <a name="group-by"></a>Group by

使用 "[表](https://docs.microsoft.com/powerquery-m/table-group)" 作为聚合值。
* 必须与聚合函数一起使用
* 支持的聚合函数[：表 RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount)，[列表. Sum](https://docs.microsoft.com/powerquery-m/list-sum)，[列表. 计数](https://docs.microsoft.com/powerquery-m/list-count)，[列表，平均值](https://docs.microsoft.com/powerquery-m/list-average)，列表，[最大](https://docs.microsoft.com/powerquery-m/list-max)[值](https://docs.microsoft.com/powerquery-m/list-min)，列表， [StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation)，列表，[列表，](https://docs.microsoft.com/powerquery-m/list-first)[最后一个](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>排序

使用[表。](https://docs.microsoft.com/powerquery-m/table-sort)对值进行排序。

## <a name="reducing-rows"></a>减少行

保留并删除 Top，保留范围（相应的 M 个函数，仅支持计数，不[Table.FirstN](https://docs.microsoft.com/powerquery-m/table-firstn)包括条件： FirstN [、](https://docs.microsoft.com/powerquery-m/table-skip) [RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn)、[明尼苏达](https://docs.microsoft.com/powerquery-m/table-minn)、 [MaxN](https://docs.microsoft.com/powerquery-m/table-maxn)）。 [Table.Range](https://docs.microsoft.com/powerquery-m/table-range)

## <a name="known-unsupported-functions"></a>已知不受支持的函数

| 函数 | 状态 |
| -- | -- |
| Table.PromoteHeaders | 不支持。 可以通过在数据集中将 "第一行作为标题" 来实现相同的结果。 |
| Table.CombineColumns | 这是一个不直接支持的常见方案，但可通过添加一个连接两个给定列的新列来实现。  例如，AddColumn （RemoveEmailColumn，"Name"，每个 [FirstName] & "" & [LastName]） |
| Table.TransformColumnTypes | 大多数情况下都支持此项。 以下方案不受支持：将字符串转换为货币类型，将字符串转换为时间类型，并将字符串转换为百分比类型。 |
| Table.NestedJoin | 仅执行联接将导致验证错误。 要使其正常工作，必须对列进行扩展。 |
| Table.Distinct | 不支持删除重复的行。 |
| Table.RemoveLastN | 不支持删除下一行。 |
| Table.RowCount | 不受支持，但是可以通过包含所有单元格为空的 add 列（可以使用 condition 列），然后对该列使用 group by。 支持组。 | 
| 行级错误处理 | 当前不支持行级别的错误处理。 例如，若要筛选出列中的非数字值，一种方法是将文本列转换为数字。 未能转换的每个单元都将处于错误状态，需要对其进行筛选。 这种情况在整理的数据流中是不可能的。 |
| Table.Transpose | 不支持 |
| Table.Pivot | 不支持 |

## <a name="next-steps"></a>后续步骤

了解如何[创建整理](wrangling-data-flow-tutorial.md)数据流。