---
title: 从地图上的形状获取数据 |Microsoft Azure 映射
description: 本文介绍如何使用 Microsoft Azure map Web SDK 获取地图上绘制的形状数据。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ecefac68a4348eeae23860d542f949b1c7ff23a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310198"
---
# <a name="get-shape-data"></a>获取形状数据

本文介绍如何获取在地图上绘制的形状的数据。 我们在 "[绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--)" 中使用了**drawingManager. GetSource ( # B1**函数。 当您想要提取绘制形状的 geojson 数据并将其用于其他位置时，有多种方案。  


## <a name="get-data-from-drawn-shape"></a>从绘制的形状获取数据

下面的函数获取绘制的形状的源数据，并将其输出到屏幕上。 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

下面是完整的运行代码示例，可在其中绘制用于测试功能的形状：

<br/>

<iframe height="686" title="获取形状数据" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>请参阅 CodePen 上的 "通过 Azure Maps () <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>获取形状数据</a>" <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

> [!div class="nextstepaction"]
> [对绘图事件做出反应](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [交互类型和键盘快捷键](drawing-tools-interactions-keyboard-shortcuts.md)

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [绘图管理器](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [绘图工具栏](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)
