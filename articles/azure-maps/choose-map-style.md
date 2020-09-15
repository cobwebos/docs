---
title: 更改 Azure Maps Web Map Control 的样式
description: 了解如何更改地图的样式和选项。 请参阅如何在 Azure Maps 中将样式选取器控件添加到地图，以便用户可以在不同的样式之间切换。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: fe07d6d54296a3c55cb0b2cadb4d4ae0a311873b
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089558"
---
# <a name="change-the-style-of-the-map"></a>更改地图的样式

地图控件支持多种不同的地图 [样式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) 和 [基本地图样式](supported-map-styles.md)。 在初始化地图控件时，可以设置所有样式。 或者，您可以使用地图控件的函数设置样式 `setStyle` 。 本文说明如何使用这些样式选项来自定义地图的外观。 此外，您还将学习如何在您的地图中实现样式选取器控件。 样式选取器控件允许用户在不同的基本样式之间切换。

## <a name="set-map-style-options"></a>设置地图样式选项

可以在 web 控件初始化期间设置样式选项。 或者，您可以通过调用地图控件的函数来更新样式选项 `setStyle` 。 若要查看所有可用样式选项，请参阅 [样式选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)。

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

以下工具显示不同样式选项如何更改地图的呈现方式。 若要查看三维建筑物，请缩小到主要城市。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="地图样式选项" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
请参阅 CodePen 上的 () ，查看笔<a href='https://codepen.io/azuremaps/pen/eYNMjPb'>映射样式 Azure Maps 选项</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="set-a-base-map-style"></a>设置基本地图样式

你还可以使用 Web SDK 中提供的一种 [基本地图样式](supported-map-styles.md) 初始化地图控件。 然后，可以使用 `setStyle` 函数以不同的地图样式来更新基本样式。

### <a name="set-a-base-map-style-on-initialization"></a>在初始化时设置基映射样式

可以在初始化过程中设置地图控件的基本样式。 在下面的代码中， `style` 地图控件的选项设置为[ `grayscale_dark` 基本图样式](supported-map-styles.md#grayscale_dark)。  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

### <a name="update-the-base-map-style"></a>更新基地图样式

可以使用函数更新基本地图样式 `setStyle` ，并将 `style` 选项设置为更改为其他基本地图样式或添加其他样式选项。

```javascript
map.setStyle({ style: 'satellite' });
```

在下面的代码中，在加载映射实例之后， `grayscale_dark` `satellite` 使用 [system.windows.forms.control.setstyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-) 函数将映射样式从更新为。

<br/>

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

## <a name="add-the-style-picker-control"></a>添加样式选取器控件

"样式选取器" 控件提供了一个带有飞出面板的 "轻松使用" 按钮，最终用户可以使用该按钮在基本样式之间切换。

样式选择器具有两个不同的布局选项： `icon` 和 `list` 。 此外，样式选取器允许你选择两个不同的样式选取器控件 `style` 选项： `light` 和 `dark` 。 在此示例中，样式选取器使用 `icon` 布局，并以图标的形式显示基本地图样式的选择列表。 样式控件选取器包括以下基本样式集： `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` 。 有关样式选取器控件选项的详细信息，请参阅 [样式控制选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)。

下图显示了布局中显示的样式选取器控件 `icon` 。

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="样式选取器图标布局":::

下图显示了布局中显示的样式选取器控件 `list` 。

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="样式选取器列表布局":::

> [!IMPORTANT]
> 默认情况下，"样式选取器" 控件列出 Azure Maps 的 S0 定价层下可用的所有样式。 如果要减少此列表中的样式数量，请将你希望在列表中显示的样式数组传递到 `mapStyle` 样式选取器的选项中。 如果使用的是 S1 并想要显示所有可用样式，请将 `mapStyles` 样式选取器的选项设置为 `"all"` 。

下面的代码演示如何重写默认 `mapStyles` 基样式列表。 在此示例中，我们将设置 `mapStyles` 选项以列出要由样式选取器控件显示的基本样式。

<br/>

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

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
