---
title: 将延伸多边形层添加到 Azure Maps |Microsoft Docs
description: 如何将延伸多边形层添加到 Azure Maps Web SDK。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fdb5e2b78d9e5817c5a5d139cdf0b34744ed011f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170706"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>向地图添加延伸多边形层

本文介绍如何使用多边形延伸层在地图上将 @no__t 0 和 @no__t 功能几何的面积渲染为延伸形状。 Azure Maps Web SDK 还支持按[扩展 GeoJSON 架构](extend-geojson.md#circle)中的定义创建圆形几何。 在地图上呈现时，这些圆圈将转换为多边形。 如果用阿特拉斯包装，还可以轻松更新所有功能几何[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)类。


## <a name="use-a-polygon-extrusion-layer"></a>使用多边形延伸层

当[多边形延伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)连接到数据源并将其加载到地图上时，它会将 `Polygon` 和 `MultiPolygon` 功能的区域呈现为延伸形状。 多边形延伸层的 @no__t 0 和 @no__t 属性定义以**米为单位**的延伸形状的地面和高度的基准距离。 下面的代码演示如何创建一个多边形，如何将其添加到数据源，并使用多边形延伸层类进行呈现。

> [!Note]
> 多边形延伸层中定义的 @no__t 0 值应小于或等于 `height` 的值。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="突出多边形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>延伸多边形</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。</iframe>


## <a name="add-data-driven-multipolygons"></a>添加数据驱动的 multipolygons

可以使用多边形延伸层来呈现等值线图地图，方法是将其 @no__t 0 和 @no__t 属性设置为与 @no__t 和 @no__t 功能几何中统计变量的度量值成正比。 下面的代码示例显示了等值线图的拉伸的映射，该映射基于按状态测量人口密度。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸的等值线图映射" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）查看笔<a href='https://codepen.io/azuremaps/pen/eYYYNox'>拉伸的等值线图映射</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>向地图添加圆圈

Azure Maps 使用 GeoJSON 架构的扩展版本，它提供了圆的定义，如[此处](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述。 可以通过使用 `Circle` 的 @no__t 属性和以**米为单位**表示半径的已编号的 @no__t 属性创建一个 `point` 功能，来在地图上呈现延伸圆。 例如：

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

Azure Maps Web SDK 会将这些 `Point` 功能转换为后台的 @no__t 一项功能，并且可以使用多边形延伸层在地图上呈现，如以下代码示例中所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="无人机空域多边形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>无人机空域多边形</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [多边形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多边形延伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他资源:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 规范扩展](extend-geojson.md#circle)
