---
title: 在地图上显示交通信息 | Microsoft Azure Maps
description: 了解如何将流量数据添加到 maps。 了解流数据，并了解如何使用 Azure Maps Web SDK 将事件数据和流数据添加到 Maps。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 584db064bf6f6a6b99e6e2d09f6046912cfcd1bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335239"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

Azure Maps 中提供了两种类型的交通数据：

- 事件数据 - 由基于点和线的数据组成，针对诸如施工、道路封闭和事故等事项。
- 流量数据 - 提供有关道路交通流量的指标。 通常，交通流量流数据用于为道路着色。 这些颜色基于相对于速度限制或其他指标而言，多大的交通会减慢流量。 Azure Maps 中的交通流量数据具有三个不同的度量指标：
    - `relative` - 相对于道路的自由流量速度。
    - `absolute` - 是道路中所有车辆的绝对速度。
    - `relative-delay` - 显示比平均预期延迟更慢的区域。

下面的代码演示如何在地图上显示交通数据。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='在地图上显示交通信息' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>（在地图上显示交通信息）。
</iframe>

## <a name="traffic-overlay-options"></a>交通状况叠加选项

以下工具可让你在不同的交通状况叠加设置之间进行切换，以查看呈现如何变化。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通状况叠加选项" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的触笔<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>交通状况叠加选项</a>。
</iframe>


## <a name="add-traffic-controls"></a>添加流量控件

可以将两个不同的流量控件添加到地图中。 第一个控件 `TrafficControl` 添加可用于打开和关闭流量的切换按钮。 此控件的选项允许您指定显示流量时要使用的流量设置。 默认情况下，此控件将显示相对的流量流和事件数据，但是，你可以将其更改为显示绝对流量流，而不显示事件（如果需要）。 第二个控件 `TrafficLegendControl` 将向地图添加一个流量流图例，该图例可帮助用户了解颜色代码的突出显示含义。 仅当流量在地图上显示时，此控件才会出现在地图上，并且在其他所有时间都将隐藏。

下面的代码演示如何将流量控件添加到映射。

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="流量控制" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "笔<a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>流量控制</a>" Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

增强用户体验：

> [!div class="nextstepaction"]
> [地图与鼠标事件交互](map-events.md)

> [!div class="nextstepaction"]
> [生成可访问的地图](map-accessibility.md)

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
