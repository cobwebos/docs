---
title: 在 Azure Maps 中使用绘图工具模块 |Microsoft Docs
description: 如何使用 Azure Maps Web SDK 设置绘图选项数据
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0ac9bc775798a14e6431718bc602d8ff41288c10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408148"
---
# <a name="use-the-drawing-tools-module"></a>使用绘图工具模块

Azure Maps Web SDK 提供了一个 "*绘图工具" 模块*。 使用此模块可以轻松地使用输入设备（例如鼠标的触摸屏幕）在地图上绘制和编辑形状。 此模块的核心类是[绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)，它提供了在地图上绘制和编辑形状所需的所有功能。 可以直接使用绘图管理器并将其与自定义工具栏 UI 集成，也可以使用内置的 "绘图"[工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)类。 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>在网页中加载绘图工具模块

1. 创建一个新的 HTML 文件，并[照常实现该映射](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control)。
2. 加载 Azure Maps 绘图工具模块。 可以通过以下两种方式之一加载它：
    - 使用 Azure Maps services 模块的全球托管的 Azure 内容分发网络版本。 将引用添加到文件的 `<head>` 元素中的 JavaScript 和 CSS 样式表中：

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0.1/atlas-drawing.min.js"></script>
        ```

    - 或者，通过[使用 npm 包在本地](https://www.npmjs.com/package/azure-maps-drawing-tools)加载 AZURE MAPS Web SDK 源代码的 "绘图工具" 模块，然后将其托管在应用中。 此程序包还包括了 TypeScript 定义。 使用此命令：
    
        > **npm 安装 azure 地图-绘图工具**
    
        然后，在文件的 `<head>` 元素中添加对 JavaScript 和 CSS 样式表的引用：

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>直接使用绘图管理器

现在，已将 "绘图工具" 模块加载到应用程序中，可以使用 "[绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)" 启用图中的绘图和编辑功能。 可以在对其进行实例化时为绘图管理器指定选项，也可以使用 `drawingManager.setOptions()` 函数。

### <a name="set-the-drawing-mode"></a>设置绘制模式

下面的代码创建一个绘图管理器实例，并设置 "绘制**模式**" 选项。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

下面的代码是一个完整运行的示例，演示如何设置绘图管理器的绘图模式。 单击地图，开始绘制多边形。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="绘制多边形" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅在<a href='https://codepen.io'>CodePen</a>上通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>绘制多边形</a>。
</iframe>


### <a name="set-the-interaction-type"></a>设置交互类型

绘图管理器支持三种不同的方法来与地图进行交互以绘制形状。

* 单击鼠标或触摸时添加 `click` 坐标。
* 当鼠标或触摸拖动到地图上时，将添加 `freehand ` 坐标。 
* 单击或拖动鼠标或触摸时添加 `hybrid` 坐标。

下面的代码启用多边形绘制模式，并设置绘图管理器应遵循 `freehand`的绘图交互的类型。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

下面是实现功能的代码示例，使用该功能可以自由地在地图上绘制多边形，同时按住鼠标左键并拖动它。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由手写绘图" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的触笔<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>自由右</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


### <a name="customizing-drawing-options"></a>自定义绘图选项

前面的示例演示了如何在实例化绘图管理器时自定义绘图选项。 还可以使用 `drawingManager.setOptions()` 函数设置 "绘图管理器" 选项。 下面是一个工具，用于测试使用 setOptions 函数对绘图管理器的所有选项的自定义。

<br/>

<iframe height="685" title="自定义绘图管理器" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>获取形状数据</a>"。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用 "绘图工具" 模块的其他功能：

> [!div class="nextstepaction"]
> [添加绘图工具栏](map-add-drawing-toolbar.md)

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
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
