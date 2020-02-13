---
title: Azure Monitor 视图设计器到工作簿磁贴转换
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5bb02edce4a3aef30f8f9528a846c99d6d8d3b39
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170859"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor 视图设计器磁贴转换
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它允许您创建自定义视图来帮助您通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 它们将被分段并替换为提供其他功能的工作簿。 本文详细介绍了如何将不同的磁贴转换为工作簿。

## <a name="donut--list-tile"></a>环形 & 列表磁贴

![环形列表](media/view-designer-conversion-tiles/donut-list.png)

在工作簿中重新创建环形 & 列表磁贴涉及两个不同的可视化效果。 对于环形部分，有两个选项。
对于这两种方法，首先选择 "**添加查询**"，然后将原始查询从视图设计器粘贴到单元格中。

**选项1：** 从 "**可视化**" 下拉列表中选择 "**饼图**"： ![饼图可视化菜单](media/view-designer-conversion-tiles/pie-chart.png)

**选项2：** 从 "**可视化**" 下拉列表中选择 "**按查询设置**"，并将 `| render piechart` 添加到查询中：

 ![可视化菜单](media/view-designer-conversion-tiles/set-by-query.png)

**示例**

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

更新的查询
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

若要创建列表和启用迷你图，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

下面的示例演示如何在工作簿中重新解释环形 & 列表磁贴：

![环形列表工作簿](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>折线图 & 列表磁贴
![折线图列表](media/view-designer-conversion-tiles/line-list.png) 

若要重新创建折线图部分，请按如下所示更新查询：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type

Updated query
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

有两个用于可视化折线图的选项

**选项1：** 从**可视化**下拉列表中选择 "**折线图**"：
 
 ![折线图菜单](media/view-designer-conversion-tiles/line-visualization.png)

**选项2：** 从 "**可视化**" 下拉列表中选择 "**按查询设置**"，并将 `| render linechart` 添加到查询中：

 ![可视化菜单](media/view-designer-conversion-tiles/set-by-query.png)

**示例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

若要创建列表和启用迷你图，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

下面的示例演示如何在工作簿中重新解释折线图 & 列表磁贴：

![折线图列表工作簿](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>编号 & 列表磁贴

 ![磁贴列表](media/view-designer-conversion-tiles/tile-list-example.png)

对于 "数字" 磁贴，按如下所示更新查询：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

更新的查询
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

将可视化效果下拉列表更改为**磁贴**，然后选择 "**磁贴设置**"。
 ![磁贴可视化](media/view-designer-conversion-tiles/tile-visualization.png)

将 "**标题**" 部分留空，并选择 "**左**"。 将**使用列**的值更改为**Count**，将**列呈现**器更改为**大数字**：

![磁贴设置](media/view-designer-conversion-tiles/tile-settings.png)

 
若要创建列表和启用迷你图，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

下面的示例演示如何在工作簿中重新解释数字 & 列表磁贴：

![数字列表工作簿](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>时间线和列表

 ![时间线列表](media/view-designer-conversion-tiles/time-list.png)

对于时间线，请按如下所示更新查询：

原始查询
```KQL
search * 
| sort by TimeGenerated desc
```

更新的查询
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

有两个选项可将查询可视化为条形图：

**选项1：** 从**可视化**下拉列表中选择 "**条形图**"： ![Barchart 可视化](media/view-designer-conversion-tiles/bar-visualization.png)
 
**选项2：** 从 "**可视化**" 下拉列表中选择 "**按查询设置**"，并将 `| render barchart` 添加到查询中：

 ![可视化菜单](media/view-designer-conversion-tiles/set-by-query.png)

 
若要创建列表和启用迷你图，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

下面的示例演示了时间线 & 列表磁贴在工作簿中的重新解释：

![时间线列表工作簿](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>后续步骤

- [工作簿的视图设计器过渡概述](view-designer-conversion-overview.md)
