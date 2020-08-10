---
title: 处理地图事件 | Microsoft Azure Maps
description: 了解用户与地图进行交互时要触发的事件。 查看所有受支持的地图事件的列表。 请参阅如何使用 Azure Maps Web SDK 来处理事件。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: f3a48e4434588b2eb64637e6f0a454148cad39f2
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036435"
---
# <a name="interact-with-the-map"></a>与地图交互

本文介绍如何使用[地图事件类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)。 该属性突出显示地图上以及地图的不同层上的事件。 还可以在与 HTML 标记交互时突出显示事件。

## <a name="interact-with-the-map"></a>与地图交互

体验下面的地图，并查看右侧突出显示的相应鼠标事件。 可以单击 JS 选项卡，查看和编辑 JavaScript 代码。 还可以单击“编辑 CodePen”来修改 CodePen 上的代码。

<br/>

<iframe height='600' scrolling='no' title='与地图交互 - 鼠标事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interacting with the map - mouse events</a>（与地图交互 - 鼠标事件）。
</iframe>

## <a name="interact-with-map-layers"></a>与地图层交互

以下代码突出显示与符号层交互时触发的事件。 符号、气泡、线条和多边形层都支持同一组事件。 热度地图和图块层不支持其中任何事件。

<br/>

<iframe height='600' scrolling='no' title='与地图交互 – 层事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a>（与地图交互 – 层事件）。
</iframe>

## <a name="interact-with-html-marker"></a>与 HTML 标记交互

以下代码将 Javascript 地图事件添加到 HTML 标记。 它还突出显示了与 HTML 标记进行交互时触发的事件的名称。

<br/>

<iframe height='500' scrolling='no' title='与地图交互 - HTML 标记事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a>（与地图交互 - HTML 标记事件）。
</iframe>

下表列出了所有受支持的地图类事件。

| 事件               | 说明 |
|---------------------|-------------|
| `boxzoomend`        | 在“框缩放”交互结束时触发。|
| `boxzoomstart`      | 在“框缩放”交互开始时触发。|
| `click`             | 在地图上的同一点按下并释放指针设备时触发。|
| `close`             | 在通过手动或编程方式关闭弹出窗口时触发。|
| `contextmenu`       | 在单击鼠标右键时触发。|
| `data`              | 在加载或更改任何地图数据时触发。 |
| `dataadded`         | 在将形状添加到 `DataSource` 时触发。|
| `dataremoved`       | 在从 `DataSource` 中移除形状时触发。|
| `datasourceupdated` | 在更新 `DataSource` 对象时触发。|
| `dblclick`          | 在地图上的同一点单击指针设备两次时触发。|
| `drag`              | 在地图、弹出窗口或 HTML 标记上进行“拖动以平移”交互期间反复触发。|
| `dragend`           | 在地图、弹出窗口或 HTML 标记上的“拖动以平移”交互结束时触发。|
| `dragstart`         | 在地图、弹出窗口或 HTML 标记上的“拖动以平移”交互开始时触发。|
| `error`             | 在发生错误时触发。|
| `idle`              | <p>在地图进入“空闲”状态之前呈现的最后一帧之后触发：<ul><li>没有正在进行的照相机转换。</li><li>当前请求的所有图块均已加载。</li><li>所有淡入/过渡动画均已完成。</li></ul></p>|
| `keydown`           | 在按下某个键时触发。|
| `keypress`          | 在按下生成可键入字符的键（ANSI 键）时触发。|
| `keyup`             | 在松开某个键时触发。|
| `layeradded`        | 在向地图添加层时触发。|
| `layerremoved`      | 在从地图移除层时触发。|
| `load`              | 在下载了所有必需的资源并进行了第一次视觉上完整的地图呈现后立即触发。|
| `mousedown`         | 在地图内按下指针设备时或在指针设备位于元素顶部时触发。|
| `mouseenter`        | 在指针设备最初移到地图或元素上时触发。 |
| `mouseleave`        | 在指针设备移出地图或元素时触发。 |
| `mousemove`         | 在指针设备于地图或元素内移动时触发。|
| `mouseout`          | 在指针设备离开地图画布或离开元素时触发。|
| `mouseover`         | 在指针设备移到地图或元素上时触发。|
| `mouseup`           | 在指针设备于地图内释放或位于元素顶部时触发。|
| `move`              | 因用户交互或方法而在从一个视图到另一个视图的动画过渡期间反复触发。|
| `moveend`           | 因用户交互或方法而在地图完成从一个视图过渡到另一个视图后触发。|
| `movestart`         | 因用户交互或方法而在地图开始从一个视图过渡到另一个视图前触发。|
| `open`              | 在通过手动或编程方式打开弹出窗口时触发。|
| `pitch`             | 因用户交互或方法而在地图的俯仰角（倾斜度）每次更改时触发。|
| `pitchend`          | 因用户交互或方法而在地图的俯仰角（倾斜度）完成更改后立即触发。|
| `pitchstart`        | 因用户交互或方法而在地图的俯仰角（倾斜度）每次开始更改时触发。|
| `ready`             | 在地图准备好以编程方式进行交互前就加载了所需的最少地图资源时触发。|
| `render`            | <p>出于以下原因而在地图每次绘制到屏幕上时触发：<ul><li>更改地图的位置、缩放、俯仰角或方位。</li><li>更改地图的样式。</li><li>更改 `DataSource` 源。</li><li>加载矢量图块、GeoJSON 文件、字形或子画面。</li></ul></p>|
| `resize`            | 在调整地图大小后立即触发。|
| `rotate`            | 在“拖动以旋转”交互期间反复触发。|
| `rotateend`         | 在“拖动以旋转”交互结束时触发。|
| `rotatestart`       | 在“拖动以旋转”交互开始时触发。|
| `shapechanged`      | 在更改形状对象属性时触发。|
| `sourcedata`        | 在其中一个地图源加载或更改时触发，包括属于某个源的图块加载或更改的情况。 |
| `sourceadded`       | 在向地图添加 `DataSource` 或 `VectorTileSource` 时触发。|
| `sourceremoved`     | 在从地图移除 `DataSource` 或 `VectorTileSource` 时触发。|
| `styledata`         | 在加载或更改地图的样式时触发。|
| `styleimagemissing` | 在层尝试从不存在的图像子画面加载图像时触发 |
| `tokenacquired`     | 在获得 AAD 访问令牌时触发。|
| `touchcancel`       | 在地图中发生 touchcancel 事件时触发。|
| `touchend`          | 在地图中发生 touchend 事件时触发。|
| `touchmove`         | 在地图中发生 touchmove 事件时触发。|
| `touchstart`        | 在地图中发生 touchstart 事件时触发。|
| `wheel`             | 在地图中发生鼠标滚轮事件时触发。|
| `zoom`              | 因用户交互或方法而在从一个缩放级别到另一个缩放级别的动画过渡期间反复触发。|
| `zoomend`           | 因用户交互或方法而在地图完成从一个缩放级别过渡到另一缩放级别后触发。|
| `zoomstart`         | 因用户交互或方法而在地图开始从一个缩放级别过渡到另一个缩放级别前触发。|


## <a name="next-steps"></a>后续步骤

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Azure Maps 服务模块](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)
