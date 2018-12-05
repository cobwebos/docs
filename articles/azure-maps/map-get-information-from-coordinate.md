---
title: 使用 Azure Maps 显示坐标信息 | Microsoft Docs
description: 如何在用户选择坐标时显示地图上某个地址的信息
author: jingjing-z
ms.author: jinzh
ms.date: 11/15/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ada579af44d1d0b4ea08a8ae9eadbec386e44f08
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2018
ms.locfileid: "51823004"
---
# <a name="get-information-from-a-coordinate"></a>从坐标获取信息

本文介绍如何进行反向地址搜索，以显示所单击的弹出位置的地址。

可通过两种方法进行反向地址搜索。 一种方法是通过服务模块查询 [Azure Maps 反向地址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 另一种方法是向该 API 发出 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 以查找地址。 下面将研究这两种方法。

## <a name="make-a-reverse-search-request-via-service-module"></a>通过服务模块发出反向搜索请求

<iframe height='500' scrolling='no' title='从坐标获取信息（服务模块）' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Get information from a coordinate (Service Module)</a>（从坐标获取信息[服务模块]）。
</iframe>

第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块中的行对客户端服务进行实例化。

第三个代码块将鼠标光标的样式更新为指针和 [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 对象。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

第四个代码块针对鼠标单击添加[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)。 单击鼠标后，它会使用被单击点的坐标创建搜索查询。 然后它使用地图的 [getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/services.search?view=azure-iot-typescript-latest#getsearchaddressreverse) 终结点来查询坐标地址。

对于成功的响应，它收集被单击位置的地址，并通过 popup 类的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函数定义弹出窗口内容和位置。

游标的更改、popup 对象和单击事件都是在映射的[加载事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中创建的，以确保在检索到坐标信息之前已完全加载映射。

## <a name="make-a-reverse-search-request-via-xmlhttprequest"></a>通过 XMLHttpRequest 发出反向搜索请求

<iframe height='500' scrolling='no' title='从坐标获取信息' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a>（从坐标获取信息）。
</iframe>

上述第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块将鼠标光标的样式更新为指针和 [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 对象。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

第三个代码块针对鼠标单击添加事件侦听器。 在单击鼠标时，它将一个 [XMLHttpRequest](https://xhr.spec.whatwg.org/) 发送到 [Azure Maps 反向地址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，以查询单击的坐标地址。 对于成功的响应，它收集被单击位置的地址，并通过 popup 类的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函数定义弹出窗口内容和位置。

游标的更改、popup 对象和单击事件都是在映射的[加载事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中创建的，以确保在检索到坐标信息之前已完全加载映射。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [显示从 A 到 B 的路线](./map-route.md)

> [!div class="nextstepaction"]
> [显示交通情况](./map-show-traffic.md)
