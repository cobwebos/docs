---
title: 使用 Azure Maps 显示路线 | Microsoft Docs
description: 如何在 Javascript 地图上显示两个位置之间的路线
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 80abd6db9888524aa6a66d9861d8dc2d05188e19
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781490"
---
# <a name="show-directions-from-a-to-b"></a>显示从 A 到 B 的路线 

本文展示了如何发出路线请求并在地图上显示路线。 

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线（服务模块）' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RBZbep/'>在地图上显示从 A 到 B 的路线（服务模块）</a>。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块中的行对服务客户端进行实例化。

第三个代码块初始化地图上的 [Line String Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)。

第四个代码块创建图钉并将其添加到地图上来表示路线的起点和终点。 有关说明，可以参阅[在地图上添加图钉](map-add-pin.md)。

下一个代码块使用 map 类的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 函数基于路线的起点和终点设置地图的边界框。

第六个代码块构造路线查询。

最后一个代码块通过 [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections) 方法查询 Azure Maps 路线服务以获取起点和目标点之间的路线。 然后使用 [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes) 方法将响应解析为 GeoJSON 格式。 它将所有那些线条添加到地图来呈现路线。 有关更多信息，可以参阅[在地图上添加线条](./map-add-shape.md#addALine)。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 

* [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.route?view=azure-iot-typescript-latest#getroutedirections)
* [getGeoJsonRoutes](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.geojson.geojsonroutedirectionsresponse?view=azure-iot-typescript-latest#getgeojsonroutes)
* [Line String Layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)

有关可向地图添加的更多代码示例，请参阅以下文章： 
* [在地图上显示交通信息](./map-show-traffic.md)
* [与地图交互 - 鼠标事件](./map-events.md)
