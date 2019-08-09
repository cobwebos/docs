---
title: Azure Maps 中的地图样式功能 | Microsoft Docs
description: 了解 Azure Maps 样式的相关功能。
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: eb667c398be0bd51e05a6b65d416d5bce54e4386
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881968"
---
# <a name="choose-a-map-style-in-azure-maps"></a>在 Azure Maps 中选择地图样式

Azure Maps 中提供了许多[支持的地图样式](./supported-map-styles.md)。 本文介绍如何使用样式的相关功能设置地图加载的样式、设置新的样式和使用样式选取器控件。

## <a name="set-style-on-map-load"></a>设置地图加载的样式

在下面的代码中, `style`在初始化时, 映射的选项`grayscale_dark`设置为。

<br/>

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上设置地图加载的样式</a>。
</iframe>

## <a name="update-the-style"></a>更新样式

在下面的代码中, 在加载映射实例之后, 会将映射样式从`road`更新为`satellite`使用地图的[system.windows.forms.control.setstyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)函数。

<br/>

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上更新样式</a>。
</iframe>

## <a name="add-the-style-picker"></a>添加样式选取器

下面的代码将[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)添加到地图中, 以便用户可以轻松地在不同的地图样式之间切换。 

<br/>

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 在 <a href='https://codepen.io'>CodePen</a> 上添加样式选取器</a>。
</iframe>

> [!TIP]
> 默认情况下, "样式选取器" 控件默认列出默认情况下使用 Azure Maps 的 S0 定价层时可用的所有样式。 如果要减少此列表中的样式数量, 请将你希望在列表中显示的样式数组传递到样式选取器的`mapStyle`选项中。 如果使用的是 S1 并想要显示所有可用样式, 请将样式`mapStyles`选取器的选项设置为`"all"`。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

将控件添加到地图:

> [!div class="nextstepaction"]
> [添加地图控件](map-add-controls.md)

> [!div class="nextstepaction"]
> [添加图钉](map-add-pin.md)
