---
title: 在 Azure Maps 中设置绘图选项 |Microsoft Docs
description: 如何使用 Azure Maps Web SDK 设置绘图选项数据
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d85525274db7818737b62ad4e9ea2026b8aef3b2
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309803"
---
# <a name="set-drawing-options"></a>设置绘图选项

本文介绍了不同选项的[绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#setoptions-drawingmanageroptions-)更改用户体验的方式。 可以在对其进行实例化时为绘图管理器指定选项，也可以使用**drawingManager. setOptions （）** 函数来设置选项。


## <a name="set-drawing-mode"></a>设置绘制模式

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
请参阅在<a href='https://codepen.io'>CodePen</a>上按 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>绘制多边形</a>。
</iframe>


## <a name="set-interaction-type"></a>设置交互类型

下面的代码设置绘图管理器应遵循的绘图交互的类型。 

```Javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

下面是实现功能的代码示例，可让你自由地在地图上绘图，同时按住鼠标左键并拖动它。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自由手写绘图" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的触笔<a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>自由右</a>Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）。
</iframe>


## <a name="customizing-drawing-options"></a>自定义绘图选项

前面的示例演示了如何在实例化绘图管理器时自定义绘图选项。 还可以使用**drawingManager. setOptions （）** 函数设置 "绘图管理器" 选项。 下面是一个工具，用于测试使用 setOptions 函数对绘图管理器的所有选项的自定义。

<br/>

<iframe height="685" title="自定义绘图管理器" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>获取形状数据</a>"。
</iframe>


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
