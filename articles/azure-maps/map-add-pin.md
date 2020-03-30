---
title: 向地图添加符号图层 |微软 Azure 地图
description: 在本文中，您将了解如何使用 Symbol 图层自定义符号，并使用 Microsoft Azure 地图 Web SDK 在地图上添加符号。
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b8d131dcc798fb2fe1d4bb650cd5b0a68903381b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209692"
---
# <a name="add-a-symbol-layer-to-a-map"></a>将符号层添加到地图

将符号连接到数据源，并用它来在给定点呈现图标或文本。 

符号层是使用 WebGL 呈现的。 使用符号图层在地图上呈现较大的点集合。 与 HTML 标记相比，符号图层在地图上呈现大量点数据，性能更好。 但是，符号图层不支持传统的 CSS 和 HTML 元素进行样式设置。  

> [!TIP]
> 默认情况下，符号层将呈现数据源中所有几何图形的坐标。 要限制图层，使其仅渲染点几何要素将图层`filter`的属性设置为`['==', ['geometry-type'], 'Point']`或`['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]`如果需要，还可以包括 MultiPoint 要素。

地图图像精灵管理器加载符号图层使用的自定义图像。 它支持以下图像格式：

- JPEG
- PNG
- SVG
- BMP
- GIF（无动画）

## <a name="add-a-symbol-layer"></a>添加符号层

在将符号图层添加到地图之前，需要执行几个步骤。 首先，创建数据源，并将其添加到地图中。 创建符号图层。 然后，将数据源传递到符号层，以便从数据源检索数据。 最后，将数据添加到数据源中，以便呈现要呈现的内容。 

下面的代码演示了加载后应添加到地图的内容。 此示例使用符号图层渲染地图上的单个点。 

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

有四种不同类型的点数据可以添加到地图中：

- GeoJSON 点几何 - 此对象仅包含点的坐标，而不包含任何其他坐标。 `atlas.data.Point`帮助器类可用于轻松创建这些对象。
- GeoJSON 多点几何 - 此对象包含多个点的坐标，而没有其他点。 `atlas.data.MultiPoint`帮助器类可用于轻松创建这些对象。
- GeoJSON 特征 - 此对象由任何 GeoJSON 几何体和一组包含与几何体关联的元数据的属性组成。 `atlas.data.Feature`帮助器类可用于轻松创建这些对象。
- `atlas.Shape`类类似于 GeoJSON 功能。 两者都由 GeoJSON 几何体和一组包含与几何体关联的元数据的属性组成。 如果将 GeoJSON 对象添加到数据源中，则可以轻松地在图层中呈现该对象。 但是，如果更新了该 GeoJSON 对象的坐标属性，则数据源和映射不会更改。 这是因为 JSON 对象中没有触发更新的机制。 形状类提供用于更新其包含的数据的函数。 进行更改时，数据源和地图将自动通知和更新。 

以下代码示例创建 GeoJSON Point 几何体并将其传递到类中`atlas.Shape`，以便轻松更新。 地图的中心最初用于渲染符号。 单击事件将添加到地图中，以便当它触发时，鼠标的坐标将与形状`setCoordinates`函数一起使用。 鼠标坐标在单击事件时记录。 然后，更新`setCoordinates`符号在地图上的位置。

<br/>

<iframe height='500' scrolling='no' title='切换图钉位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切换图钉位置</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

> [!TIP]
> 默认情况下，符号图层通过隐藏重叠的符号来优化符号的渲染。 放大时，隐藏的符号将变为可见。 要禁用此功能并随时渲染所有符号，请将`allowOverlap``iconOptions`选项的属性设置为`true`。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>将自定义图标添加到符号层

符号层是使用 WebGL 呈现的。 因此，所有资源（例如图标图像）必须载入 WebGL 上下文。 此示例演示如何向地图资源添加自定义图标。 然后使用此图标在地图上使用自定义符号渲染点数据。 符号层的 `textField` 属性要求指定一个表达式。 在这种情况下，我们要呈现温度属性。 由于温度是一个数字，它需要转换为字符串。 此外，我们希望附加"+F"到它。 表达式可用于执行此串联;例如，表达式可用于执行此串联。`['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自定义符号图像图标' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自定义符号图像图标</a>文章。
</iframe>

> [!TIP]
> Azure 地图 Web SDK 提供了多个可自定义的图像模板，可用于符号图层。 有关详细信息，请参阅[如何使用图像模板](how-to-use-image-templates-web-sdk.md)文档。

## <a name="customize-a-symbol-layer"></a>自定义符号层 

符号层有许多样式选项。 以下工具可以测试各种样式选项。

<br/>

<iframe height='700' scrolling='no' title='符号层选项' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符号层选项</a>文章。
</iframe>

> [!TIP]
> 当您只想渲染带有符号图层的文本时，可以通过将图标选项`image`的属性设置为`'none'`来隐藏图标。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [文本选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [创建数据源](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [如何使用图像模板](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡图层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)
