---
title: 更改 Azure 地图中的地图样式 |微软 Azure 地图
description: 在本文中，您将了解 Microsoft Azure 地图 Web SDK 中提供的与样式相关的功能。
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335687"
---
# <a name="change-the-style-of-the-map"></a>更改地图的样式

地图支持几个不同的[样式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)，可在地图初始化或以后使用地图`setStyle`函数时设置。 本文演示如何使用这些样式选项自定义地图外观。 在加载地图时学习设置样式，并了解如何使用样式选取器控件设置新的地图样式。

## <a name="set-the-style-options"></a>设置样式选项 

样式选项可在以后使用地图函数初始化或更新时传递到地图`setStyle`中。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

以下工具显示不同样式选项如何更改地图的呈现方式。 要查看 3D 建筑，可以放大大城市附近。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地图样式选项" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地图 （） 查看笔<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>贴图样式选项</a>。
</iframe>

## <a name="choose-a-base-map-style"></a>选择底图样式

最常见的地图样式选项之一用于更改样式设置的基本地图的样式。 [Azure 地图中支持的许多地图样式](supported-map-styles.md)在 Web SDK 中可用。 

### <a name="set-base-map-style-on-map-load"></a>在地图负载上设置基地图样式


通过设置选项在初始化地图时，`style`可以指定地图样式。 在以下代码中，`style`映射的选项设置为`grayscale_dark`初始化。

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基本地图样式

 可以使用`setStyle`函数更新地图样式，`style`并将选项设置为所需的地图样式。

```javascript
map.setStyle({ style: 'satellite' });
```

在以下代码中，加载地图实例后，地图样式将从`road`更新到`satellite`使用[setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)函数。

<br/>

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

### <a name="add-the-style-picker"></a>添加样式选取器

样式选取器控件提供了一个易于使用的按钮，带有弹出窗口面板，最终用户可以使用该按钮更改地图样式。 样式选取器有两个不同的布局选项。 默认情况下，样式选取器使用`icons`布局，并将所有地图样式显示为图标水平行。 

<center>

![样式选取器图标布局](media/choose-map-style/style-picker-icon-layout.png)</center>

调用`list`第二个布局选项并显示可滚动的地图样式列表。  

<center>

![样式选取器列表布局](media/choose-map-style/style-picker-list-layout.png)</center>


以下代码演示如何创建样式选取器控件的实例并将其添加到地图的右上角。 样式选取器设置为具有深色样式，并使用列表图层显示选定的几个地图样式。

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

以下代码将样式选取器控件及其默认设置添加到地图中，以便用户可以轻松地在不同的地图样式之间切换。 使用右上角附近的地图样式控件切换地图样式。

<br/>

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

> [!TIP]
> 默认情况下，当使用 Azure 地图的 S0 定价层时，样式选取器控件列出所有可用的样式。 如果要减少此列表中的样式数，请将要在列表中显示的样式的数组传递到样式选取器`mapStyle`的选项中。 如果使用 S1 并希望显示所有可用样式，则将`mapStyles`样式选取器的选项设置为`"all"`。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [地图](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [样式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [样式控制选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

向地图添加控件：

> [!div class="nextstepaction"]
> [添加地图控件](map-add-controls.md)

> [!div class="nextstepaction"]
> [添加图钉](map-add-pin.md)
