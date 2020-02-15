---
title: 向地图添加绘图工具栏 |Microsoft Azure 映射
description: 如何使用 Azure Maps Web SDK 将绘图工具栏添加到地图
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb0f70bc42c9ac0f7026c910593950516f027a88
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209743"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>将绘图工具工具栏添加到地图

本文介绍如何使用 "绘图工具" 模块，并在地图上显示 "绘图" 工具栏。 [DrawingToolbar](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)控件将绘图工具栏添加到地图上。 您将了解如何创建仅包含一个和所有绘图工具的地图，以及如何在绘图管理器中自定义绘图形状的呈现。

## <a name="add-drawing-toolbar"></a>添加绘图工具栏

下面的代码创建一个绘图管理器实例，并在地图上显示该工具栏。

```Javascript
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
请参阅<a href='https://codepen.io'>CodePen</a>上的 "<a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>添加绘图" 工具栏</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


## <a name="limit-displayed-toolbar-options"></a>限制显示的工具栏选项

下面的代码创建一个绘图管理器实例，并在地图上只显示一个多边形绘图工具。 

```Javascript
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
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>添加多边形绘图工具</a>。
</iframe>


## <a name="change-drawing-rendering-style"></a>更改绘制呈现样式

下面的代码从绘图管理器中获取呈现层，并修改它们的选项以更改绘图的呈现样式。 在这种情况下，将用蓝色标记图标呈现点。 线条将为红色，四像素宽。 多边形将具有绿色填充颜色和橙色轮廓。

```Javascript
var layers = drawingManager.getLayers();
    layers.pointLayer.setOptions({
        iconOptions: {
            image: 'marker-blue'
        }
    });
    layers.lineLayer.setOptions({
        strokeColor: 'red',
        strokeWidth: 4
    });
    layers.polygonLayer.setOptions({
        fillColor: 'green'
    });
    layers.polygonOutlineLayer.setOptions({
        strokeColor: 'orange'
    });
```

下面是上述功能的完整运行代码示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="更改绘制呈现样式" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>更改绘图呈现样式</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用 "绘图工具" 模块的其他功能：

> [!div class="nextstepaction"]
> [获取形状数据](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [反应绘图事件](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷方式](drawing-tools-interactions-keyboard-shortcuts.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

> [!div class="nextstepaction"]
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)
