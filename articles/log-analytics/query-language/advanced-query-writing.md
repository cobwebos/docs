---
title: Azure Log Analytics 中的高级查询 | Microsoft Docs
description: 本文提供有关在 Log Analytics 中使用 Analytics 门户编写查询的教程。
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
ms.openlocfilehash: f7594b7d1eb7d41508be435cdd0a6203433727c1
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603050"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>在 Log Analytics 中编写高级查询

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-analytics-portal.md)和[查询入门](get-started-queries.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>通过 let 重用代码
使用 `let` 将结果分配给变量，并稍后在查询中引用它：

```KQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

也可向变量分配常数值。 它支持为每次执行查询时需要更改的字段设置参数。 根据需要修改这些参数。 例如，要在给定的时间段计算可用磁盘空间和可用内存（按百分位数计）：

```KQL
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

这样，即可在下次运行查询时更改结束时间的开头。

### <a name="local-functions-and-parameters"></a>本地函数和参数
使用 `let` 语句创建可在同一查询中使用的函数。 例如，定义一个采用日期/时间字段（按 UTC 格式）并将其转换为标准美国格式的函数。 

```KQL
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>函数
可使用函数别名保存查询，以便它可被其他查询引用。 例如，以下标准查询返回昨天报告的所有缺失的安全更新：

```KQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

可将此查询另存为函数，并为其提供别名（如 security_updates_last_day）。 然后，即可在其他查询中使用它来搜索与 SQL 相关的必需安全更新：

```KQL
security_updates_last_day | where Title contains "SQL"
```

要将查询保存为函数，请在门户中选择“保存”按钮，并将“另存为”更改为“函数”。 函数别名可以包含字母、数字或下划线，但必须以字母或下划线开头。

> [!NOTE]
> 可在 Log Analytics 查询中保存功能，但此功能目前不适用于 Application Insights 查询。


## <a name="print"></a>Print
`print` 将返回一个单列单行的表，其中显示计算结果。 这通常用于需要简单计算的情况。 例如，要在 PST 中查找当前时间并添加具有 EST 的列：

```KQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable` 可以定义一组数据。 你提供一个架构和一组值，然后将表传输到其他任何查询元素。 例如，要创建 RAM 使用量表并计算每小时的平均值：

```KQL
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

创建查找表时，Datatable 构造也非常有用。 例如，要将表数据（如事件 ID）从 SecurityEvent 表映射到其他位置列出的事件类型，请使用 `datatable` 创建包含事件类型的查找表，并将此数据表与 SecurityEvent 数据联接：

```KQL
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
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