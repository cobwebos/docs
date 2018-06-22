---
title: 使用 Azure Maps 添加弹出窗口 | Microsoft Docs
description: 如何向 Javascript 地图添加弹出窗口
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: d9eeac28b204af0bb7d8e204762aae854055b33a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599297"
---
# <a name="add-a-popup-to-the-map"></a>向地图添加弹出窗口

本文展示了如何向地图添加弹出窗口。  

## <a name="understand-the-code"></a>了解代码

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='向地图添加弹出窗口' src='//codepen.io/azuremaps/embed/zRyKxj/?height=545&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/zRyKxj/'>Add a popup to a map</a>（向地图添加弹出窗口）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块添加一个图钉并将其添加到地图。 有关说明，可以参阅[向地图添加图钉](./map-add-pin.md)。

第三个代码块创建要在弹出窗口中显示的内容。 弹出窗口内容是 HTML 元素。 

第四个代码块通过 `new atlas.Popup()` 创建一个 [popup 对象](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)。 弹出窗口属性（例如内容和位置）是 [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popupoptions?view=azure-iot-typescript-latest) 的一部分。 可以在弹出窗口构造函数中或者通过 popup 类的 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) 函数定义 PopupOptions。

最后一个代码块使用 map 类的 [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener) 函数侦听图钉上的鼠标悬停事件，并且在发生该事件时使用 popup 类的 [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open) 函数打开弹出窗口。


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 

* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
    
有关可向地图添加的更多代码示例，请参阅以下文章： 
* [添加形状](./map-add-shape.md)
* [添加自定义 HTML](./map-add-custom-html.md)
