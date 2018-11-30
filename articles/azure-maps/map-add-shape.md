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
ms.openlocfilehash: 505514d836c7b3244464ac53d268cc346a9321ad
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263327"
---
# <a name="add-a-shape-to-a-map"></a>向地图添加形状

本文介绍如何将[形状](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)添加到地图，并更新地图上现有形状的属性。

<a id="addALine"></a>

## <a name="add-a-line"></a>添加线条

<iframe height='500' scrolling='no' title='向地图添加线条' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>（向地图添加线条）。
</iframe>

上述代码中的第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 线条是 [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 的一个特征。 然后，该代码块使用 `new atlas.Shape(new atlas.data.Feature((new atlas.data.LineString()))` 在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类对象中创建并包装一个线条，随后将其添加到数据源。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象。 最后一个代码块创建一个线条层并将其添加到地图。 请参阅 [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。 在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源和线条层并将其添加到地图，以确保完全加载地图后显示该线条。

<a id="addACircle"></a>

## <a name="add-a-circle"></a>添加圆圈

<iframe height='500' scrolling='no' title='向地图添加圆圈' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>（向地图添加圆圈）。
</iframe>

上述代码中的第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 圆是[点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 然后，该代码块使用 `new atlas.Shape(new atlas.data.Feature(new atlas.data.Point()))` 在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类对象中创建并包装一个圆，随后将其添加到数据源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 最后一个代码块创建一个多边形层并将其添加到地图。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源和多边形层并将其添加到地图，以确保完全加载地图后显示该圆。

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>添加多边形

可通过两种不同的方法将多边形添加到地图。 以下示例介绍了这两种方法。

### <a name="use-polygon-layer"></a>使用多边形层 

<iframe height='500' scrolling='no' title='向地图添加多边形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>（向地图添加多边形）。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 [多边形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)是 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 类的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` 用于创建多边形，其顺序坐标包装在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类对象中。 然后将 shape 对象添加到数据源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 最后一个代码块创建一个多边形层并将其添加到地图。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源和多边形层并将其添加到地图，以确保完全加载地图后显示该多边形。

### <a name="use-polygon-and-line-layer"></a>使用多边形和线条层

<iframe height='500' scrolling='no' title='用于添加多边形的多边形和线条层' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps 创建<a href='https://codepen.io/azuremaps/pen/aRyEPy/'>用于添加多边形的多边形和线层</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 [多边形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)是 [Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest) 类的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 `new atlas.Shape(new atlas.data.Feature(new atlas.data.Polygon()))` 用于创建多边形，其顺序坐标包装在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类对象中。 然后将 shape 对象添加到数据源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 是一系列线条。 请参阅 [LinestringLayerOptions](/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。 第三个代码块创建多边形和线条层。

最后一个代码块将多边形和线条层添加到地图。 在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源和层并将其添加到地图，以确保完全加载地图后显示该多边形。

## <a name="update-a-shape"></a>更新形状

Shape 类包装[几何图形](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest)或[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)，以便可以轻松更新和维护它们。
`new Shape(data: Feature<data.Geometry, any>)` 构造 shape 对象，并使用指定的特征将其初始化。

<br>

<iframe height='500' scrolling='no' title='更新形状属性' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 中有关通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>更新形状属性</a>的文章 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

上述第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

点是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 类的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 第二个代码块初始化 HTML 滑块元素的半径值，然后在 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类对象中构造并包装 point 对象。

第三个代码块创建一个函数，该函数从 HTML 范围滑块元素中取值，并使用 shape 类的 [addProperty](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#addproperty) 方法更改半径值。

在第四个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 然后将点添加到数据源。

[PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) 呈现地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的数据。 第三个代码块创建多边形层。 请参阅 [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源、点击事件侦听器和多边形层并将其添加到地图，以确保完全加载地图后显示该点。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加自定义 HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [显示搜索结果](./map-search-location.md)
