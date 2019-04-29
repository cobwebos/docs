---
title: 使用 Azure Maps 显示交通信息 | Microsoft Docs
description: 如何在 Javascript 地图上显示交通数据
author: jingjing-z
ms.author: jinzh
ms.date: 11/10/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7cd7c0dbb375dad78927183dbaffe574a0dc10c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768829"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

本文演示如何在地图上显示交通和事件信息。

## <a name="understand-the-code"></a>了解代码

<iframe height='456' scrolling='no' title='在地图上显示交通信息' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>（在地图上显示交通信息）。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](map-create.md)。

第二个代码块使用地图的[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中的 [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函数在地图上呈现交通流量和事件。

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
