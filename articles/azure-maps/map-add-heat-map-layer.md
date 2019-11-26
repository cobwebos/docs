---
title: 将热度地图层添加到 Azure Maps | Microsoft Docs
description: How to add a heat map layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f7115e7c8b95efd0e3bbc8a788528878c2d1f092
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484307"
---
# <a name="add-a-heat-map-layer"></a>添加热度地图层

热度地图，也称为点密度地图，是一种数据可视化类型，它们使用一系列颜色来表示数据密度。 它们通常用于在地图上显示数据“热点”，是呈现大型点数据集的好方法。  For example, rendering tens of thousands of points within the map view as symbols, covers most of the map area and would result in many symbols overlapping each other, making it difficult to gain much insight into the data. 然而，将相同数据集可视化为热度地图可以很容易地看到点数据最密集的位置以及相对于其他区域的密度。 很多情况都会用到热度地图。 以下是几个示例；

- 温度数据通常表示为热度地图，因为它提供了两个数据点之间温度的近似值。
- 将噪声传感器数据绘制成热度地图不仅可以显示传感器所在位置的噪声的强度，而且还可以洞察一段距离内的消散情况。 任何一个站点的噪声水平可能并不高，但是如果来自多个传感器的噪声覆盖区域重叠，那么该重叠区域可能会产生更高的噪声水平，从而显示在热度地图中。
- Visualizing a GPS trace that includes the speed as a weighted height map where the intensity of each data point is based on the speed is a great way to see where the vehicle was speeding.

> [!TIP]
> 默认情况下，热度地图层将呈现数据源中所有几何图形的坐标。 To limit the layer so that it only renders point geometry features, set the `filter` property of the layer to `['==', ['geometry-type'], 'Point']` or `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` if you want to include MultiPoint features as well.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>添加热度地图层

若要将点数据源呈现为热度地图，请将数据源传递到 `HeatMapLayer` 类的实例中，并将其添加到地图，如下所示。

In the following code, each heat point has a radius of 10 pixels at all zoom levels. When adding the heat map layer to the map, this sample inserts it below the label layer to create a better user experience as the labels are clearly visible above the heat map. The data in this sample is sourced from the [USGS Earthquake Hazards Program](https://earthquake.usgs.gov/) and represents significant earthquakes that have occurred in the last 30 days.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='简单热度地图层' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>简单热度地图层</a>。
</iframe>

## <a name="customizing-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层为自定义提供了若干选项；

* `radius`: Defines a pixel radius in which to render each data point. 半径可以设置为一个固定数字或表达式。 Using an expression, it's possible to scale the radius based on the zoom level, that appears to represent a consistent spatial area on the map (for example, 5-mile radius).
* `color`: Specifies how the heat map is colorized. A color gradient is often used for heat maps and can be achieved with an `interpolate` expression. Using a `step` expression for colorizing the heat map breaks up the density visually into ranges that more so resembles a contour or radar style map. 这些调色板定义了从最小到最大密度值的颜色。 热度地图的颜色值被指定为 `heatmap-density` 值上的表达式。 The color at index 0 in an interpolation expression or the default color of a step expression, defines the color of the area where there's no data and can be used to define a background color. 很多用户喜欢将此值设置为透明或半透明黑色。 下面是颜色表达式示例；

| Interpolation Color Expression | 递阶色表达式 | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“内插”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“线性”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0，“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“紫色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5，“#fb00fb”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1，“#00c3ff”<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“递阶”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50, 'yellow',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75, 'red'<br/>\] | 

- `opacity`: Specifies how opaque or transparent the heat map layer is.
- `intensity`: Applies a multiplier to the weight of each data point to increase the overall intensity of the heatmap and helps to make the small differences in the weight of data points become easier to visualize.
- `weight`: By default, all data points have a weight of 1, thus all data points are weighted equally. 权重选项充当乘数，可以设置为数字或表达式。 如果一个数字被设置为权重，比如 2，它就相当于将每个数据点放在地图上两次，从而使密度加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。 However, if an expression is used, the weight of each data point can be based on the properties of each data point. 以地震数据为例，每个数据点表示一个地震。 每个地震数据点的重要指标是震级值。 地震时时刻刻都在发生，但大多数震级都很低，甚至感觉不到。 Using the magnitude value in an expression to assign the weight to each data point will allow more significant earthquakes to be better represented within the heat map.
- 除了基本层选项；最小/最大缩放、可见和筛选，如果要更新数据源，还有一个 `source` 选项；如果数据源是矢量图块源，还有一个 `source-layer` 选项。

下面是一个工具，可测试不同热度地图层选项。

<br/>

<iframe height='700' scrolling='no' title='热度地图层选项' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>热度地图层选项</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistent zoomable heat map

By default, the radii of data points rendered in the heat map layer have a fixed pixel radius for all zoom levels. As the map is zoomed, the data aggregates together and the heat map layer looks different. A `zoom` expression can be used to scale the radius for each zoom level such that each data point covers the same physical area of the map. This will make the heat map layer look more static and consistent. Each zoom level of the map has twice as many pixels vertically and horizontally as the previous zoom level. Scaling the radius such that it doubles with each zoom level will create a heat map that looks consistent on all zoom levels. This can be accomplished by using the `zoom` with a base 2 `exponential interpolation` expression as shown in the sample below. Zoom the map to see how the heat map scales with the zoom level.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistent zoomable heat map" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>Consistent zoomable heat map</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> 通过在数据源上启用聚类分析，相邻的点可以作为一个群集点组合在一起。 每个群集点计数可以用作热度地图的权重表达式，并显著减少须呈现的点数。 The point count of a cluster is stored in a `point_count` property of the point feature as shown below. 
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
> [Create a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)