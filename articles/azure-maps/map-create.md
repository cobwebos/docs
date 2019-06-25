---
title: 使用 Azure Maps 创建地图 | Microsoft Docs
description: 如何创建 JavaScript 地图
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108593"
---
# <a name="create-a-map"></a>创建地图

本文展示了如何创建地图并将地图制成动画。  

## <a name="understand-the-code"></a>了解代码

可以通过两种方法来构建地图。 可以通过指定中心点和缩放级别来设置地图的照相机，也可以通过指定西南边界点和东部边界点来设置地图的照相机边界。

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>设置相机

<iframe height='500' scrolling='no' title='通过 CameraOptions 创建地图' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>通过 `CameraOptions` 创建地图</a>。
</iframe>

在上面的代码中，通过 `new atlas.Map()` 创建了一个 [Map 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)并且设置了中心和缩放。 中心和缩放级别等地图属性是 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 的一部分。

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>设置相机边界

<iframe height='500' scrolling='no' title='通过 CameraBoundsOptions 创建地图' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>通过 `CameraBoundsOptions` 创建地图</a>。
</iframe>

在上述代码中，通过 `new atlas.Map()` 构造了 [Map 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)。 可以通过 Map 类的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函数定义地图属性，例如 `CameraBoundsOptions`。 边界和填充属性是使用 `setCamera` 设置的。

### <a name="animate-map-view"></a>将地图视图制成动画

<iframe height='500' scrolling='no' title='将地图视图制成动画' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>（将地图视图制成动画）。
</iframe>

在上面的代码中，第一个代码块通过 `new atlas.Map()` 创建一个 [Map 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)。 中心和缩放级别等地图属性是 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 的一部分。 可以在地图构造函数中定义 `CameraOptions`，也可以通过地图类的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函数实现。 [地图样式](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)设置为 `road`。

第二个代码块创建一个制作地图动画函数，它通过使用 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函数定义 [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) 来将地图视图中的变化制成动画。 该函数由“将地图制成动画”按钮触发，在每次单击时将生成一个随机缩放级别。

## <a name="try-out-the-code"></a>试用代码

查看上面的示例代码。 可以在左侧的“JS”选项卡上编辑 JavaScript 代码，在右侧的“结果”选项卡上查看地图视图变化   。 还可以单击“编辑 CodePen”按钮，编辑 CodePen 中的代码  。

<a id="relatedReference"></a>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [选择地图样式](choose-map-style.md)

> [!div class="nextstepaction"]
> [添加地图控件](map-add-controls.md)
