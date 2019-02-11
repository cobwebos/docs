---
title: 使用 Azure Maps 显示路线 | Microsoft Docs
description: 如何在 Javascript 地图上显示两个位置之间的路线
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 183194d172b9ac11d4f1c5cb1324f7a09f4a157b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756302"
---
# <a name="show-directions-from-a-to-b"></a>显示从 A 到 B 的路线

本文展示了如何发出路线请求并在地图上显示路线。

可通过两种方法来执行此操作。 第一种方法是通过服务模块查询 [Azure Maps 路线 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二种方法是向该 API 发出 [XMLHttpRequest](https://xhr.spec.whatwg.org/)。 下面将讨论这两种方法。

## <a name="query-the-route-via-service-module"></a>通过服务模块查询路线

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线（服务模块）' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地图上显示从 A 到 B 的路线（服务模块）</a>。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块中的行对客户端服务进行实例化。

第三个代码块创建 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 对象并将其添加到地图。

 线条是 [LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) 的一个特征。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.deprecated.linestringlayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 第五个代码块创建符号层并将其添加到地图。

第六个代码块创建起点和终[点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)对象，并将其添加到 dataSource 对象。

第七个代码块使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性设置地图的边界。

最后一个代码块查询 Azure Maps 路线服务（[服务模块](https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=1)的一部分）。 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest) 方法用于获取起点和终点之间的路线。 然后使用 [getGeoJsonRouteDirectionsResponse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest) 方法将响应解析为 GeoJSON 格式。 然后，它将响应呈现为地图上的路线。 有关向地图添加线条的详细信息，请参阅[在地图上添加线条](./map-add-shape.md#addALine)。

路线查询、数据源、符号和线条层以及照相机边界在地图的[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中进行创建和设置，以确保在地图完全加载后才显示结果。

## <a name="query-the-route-via-xmlhttprequest"></a>通过 XMLHttpRequest 查询路线

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>（在地图上显示从 A 到 B 的方向）。
</iframe>

第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 对象并将其添加到地图。

第三个代码块创建路线的起点和终点，并将它们添加到数据源。 有关如何使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 的说明，可以参阅[在地图上添加图钉](map-add-pin.md)。

 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 第五个代码块创建符号层并将其添加到地图。 请在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 中参阅符号层的属性。

下一个代码块从起点和终点之间创建 `SouthWest` 和 `NorthEast` 点，并使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性设置地图的边界。

最后一个代码块将 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 发送到 [Azure Maps 路由 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 然后，它将解析传入的响应。 对于成功的响应，它将收集每个路线点的纬度和经度信息，并通过连接这些点来创建大量线条。 然后，它将所有这些线条添加到 dataSource 上，以在地图中呈现路线。 有关说明，可以参阅[在地图上添加线条](./map-add-shape.md#addALine)。

路线查询、数据源、符号和线条层以及照相机边界在地图的[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中进行创建和设置，以确保在地图完全加载后才显示结果。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图上显示交通信息](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [与地图交互 - 鼠标事件](./map-events.md)
