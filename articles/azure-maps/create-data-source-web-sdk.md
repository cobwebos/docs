---
title: 为地图创建数据源 |微软 Azure 地图
description: 在本文中，您将学习如何创建数据源，以及如何使用 Microsoft Azure 地图 Web SDK 将其添加到地图中。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190232"
---
# <a name="create-a-data-source"></a>创建数据源

Azure 映射 Web SDK 将数据存储在数据源中。 使用数据源可优化用于查询和呈现的数据操作。 目前有两种类型的数据源：

**GeoJSON 数据源**

基于 GeoJSON 的数据源使用`DataSource`类在本地加载和存储数据。 GeoJSON 数据可以使用[atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data)命名空间中的帮助器类手动创建或创建。 该`DataSource`类提供用于导入本地或远程 GeoJSON 文件的函数。 远程 GeoJSON 文件必须托管在启用 COR 的终结点上。 类`DataSource`提供群集点数据的功能。 而且，数据可以轻松地添加、删除和更新类`DataSource`。


> [!TIP]
> 假设您要覆盖 中的`DataSource`所有数据。 如果调用当时的`clear``add`函数，地图可能会重新渲染两次，这可能会导致一些延迟。 而是使用`setShapes`函数，该函数将删除并替换数据源中的所有数据，并且仅触发映射的单个重新渲染。

**矢量磁贴源**

矢量切片源描述如何访问矢量切片图层。 使用[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)类实例化矢量切片源。 矢量切片图层与切片图层类似，但它们不同。 切片图层是栅格图像。 矢量切片图层是压缩文件，采用 PBF 格式。 此压缩文件包含矢量映射数据以及一个或多个图层。 文件可以根据每个图层的样式在客户端上呈现和设置样式。 矢量磁贴中的数据以点、线和面的形式包含地理要素。 使用矢量切片图层而不是栅格切片图层有几个优点：

 - 矢量磁贴的文件大小通常比等效栅格磁贴小得多。 因此，使用的带宽更少。 这意味着更低的延迟、更快的地图和更好的用户体验。
 - 由于矢量切片呈现在客户端上，因此它们会适应显示它们的设备的分辨率。 因此，渲染的地图看起来更清晰，带有水晶般清晰的标签。
 - 更改矢量映射中的数据样式不需要再次下载数据，因为新样式可以应用于客户端。 相反，更改栅格切片图层的样式通常需要从服务器加载切片，然后应用新样式。
 - 由于数据以矢量形式传递，因此准备数据所需的服务器端处理更少。 因此，较新的数据可以更快地可用。

使用矢量源的所有图层都必须指定值`sourceLayer`。

创建后，数据源可以通过`map.sources`属性（即[SourceManager）](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)添加到地图中。 以下代码演示如何创建 并将其`DataSource`添加到地图中。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure 地图遵循[地图框矢量磁贴规范](https://github.com/mapbox/vector-tile-spec)，这是一个开放标准。

## <a name="connecting-a-data-source-to-a-layer"></a>将数据源连接到图层

使用渲染图层在地图上呈现数据。 单个数据源可以由一个或多个呈现图层引用。 以下呈现图层需要数据源：

- [气泡图层](map-add-bubble-layer.md)- 在地图上以缩放的圆圈渲染点数据。
- [符号图层](map-add-pin.md)- 将点数据渲染为图标或文本。
- [热图图层](map-add-heat-map-layer.md)- 渲染点数据作为密度热图。
- [线图层](map-add-shape.md)- 渲染线条和或渲染多边形的轮廓。 
- [多边形图层](map-add-shape.md)- 用纯色或图像图案填充多边形区域。

以下代码演示如何创建数据源、将其添加到地图并将其连接到气泡图层。 然后，将 GeoJSON 点数据从远程位置导入数据源。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

还有其他呈现图层不连接到这些数据源，但它们直接加载数据进行呈现。 

- [图像图层](map-add-image-layer.md)- 覆盖地图顶部的单个图像，并将其角绑定到一组指定的坐标。
- [切片图层](map-add-tile-layer.md)- 叠加地图顶部的栅格切片图层。

## <a name="one-data-source-with-multiple-layers"></a>具有多层的一个数据源

多个层可以连接到单个数据源。 在许多不同的方案中，此选项很有用。 例如，请考虑用户绘制多边形的方案。 当用户向地图添加点时，我们应该渲染和填充多边形区域。 添加样式线以勾勒出多边形，从而在用户绘制时更轻松地查看多边形的边缘。 为了方便地编辑多边形中的单个位置，我们可以在每个位置上方添加一个控点，如图钉或标记。

![显示从单个数据源呈现数据的多个图层的地图](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

在大多数映射平台中，对于多边形中的每个位置，都需要一个多边形对象、一个线对象和一个引脚。 修改多边形后，需要手动更新线和引脚，这很快就会变得复杂。

使用 Azure 映射，只需数据源中的单个多边形，如下所示。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a polygon and add it to the data source.
dataSource.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(dataSource, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(dataSource, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(dataSource, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     outlineColor: 'white',
     outlineWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [数据来源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [数据源选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [矢量切片源](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [矢量切片源选项](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符号图层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加气泡图层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加热度地图](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)