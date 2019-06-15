---
title: 在 Azure Maps 中添加地图控件 | Microsoft Docs
description: 如何在 Azure Maps 中向地图添加缩放控件、绕 X 轴旋转控件、旋转控件和样式选取器。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c1f5dd329f34d64478d605c21d229d8c75a99300
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108713"
---
# <a name="add-map-controls-to-azure-maps"></a>将地图控件添加到 Azure Maps

本文展示了如何向地图添加地图控件。 还将了解如何创建包含所有控件和一个[样式选取器](https://docs.microsoft.com/azure/azure-maps/choose-map-style)的地图。

## <a name="add-zoom-control"></a>添加缩放控件

<iframe height='500' scrolling='no' title='添加缩放控件' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>添加缩放控件</a>。
</iframe>

第一个代码块设置订阅密钥并创建 Map 对象而不预先设置样式。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

缩放控件增加了放大和缩小地图的功能。 第二个代码块使用 atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 创建一个缩放控件对象，并使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将其添加到地图中。 缩放控件位于地图**事件侦听器**内，以确保它在地图完全加载后加载。

## <a name="add-pitch-control"></a>添加绕 X 轴旋转控件

<iframe height='500' scrolling='no' title='添加绕 X 轴旋转控件' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>添加绕 X 轴旋转控件</a>。
</iframe>

第一个代码块设置订阅密钥并创建 Map 对象而不预先设置样式。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

绕 X 轴旋转控件增加了更改地图俯仰角的功能。 第二个代码块使用 atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 创建一个绕 X 轴旋转控件对象，并使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将其添加到地图中。 绕 X 轴旋转控件位于地图**事件侦听器**内，以确保它在地图完全加载后加载。

## <a name="add-compass-control"></a>添加指南针控件

<iframe height='500' scrolling='no' title='添加旋转控件' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>添加旋转控件</a>。
</iframe>

第一个代码块设置订阅密钥并创建 Map 对象而不预先设置样式。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用 atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 创建一个指南针控件对象。 它还可使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将指南针控件添加到地图中。 指南针控件位于地图**事件侦听器**内，以确保它在地图完全加载后加载。

## <a name="a-map-with-all-controls"></a>包含所有控件的地图

<iframe height='500' scrolling='no' title='包含所有控件的地图' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>包含所有控件的地图</a>。
</iframe>

第一个代码块设置订阅密钥并创建 Map 对象而不预先设置样式。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用 atlas [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) 创建一个指南针控件对象并使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将其添加到地图中。

第三个代码块使用 atlas [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 创建一个缩放控件对象并使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将其添加到地图中。

第四个代码块使用atlas [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 创建一个绕 X 轴旋转控件对象并使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将其添加到地图中。

最后一个代码块通过使用 atlas [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 创建一个样式选取器对象并使用 map 的 [controls.add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 方法将其添加到地图中。 所有控件对象都添加到地图**事件侦听器**中，以确保它们在地图完全加载之后加载。

控件对象在脚本中的顺序决定了它们在地图上的显示顺序。 若要更改控件在地图上的顺序，可以更改它们在脚本中的顺序。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

有关完整代码，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加图钉](./map-add-pin.md)

> [!div class="nextstepaction"]
> [添加弹出项](./map-add-popup.md)
