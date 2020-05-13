---
title: 在地图上显示流量 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure map Web SDK 显示地图上的流量数据。
author: Philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9c17c3cc22d478d81ed3c2b2ae9f61c173aad6cd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123915"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure Maps 提供了两种类型的流量数据：

- 事件数据-由基于点的数据和基于行的数据组成，如构造、公路和意外等。
- 流数据-提供有关公路流量的指标。 通常，流量流数据用于为道路着色。 这些颜色基于在流中减慢流量的频率，相对于速度限制或其他指标。 Azure Maps 中的流量数据具有三个不同的度量指标：
    - `relative`-相对于道路的自由流动速度。
    - `absolute`-是旅途中所有车辆的绝对速度。
    - `relative-delay`-显示比平均预期延迟慢的区域。

下面的代码演示如何在地图上显示流量数据。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='在地图上显示交通信息' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>（在地图上显示交通信息）。
</iframe>

## <a name="traffic-overlay-options"></a>交通状况叠加选项

以下工具可让你在不同的流量重叠设置之间进行切换，以查看呈现如何变化。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通状况叠加选项" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "笔<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>流量重叠" 选项</a>Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> ） <a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [映射](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

增强用户体验：

> [!div class="nextstepaction"]
> [地图与鼠标事件交互](map-events.md)

> [!div class="nextstepaction"]
> [生成可访问的地图](map-accessibility.md)

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
