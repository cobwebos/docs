---
title: 将绘图工具工具栏添加到地图 |Microsoft Azure 映射
description: 如何使用 Azure Maps Web SDK 将绘图工具栏添加到地图
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 05a409faedb2093637b3d93045b35eb4e4929626
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310741"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>将绘图工具工具栏添加到地图

本文介绍如何使用 "绘图工具" 模块，并在地图上显示 "绘图" 工具栏。 [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)控件将绘图工具栏添加到地图上。 您将了解如何创建仅包含一个和所有绘图工具的地图，以及如何在绘图管理器中自定义绘图形状的呈现。

## <a name="add-drawing-toolbar"></a>添加绘图工具栏

下面的代码创建一个绘图管理器实例，并在地图上显示该工具栏。

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="添加绘图工具栏" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "<a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>添加绘图" 工具栏</a>Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>限制显示的工具栏选项

下面的代码创建一个绘图管理器实例，并在地图上只显示一个多边形绘图工具。 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="添加多边形绘图工具" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅在 CodePen 上 Azure Maps () 中<a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>添加多边形绘图工具</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>


## <a name="change-drawing-rendering-style"></a>更改绘制呈现样式

通过使用 `drawingManager.getLayers()` 函数，然后设置各个层上的选项，可以自定义绘制的形状的样式，方法是检索绘图管理器的基础层。 编辑形状时，针对坐标显示的拖动控点为 HTML 标记。 可以通过将 HTML 标记选项传递给 `dragHandleStyle` 绘图管理器的和选项自定义拖动句柄的样式 `secondaryDragHandleStyle` 。  

下面的代码从绘图管理器中获取呈现层，并修改它们的选项以更改绘图的呈现样式。 在这种情况下，将用蓝色标记图标呈现点。 线条将为红色，四像素宽。 多边形将具有绿色填充颜色和橙色轮廓。 然后将拖动句柄的样式更改为方形图标。 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="更改绘制呈现样式" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 () ，查看笔<a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>更改绘制呈现 Azure Maps 样式</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [对绘图事件做出反应](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)
