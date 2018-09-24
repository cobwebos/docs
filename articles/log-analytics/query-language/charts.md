---
title: 通过 Azure Log Analytics 查询创建图表和关系图 |Microsoft Docs
description: 介绍在 Azure Log Analytics 中以不同的方式显示数据时的各种可视化效果。
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
ms.openlocfilehash: e63345c0265d52fdd80fe4542efb7f13324926cf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989612"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>通过 Log Analytics 查询创建图表和关系图

> [!NOTE]
> 在学习本课程之前，需完成 [Log Analytics 查询中的高级聚合](advanced-aggregations.md)。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本文在 Azure Log Analytics 中以不同的方式显示数据时的各种可视化效果。

## <a name="charting-the-results"></a>绘制结果图表
首先查看在过去 1 小时内，每个操作系统占用了多少台计算机：

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

默认情况下，以表形式显示结果：

![表](media/charts/table-display.png)

要优化视图效果，请选择“图表”，然后选择“饼图”选项以直观显示结果：

![饼图](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>时间图表
显示处理器时间的平均值、第 50 位百分值和第 95 位百分位值（按 1 小时的箱数计）。 查询将生成多个序列，然后你可选择要在时间图表中显示的序列：

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

选择“折线图”显示选项：

![折线图](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>参考线

参考线可帮助你轻松识别指标是否超出特定阈值。 要向图表添加一行，请用常数列扩展数据集：

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![参考线](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>多个维度
`summarize` 的 `by` 字句中的多个表达式在结果中创建多个行，每个值组合对应一行。

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

将结果看作图表时，它使用 `by` 子句的第一列。 以下示例演示使用 EventID. 的堆积柱形图。 维度必须为 `string` 类型，因此在本例中，EventID 要强制转换为字符串。 

![条形图 EventID](media/charts/charts-and-diagrams-multiDimension1.png)

可选择列名下拉列表切换查看不同列名。 

![条形图 AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>后续步骤
有关 Log Analytics 查询语言的用法，请参阅其他课程：

- [字符串操作](string-operations.md)
- [时间和日期操作](datetime-operations.md)
- [聚合函数](aggregations.md)
- [高级聚合](advanced-aggregations.md)
- [JSON 和数据结构](json-data-structures.md)
- [高级查询编写](advanced-query-writing.md)
- [联接](joins.md)