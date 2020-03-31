---
title: 绘图工具模块 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 设置绘图选项数据
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 711609f9382e2153cbc738d544933796dbbe2e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334311"
---
# <a name="use-the-drawing-tools-module"></a>使用绘图工具模块

Azure 地图 Web SDK 提供了一个*绘图工具模块*。 此模块便于使用输入设备（如鼠标或触摸屏）在地图上绘制和编辑形状。 此模块的核心类是[绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)。 绘图管理器提供在地图上绘制和编辑形状所需的所有功能。 它可以直接使用，并且与自定义工具栏 UI 集成。 您还可以使用内置[绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)类。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在网页中加载绘图工具模块

1. 创建新的 HTML 文件，并[像往常一样实现映射](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
2. 加载 Azure 地图绘图工具模块。 您可以通过两种方式之一加载它：
    - 使用 Azure 地图服务模块的全局托管 Azure 内容传递网络版本。 在文件`<head>`元素中添加对 JavaScript 和 CSS 样式表的引用：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - 或者，可以使用[Azure 映射绘图工具](https://www.npmjs.com/package/azure-maps-drawing-tools)npm 包在本地加载 Azure 地图 Web SDK 源代码的绘图工具模块，然后将其与应用一起托管。 此程序包还包括了 TypeScript 定义。 使用此命令：
    
        > **npm 安装 azure 映射绘图工具**
    
        然后，在文件的元素中添加对 JavaScript 和 CSS`<head>`样式表的引用：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用绘图管理器

在应用程序中加载绘图工具模块后，可以使用[绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)启用绘图和编辑功能。 您可以在实例化图形管理器时为图形管理器指定选项，也可以使用函数`drawingManager.setOptions()`。

### <a name="set-the-drawing-mode"></a>设置绘图模式

以下代码创建绘图管理器的实例并设置绘图**模式**选项。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

下面的代码是如何设置绘图管理器的绘图模式的完整运行示例。 单击地图开始绘制多边形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="绘制多边形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在 CodePen 上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>绘制多边形</a><a href='https://codepen.io'>的笔</a>。
</iframe>


### <a name="set-the-interaction-type"></a>设置交互类型

绘图管理器支持与地图交互以绘制形状的三种不同方式。

* `click`- 单击鼠标或触摸时添加坐标。
* `freehand `- 在地图上拖动鼠标或触摸时添加坐标。 
* `hybrid`- 单击或拖动鼠标或触摸时添加坐标。

以下代码启用多边形绘图模式，并设置绘图管理器应遵循的绘图交互的类型`freehand`。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 此代码示例实现在地图上绘制多边形的功能。 只需按住鼠标左键，然后自由地拖动它。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由手绘图" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （）<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>的笔自由手绘图</a>。
</iframe>


### <a name="customizing-drawing-options"></a>自定义绘图选项

前面的示例演示了如何在实例化绘图管理器时自定义绘图选项。 您还可以使用 函数设置绘图管理器选项`drawingManager.setOptions()`。 下面是一个工具，用于使用 setOptions 函数测试绘图管理器的所有选项的自定义。

<br/>

<iframe height="685" title="自定义绘图管理器" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，请参阅按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 （）<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>获取形状数据的</a>笔。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [添加绘图工具栏](map-add-drawing-toolbar.md)

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
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
