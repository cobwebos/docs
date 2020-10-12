---
title: 向 Azure Maps Power BI 视觉对象添加气泡层 |Microsoft Azure 映射
description: 在本文中，你将了解如何在 Power BI 中使用 Microsoft Azure 地图视觉对象中的气泡图层。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4443b0f479079a4722a5d62fea40afcb4a58632d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261677"
---
# <a name="add-a-bubble-layer"></a>添加气泡层

**气泡图层**将位置数据呈现为地图上的缩放圆圈。

> [!div class="mx-imgBorder"]
> ![使用气泡图层显示点数据的地图](media/power-bi-visual/bubble-layer-with-legend-color.png)

最初，所有气泡的填充颜色都相同。 如果将字段传递到 "**字段**" 窗格的 "**图例**" 存储桶中，则气泡将基于其分类进行着色。 气泡的轮廓是默认值，但可以更改为新颜色，也可以通过启用高对比度大纲选项来更改。 **高对比度大纲**选项动态分配的轮廓颜色是填充颜色的高对比度变体。 这有助于确保气泡清楚地显示，而不考虑地图的样式。 下面是 "**气泡图层**" 部分中提供的 "**格式**" 窗格中的主要设置。

| 设置               | 说明    |
|-----------------------|----------------|
| 大小                  | 每个气泡的大小。 当字段传递到 "**字段**" 窗格的**大小**存储桶中时，此选项将被隐藏。 其他选项将按本文后面的 [气泡大小缩放](#bubble-size-scaling) 主题中所述显示。 |
| 填充颜色            | 每个气泡的颜色。 将字段传递到 "**字段**" 窗格的 "**图例**" 存储桶中时，此选项将被隐藏，而 "**格式**" 窗格中将显示单独的**数据颜色**部分。 |
| 填充透明度     | 每个气泡的透明度。 |
| 高对比度大纲 | 通过使用填充颜色的高对比度变体，使轮廓颜色与填充颜色对比以提高可访问性。 |
| 轮廓颜色         | 用于勾勒气泡的颜色。 启用 **高对比度大纲** 选项后，此选项将被隐藏。 |
| 边框透明度  | 边框的透明度。 |
| 边框宽度         | 边框的宽度（以像素为单位）。 |
| 模糊                  | 应用于轮廓的模糊量。 如果值为1，则气泡将模糊，因此只有中心点没有透明度。 值0会应用任何模糊效果。 |
| 间距对齐       | 指定在音调时气泡的外观。 <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•视口相对于视区，气泡显示在其边缘上。 （默认值）<br/>&nbsp;&nbsp;&nbsp;&nbsp;•地图气泡在地图的图面上呈现。 |
| 缩放比例            | 气泡相对于缩放级别的缩放量。 缩放比例为1意味着无缩放。 当放大时，较大的值将使气泡变小并放大。 这有助于在缩小时减少地图上的混乱程度，同时在放大时确保点更突出。 如果值为1，则不应用任何缩放。 |
| 最小缩放              | 最小缩放级别磁贴可用。 |
| 最大缩放              | 最大缩放级别磁贴可用。 |
| 层位置        | 指定层相对于其他地图层的位置。 |

## <a name="bubble-size-scaling"></a>气泡大小缩放

如果将字段传递到 "**字段**" 窗格的**大小**存储桶，则气泡将相对于每个数据点的度量值进行缩放。 当字段传递到**大小**存储桶中时，"**格式**" 窗格的 "**气泡图层**" 部分中的 "**大小**" 选项将消失，因为气泡将在最小值和最大值之间进行半径缩放。 当**大小**bucket 具有指定的字段时，以下选项将显示在 "**格式**" 窗格的 "**气泡层**" 部分中。

| 设置             | 描述  |
|---------------------|--------------|
| 最小大小            | 缩放数据时的最小气泡大小。|
| 最大大小            | 缩放数据时的最大气泡大小。|
| 大小缩放方法 | 用于确定相对气泡大小的缩放算法。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•线性-以线性方式映射到最小和最大大小的输入数据范围。 （默认值）<br/>&nbsp;&nbsp;&nbsp;&nbsp;•输入数据对数映射到最小和最大大小的日志范围。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier：指定用于创建自定义缩放方法的 Cubic-Bezier 曲线的 X1，Y1，X2，Y2 值。 |

将 **大小缩放方法** 设置为 " **记录**" 时，将提供下列选项。

| 设置   | 描述      |
|-----------|------------------|
| 对数刻度 | 计算气泡大小时要应用的对数刻度。 |

当 " **大小缩放" 方法** 设置为 " **立方-贝塞尔**" 时，将提供以下选项用于自定义缩放曲线。

| 设置 | 描述                           |
|---------|---------------------------------------|
| X1      | 三次方贝塞尔曲线的 X1 参数。 |
| 是1      | 三次方贝塞尔曲线的 X2 参数。 |
| X2      | 三次方贝塞尔曲线的 Y1 参数。 |
| 是2      | 三次方贝塞尔曲线的 Y2 参数。 |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) 提供了一个用于为 Cubic-Bezier 曲线创建参数的便利工具。

## <a name="next-steps"></a>后续步骤

更改数据在地图上的显示方式：

> [!div class="nextstepaction"]
> [添加条形图层](power-bi-visual-add-bar-chart-layer.md)

向映射添加更多上下文：

> [!div class="nextstepaction"]
> [添加引用层](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [添加图块层](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)

自定义视觉对象：

> [!div class="nextstepaction"]
> [Power BI 中颜色格式设置的提示和技巧](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [自定义可视化效果的标题、背景和图例](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)