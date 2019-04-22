---
title: 使用 Azure Maps 显示坐标信息 | Microsoft Docs
description: 如何在用户选择坐标时显示地图上某个地址的信息
author: jingjing-z
ms.author: jinzh
ms.date: 3/7/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 50f906a9d8a0dc19f5eb47bef4cb68f4703f020f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256050"
---
# <a name="get-information-from-a-coordinate"></a>从坐标获取信息

本文介绍如何进行反向地址搜索，以显示所单击的弹出位置的地址。

可通过两种方法进行反向地址搜索。 一种方法是通过服务模块查询 [Azure Maps 反向地址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 另一种方法是利用[提取 API](https://fetch.spec.whatwg.org/)要向发送请求[Azure Maps 反向地址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查找一个地址。 下面将研究这两种方法。

## <a name="make-a-reverse-search-request-via-service-module"></a>通过服务模块发出反向搜索请求

<iframe height='500' scrolling='no' title='从坐标获取信息（服务模块）' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>Get information from a coordinate (Service Module)</a>（从坐标获取信息[服务模块]）。
</iframe>

在上述代码中，第一个代码块构造 map 对象并设置使用订阅密钥的身份验证机制。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建`SubscriptionKeyCredentialPolicy`使用订阅密钥向 Azure Maps 的 HTTP 请求进行身份验证。 然后`atlas.service.MapsURL.newPipeline()`采用`SubscriptionKeyCredential`策略并创建[管道](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-iot-typescript-latest)实例。 `searchURL` 表示 Azure Maps [搜索](https://docs.microsoft.com/rest/api/maps/search)操作的 URL。

代码的第三个块的指针的指针更新鼠标光标的样式，并创建[弹出窗口](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)对象。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

代码的第四个块添加单击鼠标[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)。 触发时，它创建的搜索查询与被单击点的坐标。 然后，它使用的服务模块[getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)方法来查询[获取搜索地址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)坐标的地址。 然后使用提取响应中的 GeoJSON 功能集合`geojson.getFeatures()`方法。

第五个块的代码设置 HTML 弹出内容，以显示被单击的坐标位置的响应地址。

游标的更改、popup 对象和单击事件都是在映射的[加载事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)中创建的，以确保在检索到坐标信息之前已完全加载映射。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>请通过提取 API 的反向搜索请求

单击地图即可让使用 fetch 该位置的反向地理编码请求。

<iframe height='500' scrolling='no' title='从坐标获取信息' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>Get information from a coordinate</a>（从坐标获取信息）。
</iframe>

在上述代码中，第一个代码块构造 map 对象并设置使用订阅密钥的身份验证机制。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块将鼠标光标的样式更新为指针和 [popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open) 对象。 有关说明，可以参阅[在地图上添加弹出窗口](./map-add-popup.md)。

第三个代码块针对鼠标单击添加事件侦听器。 在单击鼠标时，它利用[提取 API](https://fetch.spec.whatwg.org/)查询[Azure Maps 反向地址搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)单击的坐标地址。 对于成功的响应，它收集被单击位置的地址，并通过 popup 类的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函数定义弹出窗口内容和位置。

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
