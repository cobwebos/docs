---
title: 在地图上显示路线方向 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 在地图上的两个位置之间显示方向。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: dde9264d0cb65726b624b918982cfa01985b63ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371410"
---
# <a name="show-directions-from-a-to-b"></a>显示从 A 到 B 的路线

本文展示了如何发出路线请求并在地图上显示路线。

可通过两种方法来执行此操作。 第一种方法是通过服务模块查询 [Azure Maps 路线 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。 第二种方法是使用[Fetch API](https://fetch.spec.whatwg.org/)向[Azure 地图路由 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)发出搜索请求。 下面将讨论这两种方法。

## <a name="query-the-route-via-service-module"></a>通过服务模块查询路线

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线（服务模块）' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地图上显示从 A 到 B 的路线（服务模块）</a>。
</iframe>

在上述代码中，第一个块构造一个映射对象，并将身份验证机制设置为使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建一`TokenCredential`个用于使用访问令牌对 Azure 映射的 HTTP 请求进行身份验证。 然后，它将`TokenCredential`传递给`atlas.service.MapsURL.newPipeline()`并创建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)实例。 `routeURL` 表示 Azure Maps [Route](https://docs.microsoft.com/rest/api/maps/route) 操作的 URL。

第三个代码块创建[DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)对象并将其添加到地图中。

代码的第四个块创建起始点和结束[点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)对象，并将它们添加到 DataSource 对象。

线[是线](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)弦的功能。 [LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

[符号图层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现数据[源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中包装的基于点的数据。 文本或图标在地图上呈现为符号。 第五个代码块创建符号图层并将其添加到地图中。

第六个代码块查询 Azure 映射路由服务，该服务是[服务模块](how-to-use-services-module.md)的一部分。 RouteURL 的计算[路由方向](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods)方法用于获取起点和终点之间的路由。 然后使用`geojson.getFeatures()`方法提取响应中的 GeoJSON 要素集合，并将其添加到数据源中。 然后，它将响应呈现为地图上的路线。 有关向地图添加线条的详细信息，请参阅[在地图上添加线条](map-add-line-layer.md)。

最后一个代码块使用地图的[setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)属性设置地图的边界。

路由查询、数据源、符号、线图层和摄像机边界在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中创建。 此代码结构可确保仅在地图完全加载后显示结果。

## <a name="query-the-route-via-fetch-api"></a>通过 Fetch API 查询路由

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>（在地图上显示从 A 到 B 的方向）。
</iframe>

在上面的代码中，第一个代码块构造一个映射对象，并设置身份验证机制来使用访问令牌。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 对象并将其添加到地图。

第三个代码块创建路由的开始点和目标点。 然后，它将它们添加到数据源。 有关如何使用 [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 的说明，可以参阅[在地图上添加图钉](map-add-pin.md)。

[LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) 呈现 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中包装的线条对象（作为地图中的线条）。 第四个代码块创建线条层并将其添加到地图。 请参阅 [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest) 中介绍的线条层属性。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 第五个代码块创建符号图层并将其添加到地图中。 请在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 中参阅符号层的属性。

下一个代码块从起点和终点之间创建 `SouthWest` 和 `NorthEast` 点，并使用地图的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) 属性设置地图的边界。

最后一个代码块使用[Fetch API](https://fetch.spec.whatwg.org/)向[Azure 地图路由 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)发出搜索请求。 然后分析响应。 如果响应成功，则纬度和经度信息用于通过连接这些点来创建数组。 然后，将线数据添加到数据源以呈现地图上的路由。 有关说明，可以参阅[在地图上添加线条](map-add-line-layer.md)。

路由查询、数据源、符号、线图层和摄像机边界在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中创建。 同样，我们希望确保在地图完全加载后显示结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用路由服务的最佳做法](how-to-use-best-practices-for-search.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [地图](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [在地图上显示交通信息](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [与地图交互 - 鼠标事件](./map-events.md)
