---
title: 使用 Azure Maps 显示坐标信息 | Microsoft Docs
description: 如何在用户选择坐标时显示地图上某个地址的信息
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b640346b0d6f490457e1e82a65c0d3f373d658d3
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "42141841"
---
# <a name="get-information-from-a-coordinate"></a>从坐标获取信息

本文展示了如何进行反向地址搜索，以及如何在单击鼠标时在弹出窗口中显示被单击位置的地址。 

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='从坐标获取信息' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a>（从坐标获取信息）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块将鼠标光标的样式更新为指针。

第三个代码块创建一个弹出窗口。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

最后一个代码块添加针对鼠标单击的事件侦听器。 在单击鼠标时，它将一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 发送到 [Azure Maps 反向地址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 对于成功的响应，它收集被单击位置的地址，并通过 popup 类的 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) 函数定义弹出窗口内容和位置

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

有关可向地图添加的更多代码示例，请参阅以下文章： 
* [显示从 A 到 B 的路线](./map-route.md)
* [显示交通情况](./map-show-traffic.md)
