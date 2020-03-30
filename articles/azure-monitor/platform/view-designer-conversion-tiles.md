---
title: Azure 监视器视图设计器到工作簿磁贴转换
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658620"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Azure 监视器视图设计器磁贴转换
[视图设计器](view-designer.md)是 Azure 监视器的一项功能，允许您创建自定义视图，以帮助您在日志分析工作区中可视化数据，包括图表、列表和时间线。 它们正在逐步淘汰，代之以提供额外功能的工作簿。 本文提供了将不同磁贴转换为工作簿的详细信息。

## <a name="donut--list-tile"></a>圆环&列表磁贴

![甜甜圈列表](media/view-designer-conversion-tiles/donut-list.png)

在工作簿中重新创建圆环&列表磁贴涉及两个单独的可视化效果。 对于圆环部分，有两个选项。
对于这两个开始选择**添加查询**，并将从视图设计器中的原始查询粘贴到单元格中。

**选项 1：** 从**可视化下**拉菜单中选择**饼图**：![饼图可视化菜单](media/view-designer-conversion-tiles/pie-chart.png)

**选项 2：** 从**可视化**下拉列表中选择**按查询设置**并添加到`| render piechart`查询：

 ![可视化菜单](media/view-designer-conversion-tiles/set-by-query.png)

**示例**

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

更新查询
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

有关创建列表和启用 Sparklines，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

下面是如何在工作簿中重新解释圆环&列表磁贴的示例：

![甜甜圈列表工作簿](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>折线图&列表磁贴
![折线图列表](media/view-designer-conversion-tiles/line-list.png) 

要重新创建折线图部分，请按如下方式更新查询：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新查询
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

有两个选项用于可视化折线图

**选项 1：** 从**可视化**下拉列表中选择**折线图**：
 
 ![折线图菜单](media/view-designer-conversion-tiles/line-visualization.png)

**选项 2：** 从**可视化**下拉列表中选择**按查询设置**并添加到`| render linechart`查询：

 ![可视化菜单](media/view-designer-conversion-tiles/set-by-query.png)

**示例**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

有关创建列表和启用 Sparklines，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

以下是如何在工作簿中重新解释折线图&列表磁贴的示例：

![折线图列表工作簿](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>编号&列表磁贴

 ![磁贴列表](media/view-designer-conversion-tiles/tile-list-example.png)

对于数字磁贴，请按如下方式更新查询：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

更新查询
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

将"可视化"下拉菜单更改为**磁贴**，然后选择 **"磁贴设置**"。
 ![磁贴可视化](media/view-designer-conversion-tiles/tile-visualization.png)

将 **"标题"** 部分留空，然后选择 **"左侧**"。 将 **"使用"列的值更改为****计数**，将**列渲染器**更改为 **"大数**" ：

![磁贴设置](media/view-designer-conversion-tiles/tile-settings.png)

 
有关创建列表和启用 Sparklines，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

下面是如何在工作簿中重新解释数字&列表磁贴的示例：

![编号列表工作簿](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>时间线和列表

 ![时间线列表](media/view-designer-conversion-tiles/time-list.png)

对于时间线，请按如下方式更新查询：

原始查询
```KQL
search * 
| sort by TimeGenerated desc
```

更新查询
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

有两个选项用于将查询可视化为条形图：

**选项 1：** 从**可视化下**拉列表中选择![**条形图**：条形图可视化](media/view-designer-conversion-tiles/bar-visualization.png)
 
**选项 2：** 从**可视化**下拉列表中选择**按查询设置**并添加到`| render barchart`查询：

 ![可视化菜单](media/view-designer-conversion-tiles/set-by-query.png)

 
有关创建列表和启用 Sparklines，请参阅有关[常见任务](view-designer-conversion-tasks.md)的文章。

以下是如何在工作簿中重新解释时间线&列表磁贴的示例：

![时间轴列表工作簿](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>后续步骤

- [视图设计器到工作簿转换的概述](view-designer-conversion-overview.md)
