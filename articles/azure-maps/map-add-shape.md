---
title: 向地图添加多边形层 | Microsoft Azure Maps
description: 了解如何将多边形或圆添加到地图中。 了解如何使用 Azure Maps Web SDK 自定义几何形状，并使其易于更新和维护。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 74c8ba4ed1f228b9b3ba90b46c47f538d71ff409
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310452"
---
# <a name="add-a-polygon-layer-to-the-map"></a>向地图添加多边形层

本文介绍如何使用多边形层渲染地图上的 `Polygon` 和 `MultiPolygon` 特征几何图形区域。 Azure Maps Web SDK 还支持按[扩展 GeoJSON 架构](extend-geojson.md#circle)中定义的方式创建 Circle 几何图形。 在地图上渲染时，这些圆将转换为多边形。 使用 [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape) 类进行包装时，可轻松更新所有特征几何图形。

## <a name="use-a-polygon-layer"></a>使用多边形层 

将多边形层连接到数据源并加载到地图上时，它将渲染具有 `Polygon` 和 `MultiPolygon` 特征的区域。 要创建多边形，请将其添加到数据源，并使用多边形层通过 [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer) 类对其进行渲染。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

下面是上述代码的完整运行示例。

<br/>

<iframe height='500' scrolling='no' title='向地图添加多边形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>（向地图添加多边形）。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>结合使用多边形和线条层

线条层用于渲染多边形的轮廓。 下面的代码示例像上一个示例一样渲染了一个多边形，但添加了一个线条层。 此线条层是连接到数据源的另一个层。  

<br/>

<iframe height='500' scrolling='no' title='用于添加多边形的多边形和线条层' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps 创建<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用于添加多边形的多边形和线层</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>使用图案填充多边形

除了为多边形填充颜色之外，还可以使用图像图案来进行填充。 将图像图案加载到地图图像子画面资源中，然后使用多边形层的 `fillPattern` 属性引用该图像。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多边形填充图案" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Polygon fill pattern</a>（多边形填充图案）。
</iframe>


> [!TIP]
> Azure Maps Web SDK 提供了多个可与填充图案一起使用的可自定义的图像模板。 有关详细信息，请参阅[如何使用图像模板](how-to-use-image-templates-web-sdk.md)文档。

## <a name="customize-a-polygon-layer"></a>自定义多边形层

多边形层只有几个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>将圆添加到地图

Azure Maps 使用扩展版本的 GeoJSON 架构提供圆的定义，如[此处](extend-geojson.md#circle)所述。 通过创建 `Point` 特征，在地图上渲染了一个圆。 此 `Point` 具有一个 `subType` 属性和一个 `radius` 属性，前者的值为 `"Circle"`，后者带有一个表示半径（以米为单位）的数字。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps Web SDK 将这些 `Point` 特征转换为 `Polygon` 特征。 然后，使用多边形和线条层在地图上渲染这些特征，如下面的代码示例中所示。

<br/>

<iframe height='500' scrolling='no' title='向地图添加圆圈' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>（向地图添加圆圈）。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>简化几何图形更新

`Shape` 类包装[几何图形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry)或[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature)，以便可以轻松更新和维护这些特征。 要实例化形状变量，请将一个几何图形或一组属性传递给形状构造函数。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

下面的代码示例演示如何使用 shape 类包装 circle GeoJSON 对象。 当形状中的半径值改变时，圆圈会自动渲染在地图上。

<br/>

<iframe height='500' scrolling='no' title='更新形状属性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新形状属性</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [多边形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用图像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

其他资源：

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON 规范扩展](extend-geojson.md#circle)