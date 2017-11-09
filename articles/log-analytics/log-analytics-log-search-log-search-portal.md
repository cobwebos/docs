---
title: "在 Azure Log Analytics 中使用日志搜索门户 | Microsoft Docs"
description: "本文所含教程介绍如何使用日志搜索门户创建日志搜索并分析存储在 Log Analytics 工作区中的数据。  本教程包括运行一些简单的查询，返回不同类型的数据和分析结果。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 6fc556ceb34cde26d5f3789a2397cdaa34b0b84d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>在 Azure Log Analytics 中使用日志搜索门户创建日志搜索

> [!NOTE]
> 本文介绍 Azure Log Analytics 中使用新查询语言的日志搜索门户。  若要了解有关新语言的详细信息和工作区升级过程，请参阅[将 Azure Log Analytics 工作区升级到新日志搜索](log-analytics-log-search-upgrade.md)。  
>
> 如果尚未将工作区升级到新查询语言，应参考[在 Log Analytics 中使用日志搜索查找数据](log-analytics-log-searches.md)，了解有关日志搜索门户当前版本的信息。

本文所含教程介绍如何使用日志搜索门户创建日志搜索并分析存储在 Log Analytics 工作区中的数据。  本教程包括运行一些简单的查询，返回不同类型的数据和分析结果。  它侧重于日志搜索门户中用于修改查询而不是直接对其进行修改的功能。  有关直接编辑查询的详细信息，请参阅[查询语言参考](https://go.microsoft.com/fwlink/?linkid=856079)。

若要在高级分析门户而非日志搜索门户中创建搜索，请参阅 [Analytics 门户入门](https://go.microsoft.com/fwlink/?linkid=856587)。  这两个门户均使用相同的查询语言访问 Log Analytics 工作区中的相同数据。

## <a name="prerequisites"></a>先决条件
本教程假定学习者已具有至少连接了一个源的 Log Analytics 工作区，该源用于生成供查询分析的数据。  

- 如果没有工作区，则可以使用 [Log Analytics 工作区入门](log-analytics-get-started.md)中介绍的过程创建一个可用的工作区。
- 至少将一个 [Windows 代理](log-analytics-windows-agents.md)或一个 [Linux 代理](log-analytics-linux-agents.md)连接到工作区。  

## <a name="open-the-log-search-portal"></a>打开日志搜索门户
首先打开日志搜索门户。  可以在 Azure 门户或 OMS 门户中访问该门户。

1. 打开 Azure 门户。
2. 导航到 Log Analytics 并选择工作区。
3. 可选择“日志搜索”留在 Azure 门户中，或通过选择“OMS 门户”，然后单击“日志搜索”按钮，启动 OMS 门户。

![日志搜索按钮](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>创建简单的搜索
用于检索某些要使用的数据的最快方法是使用一个简单查询，它可返回表中的所有记录。  如果将任何 Windows 或 Linux 客户端连接到自己的工作区，将具有 Event（事件）(Windows) 或 Syslog (Linux) 表中的数据。

在搜索框中键入以下任一查询，然后单击“搜索”按钮。  

```
Event
```
```
Syslog
```

数据会返回到默认列表视图中，并可看到返回的总记录条数。

![简单查询](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

仅显示每条记录的前几个属性。  单击“显示更多”可显示特定记录的所有属性。

![记录详细信息](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>设置时间范围
Log Analytics 收集的每条记录都具有“TimeGenerated”属性，其中包含记录的创建日期和时间。  日志搜索门户中的查询只返回其所具有的“TimeGenerated”处于屏幕左侧显示的时间范围之内的记录。  

通过选择下拉列表或修改滑块，可以更改时间筛选器。  滑块显示一个条形图，其中显示范围内每个时间段的相对记录数。  该时间段因范围而异。

默认时间范围是“1 天”。  将此值更改为“7 天”，记录总数会增加。

![日期时间范围](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>筛选查询的结果
屏幕左侧是“筛选器”窗格，可用于向查询添加筛选而无需直接修改查询。  会显示所返回记录的一些属性，及属性的前 10 个值和记录计数。

如果正在处理“事件”，请选择“EVENTLEVELNAME”下“错误”旁边的复选框。   如果正在处理“Syslog”，请选择“SEVERITYLEVEL”下“err”旁边的复选框。  这会将查询更改为以下任一内容，将结果限制为错误事件。

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![筛选器](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

通过从其中一条记录上的“属性”菜单中选择“添加到筛选器”，将属性添加到“筛选器”窗格中。

![“添加到筛选器”菜单](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

通过为一个记录（具有要筛选的值）选择“属性”菜单中的“筛选器”，可以设置相同的筛选器。  

对于名称为蓝色的属性，只具有“筛选器”属性。  这些是“可搜索”字段，针对搜索条件编制了索引。  灰色字段是“自定义文本搜索”字段，只具有“显示引用”选项。  此选项返回任一属性中具有该值的记录。

![“筛选器”菜单](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

通过在“记录”菜单中选择“分组依据”选项，可以根据单个属性对结果进行分组。  这会向查询中添加一个 [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 运算符，会将结果显示为一个图表。  可以根据多个属性进行分组，但需要直接编辑查询。  选择“计算机”属性旁边的“记录”菜单，并选择“按‘计算机’分组”。  

![按计算机分组](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>处理结果
日志搜索门户具有用于处理查询结果的各种功能。  可以对结果进行排序、筛选和分组，由此对数据进行分析，而无需修改实际查询。  默认情况下，查询结果不会进行排序。

若要以表的形式查看数据 - 这种形式可提供其他用于筛选和排序的选项，请单击“表”。  

![“表”视图](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

单击某记录旁的箭头，查看该记录的详细信息。

![对结果进行排序](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

通过单击任意字段的列标题，对该字段进行排序。

![对结果进行排序](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

通过单击“筛选器”按钮并提供筛选条件，按列中的特定值筛选结果。

![筛选结果](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

通过将列的列标题拖到结果的顶端，根据该列进行分组。  通过将多个列拖到顶部，可以根据多个字段进行分组。

![对结果进行分组](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>处理性能数据
用于 Windows 和 Linux 代理的性能数据存储在 Log Analytics 工作区的“性能”表中。  性能记录看起来与任何其他记录一样，可编写一个返回所有性能记录的简单查询，与处理事件的方式一样。

```
Perf
```

![性能数据](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

返回所有性能对象和计数器的数百万条记录并不是很有用。  但可使用上面使用的同一方法来筛选数据，或只需直接在“日志搜索”框中键入以下查询。  这样只会返回 Windows 和 Linux 计算机的处理器利用率记录。

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![处理器利用率](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

这会将数据限制到一个特定的计数器，但数据的显示方式仍不会很有用。  可以折线图的形式显示数据，但首先需要按“计算机”和“TimeGenerated”对数据进行分组。  若要按多个字段进行分组，需要直接修改查询，所以请将查询修改为以下内容。  这会在“CounterValue”属性上使用 [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) 函数来计算每小时的平均值。

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![性能数据图表](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

现已对数据进行适当分组，通过添加 [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) 运算符，可在可视图表中显示数据。  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![折线图](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>后续步骤

- 在 [Analytics 门户入门](https://go.microsoft.com/fwlink/?linkid=856079)中了解有关 Log Analytics 查询语言的详细信息。
- 使用[高级分析门户](https://go.microsoft.com/fwlink/?linkid=856587)逐步学习本教程，借助该门户可以运行与日志搜索门户相同的查询并访问相同的数据。
