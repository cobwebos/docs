---
title: 向地图添加切片图层 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 在地图上叠加切片图层。 切片图层允许您在地图上渲染图像。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988594"
---
# <a name="add-a-tile-layer-to-a-map"></a>将图块层添加到地图

本文介绍如何在地图上叠加磁贴图层。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 有关 Azure 地图平铺系统的详细信息，请参阅[缩放级别和切片网格](zoom-levels-and-tile-grid.md)。

磁贴图层从服务器加载切片。 这些图像可以预渲染或动态渲染。 使用切片层可以理解的命名约定，预渲染的图像与服务器上的任何其他图像一样存储。 动态渲染的图像使用服务加载接近实时的图像。 Azure 地图[切片层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)类支持三种不同的切片服务命名约定： 

* X、Y、缩放表示法 - X 是列，Y 是磁贴网格中磁贴的行位置，缩放表示法基于缩放级别的值。
* Quadkey 表示法 - 将 x、y 和缩放信息合并到单个字符串值中。 此字符串值成为单个磁贴的唯一标识符。
* 边界框 - 在边界框坐标格式中指定图像： `{west},{south},{east},{north}`. Web[映射服务 （WMS）](https://www.opengeospatial.org/standards/wms)通常使用此格式。

> [!TIP]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 是直观显示地图上的大型数据集的好办法。 不仅可以从图像生成切片图层，还可以将矢量数据渲染为切片图层。 通过将矢量数据呈现为切片图层，地图控件只需加载文件大小小于它们表示的矢量数据的切片。 此技术通常用于在地图上呈现数百万行数据。

传递到磁贴图层中的磁贴 URL 必须是指向 TileJSON 资源的 http 或 https URL 或使用以下参数的磁贴 URL 模板： 

* `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
* `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
* `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
* `{quadkey}` - 基于必应地图图块系统命名约定的图块 quadkey 标识符。
* `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。
* `{subdomain}`- 子域值的占位符（如果指定）`subdomain`将添加。

## <a name="add-a-tile-layer"></a>添加图块层

 此示例演示如何创建指向一组切片的切片图层。 此示例使用 x、y 缩放平铺系统。 此图块层源自[爱荷华州立大学的 Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) 的气象雷达图覆盖。 在查看雷达数据时，理想情况下，用户在浏览地图时会清楚地看到城市的标签。 可以通过在`labels`图层下方插入切片图层来实现此行为。

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='使用 X、Y 和 Z 的图块层' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>使用 X、Y 和 Z 的图块层</a>。
</iframe>

## <a name="customize-a-tile-layer"></a>自定义图块层

切片图层类具有许多样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='图块层选项' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>图块层选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图像层](./map-add-image-layer.md)
