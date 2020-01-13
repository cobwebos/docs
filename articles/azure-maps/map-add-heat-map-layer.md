---
title: 向地图添加热度地图层 |Microsoft Azure 映射
description: 在本文中，将了解如何使用 Microsoft Azure Map Web SDK 将热度地图层添加到地图。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ead30a80a6568e72f922f355916d31121b49a93b
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911209"
---
# <a name="add-a-heat-map-layer"></a>添加热度地图层

热度地图也称为点密度图，是一种用于通过颜色范围表示数据密度的数据可视化。 它们通常用于在地图上显示数据 "热点"，这是一种呈现大型点数据集的好方法。 

例如，在地图视图中呈现成千上万的点，因为符号涵盖了大部分的地图区域。 这可能会导致许多符号彼此重叠，从而难以充分理解数据。 但是，将此数据集可视化为热度地图，可以轻松地查看点数据是 densest 的位置，而将相对密度显示到其他区域。

可以在许多不同的方案中使用热度地图，其中包括：

- **温度数据**：提供两个数据点之间温度的近似值。
- **噪音传感器的数据**：不仅显示传感器的噪音强度，而且还可以提供距离内的散发的见解。 任何一个站点的噪音级别可能不是高。 但如果多个传感器的噪音范围区域重叠，则这种重叠区域可能会出现更高的干扰级别，因此在热度地图中会出现这种情况。
- **GPS 跟踪**：将速度作为加权高度地图，其中每个数据点的强度基于速度。 例如，这提供了一种方法来查看车辆的加速位置。

> [!TIP]
> 默认情况下，热度地图层会呈现数据源中所有几何的坐标。 若要将该层限制为仅呈现点几何特征，请将层的 `filter` 属性设置为 `['==', ['geometry-type'], 'Point']`。 如果还想要包含 MultiPoint 功能，请将层的 `filter` 属性设置为 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>添加热度地图层

若要将点的数据源呈现为热度地图，请将数据源传递到 `HeatMapLayer` 类的实例中，并将其添加到地图中。

在下面的代码中，每个热度点在所有缩放级别上都有10个像素的半径。 向地图添加热度地图层时，此示例会将其插入到标签层下方，以创建更好的用户体验。 标签在热度地图上方清晰可见。 本示例中的数据源自[USGS 地震危险计划](https://earthquake.usgs.gov/)，表示过去30天内发生的重大地震。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

下面是上述代码的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='简单热度地图层' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>简单热度地图层</a>。
</iframe>

## <a name="customize-the-heat-map-layer"></a>自定义热度地图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热度地图层提供了用于自定义的几个选项，包括：

* `radius`：定义要在其中呈现每个数据点的像素半径。 可以将 radius 设置为固定数字或表达式。 通过使用表达式，可以根据缩放级别缩放半径，并在地图上表示一致的空间区域（例如，5英里半径）。
* `color`：指定如何着色热度地图。 颜色渐变是热度地图的一项常见功能，您可以使用 `interpolate` 表达式来实现效果。 你还可以使用 `step` 表达式着色热度地图，将密度直观地分解为类似于等高线或雷达图样式图的范围。 这些调色板定义了从最小到最大密度值的颜色。 

  将热度地图的颜色值指定为 `heatmap-density` 值的表达式。 内插表达式中索引0处的颜色或步骤表达式的默认颜色定义了没有数据的区域的颜色。 您可以使用此定义背景色。 通常，此值设置为透明或半透明黑色。 
   
  下面是颜色表达式的示例：

  | 内插颜色表达式 | 阶颜色表达式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“内插”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“线性”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0，“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“紫色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5，“#fb00fb”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1，“#00c3ff”<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“递阶”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25 "，" 绿色 "<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50、"黄色"、<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75 "red"<br/>\] |   

- `opacity`：指定热度地图层的不透明或透明程度。
- `intensity`：对每个数据点的权重应用乘数以增加热度地图的总体亮度。 这有助于使数据点权重的细微差异变得更加直观。
- `weight`：默认情况下，所有数据点的权重都为1，并且具有相同的加权。 权重选项用作乘数，你可以将其设置为数字或表达式。 如果将数字（例如，2）设置为权重，则它相当于将每个数据点置于地图两次，从而使密度翻倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。 

  但是，如果使用表达式，则每个数据点的权重可以基于每个数据点的属性。 例如，假设每个数据点表示地震。 每个地震数据点具有的一个重要指标是一个数量级值。 地震始终发生，但大多数情况下都是很低的，甚至不会感觉。 通过使用表达式中的 "量值" 将权重分配给每个数据点，可以更好地表示热图中地震的重要性。
- `source` 和 `source-layer`：允许更新数据源。

以下工具用于测试不同的热度地图层选项。

<br/>

<iframe height='700' scrolling='no' title='热度地图层选项' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>热度地图层选项</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的 zoomable 热度地图

默认情况下，热度地图层中呈现的数据点的半径对于所有缩放级别都具有固定像素半径。 缩放地图时，数据聚合在一起，热度地图层看起来不同。 

使用 `zoom` 表达式可缩放每个缩放级别的半径，使每个数据点都涵盖地图的同一物理区域。 这使得热度地图层的外观更具静态和一致性。 地图的每个缩放级别都具有两倍于与上一个缩放级别垂直和水平的像素。 

缩放半径，使每个缩放级别翻倍，从而创建一个在所有缩放级别上都保持一致的热度地图。 为此，请将 `zoom` 与 base 2 `exponential interpolation` 表达式一起使用，如下面的示例中所示。 缩放地图，查看热度地图如何随缩放级别一起缩放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的 zoomable 热度地图" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "笔<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致 zoomable 热度 map</a> Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）"。
</iframe>

> [!TIP]
> 在数据源上启用群集功能时，彼此接近的点将作为一个聚集点组合在一起。 可以使用每个分类的点计数作为热度地图的权重表达式，并大大减少需要呈现的点的数量。 群集的点计数存储在点功能的 `point_count` 属性中： 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果聚类分析半径只有几个像素，则呈现中的视觉差别很小。 更大的 radius 将更多的点分组到每个群集中，并提高热度地图的性能。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)
