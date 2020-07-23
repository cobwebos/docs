---
title: 将条形图层添加到 Azure Maps Power BI 视觉对象 |Microsoft Azure 映射
description: 本文介绍如何在 Power BI 的 Microsoft Azure 地图视觉对象中使用条形图层。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 34d2d96e28f90249ad25788f6994dac63f83b1f6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261684"
---
# <a name="add-a-bar-chart-layer"></a>添加条形图层

**条形图层**用于通过允许将位置数据可视化为地图上的3d 栏或圆柱，来将数据提取到下一个维度中。 类似于气泡图层，以后条形图可以使用颜色和相对高度，同时轻松地直观显示两个度量值。 为了使条形具有高度，需要将度量值添加到 "**字段**" 窗格的**大小**存储桶中。 如果未提供度量值，则不会将高度作为平面正方形或圆，具体取决于 "**条形图" 形状**选项。

> [!div class="mx-imgBorder"]
> ![使用条形图层显示点数据的地图](media/power-bi-visual/bar-chart-layer-styled.png)

用户可以倾斜和旋转地图，以查看不同透视图中的数据。 使用以下方法之一可以倾斜或音调地图。

-   打开 "**格式**" 窗格的 "**映射" 设置**中的 "**导航控件**" 选项。 这将添加一个按钮以倾斜地图。
-   按下鼠标右键，然后向上或向下拖动鼠标。
-   在触摸屏上使用双指触摸地图，并同时向上或向下拖动双指。
-   以地图为重点，按住**Shift**键，然后按**向上**键或**向下**键。

可以使用以下方法之一来旋转该映射。

-   打开 "**格式**" 窗格的 "**映射" 设置**中的 "**导航控件**" 选项。 这将添加一个按钮来旋转地图。
-   按下鼠标右键，然后向左或向右拖动鼠标。
-   在触摸屏上使用双指触摸地图并旋转。
-   将地图集中在一起，按住**Shift**键，然后按**向左**或**向右箭头**键。

下面是 "**条形图层**" 部分中可用的 "**格式**" 窗格中的所有设置。

| 设置              | 说明      |
|----------------------|------------------|
| 条形图            | 三维条形图的形状。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box –呈现为矩形框的条形。<br/>&nbsp;&nbsp;&nbsp;&nbsp;•柱面–呈现为圆柱的条形。 |
| 高度               | 每个条形的高度。 如果将字段传递到 "**字段**" 窗格的**大小**存储桶，则会相对于此高度值缩放条形。 |
| 缩放时的缩放高度 | 指定条形的高度是否应相对于缩放级别进行缩放。 |
| 宽度                | 每个条形的宽度。  |
| 缩放时的缩放宽度  | 指定条形的宽度是否应相对于缩放级别进行缩放。  |
| 填充颜色           | 每个条形的颜色。 将字段传递到 "**字段**" 窗格的 "**图例**" 存储桶中时，此选项将被隐藏，而 "**格式**" 窗格中将显示单独的**数据颜色**部分。 |
| 透明度         | 每个条形的透明度。 |
| 最小缩放             | 最小缩放级别磁贴可用。 |
| 最大缩放             | 最大缩放级别磁贴可用。 |
| 层位置       | 指定层相对于其他地图层的位置。 |

> [!NOTE]
> 如果条形具有较小的宽度值并且 "**缩放时缩放宽度**" 选项处于禁用状态，则当放大时，它们可能会消失，因为呈现的宽度将小于像素大小。 但是，如果启用了缩放时的**刻度宽度**选项，则会在缩放级别发生更改时执行其他计算，这会影响大型数据集的性能。

## <a name="next-steps"></a>后续步骤

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