---
title: 使用 Azure Maps 添加符号和标记 | Microsoft Docs
description: 如何将符号和标记添加到 Javascript 地图
author: walsehgal
ms.author: v-musehg
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 92d80d9a4d39a3f21539e5e6e498fc52df213a19
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248806"
---
# <a name="add-symbols-and-markers-to-a-map"></a>将符号和标记添加到地图

本文介绍如何使用数据源将符号和标记添加到地图。

## <a name="add-a-symbol-marker"></a>添加符号标记

<iframe height='500' scrolling='no' title='切换图钉位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切换图钉位置</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

上述第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 然后创建一个点并将其添加到数据源。 点是 [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 类的[特征](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest)。

第三个代码块创建[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，并在单击鼠标时使用 shape 类的 [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest#setcoordinates) 方法更新点的坐标。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。  在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源、点击事件侦听器和符号层并将其添加到地图，以确保完全加载地图后显示该点。

## <a name="add-a-custom-symbol"></a>添加自定义符号

<iframe height='500' scrolling='no' title='HTML DataSource' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>HTML DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

在上面的代码中，第一个代码块构造地图对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块使用 [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 类的 [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) 属性将 [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) 添加到地图。 [事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中的 HtmlMarker 将添加到地图，以确保完全加载地图后会显示它。

## <a name="add-bubble-markers"></a>添加气泡标记

<iframe height='500' scrolling='no' title='BubbleLayer DataSource' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

在上面的代码中，第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，定义了位置数组并创建了 [MultiPoint](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.multipoint?view=azure-iot-typescript-latest) 对象。 然后，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建数据源对象，并将 MultiPoint 对象添加到数据源。

[气泡层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)呈现作为圆包装在地图上[数据源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中的基于点的数据。 最后一个代码块创建气泡层并将其添加到地图。 请在 [BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest) 中参阅气泡层的属性。

在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建 MultiPoint 对象、数据源和气泡层并将其添加到地图，以确保完全加载地图后显示该圆。

## <a name="add-bubble-markers-with-label"></a>添加带标签的气泡标记

<iframe height='500' scrolling='no' title='MultiLayer DataSource' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>MultiLayer DataSource</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

上面的代码演示了如何在地图上可视化和标记数据。 上述第一个代码块构造 map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

第二个代码块创建 [point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) 对象。 然后，使用 [datasource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建数据源对象，并将点添加到该数据源。

[气泡层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)呈现作为圆包装在地图上[数据源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)中的基于点的数据。 第三个代码块创建气泡层并将其添加到地图。 请在 [BubblerLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.bubblelayeroptions?view=azure-iot-typescript-latest) 中参阅气泡层的属性。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。 最后一个代码块创建符号层并将其添加到地图，用于呈现气泡的文本标签。 请在 [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.symbollayeroptions?view=azure-iot-typescript-latest) 中参阅符号层的属性。

在[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数中创建数据源和层并将其添加到地图，以确保完全加载地图后显示该数据。


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加弹出项](./map-add-popup.md)

> [!div class="nextstepaction"]
> [添加形状](./map-add-shape.md)