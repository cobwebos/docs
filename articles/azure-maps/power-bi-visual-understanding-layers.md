---
title: 了解 Azure Maps Power BI 视觉对象中的层 |Microsoft Azure 映射
description: 本文介绍 Power BI Microsoft Azure Map 视觉对象中提供的不同层。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261620"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>了解 Azure Maps Power BI 视觉对象中的层

Azure Maps 视觉对象中提供了两种类型的层。 第一种类型侧重于呈现数据，该数据将传递到视觉对象的 " **字段** " 窗格中，其中包含以下各层：我们将调用这些数据呈现层。

:::row:::
    :::column span="":::
        **气泡层**

        将点呈现为地图上的缩放圆圈。

        ![地图上的气泡图层](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **条形图层**

        将点呈现为地图上的3D 条。
        
        ![地图上的条形图层](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

第二种类型的层连接添加外部数据源以进行映射，以提供更多上下文，并且包含以下各层。

:::row:::
    :::column span="":::
        **参考层**

        在地图的顶层覆盖已上传的 GeoJSON 文件。

        ![地图上的参考层](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **图块层**

        覆盖位于地图顶部的自定义图块层。
        
        ![地图上的图块层](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **流量层**

        覆盖地图上的实时流量信息。
        
        ![地图上的流量层](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

所有数据呈现层和 **图块层**都具有用于指定缩放级别范围的最小和最大缩放级别选项，这些层应显示在这些层中。 这允许在一个缩放级别使用一种类型的呈现层，并在另一个缩放级别上转换到另一个呈现层。

这些层还具有一个可相对于地图中其他层定位的选项。 当使用多个数据呈现层时，它们添加到地图的顺序决定了它们在具有相同 **层位置** 值时的相对分层顺序。

## <a name="general-layer-settings"></a>常规层设置

" **格式** " 窗格的 "常规层" 部分是适用于连接到 "字段" 窗格中 " **字段** " 窗格中的 Power BI 数据集的层的常见设置 (气泡层、条形图) 。

| 设置     | 描述   |
|-------------|---------------|
| 未选定透明度 | 选择了一个或多个形状时未选定的形状的透明度。  |
| 显示零              | 指定是否应使用最小半径在地图上显示大小值为零的点。 |
| 显示负片          | 指定是否应绘制负大小值的绝对值。   |
| 最小数据值          | 要缩放的输入数据的最小值。 适用于剪辑离群值。  |
| 最大数据值          | 要缩放的输入数据的最大值。 适用于剪辑离群值。  |

## <a name="next-steps"></a>后续步骤

更改数据在地图上的显示方式：

> [!div class="nextstepaction"]
> [添加气泡层](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加条形图层](power-bi-visual-add-bar-chart-layer.md)

向映射添加更多上下文：

> [!div class="nextstepaction"]
> [添加引用层](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [添加图块层](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [显示实时流量](power-bi-visual-show-real-time-traffic.md)
