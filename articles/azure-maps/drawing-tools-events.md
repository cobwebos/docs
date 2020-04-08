---
title: 向地图添加绘图工具栏 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 将绘图工具栏添加到地图中
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d8509af7829910bdda8bba3d63553e83626fe784
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804667"
---
# <a name="drawing-tool-events"></a>绘图工具事件

在地图上使用绘图工具时，当用户在地图上绘制时，对某些事件做出反应非常有用。 此表列出了`DrawingManager`类支持的所有事件。

| 事件 | 说明 |
|-------|-------------|
| `drawingchanged` | 当添加或更改形状中的任何坐标时触发。 | 
| `drawingchanging` | 显示形状的任何预览坐标时触发。 例如，此事件将在拖动坐标时触发多次。 | 
| `drawingcomplete` | 当形状绘制完成或从编辑模式中取出时触发。 |
| `drawingmodechanged` | 当绘图模式已更改时触发。 新的绘图模式将传递到事件处理程序中。 |
| `drawingstarted` | 当用户开始绘制形状或将形状置于编辑模式时触发。  |

以下代码显示了"绘图工具"模块中的事件的工作原理。 在地图上绘制形状并观看事件触发。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="绘图工具事件" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看笔<a href='https://codepen.io/azuremaps/pen/dyPMRWo'>绘图工具事件</a>。
</iframe>

<br/>

## <a name="examples"></a>示例

让我们看看一些使用绘图工具事件的常见方案。

### <a name="select-points-in-polygon-area"></a>选择多边形区域中的点

此代码演示如何监视用户绘图形状的事件。 在此示例中，代码监视多边形、矩形和圆的形状。 然后，它确定地图上的数据点在绘制区域内。 该`drawingcomplete`事件用于触发选择逻辑。 在选择逻辑中，代码循环遍历地图上的所有数据点。 它检查点和绘制形状的区域是否有交集。 此示例使用开源[Turf.js](https://turfjs.org/)库执行空间交集计算。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在绘制的多边形区域中选择数据" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 地图 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看<a href='https://codepen.io/azuremaps/pen/XWJdeja'>绘制的多边形区域中的笔选择数据</a>。
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>在多边形区域中绘制和搜索

此代码在用户绘制完形状后搜索形状区域内的兴趣点。 您可以通过单击框架右上角的"编辑代码笔"来修改和执行代码。 该`drawingcomplete`事件用于触发搜索逻辑。 如果用户绘制矩形或多边形，则执行几何体内的搜索。 如果绘制圆，则半径和中心位置用于执行兴趣点搜索。 该`drawingmodechanged`事件用于确定用户何时切换到绘图模式，并且此事件清除绘图画布。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="在多边形区域中绘制和搜索" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 地图 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>在<a href='https://codepen.io/azuremaps/pen/eYmZGNv'>多边形区域查看笔绘制和搜索</a>。
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>创建测量工具

下面的代码显示了如何使用绘图事件来创建测量工具。 `drawingchanging`用于监视正在绘制的形状。 当用户移动鼠标时，将计算形状的尺寸。 该`drawingcomplete`事件用于在绘制形状后对形状进行最终计算。 该`drawingmodechanged`事件用于确定用户何时切换到绘图模式。 此外，事件`drawingmodechanged`清除绘图画布并清除旧的测量信息。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="测量工具" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （） 查看笔<a href='https://codepen.io/azuremaps/pen/RwNaZXe'>测量工具</a>。
</iframe>

<br/>

## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)

了解有关服务模块的更多信息：

> [!div class="nextstepaction"]
> [服务模块](how-to-use-services-module.md)

查看更多代码示例：

> [!div class="nextstepaction"]
> [代码示例页](https://aka.ms/AzureMapsSamples)
