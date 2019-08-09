---
title: 使用 Azure Maps 创建地图 | Microsoft Docs
description: 如何创建 JavaScript 地图
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9f1188fcf2aa24791ca5181f5c94a1b0f6b15dc1
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882064"
---
# <a name="create-a-map"></a>创建地图

本文展示了如何创建地图并将地图制成动画。  

## <a name="loading-a-map"></a>加载映射

若要加载地图, 请创建[map 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)的新实例。 当初始化映射时, 将使用一个 DIV 元素 ID 来呈现映射, 并在传入映射时使用一组选项。 如果未在`atlas`命名空间上指定默认身份验证信息, 则在加载地图时, 需要在映射选项中指定此信息。 地图以异步方式加载多个资源以提高性能。 因此, 在创建映射实例之后, 将`ready`或`load`事件附加到映射, 然后添加与该事件处理程序中的映射进行交互的任何其他代码。 当映射的资源足以以编程方式交互时, 就会触发事件。`ready` 在初始映射视图完全加载完成后引发事件。`load` 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地图负载" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上的 "笔<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地图加载</a>方式" Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
</iframe>

> [!TIP]
> 可以在同一页上加载多个映射, 每个映射可以使用相同或不同的身份验证和语言设置。

## <a name="show-a-single-copy-of-the-world"></a>显示世界上单个副本

当在宽屏幕上缩小地图时, 多个世界副本将水平显示。 这在大多数情况下非常有用, 但对于某些应用程序, 可能只需要查看单个副本。 可以通过将 maps `renderWorldCopies`选项设置为来`false`完成此操作。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅<a href='https://codepen.io'>CodePen</a>上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 中的<a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> 。
</iframe>

## <a name="controlling-the-map-camera"></a>控制地图相机

可以通过两种方式使用照相机来设置显示的地图区域。 可以在加载地图时设置相机选项 (如中心和缩放), 或在映射加载后`setCamera`随时调用选项, 以编程方式更新地图视图。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>设置相机

在下面的代码中, 将创建一个[地图对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)并设置中心和缩放选项。 地图属性 (如中心和缩放级别) 是[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)的一部分。

<br/>

<iframe height='500' scrolling='no' title='通过 CameraOptions 创建地图' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>通过 `CameraOptions` 创建地图</a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>设置相机边界

在下面的代码中,[地图对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)通过`new atlas.Map()`构造。 可以通过 Map 类的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函数定义地图属性，例如 `CameraBoundsOptions`。 边界和填充属性是使用 `setCamera` 设置的。

<br/>

<iframe height='500' scrolling='no' title='通过 CameraBoundsOptions 创建地图' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>通过 `CameraBoundsOptions` 创建地图</a>。
</iframe>

### <a name="animate-map-view"></a>将地图视图制成动画

在下面的代码中, 第一个代码块创建地图并设置地图样式、中心和缩放值。 在第二个代码块中, 为 "动画" 按钮创建一个 click 事件处理程序。 当单击此按钮时, 将调用 setCamera 函数, 其中包含[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)的一些随机值。

<br/>

<iframe height='500' scrolling='no' title='将地图视图制成动画' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>（将地图视图制成动画）。
</iframe>

## <a name="try-out-the-code"></a>试用代码

查看上面的示例代码。 可以在左侧的“JS”选项卡上编辑 JavaScript 代码，在右侧的“结果”选项卡上查看地图视图变化。 还可以单击“编辑 CodePen”按钮，编辑 CodePen 中的代码。

<a id="relatedReference"></a>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [更改地图的样式](choose-map-style.md)

> [!div class="nextstepaction"]
> [向地图添加控件](map-add-controls.md)
