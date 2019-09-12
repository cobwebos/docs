---
title: 用 Azure Maps 处理事件 |Microsoft Docs
description: 如何使用地图事件创建交互式 Web SDK 地图
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899434"
---
# <a name="interact-with-the-map"></a>与地图交互

本文介绍如何使用[map class events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)属性突出显示地图上以及地图的不同层上的事件。 它还展示了在与 HTML 标记进行交互时如何使用 map 类 events 属性来突出显示事件。

## <a name="interact-with-the-map"></a>与地图交互

观看下面的地图，查看右侧突出显示的相应鼠标事件。 可以单击 JS 选项卡，查看和编辑 JavaScript 代码。 还可以单击“编辑 CodePen”按钮，编辑 CodePen 上的代码。

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

下表列出了所有支持的地图类事件。

| Event             | 描述 |
|-------------------|-------------|
| boxzoomend        | 当 "box zoom" 交互结束时激发。|
| boxzoomstart      | 当 "box zoom" 交互开始时激发。|
| 依次             | 在地图上按下并释放指针设备时激发。|
| 关闭             | 手动或以编程方式关闭弹出窗口时激发。|
| contextmenu       | 单击鼠标右键时激发。|
| dataadded         | 在将形状添加到数据源时引发。|
| dataremoved       | 在从数据源中删除形状时激发。|
| datasourceupdated | 在更新 DataSource 对象时触发。|
| dblclick          | 当在地图上同一点上单击指针设备两次时激发。|
| 入              | 在地图、popup 或 HTML 标记上的 "拖动到平移" 交互过程中重复激发。|
| dragend           | 当地图、popup 或 HTML 标记上的 "拖动到平移" 交互结束时激发。|
| dragstart         | 当 "拖动到平移" 交互从地图、popup 或 HTML 标记开始时激发。|
| 错误             | 发生错误时激发。|
| keydown           | 按下某个键时引发。|
| keypress          | 当按下生成 typable 字符（ANSI 键）的键时引发。|
| keyup             | 当释放某个键时引发。|
| layeradded        | 向映射添加层时激发。|
| 负载              | 在下载了所有必需的资源并发生了地图的第一个可视化完成呈现时立即触发。|
| mousedown         | 在地图内按下指针时激发。|
| mousemove         | 当指针移动到地图内时触发。|
| mouseout          | 当点设备离开地图的画布时激发。|
| 鼠标         | 当指针移动到地图内时触发。|
| mouseup           | 在映射中释放指针设备时激发。|
| 移动              | 由于用户交互或方法的结果，在从一个视图到另一个视图的动画转换过程中，会反复激发。|
| moveend           | 当映射完成从一个视图到另一个视图的转换（作为用户交互或方法的结果）时引发。|
| movestart         | 在映射开始从一个视图到另一个视图的转换之前激发，这是因为用户交互或方法。|
| 打开              | 当手动或以编程方式打开弹出窗口时激发。|
| 音节             | 当地图的间距（倾斜）因用户交互或方法的结果而发生更改时触发。|
| pitchend          | 在地图的螺距（倾斜）之后立即触发，作为用户交互或方法的结果进行更改。|
| pitchstart        | 每当地图的螺距（斜度）开始更改作为用户交互或方法的结果时，就会触发。|
| 就绪             | 在映射准备好以编程方式交互之前，在加载所需的最小映射资源时引发。|
| render            | <p> 在将地图绘制到屏幕上时触发，作为的结果：<ul><li>地图位置、缩放、音调或轴承的更改。</li><li>地图样式的更改。</li><li>对数据源源的更改。</li><li>矢量磁贴、GeoJSON 文件、标志符号或子画面的加载。</li></ul></p>|
| 调节            | 调整地图后立即触发。|
| 90            | 在 "拖动以旋转" 交互的过程中重复激发。|
| rotateend         | "拖动到旋转时" 交互结束时激发。|
| rotatestart       | "拖动到旋转时" 交互开始时激发。|
| shapechanged      | 形状对象属性更改时激发。|
| sourceadded       | 在将 DataSource 或 VectorTileSource 添加到映射时激发。|
| sourceremoved     | 从映射中删除 DataSource 或 VectorTileSource 时触发。|
| styledata         | 当地图的样式加载或更改时激发。|
| tokenacquired     | 获取 AAD 访问令牌时触发。|
| touchcancel       | 在映射内发生 touchcancel 事件时触发。|
| touchend          | 在映射内发生 touchend 事件时触发。|
| system.windows.uielement.touchmove>         | 在映射内发生 system.windows.uielement.touchmove> 事件时触发。|
| touchstart        | 在映射内发生 touchstart 事件时触发。|
| wheel             | 在地图内发生鼠标滚轮事件时触发。|
| 贴近              | 由于用户交互或方法的结果，从一个缩放级别的动画转换到另一个缩放级别时，会反复激发。|
| zoomend           | 当映射完成从一个缩放级别转换到另一个缩放级别（作为用户交互或方法的结果）时引发。|
| zoomstart         | 当映射从一个缩放级别开始转换到另一个缩放级别（作为用户交互或方法的结果）时引发。|


## <a name="next-steps"></a>后续步骤

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Azure Maps Services 模块](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
