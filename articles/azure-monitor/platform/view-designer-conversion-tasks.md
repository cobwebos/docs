---
title: Azure 监视器视图设计器到工作簿转换常见任务
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658738"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>查看设计器以工作簿转换常见任务
[视图设计器](view-designer.md)是 Azure 监视器的一项功能，允许您创建自定义视图，以帮助您在日志分析工作区中可视化数据，包括图表、列表和时间线。 它们正在逐步淘汰，代之以提供额外功能的工作簿。 本文详细介绍了将视图转换为工作簿时常见的任务。


## <a name="quickstart-with-preset-view-designer-templates"></a>使用预设视图设计器模板快速入门

日志分析工作区中的工作簿已经具有模板，以匹配视图设计器中的某些视图。 在 **"查看设计器指南"** 类别下，选择 **"查看设计器过渡指南"** 以了解您的选项或选择其中一个预设模板。

![示例模板](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>启用时间范围筛选器
视图设计器具有内置的默认时间范围筛选器，但是，在工作簿中，默认情况下不会启用此设置。 工作簿允许用户创建自己的时间范围筛选器，这些筛选器可能更适用于其数据日志。 生成筛选器的步骤如下所示：

选择"**添加参数"** 选项。 默认**样式**设置为 *"丸"。*

![添加 Param](media/view-designer-conversion-tasks/add-param.png)

 选择"**添加参数**"按钮。

![添加参数](media/view-designer-conversion-tasks/add-parameter.png)

从侧边栏菜单中，在**参数名称**文本框中键入*TimeRange*。 将**参数类型**设置为*时间范围选取器*。 选择"**必需"** 复选框。

![参数菜单](media/view-designer-conversion-tasks/parameter-menu.png)

将参数保存在侧边栏菜单的左上角。 默认情况下，您可以将下拉列表保留为*未设置*，或选择默认**时间范围**值，如*24 小时*。 选择 **"完成编辑**"。

参数可以通过{}在参数名称周围添加大括号在查询中使用。 有关参数的更多详细信息，请参阅[有关参数的工作簿文档](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)。

## <a name="updating-queries-with-the-timerange-parameter"></a>使用时间范围参数更新查询

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>选项 1：从时间范围下拉列表中选择时间范围

![时间参数](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>选项 2：更新日志查询

在查询中添加行：`| where TimeGenerated {TimeRange}`如以下示例所示：

原始查询
```KQL
search * 
| summarize count() by Type
```

更新查询
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>包括列表
大多数视图设计器视图都包含一个列表，您可以在工作簿中重现此标准列表。

![磁贴列表](media/view-designer-conversion-tasks/tile-list.png)

通过单击"从单元格选项**添加查询**"来添加可视化效果。

![添加 Param](media/view-designer-conversion-tasks/add-param.png)

视图设计器使用与原始示例中的语法匹配的默认查询。 可以通过将查询更改为更新的窗体来更新，如以下示例所示：

原始查询
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

更新查询
```KQL
search * 
| summarize Count = count() by Type
```

这将生成如下所示的列表：

![列表示例](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>启用火花线
网格的一个常见功能是添加火花线来汇总随时间变化的各种数据模式。 视图设计器为所有列表提供**启用 Sparklines**功能，工作簿也是如此。 要在数据中包含与视图设计器匹配的火花线，请将数据与原始查询联接，如以下示例所示：

原始查询
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

更新查询
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

选择**列设置**。
![列设置](media/view-designer-conversion-tasks/column-settings.png)

将**列渲染器**下拉列表更新为*Spark 区域*。
![迷你图](media/view-designer-conversion-tasks/sparkline.png)

保存设置并再次运行查询以更新表以包含一个火花线。

生成的网格将类似于以下内容：Sparkline![示例](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>高级单元格设置
要镜像视图设计器，可以执行更改工作簿单元格大小或向日志添加引脚和外部链接等任务。

要访问 **"高级设置"，** 请选择每个单元底部的齿轮图标。

![高级设置](media/view-designer-conversion-tasks/advanced-settings.png)

这将显示包含各种选项的菜单：

![高级设置设置](media/view-designer-conversion-tasks/advanced-settings-settings.png)

要添加指向外部查询的引脚和链接，请选择相应的复选框。 要向单元格添加标题，请将所需的标题键入 **"图表标题**"部分。

默认情况下，任何工作簿单元格都设置为占用整个页面宽度，但您可以通过在 **"高级设置"** 菜单的 **"样式"** 选项卡下向下缩放单元格来调整此页

![高级设置样式](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>其他参数
选择 **"添加参数**"在工作簿中创建新参数。 

要选择订阅，请在侧菜单中的 **"参数名称**"字段中键入 *"订阅"，* 然后从 **"参数类型**"下拉列表中选择 *"订阅选取器*"

![订阅菜单](media/view-designer-conversion-tasks/subscription-filter.png)

要选择"资源"，请在侧菜单中的 **"参数名称**"字段中键入 *"资源"，* 然后从 **"参数类型**"下拉下下下下下下选点选择 *"资源选取器*"。

![资源菜单](media/view-designer-conversion-tasks/resource-filter.png)

这将插入下拉列表，以便您访问各种订阅和资源。

![订阅资源下拉列表](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>后续步骤
- [磁贴转换](view-designer-conversion-tiles.md)
