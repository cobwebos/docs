---
title: 使用 Azure Maps 处理鼠标事件 | Microsoft Docs
description: 如何使用地图事件创建交互式 Web SDK 地图
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 37a3fc3178fe5caeacedfd355a6065ee189a5890
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976534"
---
# <a name="interact-with-the-map---mouse-events"></a>与地图交互 - 鼠标事件

本文介绍如何使用[map class events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)属性突出显示地图上以及地图的不同层上的事件。 它还展示了在与 HTML 标记进行交互时如何使用 map 类 events 属性来突出显示事件。

## <a name="interact-with-the-map"></a>与地图交互

观看下面的地图, 查看右侧突出显示的相应鼠标事件。 可以单击 JS 选项卡，查看和编辑 JavaScript 代码。 还可以单击“编辑 CodePen”按钮，编辑 CodePen 上的代码。

<br/>

<iframe height='600' scrolling='no' title='与地图交互 - 鼠标事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interacting with the map - mouse events</a>（与地图交互 - 鼠标事件）。
</iframe>

## <a name="interact-with-map-layers"></a>与地图层交互

下面的代码重点介绍当您与符号层交互时触发的事件的名称。 符号、气泡图、线条和多边形层都支持相同的一组事件。 热度地图和图块层不支持这些事件中的任何一个。

<br/>

<iframe height='600' scrolling='no' title='与地图交互 – 层事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a>（与地图交互 – 层事件）。
</iframe>

## <a name="interact-with-html-marker"></a>与 HTML 标记交互

下面的代码将 Javascript 映射事件添加到 HTML 标记。 它还突出显示了与 HTML 标记进行交互时触发的事件的名称。

<br/>

<iframe height='500' scrolling='no' title='与地图交互 - HTML 标记事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a>（与地图交互 - HTML 标记事件）。
</iframe>

## <a name="next-steps"></a>后续步骤

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Azure Maps Services 模块](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
