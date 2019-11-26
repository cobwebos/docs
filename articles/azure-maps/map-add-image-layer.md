---
title: 将图像层添加到 Azure Maps | Microsoft Docs
description: How to add an Image Layer to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fadaaf7c64b11a6d6d94c68234f8288d1b3f8d07
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480491"
---
# <a name="add-an-image-layer-to-a-map"></a>将图像层添加到地图

本文介绍如何将图像叠加到地图上的固定坐标集。 有很多在地图上叠加图像的情景。 以下是经常叠加在地图上的图像类型的几个示例；

* 从无人机捕获的图像。
* 建筑平面图。
* 历史或其他专门的地图图像。
* 工作地点蓝图。
* 天气雷达图像。

> [!TIP]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 是一种在地图上叠加图像的快捷且轻松的方式。 但是，如果图像很大，浏览器可能难以加载它。 在这种情况下，请考虑将图像分解为图块并将其作为 [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 加载到地图中。

The image layer supports the following image formats:

- JPEG
- PNG
- BMP
- GIF (no animations)

## <a name="add-an-image-layer"></a>添加图像层

In the following code overlays an image of a [map of Newark New Jersey from 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) on the map. An [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) is created by passing a URL to an image and coordinates for the four corners in the format `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

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

Below is the complete running code sample of the above functionality.

<br/>

<iframe height='500' scrolling='no' title='简单图像层' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>简单图像层</a>。
</iframe>

## <a name="import-a-kml-ground-overlay"></a>导入 KML 地面叠加

此示例演示如何将 KML 地面叠加信息叠加为地图上的图像层。 KML ground overlays provide north, south, east, and west coordinates and a counter-clockwise rotation, whereas the image layer expects coordinates for each corner of the image. 此示例中的 KML 地面叠加是来自[维基媒体](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)的 Chartres 大教堂。

The following code uses the static `getCoordinatesFromEdges` function of the [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) class to calculate the four corners of the image from the north, south, east, west and rotation information from the KML ground overlay.

<br/>

<iframe height='500' scrolling='no' title='KML 地面叠加作为图像层' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML 地面叠加作为图像层</a>。
</iframe>

## <a name="customize-an-image-layer"></a>自定义图像层

图像层有许多样式选项。 以下工具可用来试用这些选项。

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