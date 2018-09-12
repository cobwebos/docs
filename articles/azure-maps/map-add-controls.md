---
title: 在 Azure Maps 中添加地图控件 | Microsoft Docs
description: 如何在 Azure Maps 中向地图添加缩放控件、绕 X 轴旋转控件、旋转控件和样式选取器。
author: walsehgal
ms.author: v-musehg
ms.date: 08/29/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 435c6545b69b4457c3e1035fb8125399d4c9c23a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666125"
---
# <a name="add-map-controls-to-azure-maps"></a>将地图控件添加到 Azure Maps

本文展示了如何向地图添加地图控件。 还将了解如何创建包含所有控件和一个[样式选取器](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker)的地图。

## <a name="add-zoom-control"></a>添加缩放控件

<iframe height='500' scrolling='no' title='添加缩放控件' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>添加缩放控件</a>。
</iframe>

上面代码中的第一个代码块创建地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用地图集 [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) 创建缩放控件对象。

缩放控件增加了放大和缩小地图的功能。 第三个块使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将缩放控件添加到地图。

## <a name="add-pitch-control"></a>添加绕 X 轴旋转控件

<iframe height='500' scrolling='no' title='添加绕 X 轴旋转控件' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>添加绕 X 轴旋转控件</a>。
</iframe>

上面的第一个代码块创建一个样式设置为灰度的地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用地图集 [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) 创建绕 X 轴旋转控件对象。

绕 X 轴旋转控件增加了更改地图俯仰角的功能。 第三个块使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将绕 X 轴旋转控件添加到地图。

## <a name="add-compass-control"></a>添加指南针控件

<iframe height='500' scrolling='no' title='添加旋转控件' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>添加旋转控件</a>。
</iframe>

上面代码中的第一个代码块创建地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用地图集[指南针控件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)创建指南针控件对象。 它还可使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将指南针控件添加到地图。

## <a name="a-map-with-all-controls"></a>包含所有控件的地图

<iframe height='500' scrolling='no' title='包含所有控件的地图' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 发布的 Pen <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>包含所有控件的地图</a>。
</iframe>

上面代码中的第一个代码块创建地图对象。 有关如何创建地图的说明，请参阅[创建地图](./map-create.md)。

第二个代码块使用地图集 [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) 创建指南针控件对象并使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将其添加到地图。

第三个代码块使用地图集 [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) 创建缩放控件对象并使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将其添加到地图。

第四个代码块使用地图集 [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) 创建绕 X 轴旋转控件对象并使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将其添加到地图。

最后一个代码块通过使用地图集 [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) 向地图添加样式选取器对象并使用地图的 [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) 方法将其添加到地图。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法： 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol)

* [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)
    * [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol)
    * [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest)
    * [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest)
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol)
    
有关可向地图添加的更多代码示例，请参阅以下文章： 
* [添加图钉](./map-add-pin.md)
* [添加弹出项](./map-add-popup.md)