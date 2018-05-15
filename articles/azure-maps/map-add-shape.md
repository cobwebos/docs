---
title: 使用 Azure Maps 添加形状 | Microsoft Docs
description: 如何向 Javascript 地图添加形状
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>向地图添加形状

本文介绍如何向地图添加线条、圆圈和多边形。 

<a id="addALine"></a>

## <a name="add-a-line"></a>添加线条

<iframe height='500' scrolling='no' title='向地图添加线条' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Add a line to a map</a>（向地图添加线条）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一个线条。 线条是 LineString 的一项[功能](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)，使用 LineStringProperties 作为其 Feature 属性。 请使用 `new atlas.data.Feature(new atlas.data.LineString())` 创建线条并定义其属性。 

线条层是一个线条数组。 最后一个代码块使用 map 类的 [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) 函数向地图添加线条层并定义线条层的属性。 请参阅 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

<a id="addACircle"></a>

## <a name="add-a-circle"></a>添加圆圈

<iframe height='500' scrolling='no' title='向地图添加圆圈' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Add a circle to a map</a>（向地图添加圆圈）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一个圆圈。 圆圈是[点](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest)的一项[功能](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)，使用 [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) 作为其 Feature 属性。 请使用 `new atlas.data.Feature(new atlas.data.Point())` 创建圆圈并定义其属性。

圆圈层是一个圆圈数组。 最后一个代码块使用 map 类的 [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) 函数向地图添加圆圈层并定义圆圈层的属性。 请参阅 [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest) 中介绍的圆圈层属性。

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>添加多边形
<iframe height='500' scrolling='no' title='向地图添加多边形 ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Add a polygon to a map</a>（向地图添加多边形）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一个多边形。 多边形是[多边形](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest)的一项[功能](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest)，使用 [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) 作为其 Feature 属性。 请使用 `new atlas.data.Feature(new atlas.data.Polygon())` 创建多边形并定义其属性。 请在多边形构造函数中提供多边形路径的有序坐标。

多边形层是一个多边形数组。 最后一个代码块使用 map 类的 [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) 函数向地图添加多边形层并定义其属性。 请参阅 [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest) 中介绍的多边形层属性。 
