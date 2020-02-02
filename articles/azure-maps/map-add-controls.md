---
title: 向地图添加控件 |Microsoft Azure 映射
description: 如何将缩放控件、螺距控件、旋转控件和样式选取器添加到 Microsoft Azure 映射中的地图。
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e2ad8eb181685d3ac3de8b1e0ed7ef8ddfa4e224
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933284"
---
# <a name="add-controls-to-a-map"></a>向地图添加控件

本文介绍如何将控件添加到地图中。 你还将了解如何创建包含所有控件和[样式选取器](https://docs.microsoft.com/azure/azure-maps/choose-map-style)的地图。

## <a name="add-zoom-control"></a>添加缩放控件

缩放控件添加了用于放大和缩小地图的按钮。下面的代码示例创建[ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol)类的一个实例，并将其添加到地图的右下角。

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='添加缩放控件' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>添加缩放控件</a>。
</iframe>

## <a name="add-pitch-control"></a>添加绕 X 轴旋转控件

螺距控件添加倾斜的按钮，以相对于水平位置进行地图。 下面的代码示例创建[PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol)类的实例。 它将 PitchControl 添加到地图的右上角。

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='添加绕 X 轴旋转控件' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>添加绕 X 轴旋转控件</a>。
</iframe>

## <a name="add-compass-control"></a>添加指南针控件

指南针控件添加了用于旋转地图的按钮。 下面的代码示例创建一个[罗盘控件](/javascript/api/azure-maps-control/atlas.control.compasscontrol)类的实例，并将其添加到地图的左下角。

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

下面是上述功能的完整运行代码示例。

<br/>

<iframe height='500' scrolling='no' title='添加旋转控件' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>添加旋转控件</a>。
</iframe>

## <a name="a-map-with-all-controls"></a>包含所有控件的地图

下面的代码示例将样式选择器、缩放、间距和罗盘控件添加到地图的右下角。 请注意它们如何自动堆积。 控件对象在脚本中的顺序决定了它们在地图上的显示顺序。 若要更改控件在地图上的顺序，可以更改它们在脚本中的顺序。

<br/>

<iframe height='500' scrolling='no' title='包含所有控件的地图' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>包含所有控件的地图</a>。
</iframe>

样式选取器控件由[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)类定义。 有关使用样式选取器控件的详细信息，请参阅[选择地图样式](choose-map-style.md)。

## <a name="customize-controls"></a>自定义控件

下面是一个工具，用于测试用于自定义控件的各种选项。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="导航控件选项" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "笔<a href='https://codepen.io/azuremaps/pen/LwBZMx/'>导航控件选项</a>" Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

如果要创建自定义的导航控件，请创建一个从 `atlas.Control` 类进行扩展的类，或创建一个 HTML 元素，并将其放置在地图 div 上方。 让此 UI 控件调用 `setCamera` 函数的 maps 来移动地图。 

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [罗盘控件](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

有关完整代码，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图钉](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加弹出项](./map-add-popup.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

