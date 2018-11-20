---
title: 使用 Azure Maps 添加弹出窗口 | Microsoft Docs
description: 如何向 Javascript 地图添加弹出窗口
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0be10c155398133887fadb1fe9954068f3afb9d9
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568108"
---
# <a name="add-a-popup-to-the-map"></a>向地图添加弹出窗口

本文展示了如何向地图上的点添加弹出窗口。

## <a name="understand-the-code"></a>了解代码

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='使用 Azure Maps 添加弹出窗口' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Add a pop up using Azure Maps</a>（使用 Azure Maps 添加弹出窗口）。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。 它还创建要在弹出窗口内显示的 HTML 内容。

第二个代码块使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建一个数据源对象。 点是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 类的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。 然后它创建一个具有名称和说明属性的点对象并将其添加到数据源。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。  第三个代码块创建一个符号层。 将数据源添加到符号层，然后将符号层添加到地图。

第四个代码块通过 `new atlas.Popup()` 创建一个 [popup 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)。 弹出窗口属性（例如 position 和 pixelOffset）是 [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.popupoptions?view=azure-iot-typescript-latest) 的一部分。 可以在弹出窗口构造函数中或者通过 popup 类的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函数定义 PopupOptions。 然后创建符号层的 `mouseover` 事件侦听器。

最后一个代码块创建由 `mouseover` 事件侦听器触发的函数。 它设置弹出窗口的内容和属性，并将弹出窗口对象添加到地图。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

有关完整代码示例，请参阅以下精彩文章：

> [!div class="nextstepaction"]
> [添加形状](./map-add-shape.md)

> [!div class="nextstepaction"]
> [添加自定义 HTML](./map-add-custom-html.md)
