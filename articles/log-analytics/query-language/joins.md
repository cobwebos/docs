---
title: Azure Log Analytics 查询中的联接 | Microsoft Docs
description: 本文包含的课程介绍了如何在 Log Analytics 查询语言中使用联接。
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
ms.openlocfilehash: 39a461a27e8d9d6d1b9712449586bfabf6124d22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989441"
---
# <a name="joins-in-log-analytics-queries"></a>Log Analytics 查询中的联接

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-analytics-portal.md)和[查询入门](get-started-queries.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

利用联接，可在同一查询中分析来自多个表的数据。 它们通过匹配指定列的值来合并两个数据集的行。


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

在本例中，第一个数据集筛选所有登录事件。 这与筛选所有注销事件的第二个数据集进行联接。 投影列包括计算机、帐户、TargetLogonId 和 TimeGenerated。 数据集由共享列 TargetLogonId 进行关联。 输出实为具有登录时间和注销时间的记录，每个关联对应一条记录。

如果这两个数据集具有名称相同的列，则右侧数据集的列将附加索引号，因此本例中，结果显示为 TargetLogonId 具有左侧表的值，而 TargetLogonId1 具有右侧表的值。 在此情况下，已使用 `project-away` 运算符删除第二个 _TargetLogonId1_。

> [!NOTE]
> 为提高性能，请使用 `project` 运算符仅保留已联接的数据集的相关列。


请使用以下语法来联接两个数据集，所联接的键在两个表之间具有不同的名称：
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>查找表
联接的常见用法是通过 `datatable` 静态映射值，这有助于将结果转换为更易呈现的形式。 例如，使用每个事件 ID 的事件名称来丰富安全事件数据。

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![与数据表联接](media/joins/dim-table.png)

## <a name="join-kinds"></a>联接类型
使用 kind 参数来指定联接类型。 如下表所述，每种类型在给定表的记录之间执行不同的匹配。

| 联接类型 | Description |
|:---|:---|
| innerunique | 这是默认的联接模式。 首先找到左表中匹配列的值，并删除重复值。  然后将该组唯一值集与右表匹配。 |
| 内部 | 结果中只包括在两个表中匹配的记录。 |
| leftouter | 结果中包含左表中的所有记录和右表中的匹配记录。 不匹配的输出属性包含 null。  |
| leftanti | 结果中包含左表中没有右表匹配项的记录。 结果表只包含左表中的列。 |
| leftsemi | 结果中包含左表中具有右表匹配项的记录。 结果表只包含左表中的列。 |


## <a name="best-practices"></a>最佳实践

要实现最佳性能，请注意以下几点：

- 在每个表上使用时间筛选器，减少为联接计算得出的最小记录量。
- 使用 `where` 和 `project` 可减少联接前输入表中的行数和列数。
* 如果某个表始终小于另一个表，则将其用作联接的左侧。


## <a name="next-steps"></a>后续步骤
有关 Log Analytics 查询语言的用法，请参阅其他课程：

- [字符串操作](string-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [高级查询编写](advanced-query-writing.md)
- [图表](charts.md)