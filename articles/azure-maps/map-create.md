---
title: 使用 Azure 地图创建地图 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Microsoft Azure 地图 Web SDK 在网页上呈现地图。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802325"
---
# <a name="create-a-map"></a>创建地图

本文展示了如何创建地图并将地图制成动画。  

## <a name="loading-a-map"></a>加载地图

要加载地图，请创建[Map 类](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)的新实例。 初始化地图时，传递 DIV 元素 ID 以呈现地图，并传递一组加载地图时要使用的选项。 如果未在`atlas`命名空间上指定默认身份验证信息，则加载地图时需要在地图选项中指定此信息。 地图以异步方式加载多个资源以进行性能加载。 因此，在创建地图实例后，将 或`ready``load`事件附加到地图，然后将与映射交互的任何其他代码添加到事件处理程序。 一`ready`旦地图加载了足够的资源以编程方式与之交互，事件就会触发。 初始`load`地图视图完全加载后，事件将触发。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地图负载" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看笔<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地图加载</a>。
</iframe>

> [!TIP]
> 您可以在同一页上加载多个地图。 同一页上的多个地图可能使用相同的或不同的身份验证和语言设置。

## <a name="show-a-single-copy-of-the-world"></a>显示世界的单一副本

当地图在宽屏幕上缩小时，世界的多个副本将水平显示。 此选项对于某些方案非常理想，但对于其他应用程序，最好看到世界的单一副本。 此行为通过将地图`renderWorldCopies`选项设置为`false`实现。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="渲染世界副本 = 假" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，请参阅"按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 显示的笔<a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>呈现世界副本 = false。</a>
</iframe>


## <a name="map-options"></a>地图选项

在那里创建地图时，可以传入几种不同类型的选项来自定义地图的函数方式，如下所示。

- [Camera 选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)和[CameraBound 选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions)用于指定地图应显示的区域。
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions)用于指定地图应如何与为地图供电的服务进行交互。
- [样式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)用于指定地图应设置样式和渲染。
- [用户交互选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions)用于指定当用户与地图交互时地图应如何到达。 

`setCamera`在使用`setServiceOptions`、`setStyle`和`setUserInteraction`函数加载地图后，也可以更新这些选项。 

## <a name="controlling-the-map-camera"></a>控制地图摄像机

有两种方法可以使用地图的相机设置地图的显示区域。 加载地图时，可以设置摄像机选项。 或者，在地图加载后`setCamera`，您可以随时调用该选项以以编程方式更新地图视图。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>设置相机

地图摄像机控制地图画布的视口中显示的内容。 在初始化或传递到地图函数中时，可以将相机选项传递到地图`setCamera`选项中。

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

在以下代码中，将创建一个[Map 对象](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)，并设置中心和缩放选项。 地图属性（如中心级和缩放级别）是["相机选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)"的一部分。

<br/>

<iframe height='500' scrolling='no' title='通过 CameraOptions 创建地图' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>通过  创建地图`CameraOptions` </a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>设置相机边界

边界框可用于更新地图摄像机。 如果边界框是根据点数据计算的，则通常也很有用，在摄像机选项中指定像素填充值以考虑图标大小。 这将有助于确保点不会从地图视口边缘脱落。

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

在以下代码中，通过 构造[Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) `new atlas.Map()`对象。 可以通过 Map 类的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 函数定义地图属性，例如 `CameraBoundsOptions`。 边界和填充属性是使用 `setCamera` 设置的。

<br/>

<iframe height='500' scrolling='no' title='通过 CameraBoundsOptions 创建地图' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>通过  创建地图`CameraBoundsOptions` </a>。
</iframe>

### <a name="animate-map-view"></a>将地图视图制成动画

设置地图的相机选项时，也可以设置[动画选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions)。 这些选项指定移动摄像机所应采用的动画类型和持续时间。

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

在以下代码中，第一个代码块创建一个地图并设置输入和缩放地图样式。 在第二个代码块中，为动画按钮创建一个单击事件处理程序。 单击此按钮时，使用`setCamera`["相机选项](/javascript/api/azure-maps-control/atlas.cameraoptions)"和"[动画选项](/javascript/api/azure-maps-control/atlas.animationoptions)"的一些随机值调用该函数。

<br/>

<iframe height='500' scrolling='no' title='将地图视图制成动画' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上由 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 提供的 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Animate Map View</a>（将地图视图制成动画）。
</iframe>

## <a name="try-out-the-code"></a>试用代码

查看代码示例。 您可以在**JS 选项卡**中编辑 JavaScript 代码，并在 **"结果"选项卡**上看到地图视图更改。您还可以在右上角单击**CodePen 上的"编辑**"，并在 CodePen 中修改代码。

<a id="relatedReference"></a>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [地图](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [相机选项](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [动画选项](/javascript/api/azure-maps-control/atlas.animationoptions)

请参阅向应用添加功能的代码示例：

> [!div class="nextstepaction"]
> [更改地图的样式](choose-map-style.md)

> [!div class="nextstepaction"]
> [将控件添加到地图](map-add-controls.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)
