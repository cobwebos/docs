---
title: 向地图添加绘图工具栏 |微软 Azure 地图
description: 如何使用 Azure 地图 Web SDK 向地图添加绘图工具栏
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: bebf1ddfbca3aec5a551193609381cf3510bc3ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334503"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>向地图添加绘图工具工具栏

本文介绍如何使用绘图工具模块并在地图上显示绘图工具栏。 ["绘图工具栏"](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)控件在地图上添加绘图工具栏。 您将学习如何仅使用一个和所有绘图工具创建地图，以及如何自定义绘图管理器中图形形状的渲染。

## <a name="add-drawing-toolbar"></a>添加绘图工具栏

以下代码创建绘图管理器的实例，并在地图上显示工具栏。

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

<iframe height="500" style="width: 100%;" scrolling="no" title="添加绘图工具栏" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 添加绘图<a href='https://codepen.io'>栏的笔</a><a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>添加绘图工具栏</a>。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>限制显示的工具栏选项

以下代码创建绘图管理器的实例，并在地图上仅显示一个多边形绘图工具的工具栏。 

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

<iframe height="500" style="width: 100%;" scrolling="no" title="添加多边形绘图工具" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅在 CodePen 上按 Azure 映射 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a><a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>添加多边形绘图工具</a><a href='https://codepen.io'>的笔</a>。
</iframe>


## <a name="change-drawing-rendering-style"></a>更改绘图渲染样式

通过使用`drawingManager.getLayers()`函数，然后在各个图层上设置选项，可以自定义绘制的形状样式，从而检索绘图管理器的基础图层。 编辑形状时为坐标显示的拖动控点是 HTML 标记。 通过将 HTML 标记选项传递到绘图管理器 的 和`dragHandleStyle``secondaryDragHandleStyle`选项，可以自定义拖动控点的样式。  

以下代码从绘图管理器获取呈现图层，并修改其选项以更改绘图的呈现样式。 在这种情况下，点将呈现与蓝色标记图标。 线条为红色，四个像素宽。 多边形将具有绿色填充颜色和橙色轮廓。 然后，它将拖动控点的样式更改为方形图标。 

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

<iframe height="500" style="width: 100%;" scrolling="no" title="更改绘图渲染样式" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 地图<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>更改绘图呈现样式</a>。
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
> [地图](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
