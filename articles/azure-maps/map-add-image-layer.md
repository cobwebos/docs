---
title: 将图像层添加到 Azure Maps | Microsoft Docs
description: 如何将图像层添加到 Javascript 地图
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d36179685e2098455e00186827d35e6de18bf975
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892814"
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

## <a name="add-an-image-layer"></a>添加图像层

此示例显示如何在地图上叠加 [1922 年新泽西州纽瓦克地图]((https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg))的图像。

<br/>

<iframe height='500' scrolling='no' title='简单图像层' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eQodRo/'>简单图像层</a>。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，通过将 URL 传递给图像并以 `[Top Left Corner, Top Right Corner, Bottom Right Conter, Bottom Left Corner]` 格式设置四个角的坐标来创建 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)。

## <a name="import-a-kml-ground-overlay"></a>导入 KML 地面叠加

此示例演示如何将 KML 地面叠加信息叠加为地图上的图像层。 KML 地面叠加提供东南西北的坐标以及逆时针旋转，其中图像层需要图像每个角的坐标。 此示例中的 KML 地面叠加是来自[维基媒体](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)的 Chartres 大教堂。

<br/>

<iframe height='500' scrolling='no' title='KML 地面叠加作为图像层' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>KML 地面叠加作为图像层</a>。
</iframe>

上面的代码使用 [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) 类的静态 `getCoordinatesFromEdges` 函数来从东南西北计算图像的四个角以及 KML 地面叠加的旋转信息。


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