---
title: 向地图添加图像图层 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 在地图上叠加图像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 69bf41f9d88081b9a416b9bee91e8650a84f12c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209709"
---
# <a name="add-an-image-layer-to-a-map"></a>将图像层添加到地图

本文介绍如何将图像叠加到一组固定的坐标。 下面是一些可以叠加在地图上的不同图像类型的示例：

* 从无人机捕获的图像
* 建筑平面图
* 历史或其他专用地图图像
* 作业地点的蓝图
* 天气雷达图像

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)是一种在地图上叠加图像的简单方法。 请注意，浏览器可能难以加载大型图像。 在这种情况下，请考虑将图像分解为切片，并将其加载到地图中作为[TileLayer。](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

图像图层支持以下图像格式：

- JPEG
- PNG
- BMP
- GIF（无动画）

## <a name="add-an-image-layer"></a>添加图像层

以下代码在地图上叠加了[1922 年新泽西州纽瓦克地图](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg)的图像。 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)是通过将 URL 传递给图像来创建的，并且以格式`[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`对四个角的坐标。

```javascript
//Create an image layer and add it to the map.
map.layers.add(new atlas.layer.ImageLayer({
    url: 'newark_nj_1922.jpg',
    coordinates: [
        [-74.22655, 40.773941], //Top Left Corner
        [-74.12544, 40.773941], //Top Right Corner
        [-74.12544, 40.712216], //Bottom Right Corner
        [-74.22655, 40.712216]  //Bottom Left Corner
    ]
}));
```

下面是前面代码的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='简单图像层' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>简单图像层</a>。
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>将 KML 文件导入地面叠加

此示例演示如何在地图上将 KML 地面叠加信息添加为图像图层。 KML 地面叠加提供北、南、东和西坐标，以及逆时针旋转。 但是，图像图层需要图像的每个角的坐标。 此示例中的 KML 地面覆盖是为查特雷斯大教堂，它来自[维基媒体](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)。

代码使用`getCoordinatesFromEdges`[ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)类中的静态函数。 它使用 KML 地面叠加的北、南、东、西和旋转信息计算图像的四个角。

<br/>

<iframe height='500' scrolling='no' title='KML 地面叠加作为图像层' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML 地面叠加作为图像层</a>。
</iframe>

## <a name="customize-an-image-layer"></a>自定义图像层

图像图层具有许多样式选项。 这里有一个工具来尝试它们。

<br/>

<iframe height='700' scrolling='no' title='图像层选项' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>图像层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图块层](./map-add-tile-layer.md)