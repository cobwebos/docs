---
title: 在地图上显示路线方向 |Microsoft Azure 映射
description: 本文介绍如何使用 Microsoft Azure map Web SDK 在地图上显示两个位置之间的方向。
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 1cde1aaa7c9dba3e28407439a46b0e0a3326e4fc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123966"
---
# <a name="show-directions-from-a-to-b"></a>显示从 A 到 B 的路线

本文展示了如何发出路线请求并在地图上显示路线。

可通过两种方法来执行此操作。 第一种方法是通过服务模块查询 [Azure Maps 路线 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二种方法是使用[FETCH api](https://fetch.spec.whatwg.org/)向[Azure Maps 路由 api](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)发出搜索请求。 下面将讨论这两种方法。

## <a name="query-the-route-via-service-module"></a>通过服务模块查询路线

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线（服务模块）' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地图上显示从 A 到 B 的路线（服务模块）</a>。
</iframe>

在上面的代码中，第一个块构造一个 map 对象，并将身份验证机制设置为使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建 `TokenCredential` ，使用访问令牌对要 Azure Maps 的 HTTP 请求进行身份验证。 然后，它将传递 `TokenCredential` 给 `atlas.service.MapsURL.newPipeline()` 并创建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)实例。 `routeURL` 表示 Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) 操作的 URL。

第三个代码块创建[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)对象并将其添加到该映射。

第四个代码块创建开始点和终结[点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)对象，并将它们添加到 dataSource 对象。

线条是 LineString 的一[项功能](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标呈现包装在[数据源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中的基于点的数据。 文本或图标呈现为地图上的符号。 第五个代码块创建符号层并将其添加到地图中。

第六个代码块查询 Azure Maps 路由服务，该服务是[服务模块](how-to-use-services-module.md)的一部分。 RouteURL 的[calculateRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods)方法用于获取起点和终点之间的路线。 然后使用方法提取响应中的 GeoJSON 功能集合 `geojson.getFeatures()` ，并将其添加到数据源。 然后，它将响应呈现为地图上的路线。 有关向地图添加线条的详细信息，请参阅[在地图上添加线条](map-add-line-layer.md)。

最后一个代码块使用地图的[setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)属性设置地图的界限。

路由查询、数据源、符号、线条层和相机边界是在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内创建的。 此代码结构可确保仅在映射完全加载后显示结果。

## <a name="query-the-route-via-fetch-api"></a>通过提取 API 查询路由

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>（在地图上显示从 A 到 B 的方向）。
</iframe>

在上面的代码中，第一个代码块构造一个 map 对象，并将身份验证机制设置为使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 对象并将其添加到地图。

第三个代码块创建路由的起点和终点。 然后，将其添加到数据源。 有关如何使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 的说明，可以参阅[在地图上添加图钉](map-add-pin.md)。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 第五个代码块创建符号层并将其添加到地图中。 请在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 中参阅符号层的属性。

下一个代码块从起点和终点之间创建 `SouthWest` 和 `NorthEast` 点，并使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性设置地图的边界。

最后一个代码块使用[FETCH api](https://fetch.spec.whatwg.org/)向[Azure Maps 路由 api](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)发出搜索请求。 然后分析响应。 如果响应成功，则通过连接这些点将纬度和经度信息用于创建一条直线。 然后，将行数据添加到数据源，以在地图上呈现路由。 有关说明，可以参阅[在地图上添加线条](map-add-line-layer.md)。

路由查询、数据源、符号、线条层和相机边界是在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)内创建的。 同样，我们想要确保在地图完全加载后显示结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [有关使用路由服务的最佳做法](how-to-use-best-practices-for-search.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [映射](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图上显示交通信息](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [与地图交互 - 鼠标事件](./map-events.md)
