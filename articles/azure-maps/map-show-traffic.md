---
title: 使用 Azure Maps 显示交通信息 | Microsoft Docs
description: 如何在 Javascript 地图上显示交通数据
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 532001a0cda22903d0bdf807ee868aef211336e0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240078"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

本文演示如何在地图上显示交通和事件信息。

## <a name="understand-the-code"></a>了解代码

<iframe height='456' scrolling='no' title='在地图上显示交通信息' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>（在地图上显示交通信息）。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](map-create.md)。

第二个代码块使用 map 类的 [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#settraffic) 函数在地图上呈现交通流量和事件。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)

增强用户体验：

> [!div class="nextstepaction"]
> [地图与鼠标事件交互](./map-events.md)

> [!div class="nextstepaction"]
> [生成可访问的地图](./map-accessibility.md)
