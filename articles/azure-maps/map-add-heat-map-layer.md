---
title: 向地图添加热图图层 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 将热图图层添加到地图中。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4a853871ef5f66881235e5a6ffec0886b81f5a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208533"
---
# <a name="add-a-heat-map-layer"></a>添加热度地图层

热度地图也称为点密度地图，是一种数据可视化效果。 它们用于使用一系列颜色表示数据密度，并在地图上显示数据"热点"。 热图是渲染具有大量点的数据数据集的好方法。 

将数以万计的点渲染为符号可以覆盖地图区域的大部分。 这种情况可能会导致许多符号重叠。 使数据难以更好地了解。 但是，将此数据集与热图可视化，便于查看每个数据点的密度和相对密度。

您可以在许多不同的方案中使用热图，包括：

- **温度数据**：提供两个数据点之间的温度的近似值。
- **噪声传感器的数据**：不仅显示传感器所在位置的噪声强度，还可以提供对远距离耗散情况的洞察。 任何一个站点的噪声级别可能并不高。 如果来自多个传感器的噪声覆盖区域重叠，则此重叠区域可能会遇到更高的噪声级别。 因此，重叠区域将在热图中可见。
- **GPS 跟踪**：包括速度作为加权高度图，其中每个数据点的强度基于速度。 例如，此功能提供了一种查看车辆超速位置的方法。

> [!TIP]
> 默认情况下，热贴图图层呈现数据源中所有几何体的坐标。 要限制图层以便仅渲染点几何要素，将图层的属性设置为`filter``['==', ['geometry-type'], 'Point']`。 如果还希望也包括 MultiPoint 要素，请将`filter`图层的属性设置为`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="add-a-heat-map-layer"></a>添加热度地图层

要将点的数据源呈现为热图，请将数据源传递到`HeatMapLayer`类的实例中，并将其添加到地图中。

在以下代码中，每个热点在所有缩放级别上都有 10 像素的半径。 为了确保更好的用户体验，热图位于标签图层下方。 标签保持清晰可见。 此示例中的数据来自[美国地质调查局地震危害计划](https://earthquake.usgs.gov/)。 这是针对过去30天发生的大地震。

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a dataset of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heat map and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

下面是前面代码的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='简单热度地图层' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>简单热度地图层</a>。
</iframe>

## <a name="customize-the-heat-map-layer"></a>自定义热图图层

前面的示例通过设置半径和不透明度选项来自定义热度地图。 热图图层提供了多种自定义选项，包括：

* `radius`： 定义用于渲染每个数据点的像素半径。 您可以将半径设置为固定数字或表达式。 通过使用表达式，可以根据缩放级别缩放半径，并表示地图上的一致空间区域（例如，5 英里半径）。
* `color`：指定热图的着色方式。 颜色渐变是热图的常见特征。 您可以使用`interpolate`表达式实现效果。 您还可以使用`step`表达式对热图进行着色，将密度直观地分解为类似于轮廓或雷达样式图的范围。 这些调色板定义了从最小到最大密度值的颜色。 

  将热图的颜色值指定为值上的`heatmap-density`表达式。 没有数据的区域的颜色在"插值"表达式的索引 0 或"步进"表达式的默认颜色中定义。 可以使用此值定义背景颜色。 通常，此值设置为透明或半透明黑色。 
   
  下面是颜色表达式的示例：

  | 插值颜色表达式 | 阶梯颜色表达式 | 
  |--------------------------------|--------------------------|
  | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“内插”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“线性”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0，“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“紫色”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5，“#fb00fb”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;1，“#00c3ff”<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;“递阶”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[“热度地图密度”\]，<br/>&nbsp;&nbsp;&nbsp;&nbsp;“透明”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.01，“海军蓝”，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25，"绿色"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.50，"黄色"，<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.75，"红色"<br/>\] |   

- `opacity`：指定热图图层的不透明程度或透明度。
- `intensity`：对每个数据点的重量应用乘数，以增加热图的总体强度。 它会导致数据点重量的差异，从而更易于可视化。
- `weight`： 默认情况下，所有数据点的权重为 1，并且权重相等。 权重选项充当乘数，您可以将其设置为数字或表达式。 如果将数字设置为权重，则是将每个数据点放在地图上两次的等效。 例如，如果权重为 2，则密度加倍。 将权重选项设置为一个数字，以类似于使用强度选项的方式来呈现热度地图。 

  但是，如果使用表达式，则每个数据点的权重可以基于每个数据点的属性。 例如，假设每个数据点表示地震。 震级值是每个地震数据点的重要指标。 地震一直发生，但大多数地震的震级较低，没有引起注意。 使用表达式中的量级值将权重分配给每个数据点。 通过使用震级值来分配权重，您可以在热图中更好地表示地震的重要性。
- `source`和`source-layer`： 使您能够更新数据源。

下面是一个用于测试不同热图图层选项的工具。

<br/>

<iframe height='700' scrolling='no' title='热度地图层选项' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>热度地图层选项</a>。
</iframe>

## <a name="consistent-zoomable-heat-map"></a>一致的可缩放热图

默认情况下，热贴图图层中呈现的数据点的半径为所有缩放级别的固定像素半径。 缩放地图时，数据聚合在一起，热图图层看起来不同。 

使用`zoom`表达式缩放每个缩放级别的半径，以便每个数据点覆盖地图的相同物理区域。 此表达式使热图图层看起来更静态且一致。 地图的每个缩放级别垂直和水平的像素数是上一个缩放级别的两倍。 

缩放半径，使其与每个缩放级别加倍，可创建在所有缩放级别上看起来一致的热图。 要应用此缩放，请使用`zoom`基 2`exponential interpolation`表达式，如以下示例所示。 缩放地图以查看热图如何与缩放级别一起缩放。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="一致的可缩放热图" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （） 提供的"笔<a href='https://codepen.io/azuremaps/pen/OGyMZr/'>一致可缩放热图</a>"。
</iframe>

> [!TIP]
> 在数据源上启用聚类时，彼此接近的点将分组为聚类点。 您可以将每个群集的点计数用作热图的重量表达式。 这可以显著减少要呈现的点数。 群集的点计数存储在点要素的属性中`point_count`： 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> 如果聚类半径只有几像素，则渲染中会有很小的视觉差异。 半径越大，将更多点分组到每个群集中，并提高了热图的性能。

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
