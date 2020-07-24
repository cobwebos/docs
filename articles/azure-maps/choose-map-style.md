---
title: 在 Azure Maps 中更改地图的样式 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure Map web SDK 中可用的与样式相关的功能。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 815b76f2a1c8872ff01439b126cb2ba1cdf27953
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133361"
---
# <a name="change-the-style-of-the-map"></a>更改地图的样式

此地图支持多个不同的[样式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)，这些选项可以在使用 map 函数初始化或以后使用 map 函数进行初始化时设置 `setStyle` 。 本文说明如何使用这些样式选项来自定义地图外观。 了解如何在加载地图时设置样式，并学习如何使用样式选取器控件设置新的地图样式。

## <a name="set-the-style-options"></a>设置样式选项 

使用 map 函数初始化或更新样式选项后，可以将其传递到地图中 `setStyle` 。

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

以下工具显示不同样式选项如何更改地图的呈现方式。 若要查看三维建筑物，请缩小到主要城市。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地图样式选项" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 "笔<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>地图样式选项</a>" Azure Maps （ <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>）。
</iframe>

## <a name="choose-a-base-map-style"></a>选择基本地图样式

最常见的地图样式选项之一是，用于更改已设置样式的基本地图的样式。 Azure Maps 中提供了许多[支持的地图样式](supported-map-styles.md)。 

### <a name="set-base-map-style-on-map-load"></a>在地图负载上设置基本地图样式


通过设置选项来初始化映射时，可以指定地图样式 `style` 。 在下面的代码中，在 `style` 初始化时，映射的选项设置为 `grayscale_dark` 。

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基地图样式

 可以使用函数更新地图样式 `setStyle` ，并将 `style` 选项设置为所需的地图样式。

```javascript
map.setStyle({ style: 'satellite' });
```

在下面的代码中，在加载映射实例之后， `road` `satellite` 使用[system.windows.forms.control.setstyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)函数将映射样式从更新为。

<br/>

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

### <a name="add-the-style-picker"></a>添加样式选取器

"样式选取器" 控件提供具有飞出面板的 "轻松使用" 按钮，最终用户可以使用该按钮更改地图样式。 样式选择器具有两个不同的布局选项。 默认情况下，样式选取器使用 `icons` 布局，并将所有地图样式显示为图标的水平行。 

<center>

![样式选取器图标布局](media/choose-map-style/style-picker-icon-layout.png)</center>

第二个布局选项称为 `list` ，并显示地图样式的可滚动列表。  

<center>

![样式选取器列表布局](media/choose-map-style/style-picker-list-layout.png)</center>


下面的代码演示如何创建样式选取器控件的实例并将其添加到地图的右上角。 样式选取器设置为具有深色样式，并使用列表层显示选定的几个地图样式。

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

下面的代码将具有其默认设置的样式选取器控件添加到地图，以便用户可以轻松地在不同的地图样式之间切换。 使用右上角附近的 "地图样式" 控件切换地图样式。

<br/>

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

> [!TIP]
> 默认情况下，使用 Azure Maps 的 S0 定价层时，样式选取器控件将列出所有可用样式。 如果要减少此列表中的样式数量，请将你希望在列表中显示的样式数组传递到 `mapStyle` 样式选取器的选项中。 如果使用的是 S1 并想要显示所有可用样式，请将 `mapStyles` 样式选取器的选项设置为 `"all"` 。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

将控件添加到地图：

> [!div class="nextstepaction"]
> [添加地图控件](map-add-controls.md)

> [!div class="nextstepaction"]
> [添加图钉](map-add-pin.md)
