---
title: 向地图添加多边形层 |Microsoft Azure 映射
description: 在本文中，你将了解如何在 Microsoft Azure map Web SDK 中的地图上呈现多边形和多个多边形。
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 91d6ed24c85d08c4b3358204980498d64e1b36ba
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124034"
---
# <a name="add-a-polygon-layer-to-the-map"></a>向地图添加多边形层

本文介绍如何 `Polygon` `MultiPolygon` 使用多边形层在地图上呈现和功能几何。 Azure Maps Web SDK 还支持按[扩展 GeoJSON 架构](extend-geojson.md#circle)中的定义创建圆形几何。 在地图上呈现时，这些圆圈将转换为多边形。 用阿特拉斯包装时，所有功能几何都可以轻松地进行更新[。Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)类。

## <a name="use-a-polygon-layer"></a>使用多边形层 

当多边形层连接到数据源并将其加载到地图上时，它将呈现带有 `Polygon` 和功能的区域 `MultiPolygon` 。 若要创建多边形，请将其添加到数据源，并使用[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)类将其呈现为多边形层。

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

下面是上述代码的完整和运行的示例。

<br/>

<iframe height='500' scrolling='no' title='向地图添加多边形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>（向地图添加多边形）。
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>一起使用多边形和线条层

线条层用于呈现多边形的轮廓。 下面的代码示例将呈现与上一个示例类似的多边形，但现在会添加一个线条层。 此线条层是连接到数据源的另一层。  

<iframe height='500' scrolling='no' title='用于添加多边形的多边形和线条层' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps 创建<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用于添加多边形的多边形和线层</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>使用图案填充多边形

除了使用颜色填充多边形以外，还可以使用图像模式来填充多边形。 将图像模式加载到地图图像 sprite 资源，然后使用多边形层的属性引用此图像 `fillPattern` 。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多边形填充图案" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "笔<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多边形填充模式</a>" Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>）。
</iframe>


> [!TIP]
> Azure Maps web SDK 提供了几个可自定义的图像模板，可将其用作填充模式。 有关详细信息，请参阅[如何使用映像模板](how-to-use-image-templates-web-sdk.md)文档。

## <a name="customize-a-polygon-layer"></a>自定义多边形层

多边形层只有几个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>将圆添加到地图

Azure Maps 使用 GeoJSON 架构的扩展版本，它提供了圆形的定义，如[此处](extend-geojson.md#circle)所述。 通过创建一项功能，在地图上呈现一个圆圈 `Point` 。 此 `Point` 属性具有 `subType` 值为的属性和属性，该属性的值 `"Circle"` `radius` 表示以米为单位的半径。 

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

Azure Maps Web SDK 将这些 `Point` 功能转换为 `Polygon` 功能。 然后，使用多边形和线条层在地图上呈现这些功能，如下面的代码示例中所示。

<br/>

<iframe height='500' scrolling='no' title='向地图添加圆圈' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>（向地图添加圆圈）。
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>使几何易于更新

`Shape`类包装[几何](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)，使更新和维护这些功能变得轻松。 若要实例化形状变量，请将一个或一组属性传递给形状构造函数。

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

下面的代码示例演示如何使用 shape 类包装 circle GeoJSON 对象。 当形状中的半径值改变时，圆圈会自动呈现在地图上。

<br/>

<iframe height='500' scrolling='no' title='更新形状属性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新形状属性</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

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