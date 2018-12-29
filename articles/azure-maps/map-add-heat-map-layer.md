---
title: 将热度地图层添加到 Azure Maps | Microsoft Docs
description: 如何将热度地图层添加到 Javascript 地图
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec1343f85216171adac22f873f9be2e72bb4c282
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892816"
---
# <a name="add-a-heat-map-layer"></a>添加热度地图层

热度地图，也称为点密度地图，是一种数据可视化类型，它们使用一系列颜色来表示数据密度。 它们通常用于在地图上显示数据“热点”，是呈现大型点数据集的好方法。  例如，将地图视图中的数万个点呈现为符号，这将覆盖地图的大部分区域，并导致许多符号被其他符号所覆盖，从而难以深入了解数据。 然而，将相同数据集可视化为热度地图可以很容易地看到点数据最密集的位置以及相对于其他区域的密度。 很多情况都会用到热度地图。 以下是几个示例；

* 温度数据通常表示为热度地图，因为它提供了两个数据点之间温度的近似值。
* 将噪声传感器数据绘制成热度地图不仅可以显示传感器所在位置的噪声的严重程度，而且还可以洞察一段距离内的消散情况。 任何一个站点的噪声水平可能并不高，但是如果来自多个传感器的噪声覆盖区域重叠，那么该重叠区域可能会产生更高的噪声水平，从而显示在热度地图中。
* 将包含速度的 GPS 轨迹可视化为权重高度地图，其中每个数据点的强度基于速度，这是快速查看车辆超速位置的好方法。

> [!TIP]
> 默认情况下，气泡层将呈现数据源中所有几何图形的坐标。 若要限制层以便仅呈现点几何图形功能，请将层的 `filter` 属性设置为 `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>添加热度地图层

要将点数据源呈现为热度地图，只需将数据源传递到 HeatMapLayer 类的实例中，并将其添加到地图，如下所示。

<br/>

<iframe height='500' scrolling='no' title='简单热度地图层' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>简单热度地图层</a>。
</iframe>

在此示例中，每个热点在所有缩放级别的半径均为 10 像素。 在向地图添加热度地图层时，此示例会将其插入到标签层下面。 这将创建更好的用户体验，因为标签在热度地图上方清晰可见。 本示例中的数据源自 [USGS 地震灾害项目](https://earthquake.usgs.gov/)，由代表过去 30 天发生的重大地震的点组成。

## <a name="customizing-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层为自定义提供了若干选项；

* `radius`：定义在其中呈现每个数据点的像素半径。 半径可以设置为一个固定数字或表达式。 将半径设置为基于缩放级别设置半径的表达式，会导致热度地图看起来有一个表示地图上一致空间区域的半径。
* `color`：指定如何对热度地图进行着色。 热度地图通常使用渐变色调色板，但如果希望热度地图看起来更像轮廓数据，递阶色调色板也很有用。 这些调色板定义了从最小到最大密度值的颜色。 热度地图的颜色值被指定为 `heatmap-density` 值上的表达式。 渐变表达式中索引 0 处的颜色或递阶色的默认颜色定义没有数据的区域颜色或背景色。 很多用户喜欢将此值设置为透明或半透明黑色。 下面是颜色表达式示例；

| 渐变色表达式 | 递阶色表达式 | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“内插”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“线性”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0，“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“紫色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5，“#fb00fb”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1，“#00c3ff”<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“递阶”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50，“绿色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，“黄色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1.00，“红色”<br/>\] |   

* `opacity`：指定热度图层的不透明或透明程度。
* `intensity`：对每个数据点的权重应用乘数，以增加热度地图的整体强度。 这有助于更加轻松地可视化数据点权重中的细微差异。
* `weight`：默认情况下，所有数据点的权重都是 1，因此所有数据点权重都相等。 权重选项充当乘数，可以设置为数字或表达式。 如果一个数字被设置为数字，比如 2，它就相当于将每个数据点放在地图上两次，从而使密度加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。 但是，如果使用表达式，每个数据点的权重可以基于不同的数据点，也可以基于该点属性中的某些指标。 以地震数据为例，每个数据点表示一个地震，每个地震的重要指标是震级。 地震时时刻刻都在发生，但大多数震级都很低，甚至感觉不到。 在表达式中使用震级值来分配权重选项将使更重大的地震数增加，以此更清晰地显示在热度地图中。
* 除了基本层选项；最小/最大缩放、可见和筛选，如果要更新数据源，还有一个 `source` 选项；如果数据源是矢量图块源，还有一个 `source-layer` 选项。

下面是一个工具，可测试不同热度地图层选项。

<br/>

<iframe height='700' scrolling='no' title='热度地图层选项' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>热度地图层选项</a>。
</iframe>

> [!TIP]
> 通过在数据源上启用聚类分析，相邻的点可以作为一个群集点组合在一起。 每个群集点计数可以用作热度地图的权重表达式，并显著减少须呈现的点数。 群集点计数存储在点功能的 point_count 属性中，如下所示。 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果聚类分析半径只有几个像素，那么呈现的视觉效果几乎没有差异。 更大的半径会将更多点组合到每个群集并提高热度地图的性能，但具有的差异将会更明显。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加符号层](./map-add-pin.md)

