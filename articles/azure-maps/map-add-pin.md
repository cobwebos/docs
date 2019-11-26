---
title: 将符号层添加到 Azure Maps | Microsoft Docs
description: How to add symbols to the Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: fff73801d20333a6df5e7952d02ed664c17fe40b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480622"
---
# <a name="add-a-symbol-layer-to-a-map"></a>将符号层添加到地图

A symbol can be connected up to a data source and used to render an icon and/or text at a given point. Symbol layers are rendered using WebGL and can be used to render large collections of points on the map. This layer can render a lot more point data on the map, with good performance, than what is achievable using HTML markers. However, the symbol layer doesn't support traditional CSS and HTML elements for styling.  

> [!TIP]
> 默认情况下，符号层将呈现数据源中所有几何图形的坐标。 To limit the layer such that it only renders point geometry features set the `filter` property of the layer to `['==', ['geometry-type'], 'Point']` or `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` if you want to include MultiPoint features as well.

The maps image sprite manager, which is used to load custom images used by the symbol layer supports the following image formats:

- JPEG
- PNG
- SVG
- BMP
- GIF (no animations)

## <a name="add-a-symbol-layer"></a>添加符号层

To add a symbol layer to the map and render data, a data source first needs to be created and added the map. A symbol layer can then be created and passed in the data source to retrieve the data from. Finally, data needs to be added into the data source so that there is something to be rendered. The following code shows the code that should be added to the map after it has loaded to render a single point on the map using a symbol layer. 

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

There are four different types of point data to that can be added to the map:

- GeoJSON Point geometry - This object only contains a coordinate of a point and nothing else. The `atlas.data.Point` helper class can be used to easily create these objects.
- GeoJSON MultiPoint geometry - This object contains the coordinates of multiple points but nothing else. The `atlas.data.MultiPoint` helper class can be used to easily create these objects.
- GeoJSON Feature - This object consists of any GeoJSON geometry and a set of properties that contain metadata associated to the geometry. The `atlas.data.Feature` helper class can be used to easily create these objects.
- `atlas.Shape` class is similar to the GeoJSON feature in that it consists of a GeoJSON geometry and a set of properties that contain metadata associated to the geometry. If a GeoJSON object is added to a data source it can easily be rendered in a layer, however, if the coordinates property of that GeoJSON object is updated, the data source and map don't change as there is no mechanism in the JSON object to trigger an update. The shape class provides functions for updating the data it contains, and when a change is made, the data source and map are automatically notified and updated. 

The following code sample creates a GeoJSON Point geometry and passes it into the `atlas.Shape` class to make it easy to update. The center of the map is used initially to render a symbol. A click event is added to the map such that when it fires, the coordinates of where the mouse was clicked are used with the shapes `setCoordinates` function that updates the location of the symbol on the map.

<br/>

<iframe height='500' scrolling='no' title='切换图钉位置' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的通过 Azure Maps <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>切换图钉位置</a> (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 文章。
</iframe>

> [!TIP]
> By default, for performance, symbol layers optimize the rendering of symbols by hiding symbols that overlap. As you zoom in the hidden symbols become visible. To disable this feature and render all symbols at all times, set the `allowOverlap` property of the `iconOptions` options to `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>将自定义图标添加到符号层

符号层是使用 WebGL 呈现的。 因此，所有资源（例如图标图像）必须载入 WebGL 上下文。 This sample shows how to add a custom icon to the map resources and then use it to render point data with a custom symbol on the map. 符号层的 `textField` 属性要求指定一个表达式。 In this case, we want to render the temperature property but since it's a number, it needs to be converted to a string. Additionally we want to append the "°F" to it. An expression can be used to do this; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='自定义符号图像图标' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>自定义符号图像图标</a>文章。
</iframe>

> [!TIP]
> The Azure Maps web SDK provides several customizable image templates you can use with the symbol layer. For more infromation, see the [How to use image templates](how-to-use-image-templates-web-sdk.md) document.

## <a name="customize-a-symbol-layer"></a>自定义符号层 

符号层有许多样式选项。 以下工具可以测试各种样式选项。

<br/>

<iframe height='700' scrolling='no' title='符号层选项' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅 <a href='https://codepen.io'>CodePen</a> 上的 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) <a href='https://codepen.io/azuremaps/pen/PxVXje/'>符号层选项</a>文章。
</iframe>

> [!TIP]
> When you only want to render text with a symbol layer, you can hide the icon by setting the `image` property of the icon options to `'none'`.

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
> [Create a data source](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [How to use image templates](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Add a line layer](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Add a polygon layer](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加 HTML 标记](map-add-bubble-layer.md)
