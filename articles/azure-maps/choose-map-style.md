---
title: 地图样式功能 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure Map web SDK 中可用的样式相关功能。
author: farah-alyasari
ms.author: v-faalya
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b793a0fc977d3da4b4b6753877e838441e70e800
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210083"
---
# <a name="choose-a-map-style-in-azure-maps"></a>在 Azure Maps 中选择地图样式

Azure Maps 中提供了许多[支持的地图样式](./supported-map-styles.md)。 本文介绍如何使用与样式相关的功能。 了解如何在加载地图时设置样式，并学习如何使用样式选取器控件设置新的地图样式。

## <a name="set-style-on-map-load"></a>设置地图加载的样式

在下面的代码中，在初始化时，映射的 `style` 选项设置为 `grayscale_dark`。

<br/>

<iframe height='500' scrolling='no' title='设置地图加载的样式' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>通过 Azure Maps (</a><a href='https://codepen.io/azuremaps'>@azuremaps) 在 </a>CodePen<a href='https://codepen.io'> 上设置地图加载的样式</a>。
</iframe>

## <a name="update-the-style"></a>更新样式

在下面的代码中，在加载映射实例之后，使用[system.windows.forms.control.setstyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)函数将映射样式从 `road` 更新为 `satellite`。

<br/>

<iframe height='500' scrolling='no' title='更新样式' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>通过 Azure Maps (</a><a href='https://codepen.io/azuremaps'>@azuremaps) 在 </a>CodePen<a href='https://codepen.io'> 上更新样式</a>。
</iframe>

## <a name="add-the-style-picker"></a>添加样式选取器

下面的代码将[StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol)添加到地图中，因此用户可以轻松地在不同的地图样式之间切换。 使用右上角附近的 "地图样式" 控件切换地图样式。

<br/>

<iframe height='500' scrolling='no' title='添加样式选取器' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅画笔 <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>通过 Azure Maps (</a><a href='https://codepen.io/azuremaps'>@azuremaps) 在 </a>CodePen<a href='https://codepen.io'> 上添加样式选取器</a>。
</iframe>

> [!TIP]
> 默认情况下，使用 Azure Maps 的 S0 定价层时，样式选取器控件将列出所有可用样式。 如果要减少此列表中的样式数量，请将希望在列表中显示的样式数组传递到样式选取器的 "`mapStyle`" 选项中。 如果使用的是 S1 并想要显示所有可用样式，请将样式选取器的 "`mapStyles`" 选项设置为 "`"all"`"。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

将控件添加到地图：

> [!div class="nextstepaction"]
> [添加地图控件](map-add-controls.md)

> [!div class="nextstepaction"]
> [添加图钉](map-add-pin.md)
