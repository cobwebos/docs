---
title: 向地图添加绘图工具栏 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure map Web SDK 将绘图工具栏添加到地图
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fd235f3f39d67f86c8387add79ca0dbf17dc5906
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911671"
---
# <a name="drawing-tool-events"></a>绘图工具事件

在地图上使用绘图工具时，在用户在地图上绘制时，对某些事件做出反应通常非常有用。 下表列出了 `DrawingManager` 类支持的所有事件。

| 事件 | Description |
|-------|-------------|
| `drawingchanged` | 在添加或更改形状中的任何坐标时激发。 | 
| `drawingchanging` | 在显示形状的任何预览坐标时激发。 例如，将在拖动坐标时多次激发。 | 
| `drawingcomplete` | 在形状完成绘制或退出编辑模式时激发。 |
| `drawingmodechanged` | 在绘制模式发生更改时激发。 新的绘制模式将传递到事件处理程序中。 |
| `drawingstarted` | 当用户开始绘制形状或将形状置于编辑模式时激发。  |

下面的代码演示了 "绘图工具" 模块中的事件是如何工作的。 在地图上绘制形状，并在事件激发时监视。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="绘图工具事件" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "<a href='https://codepen.io/azuremaps/pen/dyPMRWo'>绘图工具事件</a>，按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）"。
</iframe>

<br/>

## <a name="examples"></a>示例

下面是一些使用绘图工具事件的常见方案的示例。

### <a name="select-points-in-polygon-area"></a>在多边形区域中选择点

下面的代码演示如何监视表示多边形区域（多边形、矩形和圆形）的形状的绘图，并确定地图上的哪些数据点位于绘制区域内。 `drawingcomplete` 事件用于触发 select 逻辑。 在 select 逻辑中，地图上的所有数据点都循环，并测试与绘制形状的多边形区域的交集。 此示例使用开源[Turf](https://turfjs.org/)库来执行空间交集计算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="选择绘制的多边形区域中的数据" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）在<a href='https://codepen.io/azuremaps/pen/XWJdeja'>绘制的多边形区域中选择数据</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多边形区域中绘制和搜索

下面的代码演示了在用户完成绘制形状后，如何在形状区域内搜索兴趣点。 `drawingcomplete` 事件用于触发搜索逻辑。 如果用户绘制矩形或多边形，则会执行几何图形内的搜索。 如果绘制圆形，则使用半径和中心位置来执行兴趣点搜索。 `drawingmodechanged` 事件用于确定用户何时切换到绘制模式，以及如何清除绘图画布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多边形区域中绘制和搜索" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上的 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>），查看笔<a href='https://codepen.io/azuremaps/pen/eYmZGNv'>在多边形区域中的绘图和搜索</a>。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>创建测量工具

下面的代码演示如何使用绘图事件来创建度量工具。 `drawingchanging` 用于在绘制形状时对其进行监视。 当用户移动鼠标时，将计算形状的尺寸。 `drawingcomplete` 事件用于在绘制形状后对其执行最终计算。 `drawingmodechanged` 事件用于确定用户何时切换到绘制模式，并清除绘图画布和旧的测量信息。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="度量工具" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "笔<a href='https://codepen.io/azuremaps/pen/RwNaZXe'>度量工具</a>" Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

<br/>

## <a name="next-steps"></a>后续步骤

了解如何使用 "绘图工具" 模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷方式](drawing-tools-interactions-keyboard-shortcuts.md)

详细了解 "服务" 模块：

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)

查看更多代码示例：

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
