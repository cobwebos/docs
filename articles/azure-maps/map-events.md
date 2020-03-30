---
title: 处理地图事件 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 使用地图事件制作交互式 Web SDK 地图。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534892"
---
# <a name="interact-with-the-map"></a>与地图交互

本文介绍如何使用[地图事件类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)。 属性突出显示地图上和地图不同图层上的事件。 您还可以在与 HTML 标记交互时突出显示事件。

## <a name="interact-with-the-map"></a>与地图交互

使用下面的地图播放，并看到右侧突出显示的相应鼠标事件。 可以单击 JS 选项卡，查看和编辑 JavaScript 代码****。 您还可以单击**CodePen 上的"编辑**"来修改 CodePen 上的代码。

<br/>

<iframe height='600' scrolling='no' title='与地图交互 - 鼠标事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interacting with the map - mouse events</a>（与地图交互 - 鼠标事件）。
</iframe>

## <a name="interact-with-map-layers"></a>与地图层交互

以下代码突出显示与符号图层交互时触发的事件。 符号、气泡、线和面图层都支持同一组事件。 热图和切片图层不支持任何这些事件。

<br/>

<iframe height='600' scrolling='no' title='与地图交互 – 层事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interacting with the map – Layer Events</a>（与地图交互 – 层事件）。
</iframe>

## <a name="interact-with-html-marker"></a>与 HTML 标记交互

以下代码将 Javascript 映射事件添加到 HTML 标记中。 它还突出显示了与 HTML 标记进行交互时触发的事件的名称。

<br/>

<iframe height='500' scrolling='no' title='与地图交互 - HTML 标记事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interacting with the map - HTML Marker events</a>（与地图交互 - HTML 标记事件）。
</iframe>

下表列出了所有支持的地图类事件。

| 事件               | 描述 |
|---------------------|-------------|
| `boxzoomend`        | 当"框缩放"交互结束时激发。|
| `boxzoomstart`      | 启动"框缩放"交互时激发。|
| `click`             | 当按下指针设备并在地图上的同一点释放时触发。|
| `close`             | 当弹出窗口手动或以程序方式关闭时触发。|
| `contextmenu`       | 单击鼠标右键时触发。|
| `data`              | 当任何地图数据加载或更改时触发。 |
| `dataadded`         | 当将形状添加到 时`DataSource`触发。|
| `dataremoved`       | 从 中删除形状时触发`DataSource`。|
| `datasourceupdated` | 更新`DataSource`对象时触发。|
| `dblclick`          | 当在地图上的同一点单击指针设备两次时触发。|
| `drag`              | 在地图、弹出窗口或 HTML 标记上的"拖动到平移"交互期间重复触发。|
| `dragend`           | 当"拖动到平移"交互在地图、弹出窗口或 HTML 标记上结束时触发。|
| `dragstart`         | 当"拖动到平移"交互开始在地图、弹出窗口或 HTML 标记上时触发。|
| `error`             | 发生错误时触发。|
| `idle`              | <p>在地图进入"空闲"状态之前渲染的最后一帧后触发：<ul><li>没有摄像机转换正在进行。</li><li>已加载所有当前请求的磁贴。</li><li>所有淡入淡出/过渡动画都已完成。</li></ul></p>|
| `keydown`           | 按下键时触发。|
| `keypress`          | 按下生成可键入字符（ANSI 密钥）的键时触发。|
| `keyup`             | 释放密钥时触发。|
| `layeradded`        | 将图层添加到地图时触发。|
| `layerremoved`      | 从地图中删除图层时触发。|
| `load`              | 下载所有必要的资源并首次直观地呈现地图后立即触发。|
| `mousedown`         | 在地图中按下指针设备或元素顶部时触发。|
| `mouseenter`        | 当指针设备最初在地图或元素上移动时触发。 |
| `mouseleave`        | 当指针设备移出地图或元素时触发。 |
| `mousemove`         | 在地图或元素中移动指针设备时触发。|
| `mouseout`          | 当点设备离开地图的画布时，我们留下一个元素。|
| `mouseover`         | 当指针设备在地图或元素上移动时触发。|
| `mouseup`           | 当在地图中释放指针设备或在元素顶部释放时触发。|
| `move`              | 在从一个视图到另一个视图的动画转换期间，由于用户交互或方法的结果，重复触发。|
| `moveend`           | 在地图完成从一个视图到另一个视图的过渡后触发，这是用户交互或方法的结果。|
| `movestart`         | 在地图开始从一个视图转换到另一个视图之前触发，这是用户交互或方法的结果。|
| `open`              | 手动或以编程方式打开弹出窗口时触发。|
| `pitch`             | 每当地图的间距（倾斜）因用户交互或方法而改变时触发。|
| `pitchend`          | 在地图的间距（倾斜）完成更改后，立即触发，因为用户交互或方法。|
| `pitchstart`        | 每当地图的间距（倾斜）由于用户交互或方法而开始更改时，都会触发。|
| `ready`             | 在地图准备好以编程方式与之交互之前加载所需的最小地图资源时触发。|
| `render`            | <p>每当地图绘制到屏幕时，由于以下情况，即：<ul><li>更改地图的位置、缩放、俯仰或轴承。</li><li>更改地图样式。</li><li>对源的`DataSource`更改。</li><li>矢量磁贴、GeoJSON 文件、字形或精灵的加载。</li></ul></p>|
| `resize`            | 在地图调整大小后立即触发。|
| `rotate`            | 在"拖动旋转"交互期间反复触发。|
| `rotateend`         | 当"拖动旋转"交互结束时触发。|
| `rotatestart`       | 当"拖动旋转"交互启动时触发。|
| `shapechanged`      | 更改形状对象属性时触发。|
| `sourcedata`        | 当地图的一个源加载或更改时触发，包括属于源的磁贴加载或更改。 |
| `sourceadded`       | 当 或`DataSource``VectorTileSource`添加到地图时触发。|
| `sourceremoved`     | 从`DataSource`地图中删除`VectorTileSource`或 时触发。|
| `styledata`         | 当地图的样式加载或更改时触发。|
| `styleimagemissing` | 当图层尝试从不存在的图像子画面加载图像时触发 |
| `tokenacquired`     | 获取 AAD 访问令牌时触发。|
| `touchcancel`       | 当地图中发生触摸取消事件时触发。|
| `touchend`          | 当地图中发生触摸端事件时触发。|
| `touchmove`         | 当在地图中发生触摸移动事件时触发。|
| `touchstart`        | 当在地图中发生触摸启动事件时触发。|
| `wheel`             | 当在地图中发生鼠标滚轮事件时触发。|
| `zoom`              | 在从一个缩放级别到另一个缩放级别的动画过渡期间反复激发，这是用户交互或方法的结果。|
| `zoomend`           | 在地图完成从一个缩放级别到另一个缩放级别的转换后，由于用户交互或方法的结果，即激发。|
| `zoomstart`         | 在地图开始从一个缩放级别过渡到另一个缩放级别之前触发，这是用户交互或方法的结果。|


## <a name="next-steps"></a>后续步骤

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Azure 地图服务模块](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
