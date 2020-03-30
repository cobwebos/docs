---
title: 将多边形拉伸图层添加到地图 |微软 Azure 地图
description: 如何向 Microsoft Azure 映射 Web SDK 添加多边形拉伸图层。
author: philmea
ms.author: philmea
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7405098bd4924333aafcd1c285eb2f37bb1d4f75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334545"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>向地图添加多边形拉伸图层

本文介绍如何使用多边形拉伸图层将几何区域渲染为拉伸形状`Polygon`，并将`MultiPolygon`几何体特征化为拉伸形状。 Azure 地图 Web SDK 支持呈现扩展的[GeoJSON 架构](extend-geojson.md#circle)中定义的圆几何体。 在地图上渲染时，这些圆可以转换为多边形。 使用地图集包装时，所有要素几何体都可以轻松更新[。形状](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)类。

## <a name="use-a-polygon-extrusion-layer"></a>使用多边形拉伸图层

将[多边形拉伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)连接到数据源。 然后，加载到地图上。 多边形拉伸图层将 和`Polygon``MultiPolygon`要素的面积渲染为拉伸形状。 多边`height`形`base`拉伸图层的 和 属性定义与拉伸形状的基本距离和高度 **（以米**为单位）。 以下代码演示如何创建多边形、将其添加到数据源，以及使用多边形拉伸图层类呈现它。

> [!Note]
> 在`base`多边形拉伸图层中定义的值应小于或等于 。 `height`

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸多边形" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure 贴<a href='https://codepen.io/azuremaps'>@azuremaps</a>图 （）<a href='https://codepen.io/azuremaps/pen/wvvBpvE'>拉伸的</a>笔拉伸面。</iframe>


## <a name="add-data-driven-polygons"></a>添加数据驱动的多边形

可以使用多边形拉伸图层渲染色色谱贴图。 将`height`拉伸图层`fillColor`和属性设置为`Polygon`和`MultiPolygon`要素几何中统计变量的度量。 以下代码示例显示基于按州测量的人口密度的美国挤压性乔普勒图。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="拉伸的乔龙图" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure 地图（）<a href='https://codepen.io/azuremaps'>@azuremaps</a>绘制的笔拉伸<a href='https://codepen.io/azuremaps/pen/eYYYNox'>图</a>。
</iframe>

## <a name="add-a-circle-to-the-map"></a>将圆添加到地图

Azure 映射使用 GeoJSON 架构的扩展版本，该架构为[此处](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle)所述的圆圈提供定义。 通过`point`创建属性`subType``Circle`和表示半径（`Radius`**以米**为单位的编号属性）的要素，可以在地图上渲染拉伸圆。 例如：

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

Azure 地图 Web SDK`Point`将这些功能`Polygon`转换为引擎盖下的要素。 这些`Point`要素可以使用多边形拉伸图层在地图上呈现，如下代码示例所示。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="无人机空域多边形" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 的笔<a href='https://codepen.io/azuremaps/pen/zYYYrxo'>无人机空域面</a>。
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>自定义多边形拉伸图层

多边形拉伸层具有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='普格布雷杰' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上通过 Azure 地图<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看 Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ。</a>
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [多边形拉伸层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

其他资源：

> [!div class="nextstepaction"]
> [Azure 映射 GeoJSON 规范扩展](extend-geojson.md#circle)
