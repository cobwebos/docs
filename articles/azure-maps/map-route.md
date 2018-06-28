---
title: 使用 Azure Maps 显示路线 | Microsoft Docs
description: 如何在 Javascript 地图上显示两个位置之间的路线
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 5e9ab73ddc16517e17894cddd9bc102f3941f00c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "35768295"
---
# <a name="show-directions-from-a-to-b"></a>显示从 A 到 B 的路线 

本文展示了如何发出路线请求并在地图上显示路线。 

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='在地图上显示从 A 到 B 的路线' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Show directions from A to B on a map</a>（在地图上显示从 A 到 B 的方向）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建图钉并将其添加到地图上来表示路线的起点和终点。 有关说明，可以参阅[在地图上添加图钉](map-add-pin.md)。

第三个代码块使用 map 类的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 函数基于路线的起点和终点设置地图的边界框。

第四个代码块将一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 发送到 [Azure Maps 路由 API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)。

最后一个代码块分析传入的响应。 对于成功的响应，它收集每个路标的纬度和经度信息。 它通过将每个路标连接到其后面的路标来创建线条数组。 它将所有那些线条添加到地图来呈现路线。 有关说明，可以参阅[在地图上添加线条](./map-add-shape.md#addALine)。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)

有关可向地图添加的更多代码示例，请参阅以下文章： 
* [在地图上显示交通信息](./map-show-traffic.md)
* [与地图交互 - 鼠标事件](./map-events.md)
