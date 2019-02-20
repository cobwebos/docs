---
title: Azure Monitor 日志查询中的聚合 | Microsoft Docs
description: 介绍 Azure Monitor 日志查询中的聚合函数，这些函数提供了有用的数据分析方式。
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 31ff0435487592577915e526abcb97197f591449
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000841"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Azure Monitor 日志查询中的聚合

> [!NOTE]
> 在学习本课程之前，需完成 [Analytics 门户入门](get-started-portal.md)和[查询入门](get-started-queries.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本文介绍 Azure Monitor 日志查询中的聚合函数，这些函数提供了有用的数据分析方式。 这些函数都适用于 `summarize` 运算符，后者生成一个包含输入表中聚合结果的表。

## <a name="counts"></a>计数

### <a name="count"></a>计数
在应用任一筛选器后，计算结果集中的行数。 以下示例返回过去 30 分钟内 Perf 表中的总行数。 结果将在名为“count_”的列中返回，除非为其指定特定名称：


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

时间表可视化效果可用于查看随时间变化的趋势：

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

本例中输出以 5 分钟为间隔显示了 perf 记录计数趋势线：

![计数趋势](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
使用 `dcount` 和 `dcountif` 计算特定列中非重复值的数量。 以下查询计算过去一小时内发送检测信号的非重复计算机的数量：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

要想只计算发送检测信号的 Linux 计算机，请使用 `dcountif`：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>计算子组
要在数据中的子组上执行计数或其他聚合，请使用 `by` 关键字。 例如，要计算每个国家/地区发送检测信号的非重复 Linux 计算机的数量：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|美国    | 19                  |
|加拿大           | 3                   |
|爱尔兰          | 0                   |
|英国   | 0                   |
|荷兰      | 2                   |


要分析更小的数据子组，请在 `by` 部分中添加其他列名称。 例如，想要根据每个 OSType 来计算来自每个国家/地区的非重复计算机的数量：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>百分位数和方差
计算数值时，通常的做法是使用 `summarize avg(expression)` 求其平均值。 平均值受到仅描述少数情况的极值的影响。 要解决该问题，可使用敏感程度较低的函数，例如 `median` 或 `variance`。

### <a name="percentile"></a>百分位数
要查找中值，请使用带值的 `percentile` 函数指定百分位数：

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

此外，可指定不同的百分位数，以获取每一个的聚合结果：

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

这可能会显示出一些计算机 CPU 具有相似的中值，但一些在中值附近稳定，另外一些计算机报告显示 CPU 值高出或低出很多（这意味着它们经历了峰值）。

### <a name="variance"></a>Variance
要直接计算值的方差，请使用标准偏差和方差方法：

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

要分析 CPU 使用量的稳定性，一种很好的方式是将 stdev 与中值计算相结合：

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

请参阅有关将[数据资源管理器查询语言](/azure/kusto/query/)与 Azure Monitor 日志数据配合使用的其他课程：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [高级查询编写](advanced-query-writing.md)
- [联接](joins.md)
- [图表](charts.md)
