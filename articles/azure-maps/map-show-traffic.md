---
title: 使用 Azure Maps 显示交通信息 | Microsoft Docs
description: 如何在 Javascript 地图上显示交通数据
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 6ff7a0270509c244fc97bd04d8ba648fd262dc58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600113"
---
# <a name="show-traffic-on-the-map"></a>在地图上显示交通信息

本文演示如何在地图上显示交通和事件信息。 

## <a name="understand-the-code"></a>了解代码

<iframe height='456' scrolling='no' title='在地图上显示交通信息' src='//codepen.io/azuremaps/embed/WMLRPw/?height=456&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>Show traffic on a map</a>（在地图上显示交通信息）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](map-create.md)。

第二个代码块使用 map 类的 [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic) 函数在地图上呈现交通流量和事件。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setTraffic](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#settraffic)

有关可向地图添加的更多代码示例，请参阅以下文章： 
* [与地图交互 - 鼠标事件](./map-events.md)
* [生成可访问的地图](./map-accessibility.md)

查看我们的[代码示例页](http://aka.ms/AzureMapsSamples)，了解更多映射方案。
