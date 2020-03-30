---
title: 在地图上显示流量 |微软 Azure 地图
description: 在本文中，您将学习如何使用 Microsoft Azure 地图 Web SDK 在地图上显示流量数据。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534808"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure 地图中有两种类型的流量数据可用：

- 事故数据 - 由建筑、道路封闭和事故等基于点和线的数据组成。
- 流量数据 - 提供有关道路上交通流量的指标。 通常，交通流量数据用于对道路进行着色。 颜色基于流量减慢流量的速度、相对于速度限制或其他指标。 Azure 地图中的流量数据有三种不同的度量指标：
    - `relative`- 相对于道路的自由流动速度。
    - `absolute`- 是道路上所有车辆的绝对速度。
    - `relative-delay`- 显示低于平均预期延迟的区域。

以下代码演示如何在地图上显示流量数据。

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

以下工具允许您在不同的流量叠加设置之间切换，以查看呈现如何更改。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通状况叠加选项" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>流量叠加选项</a>。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [地图](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [流量选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

增强用户体验：

> [!div class="nextstepaction"]
> [地图与鼠标事件交互](map-events.md)

> [!div class="nextstepaction"]
> [生成可访问的地图](map-accessibility.md)

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
