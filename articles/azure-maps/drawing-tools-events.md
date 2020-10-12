---
title: 绘制工具事件 |Microsoft Azure 映射
description: 本文介绍如何使用 Microsoft Azure Maps Web SDK 向地图添加绘图工具栏
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089337"
---
# <a name="drawing-tool-events"></a>绘图工具事件

在地图上使用绘图工具时，在用户在地图上进行绘制时对特定事件做出响应很有用。 下表列出了 `DrawingManager` 类支持的所有事件。

| 事件 | 说明 |
|-------|-------------|
| `drawingchanged` | 在添加或更改形状中的任何坐标时触发。 | 
| `drawingchanging` | 在显示形状的任何预览坐标时触发。 例如，此事件将在拖动坐标时多次触发。 | 
| `drawingcomplete` | 在形状完成绘制或退出编辑模式时触发。 |
| `drawingmodechanged` | 在更改绘制模式时触发。 新绘制模式会传入事件处理程序。 |
| `drawingstarted` | 在用户开始绘制形状或将形状置于编辑模式时触发。  |

以下代码演示“绘图工具”模块中的事件如何工作。 在地图上绘制形状并观察事件的触发过程。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="绘图工具事件" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>绘图工具事件</a>。
</iframe>

<br/>

## <a name="examples"></a>示例

让我们看看一些使用绘图工具事件的常见方案。

### <a name="select-points-in-polygon-area"></a>在多边形区域中选择点

此代码演示如何监视用户绘制形状的事件。 对于此示例，代码监视多边形、矩形和圆形的形状。 然后，它确定地图上的哪些数据点在绘制区域内。 `drawingcomplete` 事件用于触发选择逻辑。 在选择逻辑中，代码循环访问地图上的所有数据点。 它检查点和绘制形状所在的区域是否有交集。 此示例利用 [Turf.js](https://turfjs.org/) 开放源代码库执行空间相交计算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在绘制的多边形区域中选择数据" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/XWJdeja'>在绘制的多边形区域中选择数据</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多边形区域中绘制和搜索

用户完成绘制形状后，此代码将在形状区域内搜索兴趣点。 可以通过单击框架右上角的“编辑 CodePen”来修改和执行代码。 `drawingcomplete` 事件用于触发搜索逻辑。 如果用户绘制矩形或多边形，则会在几何图形内部进行搜索。 如果绘制圆形，则使用半径和中心位置执行兴趣点搜索。 `drawingmodechanged` 事件用于确定用户何时切换到绘制模式，并且此事件会清除绘图画布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多边形区域中绘制和搜索" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>在多边形区域中绘制和搜索</a>。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>创建测量工具

下面的代码演示如何使用绘图事件来创建测量工具。 `drawingchanging` 用于在绘制形状时对其进行监视。 当用户移动鼠标时，将计算形状的尺寸。 `drawingcomplete` 事件用于在绘制形状后对其进行最终计算。 `drawingmodechanged` 事件用于确定用户何时切换到绘制模式。 此外，`drawingmodechanged` 事件还会清除绘图画布并清除旧的测量信息。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="测量工具" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>测量工具</a>。
</iframe>

<br/>

## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)

了解有关服务模块的详细信息：

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)

查看更多代码示例：

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
