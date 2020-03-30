---
title: 从地图上的形状获取数据 |微软 Azure 地图
description: 在本文中，了解如何使用 Microsoft Azure 地图 Web SDK 在地图上绘制形状数据。
author: philmea
ms.author: philmea
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 88db018575f92e777223f383c65cd6db51ba515a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334414"
---
# <a name="get-shape-data"></a>获取形状数据

本文介绍如何获取在地图上绘制的形状的数据。 我们使用**绘图管理器内部的绘图管理器.getSource（）** 函数。 [drawing manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) 当您要提取绘制形状的 Geojson 数据并将其用于其他位置时，有各种方案。  


## <a name="get-data-from-drawn-shape"></a>从绘制的形状获取数据

以下函数获取绘制的形状的源数据并将其输出到屏幕。 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

下面是完整的运行代码示例，您可以在其中绘制形状以测试功能：

<br/>

<iframe height="686" title="获取形状数据" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，请参阅按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 （）<a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>获取形状数据的</a>笔。
</iframe>


## <a name="next-steps"></a>后续步骤

了解如何使用绘图工具模块的其他功能：

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
