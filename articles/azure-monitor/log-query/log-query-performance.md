---
title: 在 Azure Monitor 中编写有效的日志查询 | Microsoft Docs
description: 有关如何在 Log Analytics 中编写查询的资源参考。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60519368"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>在 Azure Monitor 中编写有效的日志查询
本文提供有关在 Azure Monitor 中编写有效日志查询的建议。 使用这些策略可确保以最低的开销快速运行查询。

## <a name="scope-your-query"></a>限定查询范围
如果查询处理的数据多过实际需要处理的数据，则查询可能会长时间运行，并且往往会在结果中返回过多的数据，从而影响分析的效率。 在极端情况下，查询甚至可能会超时并失败。

### <a name="specify-your-data-source"></a>指定数据源
编写有效查询的第一步是将查询范围限定为所需的数据源。 指定某个表始终优先于运行宽泛的文本搜索（例如 `search *`）。 若要查询特定的表，请先在查询中指定表名，如下所示：

``` Kusto
requests | ...
```

可以在查询中使用 [search](/azure/kusto/query/searchoperator) 来搜索特定表中多个列内的某个值，如下所示：

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

使用 [union](/azure/kusto/query/unionoperator) 可以查询多个表，如下所示：

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>指定时间范围
还应将查询限制为所需数据的时间范围。 默认情况下，查询包括过去 24 小时内收集的数据。 可以在[时间范围选择器](get-started-portal.md#select-a-time-range)中更改该选项，或将其显式添加到查询。 最好是紧接在表名后面添加时间筛选器，使剩余的查询部分只处理该范围内的数据：

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>仅获取最新记录

如果只想返回最新记录，请按以下查询中所示使用 *top* 运算符，以返回 *traces* 表中记录的最新 10 条记录：

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>筛选记录
如果只想查看与给定条件匹配的日志，请按以下查询中所示使用 *where* 运算符，以便仅返回 _severityLevel_ 值大于 0 的记录：

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>字符串比较
[评估字符串](/azure/kusto/query/datatypes-string-operators)时，通常会使用 `has` 而不是 `contains` 来查找完整令牌。 `has` 更加有效，因为它不需要查找子字符串。

## <a name="returned-columns"></a>返回的列

使用 [project](/azure/kusto/query/projectoperator) 将所要处理的列集范围缩小为所需的列：

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

尽管可以使用 [extend](/azure/kusto/query/extendoperator) 来计算值和创建自己的列，但基于表列进行筛选通常更加有效。

例如，以下第一个查询基于 _operation\_Name_ 进行筛选，第二个查询创建新的 _subscription_ 列并基于此列进行筛选，前者比后者更有效：

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>使用联接
使用 [join](/azure/kusto/query/joinoperator) 运算符时，请在查询的左侧选择行数更少的表。


## <a name="next-steps"></a>后续步骤
若要详细了解查询的最佳做法，请参阅[查询最佳做法](/azure/kusto/query/best-practices)。