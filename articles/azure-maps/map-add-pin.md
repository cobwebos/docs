---
title: 将符号层添加到 Azure Maps | Microsoft Docs
description: 如何将符号添加到 Azure Maps Web SDK。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 10f6a7ef92bfd6558ed93e7fb40df9e48e1b92f5
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976178"
---
# <a name="add-a-symbol-layer-to-a-map"></a>将符号层添加到地图

符号可以连接到数据源, 并用于在给定点呈现图标和/或文本。 符号层是使用 WebGL 呈现的, 可用于在地图上呈现大型点集合。 此层可以在地图上呈现更多的点数据, 其性能比使用 HTML 标记实现的效果好。 但是, 符号层不支持传统的 CSS 和 HTML 元素进行样式设置。  

> [!TIP]
> 默认情况下，符号层将呈现数据源中所有几何图形的坐标。 若要将层限制为仅呈现点几何特征, 请将`filter`层的属性设置为`['==', ['geometry-type'], 'Point']` , `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`或者如果要包含 MultiPoint 功能, 则设置为。

## <a name="add-a-symbol-layer"></a>添加符号层

若要将符号层添加到地图并呈现数据, 首先需要创建一个数据源并将其添加到地图中。 然后, 可以在数据源中创建并传递符号层, 以从检索数据。 最后, 需要将数据添加到数据源, 以便呈现一些内容。 下面的代码演示了在加载后, 应将代码添加到映射, 以使用符号层呈现地图上的单个点。 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

可向地图添加四种不同类型的点数据:

- GeoJSON 点几何-此对象只包含一个点的坐标, 而不包含其他任何内容。 `atlas.data.Point` Helper 类可用于轻松创建这些对象。
- GeoJSON MultiPoint geometry-此对象包含多个点的坐标, 但不包含其他任何点。 `atlas.data.MultiPoint` Helper 类可用于轻松创建这些对象。
- GeoJSON 功能-此对象包含任何 GeoJSON 几何和一组属性, 其中包含与几何图形关联的元数据。 `atlas.data.Feature` Helper 类可用于轻松创建这些对象。
- `atlas.Shape`类类似于 GeoJSON 功能, 因为它包含 GeoJSON 几何和一组属性, 其中包含与几何关联的元数据。 如果将 GeoJSON 对象添加到数据源, 则可以轻松地在层中呈现该对象, 但如果更新该 GeoJSON 对象的 "坐标" 属性, 则数据源和映射不会更改, 因为 JSON 对象中没有用于触发更新的机制。 Shape 类提供用于更新其包含的数据的功能, 进行更改后, 将自动通知和更新数据源和映射。 

下面的代码示例创建一个 GeoJSON 点几何, 并将其`atlas.Shape`传递给类以使其易于更新。 地图的中心最初用于呈现符号。 将单击事件添加到地图中, 以便在其触发时, 单击鼠标的位置的坐标与更新地图上符号位置的`setCoordinates` shapes 函数一起使用。

<br/>

<iframe height='500' scrolling='no' title='切换图钉位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切换图钉位置</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

> [!TIP]
> 默认情况下, 对于性能, 符号层通过隐藏重叠的符号来优化符号的呈现。 当您放大隐藏的符号时, 隐藏符号就会变得可见。 若要禁用此功能并始终呈现所有符号, 请将`allowOverlap` `iconOptions`选项的属性设置为`true`。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>将自定义图标添加到符号层

符号层是使用 WebGL 呈现的。 因此，所有资源（例如图标图像）必须载入 WebGL 上下文。 此示例演示如何将自定义图标添加到地图资源, 并将其用于使用地图上的自定义符号呈现点数据。 符号层的 `textField` 属性要求指定一个表达式。 在这种情况下, 我们希望呈现温度属性, 但由于它是一个数字, 因此需要将其转换为字符串。 此外, 我们还需要在其中追加 "° F"。 表达式可用于执行此操作;`['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自定义符号图像图标' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自定义符号图像图标</a>文章。
</iframe>

> [!TIP]
> Azure Maps web SDK 提供了几个可自定义的图像模板, 你可以将其用于符号层。 有关详细信息, 请参阅[如何使用映像模板](how-to-use-image-templates-web-sdk.md)文档。

## <a name="customize-a-symbol-layer"></a>自定义符号层 

符号层有许多样式选项。 以下工具可以测试各种样式选项。

<br/>

<iframe height='700' scrolling='no' title='符号层选项' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符号层选项</a>文章。
</iframe>

> [!TIP]
> 如果只想使用符号层呈现文本, 可以通过将图标选项的`image`属性设置为来`'none'`隐藏该图标。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用映像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)
