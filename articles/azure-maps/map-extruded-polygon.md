---
title: 向地图添加多边形延伸层 |Microsoft Azure 映射
description: 如何将多边形延伸层添加到 Microsoft Azure Map Web SDK。
author: farah-alyasari
ms.author: v-faalya
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 38a7e6c5f4c139343e735ae82616c11a224ae7ca
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209675"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>向地图添加多边形延伸层

本文介绍如何使用多边形延伸层将 `Polygon` 和 `MultiPolygon` 特征几何的面积渲染为延伸形状。 Azure Maps Web SDK 支持按[扩展 GeoJSON 架构](extend-geojson.md#circle)中的定义呈现圆形几何。 在地图上呈现时，可以将这些圆转换为多边形。 用阿特拉斯包装时，所有功能几何都可以轻松更新[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)类。

## <a name="use-a-polygon-extrusion-layer"></a>使用多边形延伸层

将[多边形延伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)连接到数据源。 然后，将其加载到地图中。 多边形延伸层将 `Polygon` 和 `MultiPolygon` 特征的面积渲染为延伸形状。 多边形延伸层的 "`height`" 和 "`base`" 属性定义了与延伸形状的地面和高度的基准距离（以**米为单位**）。 下面的代码演示如何创建一个多边形，如何将其添加到数据源中，以及如何使用多边形延伸层类进行呈现。

> [!Note]
> 多边形延伸层中定义的 `base` 值应小于或等于 `height`的值。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="突出多边形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>延伸多边形</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。</iframe>


## <a name="add-data-driven-polygons"></a>添加数据驱动多边形

可以使用多边形延伸层来呈现等值线图地图。 将延伸层的 "`height`" 和 "`fillColor` 属性" 设置为 "`Polygon` 和 `MultiPolygon` 特征几何中统计变量的度量值。 下面的代码示例显示了等值线图的拉伸的映射，该映射基于按状态测量人口密度。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸的等值线图映射" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/eYYYNox'>延伸等值线图映射</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="add-a-circle-to-the-map"></a>向地图添加圆圈

Azure Maps 使用 GeoJSON 架构的扩展版本，它提供了圆的定义，如[此处](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述。 可以通过创建一个 `point` 功能，该功能的 `subType` 属性为 `Circle`，以**米为单位**表示半径的已编号的 `Radius` 属性，来在地图上呈现拉伸圆形。 例如：

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

Azure Maps Web SDK 会将这些 `Point` 功能转换为后台 `Polygon` 功能。 这些 `Point` 功能可以使用多边形延伸层在地图上呈现，如下面的代码示例中所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="无人机空域多边形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>无人机空域多边形</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自定义多边形延伸层

多边形延伸层具有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [多边形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多边形延伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他资源：

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 规范扩展](extend-geojson.md#circle)
