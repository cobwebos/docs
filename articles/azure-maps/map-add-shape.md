---
title: 使用 Azure Maps 添加形状 | Microsoft Docs
description: 如何向 Javascript 地图添加形状
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: f61c7a939902ee5d02b2e9ba896c7555968f9d0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769509"
---
# <a name="add-a-shape-to-a-map"></a>向地图添加形状

本文介绍了如何呈现地图使用线条和多边形层上的几何图形。 Azure Maps Web SDK 还支持创建圆形几何中定义[扩展的 GeoJSON 架构](extend-geojson.md#circle)。 所有功能几何图形还可以轻松地都更新如果与包装[形状](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)类。

<a id="addALine"></a>

## <a name="add-lines-to-the-map"></a>将行添加到代码图

`LineString` 和`MultiLineString`功能用于表示路径并在地图上的概述。

### <a name="add-a-line"></a>添加线条

<iframe height='500' scrolling='no' title='向地图添加线条' src='//codepen.io/azuremaps/embed/qomaKv/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>（向地图添加线条）。
</iframe>

上述代码中的第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 将创建 [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest) 对象并将其添加到数据源。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象。 最后一个代码块创建一个线条层并将其添加到地图。 请参阅 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。 创建并添加到映射中的数据源和线条层[事件处理程序](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以确保映射完全加载后显示的行。

### <a name="add-symbols-along-a-line"></a>添加沿着一条线的符号

此示例演示如何在地图上添加沿着一条线的箭头图标。 当使用符号层设置为"行"的"放置"选项时，这将呈现直线符号并旋转图标 (0 度 = right)。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="显示行沿箭头" src="//codepen.io/azuremaps/embed/drBJwX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/drBJwX/'>沿行显示箭头</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="line-stroke-gradient"></a> 将笔划渐变添加到行

除了能够将单个笔画颜色应用到的行之外还可以使用渐变的颜色以显示一条线段从过渡到下一步中填充行。 例如，可以使用行渐变来表示随时间和距离或不同的温度的更改，跨连接的直线的对象。 若要将此功能应用于行，数据源必须具有`lineMetrics`选项设置为 true，并随后的颜色渐变的表达式可以传递到`strokeColor`行的选项。 笔划渐变表达式具有到引用`['line-progress']`公开为表达式计算出来的曲线指标的数据表达式。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="笔划渐变线" src="//codepen.io/azuremaps/embed/wZwWJZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/wZwWJZ/'>笔划渐变线</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="customize-a-line-layer"></a>自定义线条层

线条层有多个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='线条层选项' src='//codepen.io/azuremaps/embed/GwLrgb/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/GwLrgb/'>线条层选项</a>。
</iframe>

<a id="addAPolygon"></a>

## <a name="add-a-polygon-to-the-map"></a>向地图添加多边形

`Polygon` 和`MultiPolygon`功能通常用于表示在地图上的区域。 

### <a name="use-a-polygon-layer"></a>使用多边形层 

多边形层可呈现多边形的面积。 

<iframe height='500' scrolling='no' title='向地图添加多边形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>（向地图添加多边形）。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 将从坐标数组创建 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 并将其添加到数据源。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 最后一个代码块创建一个多边形层并将其添加到地图。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 创建并添加到映射中的数据源和多边形层[事件处理程序](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以确保此多边形映射完全加载后显示。

### <a name="use-a-polygon-and-line-layer-together"></a>一起使用的多边形和线层

线条层可用于呈现轮廓的多边形。 

<iframe height='500' scrolling='no' title='用于添加多边形的多边形和线条层' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps 创建<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用于添加多边形的多边形和线层</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 将从坐标数组创建 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 并将其添加到数据源。 

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 是一系列线条。 请参阅 [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。 第三个代码块创建多边形和线条层。

最后一个代码块将多边形和线条层添加到地图。 创建并添加到映射中的数据源和层[事件处理程序](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以确保此多边形映射完全加载后显示。

> [!TIP]
> 线条层默认情况下将呈现为数据源中的行或多边形的坐标。 若要限制层，以便它仅呈现 LineString 功能集`filter`到层的属性`['==', ['geometry-type'], 'LineString']`或`['any', ['==', ['geometry-type'], 'LineString'], ['==', ['geometry-type'], 'MultiLineString']]`如果你想要包括 MultiLineString 的功能。

### <a name="fill-a-polygon-with-a-pattern"></a>用图案填充多边形

除了使用一种颜色填充多边形还可以使用映像模式。 加载到地图图像子画面资源映像模式，然后引用此映像`fillPattern`多边形层属性。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="多边形填充模式" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅笔<a href='https://codepen.io/azuremaps/pen/JzQpYX/'>多边形填充图案</a>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="customize-a-polygon-layer"></a>自定义多边形层

多边形层只有几个样式选项。 以下工具可用来试用这些选项。

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a>。
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>向映射添加一个圆圈

Azure Maps 使用提供的圆所述定义 GeoJSON 架构的扩展的版本[此处](extend-geojson.md#circle)。 可以通过创建代码图上呈现一个圆圈`Point`具有的功能`subType`属性值为`"Circle"`和一个`radius`属性具有一个数字，表示以米为单位的半径。 例如：

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

Azure Maps Web SDK 将这些数据转换`Pooint`功能到`Polygon`功能在后台，并可以使用多边形和线图层，如下所示在地图上呈现。

<iframe height='500' scrolling='no' title='向地图添加圆圈' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>（向地图添加圆圈）。
</iframe>

上述代码中的第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 是一个圆[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)的[点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)并且具有`subType`属性设置为`"Circle"`和`radius`以米为单位的属性值。 时具有的点功能`subType`的`"Circle"`添加到数据源，则其转换为地图内的循环多边形。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 最后一个代码块创建一个多边形层并将其添加到地图。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 创建并添加到映射中的数据源和多边形层[事件处理程序](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以确保映射完全加载后，显示该圆形。

## <a name="make-a-geometry-easy-to-update"></a>轻松地更新几何图形

一个`Shape`类包装[Geometry](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或[功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)并使其易于更新和维护它们。
`new Shape(data: Feature<data.Geometry, any>)` 构造 shape 对象，并使用指定的特征将其初始化。

<br/>

<iframe height='500' scrolling='no' title='更新形状属性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新形状属性</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

上述第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

点是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 类的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 第二个代码块初始化 HTML 滑块元素的半径值，然后在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类对象中构造并包装 point 对象。

第三个代码块创建一个函数，该函数从 HTML 范围滑块元素中取值，并使用 shape 类的 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 方法更改半径值。

在第四个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 然后将点添加到数据源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 第三个代码块创建多边形层。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 创建和添加到映射中的数据源、 click 事件处理程序和多边形层[事件处理程序](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)以确保该映射完全加载后，会显示的点。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)
