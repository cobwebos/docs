---
title: Azure Monitor 视图设计器到工作簿磁贴转换
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658620"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure Monitor 视图设计器磁贴转换
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它让你能够创建自定义视图，帮助你通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 这些元素已逐步淘汰，取而代之的是除这些功能外还可提供其他功能的工作簿。 本文详细介绍如何将不同的磁贴转换为工作簿。

## <a name="donut--list-tile"></a>圆环图和列表磁贴

![圆环图列表](media/view-designer-conversion-tiles/donut-list.png)

在工作簿中重新创建圆环图和列表磁贴涉及两种不同的可视化效果。 对于圆环图部分，有两个选项。
对于这两个选项，首先选择“添加查询”，然后将原始查询从视图设计器粘贴到单元格中。

选项 1：从“可视化效果”下拉列表中选择“饼图” ：![饼图可视化效果菜单](media/view-designer-conversion-tiles/pie-chart.png)

选项 2：从“可视化效果”下拉列表中选择“按查询设置”，然后将 `| render piechart` 添加到查询 ：

 ![可视化效果菜单](media/view-designer-conversion-tiles/set-by-query.png)

**示例**

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

更新后的查询
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

有关创建列表和启用迷你图的说明，请参阅关于[常见任务](view-designer-conversion-tasks.md)的文章。

下面是在工作簿中重新解释圆环图和列表磁贴的效果示例：

![圆环图列表工作簿](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>折线图和列表磁贴
![折线图列表](media/view-designer-conversion-tiles/line-list.png) 

若要重新创建折线图部分，请更新查询，如下所示：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新后的查询
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

有两个可用于将折线图可视化的选项

选项 1：从“可视化效果”下拉列表中选择“折线图” ：
 
 ![折线图菜单](media/view-designer-conversion-tiles/line-visualization.png)

选项 2：从“可视化效果”下拉列表中选择“按查询设置”，然后将 `| render linechart` 添加到查询 ：

 ![可视化效果菜单](media/view-designer-conversion-tiles/set-by-query.png)

**示例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

有关创建列表和启用迷你图的说明，请参阅关于[常见任务](view-designer-conversion-tasks.md)的文章。

下面是在工作簿中重新解释折线图和列表磁贴的效果示例：

![折线图列表工作簿](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>数字和列表磁贴

 ![磁贴列表](media/view-designer-conversion-tiles/tile-list-example.png)

对于数字磁贴，请按如下所示更新查询：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

更新后的查询
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

将“可视化效果”下拉列表更改为“磁贴”，然后选择“磁贴设置” 。
 ![磁贴可视化效果](media/view-designer-conversion-tiles/tile-visualization.png)

将“标题”部分留空，然后选择“左” 。 将“使用列”的值更改为“计数”，并将“列呈现器”的值更改为“大数字”   ：

![磁贴设置](media/view-designer-conversion-tiles/tile-settings.png)

 
有关创建列表和启用迷你图的说明，请参阅关于[常见任务](view-designer-conversion-tasks.md)的文章。

下面是在工作簿中重新解释数字和列表磁贴的效果示例：

![数字列表工作簿](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>时间线和列表

 ![时间线列表](media/view-designer-conversion-tiles/time-list.png)

对于时间线，请按如下所示更新查询：

原始查询
```KQL
search * 
| sort by TimeGenerated desc
```

更新后的查询
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

有两个可用于将查询可视化为条形图的选项：

选项 1：从“可视化效果”下拉列表中选择“条形图” ：![可视化效果](media/view-designer-conversion-tiles/bar-visualization.png)
 
选项 2：从“可视化效果”下拉列表中选择“按查询设置”，然后将 `| render barchart` 添加到查询 ：

 ![可视化效果菜单](media/view-designer-conversion-tiles/set-by-query.png)

 
有关创建列表和启用迷你图的说明，请参阅关于[常见任务](view-designer-conversion-tasks.md)的文章。

下面是在工作簿中重新解释时间线和列表磁贴的效果示例：

![时间线列表工作簿](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>后续步骤

- [视图设计器到工作簿转换概述](view-designer-conversion-overview.md)
