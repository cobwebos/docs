---
title: 使用 Azure Maps 创建地图 | Microsoft Docs
description: 如何创建 JavaScript 地图
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec85854e5d9b7ee0d5e2c54881a417ba6cbb366e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599807"
---
# <a name="create-a-map"></a>创建地图

本文介绍如何创建地图。  

## <a name="understand-the-code"></a>了解代码

可以通过两种方法来构建地图。 可以通过指定中心点和缩放级别来设置地图的照相机，也可以通过指定西南边界点和东部边界点来设置地图的照相机边界。

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>设置照相机

<iframe height='310' scrolling='no' title='通过 CameraOptions 创建地图' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>通过 CameraOptions 创建地图</a>。
</iframe>

在上述代码中，通过 `new atlas.Map()` 创建了[地图对象](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)。 中心和缩放级别等地图属性是 [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest) 的一部分。 可以在地图构造函数中定义 CameraOptions，也可以通过地图类的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) 函数来这样做。

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>设置照相机边界

<iframe height='310' scrolling='no' title='通过 CameraBoundsOptions 创建地图' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 的 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>通过 CameraBoundsOptions 创建地图</a>。
</iframe>

在上述代码中，通过 `new atlas.Map()` 构造了[地图对象](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)。 边框等地图属性是 [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest) 的一部分。 可以通过地图类的 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) 函数来定义 CameraBoundsOptions。

## <a name="try-out-the-code"></a>试用代码 

查看上面的示例代码。 可以在左侧的“JS”选项卡上编辑 JavaScript 代码，在右侧的“结果”选项卡上查看地图视图变化。 还可以单击“编辑 CodePen”按钮，编辑 CodePen 中的代码。 

<a id="relatedReference"></a>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    
有关可向地图添加的更多代码示例，请参阅以下文章： 
* [添加图钉](./map-add-pin.md)
* [添加弹出项](map-add-popup.md)
    

