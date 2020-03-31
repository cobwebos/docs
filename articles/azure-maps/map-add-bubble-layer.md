---
title: 向地图添加气泡图层 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 将气泡图层添加到地图中。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77208550"
---
# <a name="add-a-bubble-layer-to-a-map"></a>将气泡层添加地图

本文介绍如何将数据源的点数据呈现为地图上的气泡图层。 气泡图层在地图上渲染点，使其为具有固定像素半径的圆圈。 

> [!TIP]
> 默认情况下，气泡层将呈现数据源中所有几何图形的坐标。 要限制图层，使其仅渲染点几何要素，`filter`请将图层的属性设置为`['==', ['geometry-type'], 'Point']`或`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`是否还希望也包含 MultiPoint 要素。

## <a name="add-a-bubble-layer"></a>添加气泡层

以下代码将一系列点加载到数据源中。 然后，它连接数据点是到[气泡层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)。 气泡图层以五个像素和填充颜色白色渲染每个气泡的半径。 并且，描边颜色为蓝色，描边宽度为 6 像素。 

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

此代码演示如何使用气泡图层在地图上呈现点。 以及如何使用符号图层来渲染标签。 要隐藏符号图层的图标，将`image`图标选项的属性设置为`'none'`。

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
> [添加符号图层](map-add-pin.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
