---
title: Azure Log Analytics 中的高级聚合 | Microsoft Docs
description: 介绍一些适用于 Log Analytics 查询的较高级聚合选项。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 661ff7c07ba2bb17eb5830b38bb39e1c3e80bb55
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2018
ms.locfileid: "45602899"
---
# <a name="advanced-aggregations-in-log-analytics-queries"></a>Log Analytics 查询中的高级聚合

> [!NOTE]
> 在完成本课程之前，应先完成 [Log Analytics 查询中的聚合](./aggregations.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本文介绍一些适用于 Log Analytics 查询的较高级聚合选项。

## <a name="generating-lists-and-sets"></a>生成列表和集
可以使用 `makelist` 根据特定列中的值顺序创建数据透视图。 例如，你可能想要浏览计算机上发生的最常见有序事件。 实际上，可以根据每台计算机上 EventID 的顺序创建数据透视图。 

```KQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` 根据数据的传入顺序生成列表。 若要以最旧到最新的顺序排序事件，请在 order 语句中使用 `asc`，而不要使用 `desc`。 

创建只包含非重复值的列表也很有用。 此列表称为“集”，它是使用 `makeset` 生成的：

```KQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

与 `makelist` 一样，`makeset` 也能处理有序数据，并可基于行的传入顺序生成数组。

## <a name="expanding-lists"></a>展开列表
`makelist` 或 `makeset` 的反向操作是 `mvexpand`，该操作将值列表展开为单独的行。 它可以展开任意数目的动态列（包括 JSON 和数组）。 例如，可以在“检测信号”表中检查在过去一小时发送了检测信号的计算机中发送数据的解决方案：

```KQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```
| Computer | 解决方案 | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... | ... |

使用 `mvexpand` 可以显示单独行（而不是逗号分隔列表）中的每个值：

Heartbeat | where TimeGenerated > ago(1h) | project Computer, split(Solutions, ",") | mvexpand Solutions
```
| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... | ... |
```

然后，可以再次使用 `makelist` 将项分组到一起，这次会看到每个解决方案的计算机列表：

```KQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|解决方案 | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>处理缺失的 bin
需要为缺失的 bin 填写默认值时，非常适合应用 `mvexpand`。例如，假设你要通过浏览特定计算机的检测信号来查看该计算机的正常运行时间。 此外，你想要查看 _category_ 列中检测信号的源。 通常，我们会使用一个简单的 summarize 语句，如下所示：

```KQL
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```
| 类别 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理 | 2017-06-06T17:00:00Z | 15 |
| 直接代理 | 2017-06-06T18:00:00Z | 60 |
| 直接代理 | 2017-06-06T20:00:00Z | 55 |
| 直接代理 | 2017-06-06T21:00:00Z | 57 |
| 直接代理 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

不过，在这些结果中，与“2017-06-06T19:00:00Z”关联的存储桶缺失，因为在该小时没有任何检测信号数据。 使用 `make-series` 函数将默认值赋给空存储桶。 这会针对每个类别生成包含两个额外数组列的行，其中一个列包含值，另一个列包含匹配时间存储桶：

```KQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| 类别 | count_ | TimeGenerated |
|---|---|---|
| 直接代理 | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*count_* 数组的第三个元素预期为 0，_TimeGenerated_ 数组中包含“2017-06-06T19:00:00.0000000Z”的匹配时间戳。 不过，此数组的格式难以阅读。 使用 `mvexpand` 展开数组，并生成 `summarize` 所生成的相同格式输出：

```KQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```
| 类别 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理 | 2017-06-06T17:00:00Z | 15 |
| 直接代理 | 2017-06-06T18:00:00Z | 60 |
| 直接代理 | 2017-06-06T19:00:00Z | 0 |
| 直接代理 | 2017-06-06T20:00:00Z | 55 |
| 直接代理 | 2017-06-06T21:00:00Z | 57 |
| 直接代理 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>将结果缩小为一组元素：`let`, `makeset`, `toscalar`, `in`
一种常见的方案是基于一组条件选择某些特定实体的名称，然后将不同的数据集筛选为该实体集。 例如，可以查找已知缺少更新的计算机，并识别这些计算机调用的 IP：


```KQL
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>后续步骤

有关 Log Analytics 查询语言的用法，请参阅其他课程：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [联接](joins.md)
- [图表](charts.md)