---
title: 将符号层添加到 Azure Maps | Microsoft Docs
description: 如何将符号添加到 Javascript 地图
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 2580f1177bf9e6e3a92934f88a5d8ab51894e8d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771662"
---
# <a name="add-a-symbol-layer-to-a-map"></a>将符号层添加到地图

本文介绍如何将数据源中的点数据呈现为地图上的符号层。 符号层使用 WebGL 呈现和支持比 HTML 标记的点太多更大的集，但不支持传统的 CSS 和 HTML 元素的样式设置。  

> [!TIP]
> 默认情况下，符号层将呈现数据源中所有几何图形的坐标。 功能集限制在层，以便它仅呈现点几何图形`filter`到层的属性`['==', ['geometry-type'], 'Point']`或`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`如果想要包括多点功能。

## <a name="add-a-symbol-layer"></a>添加符号层

<iframe height='500' scrolling='no' title='切换图钉位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切换图钉位置</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

上述第一个代码块构造 Map 对象。 有关说明，可以参阅[创建地图](./map-create.md)。

在第二个代码块中，使用 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 类创建了一个数据源对象。 包含[点](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)几何图形的 [特征] 由 [Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 类包装（因此更容易更新），并在创建后添加到数据源。

第三个代码块创建[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，并在单击鼠标时使用 shape 类的 [setCoordinates](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) 方法更新点的坐标。

某个[符号层](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)使用文本或图标来呈现作为符号包装在地图上 [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) 中的基于点的数据。  创建和添加到映射中的数据源、 单击事件侦听器和符号层`ready`[事件侦听器](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)函数，以确保在加载并可访问映射后显示的点。

> [!TIP]
> 默认情况下，为性能，符号层优化通过隐藏重叠的符号的符号的呈现。 在隐藏符号中放大时变得可见。 若要禁用此功能，并在所有时间呈现所有符号，设置`allowOverlap`的属性`iconOptions`到选项`true`。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>将自定义图标添加到符号层

符号层是使用 WebGL 呈现的。 因此，所有资源（例如图标图像）必须载入 WebGL 上下文。 此示例演示如何将自定义图标添加到映射资源，然后使用它来呈现与在地图上的自定义符号的点数据。 符号层的 `textField` 属性要求指定一个表达式。 在这种情况下，我们想要呈现的温度属性，但由于它是一个数字，它需要转换为字符串。 此外，我们想要向其追加"° F"。 表达式可用于此;`['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自定义符号图像图标' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自定义符号图像图标</a>文章。
</iframe>

## <a name="customize-a-symbol-layer"></a>自定义符号层 

符号层有许多样式选项。 以下工具可以测试各种样式选项。

<br/>

<iframe height='700' scrolling='no' title='符号层选项' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符号层选项</a>文章。
</iframe>

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TexTOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [添加形状](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)
