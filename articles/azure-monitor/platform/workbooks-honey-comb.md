---
title: Azure Monitor 工作簿蜜标梳理数可视化效果
description: 了解 Azure Monitor 工作簿蜜标梳理数可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: 859240623c5dd3009890cd2633ffb9cccf230080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342663"
---
# <a name="honey-comb-visualizations"></a>蜜标梳理数可视化

蜜标 jessi 允许可选择按分类分组的度量值或类别的高密度视图。 在直观地识别热点并进一步钻取时，它们非常有用。

下图显示了跨两个订阅的虚拟机的 CPU 使用率。 每个单元表示一个虚拟机，颜色/标签代表其平均 CPU 使用率， (redis 是) 的热机。 虚拟机按订阅进行群集。

[![屏幕截图显示了跨两个订阅的虚拟机的 CPU 使用率](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>添加蜜标梳理数

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用底部的 " **添加**  "，然后 *添加查询* 向工作簿添加日志查询控件。
3. 选择 "日志" 作为 *数据源*，选择 "Log Analytics" 作为 *资源类型*，并将资源点的 *资源* 点选择为包含虚拟机性能日志的工作区。
4. 使用查询编辑器为分析输入 KQL。

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. 运行查询。
6. 将 *可视化效果* 设置为 "Graph"。
7. 选择 " **图形设置**"。
    1. 在底部的“布局字段”中，设置：
        1. 图形类型： **Hive 群集**。
        2. 节点 Id： `Id` 。
        3. 分组依据： `None` 。
        4. 节点大小：100。
        5. 六边形之间的边距： `5` 。
        6. 颜色类型： **热度地图**。
        7. 节点颜色字段： `CouterValue` 。
        8. 调色板： `Red to Green` 。
        9. 最小值： `100` 。
        10. 最大值： `2000` 。
    2. 在顶部的“节点格式设置”中，设置：
        1. 热门内容：
            1. 使用列： `Computer` 。
            2. 列呈现器 " `Text` 。
        9. 中心内容：
            1. 使用列： `CounterValue` 。
            2. 列呈现器： `Big Number` 。
            3. 调色板： `None` 。
            4. 选中 " *自定义数字格式* " 框。
            5. 单位： `Megabytes` 。
            6. 最大小数位： `1` 。
8. 选择窗格底部的 " **保存并关闭** " 按钮。

[![带有以上查询和设置的查询控件、图形设置和蜜标梳理数的屏幕截图](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>蜜标梳理数布局设置

| 设置 | 说明 |
|:------------- |:-------------|
| `Node Id` | 选择提供节点的唯一 ID 的列。 列的值可以是字符串或数字。 |
| `Group By Field` | 选择要对其群集节点的列。 |
| `Node Size` | 设置六角单元格的大小。 与属性一起使用 `Margin between hexagons` 以自定义蜜标梳理数图的外观。 |
| `Margin between hexagons` | 设置六角单元格之间的间隔。 与属性一起使用 `Node size` 以自定义蜜标梳理数图的外观。 |
| `Coloring type` | 选择用于为节点着色的方案。 |
| `Node Color Field` | 选择一个列，该列提供节点区域将基于的指标。 |

## <a name="node-coloring-types"></a>节点颜色类型

| 颜色类型 | 说明 |
|:------------- |:-------------|
| `None` | 所有节点都具有相同的颜色。 |
| `Categorical` | 根据结果集中的列的值或类别为节点分配颜色。 在上面的示例中，颜色基于结果集的列 _类型_ 。 支持的调色板包括 `Default` 、 `Pastel` 和 `Cool tone` 。  |
| `Heatmap` | 在此类型中，单元将基于度量值列和调色板进行着色。 这提供了一种简单的方法来强调跨单元的度量值分布。 |
| `Thresholds` | 在此类型中，单元颜色由阈值规则设置 (例如， _CPU > 90% => 红色，60% > CPU > 90% => 黄色，cpu < 60% => 绿色_)  |
| `Field Based` | 在此类型中，列提供要用于节点的特定 RGB 值。 提供最大的灵活性，但通常需要更多的工作才能实现。  |
      
## <a name="node-format-settings"></a>节点格式设置

蜜标梳理数作者可以指定向节点的不同部分发送哪些内容：顶部、左侧、中心、右侧和底部。 作者可随意使用任何呈现器工作簿， (文本、大数字、spark 行、图标等 ) 。

## <a name="next-steps"></a>后续步骤

- 了解如何 [在工作簿中创建复合条呈现](workbooks-composite-bar.md)器。
- 了解如何将 [日志数据导入 Power BI 中 Azure Monitor](powerbi.md)。
