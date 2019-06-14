---
title: Azure Maps 中的地图样式功能 | Microsoft Docs
description: 了解 Azure Maps 样式的相关功能。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ffed12b9184c7b6a690c30db9826f031fe6c9f9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60795898"
---
# <a name="choose-a-map-style-in-azure-maps"></a>在 Azure Maps 中选择地图样式

Azure Maps 有四种不同的地图样式可供选择。 有关地图样式的详细信息，请参阅 [Azure Maps 中支持的地图样式](./supported-map-styles.md)。 本文介绍如何使用样式的相关功能设置地图加载的样式、设置新的样式和使用样式选取器控件。

## <a name="set-style-on-map-load"></a>设置地图加载的样式

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

上面的代码块设置订阅密钥并创建一个 Map 对象，其样式设置为 grayscale_dark。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

## <a name="update-the-style"></a>更新样式

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

上面的代码块设置订阅密钥并创建一个 Map 对象而不预先设置样式。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用 map 的 [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将地图样式设置为卫星。

## <a name="add-the-style-picker"></a>添加样式选取器

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

上述代码中的第一个代码块设置订阅密钥并创建一个 Map 对象，地图样式预先设置为 grayscale_dark。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用 atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 构造函数来构造样式选取器。

样式选取器支持为地图选择样式。 第三个代码块使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法向地图添加样式选取器。 样式选取器位于地图**事件侦听器**内，以确保它在地图完全加载后加载。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

将控件添加到地图：

> [!div class="nextstepaction"]
> [添加地图控件](./map-add-controls.md)

添加地图图钉：

> [!div class="nextstepaction"]
> [添加图钉](./map-add-pin.md)
