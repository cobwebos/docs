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
ms.openlocfilehash: 82a6d6b2af7df91696844b09fb7650c547cb6bd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108628"
---
# <a name="add-a-heat-map-layer"></a>添加热度地图层

热度地图，也称为点密度地图，是一种数据可视化类型，它们使用一系列颜色来表示数据密度。 它们通常用于在地图上显示数据“热点”，是呈现大型点数据集的好方法。  例如，呈现数以万计的点的地图视图中为符号，适用于大部分映射区和会导致很多重叠相互，因此很难获得更深入了解数据的符号。 然而，将相同数据集可视化为热度地图可以很容易地看到点数据最密集的位置以及相对于其他区域的密度。 很多情况都会用到热度地图。 以下是几个示例；

* 温度数据通常表示为热度地图，因为它提供了两个数据点之间温度的近似值。
* 将噪声传感器数据绘制成热度地图不仅可以显示传感器所在位置的噪声的强度，而且还可以洞察一段距离内的消散情况。 任何一个站点的噪声水平可能并不高，但是如果来自多个传感器的噪声覆盖区域重叠，那么该重叠区域可能会产生更高的噪声水平，从而显示在热度地图中。
* 可视化 GPS 跟踪每个数据点的强度基于速度的加权的高度图包含速度是请参阅其中车辆时加快速度的好办法。

> [!TIP]
> 默认情况下，热度地图层将呈现数据源中所有几何图形的坐标。 若要限制层，以便它仅呈现点几何功能设置`filter`到层的属性`['==', ['geometry-type'], 'Point']`或`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`如果想要包括多点功能。

## <a name="add-a-heat-map-layer"></a>添加热度地图层

若要将点数据源呈现为热度地图，请将数据源传递到 `HeatMapLayer` 类的实例中，并将其添加到地图，如下所示。

<br/>

<iframe height='500' scrolling='no' title='简单热度地图层' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>简单热度地图层</a>。
</iframe>

在此示例中，每个热点在所有缩放级别的半径均为 10 像素。 时添加到映射的热度地图层，此示例会将其插入标签层来创建更好的用户体验的标签是清楚地显示热度地图上方按下。 在此示例中的数据源自[USGS 地震危险程序](https://earthquake.usgs.gov/)，表示在过去 30 天内发生的重要地震。

## <a name="customizing-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层为自定义提供了若干选项；

* `radius`：定义在其中呈现每个数据点的像素半径。 半径可以设置为一个固定数字或表达式。 使用表达式，它是可以扩展基于缩放级别，显示用于表示映射 （例如，5 英里为半径） 上的一致空间区域的角半径。
* `color`：指定如何对热度地图进行着色。 颜色渐变常用于热图，并且可以通过实现`interpolate`表达式。 使用`step`着色热度地图的表达式分解，类似于轮廓或雷达图样式映射到范围以可视方式密度。 这些调色板定义了从最小到最大密度值的颜色。 热度地图的颜色值被指定为 `heatmap-density` 值上的表达式。 在索引内插表达式中的 0 处的颜色或步骤表达式的默认颜色定义区域的颜色其中没有任何数据，用于定义背景色。 很多用户喜欢将此值设置为透明或半透明黑色。 下面是颜色表达式示例；

| 内插颜色表达式 | 递阶色表达式 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“内插”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“线性”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0，“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“紫色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5，“#fb00fb”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1，“#00c3ff”<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“递阶”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25，绿色，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50，黄色，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，红色<br/>\] | 

* `opacity`：指定热度图层的不透明或透明程度。
* `intensity`：适用于每个数据点来提高整体的热度地图的权重的乘数和可提升中变得更容易可视化的数据点的权重的细微差异。
* `weight`：默认情况下，所有数据点的权重都是 1，因此所有数据点权重都相等。 权重选项充当乘数，可以设置为数字或表达式。 如果一个数字被设置为权重，比如 2，它就相当于将每个数据点放在地图上两次，从而使密度加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。 但是，如果使用一个表达式，则每个数据点的权重可以基于每个数据点的属性。 以地震数据为例，每个数据点表示一个地震。 每个地震数据点的重要指标是震级值。 地震时时刻刻都在发生，但大多数震级都很低，甚至感觉不到。 在表达式中使用数量值以将权重分配给每个数据点将允许更重要的地震来更好地表示热度地图中。
* 除了基本层选项；最小/最大缩放、可见和筛选，如果要更新数据源，还有一个 `source` 选项；如果数据源是矢量图块源，还有一个 `source-layer` 选项。

下面是一个工具，可测试不同热度地图层选项。

<br/>

<iframe height='700' scrolling='no' title='热度地图层选项' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>热度地图层选项</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可缩放热度地图

默认情况下，热度地图层中呈现的数据点的半径具有所有的缩放级别的固定的像素半径。 如放大地图组合在一起的数据聚合和热度地图层看起来不同。 一个`zoom`表达式可用于缩放的半径的每个缩放级别，使每个数据点包括映射的同一个物理区域。 这会使查找热度地图层，更静态和一致。 映射的每个缩放级别的整个像素由双倍的垂直和水平方向，为以前的缩放级别。 缩放半径，以便它与每个缩放级别加倍将创建所有的缩放级别看上去一致热度地图。 这可以通过实现`zoom`基 2`exponential interpolation`表达式，如下面的示例中所示。 缩放地图来查看热图如何缩放的缩放级别。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可缩放热度地图" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致的可缩放热度地图</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

> [!TIP]
> 通过在数据源上启用聚类分析，相邻的点可以作为一个群集点组合在一起。 每个群集点计数可以用作热度地图的权重表达式，并显著减少须呈现的点数。 群集的点计数存储在`point_count`点功能，如下所示的属性。 
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

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)