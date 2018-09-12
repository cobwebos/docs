---
title: 使用 Azure Maps 显示坐标信息 | Microsoft Docs
description: 如何在用户选择坐标时显示地图上某个地址的信息
author: jingjing-z
ms.author: jinzh
ms.date: 08/31/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 331e687c40f21b0bf6074239969848c632682773
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667475"
---
# <a name="get-information-from-a-coordinate"></a>从坐标获取信息

本文展示了如何进行反向地址搜索，以及如何在单击鼠标时在弹出窗口中显示被单击位置的地址。

## <a name="understand-the-code"></a>了解代码

<iframe height='500' scrolling='no' title='从坐标获取信息（服务模块）' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Get information from a coordinate (Service Module)</a>（从坐标获取信息[服务模块]）。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块中的行对服务客户端进行实例化。

第三个代码块将鼠标光标的样式更新为指针。

第四个代码块创建一个弹出窗口。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

最后一个代码块添加针对鼠标单击的事件侦听器。 单击鼠标后，它会使用被单击点的坐标创建搜索查询。 然后它使用地图的 [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) 终结点来查询坐标地址。

对于成功的响应，它收集被单击位置的地址，并通过 popup 类的 [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions) 函数定义弹出窗口内容和位置。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [反向地址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addeventlistener)
* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

有关可向地图添加的更多代码示例，请参阅以下文章：
* [显示从 A 到 B 的路线](./map-route.md)
* [显示交通情况](./map-show-traffic.md)
