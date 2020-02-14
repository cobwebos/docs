---
title: 向地图添加绘图工具栏 |Microsoft Azure 映射
description: 在本文中，你将学习如何使用 Microsoft Azure Maps Web SDK 将绘图工具栏添加到地图
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198303"
---
# <a name="drawing-tool-events"></a>绘图工具事件

在地图上使用绘图工具时，在用户在地图上绘制时，对某些事件做出反应很有用。 此表列出了 `DrawingManager` 类支持的所有事件。

| 事件 | 说明 |
|-------|-------------|
| `drawingchanged` | 在添加或更改形状中的任何坐标时激发。 | 
| `drawingchanging` | 在显示形状的任何预览坐标时激发。 例如，在拖动坐标时，将多次激发此事件。 | 
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

让我们看看一些使用绘图工具事件的常见方案。

### <a name="select-points-in-polygon-area"></a>在多边形区域中选择点

此代码演示如何监视用户绘制形状的事件。 在此示例中，代码监视多边形、矩形和圆形的形状。 然后，它确定地图上的哪些数据点位于绘制区域内。 `drawingcomplete` 事件用于触发 select 逻辑。 在 select 逻辑中，代码循环遍历地图上的所有数据点。 它检查是否存在所绘制形状的点和面积的交集。 此示例使用开源[Turf](https://turfjs.org/)库来执行空间交集计算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="选择绘制的多边形区域中的数据" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）在<a href='https://codepen.io/azuremaps/pen/XWJdeja'>绘制的多边形区域中选择数据</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多边形区域中绘制和搜索

此代码在用户完成绘制形状后，在形状区域内搜索兴趣点。 您可以通过单击框架右上角的 "代码笔" 上的 "编辑" 来修改和执行代码。 `drawingcomplete` 事件用于触发搜索逻辑。 如果用户绘制矩形或多边形，则会执行几何图形内的搜索。 如果绘制圆形，则使用半径和中心位置来执行兴趣点搜索。 `drawingmodechanged` 事件用于确定用户何时切换到绘制模式，此事件将清除绘图画布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多边形区域中绘制和搜索" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上的 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>），查看笔<a href='https://codepen.io/azuremaps/pen/eYmZGNv'>在多边形区域中的绘图和搜索</a>。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>创建测量工具

下面的代码演示如何使用绘图事件来创建度量工具。 `drawingchanging` 用于在绘制形状时对其进行监视。 当用户移动鼠标时，将计算形状的尺寸。 `drawingcomplete` 事件用于在绘制形状后对其执行最终计算。 `drawingmodechanged` 事件用于确定用户何时切换为绘图模式。 此外，`drawingmodechanged` 事件会清除绘图画布并清除旧的测量信息。

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
