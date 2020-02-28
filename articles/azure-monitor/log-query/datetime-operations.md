---
title: 在 Azure Monitor 日志查询中使用日期时间值 | Microsoft Docs
description: 介绍如何在 Azure Monitor 日志查询中使用日期和时间数据。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655372"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>在 Azure Monitor 日志查询中使用日期时间值

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-portal.md)和[查询入门](get-started-queries.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本文介绍如何在 Azure Monitor 日志查询中使用日期和时间数据。


## <a name="date-time-basics"></a>日期时间基本信息
Kusto 查询语言主要具有两种与日期和时间相关的数据类型：日期/时间和时间跨度。 所有日期均以 UTC 表示。 尽管支持多种日期时间格式，但首选 ISO8601 格式。 

时间跨度的表示形式是十进制后面接时间单位：

|简写   | 时间单位    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|毫秒          | millisecond  |
|微秒 | 微秒  |
|时钟周期        | 纳秒   |

可使用 `todatetime` 运算符强制转换字符串，从而创建日期/时间。 例如，要查看在特定时间范围内发送的 VM 检测信号，使用指定时间范围的 `between` 运算符。

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

还有一个常见的情况是将日期时间与当前时间进行比较。 例如，要查看过去两分钟内的所有检测信号，可结合使用 `now` 运算符和表示两分钟的时间跨度：

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

还可通过快捷方式使用此函数：
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

但是，最简短且可读性最强的方式是使用 `ago` 运算符：
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

假设你不知道开始和结束时间，但知道开始时间和持续时间。 可按如下方式重写查询：

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>转换时间单位
你可能希望以非默认时间单位表示日期/时间或时间跨度。 例如，假设要检查过去 30 分钟内的错误事件，且需要一个显示事件发生时间的计算列：

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` 列包含以下值：“00:09:31.5118992”，这意味着它们的格式为 hh:mm:ss.fffffff。 如果要将这些值的格式设置为自开始时间以来的 `numver` 分钟数，则用该值除以“1 分钟”：

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>按时间间隔进行聚合和分桶
还有一种常见的情况是，需要按特定时间粒度获取某一时间段内的统计数据。 为此，可在 summarize 子句中使用 `bin` 运算符。

使用以下查询获取过去半小时内每 5 分钟发生的事件数：

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

此查询会生成以下表：  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

还可使用函数（例如 `startofday`）来创建结果存储桶：

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

此查询会生成以下结果：

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>时区
由于所有日期/时间值都以 UTC 表示，因此将这些值转换为本地时区通常很有用。 例如，使用此计算将 UTC 转换为 PST 时间：

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>相关函数

| 类别 | Functions |
|:---|:---|
| 转换数据类型 | [system.xml.xmlconvert.todatetime](/azure/kusto/query/todatetimefunction)  [system.xml.xmlconvert.totimespan](/azure/kusto/query/totimespanfunction)  |
| 将值舍入到箱大小 | [bin](/azure/kusto/query/binfunction) |
| 获取特定的日期或时间 | [前](/azure/kusto/query/agofunction) [现在](/azure/kusto/query/nowfunction)   |
| 获取部分值 | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| 获取相对日期值  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth startofyear](/azure/kusto/query/startofmonthfunction) [](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>后续步骤
请参阅有关将 [Kusto 查询语言](/azure/kusto/query/)与 Azure Monitor 日志数据配合使用的其他课程：

- [字符串操作](string-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [高级查询编写](advanced-query-writing.md)
- [联接](joins.md)
- [图表](charts.md)
