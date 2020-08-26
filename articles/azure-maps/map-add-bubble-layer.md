---
title: 向地图添加气泡图层 |Microsoft Azure 映射
description: 了解如何将地图上的点呈现为固定大小的圆圈。 若要实现此目的，请参阅如何使用 Azure Maps Web SDK 添加和自定义气泡图层。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 47ee9e67c6885829e3cb0082aea63367da4aa626
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006173"
---
# <a name="add-a-bubble-layer-to-a-map"></a>将气泡层添加地图

本文介绍如何将数据源中的点数据呈现为地图上的气泡图层。 气泡图层用固定像素半径将点呈现为地图上的圆圈。 

> [!TIP]
> 默认情况下，气泡层将呈现数据源中所有几何图形的坐标。 若要将层限制为仅呈现点几何特征，请将 `filter` 层的属性设置为， `['==', ['geometry-type'], 'Point']` 或者 `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` 如果要包含 MultiPoint 功能，则设置为。

## <a name="add-a-bubble-layer"></a>添加气泡层

下面的代码将一个点数组加载到数据源中。 然后，它将数据点连接到[气泡图层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)。 气泡图层用5个像素呈现每个气泡的半径，并使用白色填充颜色。 和，颜色为蓝色，笔划宽度为六个像素。 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>显示具有气泡层的标签

此代码演示如何使用气泡图层呈现地图上的点。 以及如何使用符号层呈现标签。 若要隐藏符号层的图标，请将 `image` 图标选项的属性设置为 `'none'` 。

<br/>

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

## <a name="customize-a-bubble-layer"></a>自定义气泡层

气泡层只有几个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='气泡层选项' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>通过 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 查看 Pen <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>气泡层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
