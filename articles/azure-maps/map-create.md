---
title: 使用 Azure Maps 创建地图 | Microsoft Docs
description: 如何创建 JavaScript 地图
author: jingjing-z
ms.author: jinzh
ms.date: 09/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9759c4149c6b026837e550dcf3ab0a0156bbb736
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730003"
---
# <a name="create-a-map"></a>创建地图

本文介绍如何创建地图。  

## <a name="understand-the-code"></a>了解代码

可以通过两种方法来构建地图。 可通过指定中心点和缩放级别来设置地图相机。 通过指定西南边界点和东北边界点来设置地图相机边界。

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>设置相机

<iframe height='310' scrolling='no' title='通过 CameraOptions 创建地图' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>通过 `CameraOptions` 创建地图</a>。
</iframe>

在上述代码中，通过 `new atlas.Map()` 创建了[地图对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)。 中心和缩放级别等地图属性是 [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraoptions?view=azure-iot-typescript-latest) 的一部分。 可以在地图构造函数中定义 `CameraOptions`，也可以通过地图类的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera) 函数实现。

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>设置相机边界

<iframe height='310' scrolling='no' title='通过 CameraBoundsOptions 创建地图' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>通过 `CameraBoundsOptions` 创建地图</a>。
</iframe>

在上述代码中，通过 `new atlas.Map()` 构造了 [Map 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)。 边框等地图属性是 [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 的一部分。 可以通过地图类的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) 函数定义 `CameraBoundsOptions`。

## <a name="try-out-the-code"></a>试用代码

查看上面的示例代码。 可以在左侧的“JS”选项卡上编辑 JavaScript 代码，在右侧的“结果”选项卡上查看地图视图变化。 还可以单击“编辑 CodePen”按钮，编辑 CodePen 中的代码。

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
