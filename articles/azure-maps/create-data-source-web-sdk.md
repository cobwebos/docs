---
title: 为地图创建数据源 |Microsoft Azure 映射
description: 在本文中，你将了解如何创建数据源，并使用 Microsoft Azure map Web SDK 将其添加到地图中。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen
ms.openlocfilehash: 1675d63fd3a65beda46042f4a78535bb4e066e62
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190232"
---
# <a name="create-a-data-source"></a>创建数据源

Azure Maps Web SDK 将数据存储在数据源中。 使用数据源优化用于查询和呈现的数据操作。 目前有两种类型的数据源：

**GeoJSON 数据源**

基于 GeoJSON 的数据源使用 `DataSource` 类在本地加载和存储数据。 可以使用[GeoJSON 命名空间](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data)中的帮助器类手动创建或创建数据。 `DataSource` 类提供用于导入本地或远程 GeoJSON 文件的函数。 远程 GeoJSON 文件必须托管在已启用 CORs 的终结点上。 `DataSource` 类为聚集点数据提供功能。 而且，可以通过 `DataSource` 类轻松地添加、删除和更新数据。


> [!TIP]
> 假设您希望覆盖 `DataSource`中的所有数据。 如果调用 `clear` 然后 `add` 函数，则映射可能会重新呈现两次，这可能会导致一些延迟。 改为使用 `setShapes` 函数，该函数将移除并替换数据源中的所有数据，并且只触发地图的单次重新呈现。

**矢量磁贴源**

矢量图块源介绍如何访问矢量图块层。 使用[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)类实例化矢量图块源。 矢量图块层类似于图块层，但它们不同。 图块层是光栅图像。 矢量图块层是压缩文件，采用 PBF 格式。 此压缩文件包含向量映射数据以及一个或多个层。 基于每个层的样式，可以在客户端上呈现并设计文件样式。 矢量磁贴中的数据包含点、线条和多边形格式的地理功能。 使用矢量图块层（而不是光栅图块层）有多种优点：

 - 矢量磁贴的文件大小通常比等效的光栅磁贴小得多。 因此，使用的带宽更少。 这意味着延迟较低、更快的地图和更好的用户体验。
 - 由于矢量磁贴是在客户端上呈现的，因此它们会适应其所显示的设备的分辨率。 因此，呈现的地图的定义更清晰，并显示 crystal 清晰标签。
 - 更改向量图中的数据样式不需要再次下载数据，因为新样式可应用于客户端。 与此相反，更改光栅图块层的样式通常需要从服务器加载磁贴，然后应用新样式。
 - 由于数据是以矢量形式传递的，因此，进行数据准备需要的服务器端处理就越少。 因此，可以更快地提供较新的数据。

使用向量源的所有层都必须指定 `sourceLayer` 值。

创建数据源后，可以通过 `map.sources` 属性（ [SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)属性）将数据源添加到地图中。 下面的代码演示如何创建 `DataSource` 并将其添加到地图中。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

Azure Maps 遵循[Mapbox Vector 磁贴规范](https://github.com/mapbox/vector-tile-spec)，即开放标准。

## <a name="connecting-a-data-source-to-a-layer"></a>将数据源连接到层

使用呈现层在地图上呈现数据。 一个或多个呈现层可以引用单个数据源。 以下呈现层需要数据源：

- [气泡图层](map-add-bubble-layer.md)-将点数据呈现为地图上的缩放圆圈。
- [符号层](map-add-pin.md)-将点数据呈现为图标或文本。
- [热度地图层](map-add-heat-map-layer.md)-将点数据呈现为密度热度地图。
- [线条层](map-add-shape.md)-呈现线条并呈现多边形的轮廓。 
- [多边形层](map-add-shape.md)-使用纯色或图像图案填充多边形的面积。

下面的代码演示如何创建数据源，将其添加到地图中，然后将其连接到气泡图层。 然后，将 GeoJSON 点数据从远程位置导入到数据源中。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

还有其他呈现层不连接到这些数据源，但它们直接加载要呈现的数据。 

- [图像层](map-add-image-layer.md)-将单个图像叠加到地图的顶部，并将其角部绑定到一组指定坐标。
- 图[块层](map-add-tile-layer.md)-superimposes 在地图的顶部。

## <a name="one-data-source-with-multiple-layers"></a>一个具有多个层的数据源

多个层可以连接到单个数据源。 在许多不同方案中，此选项很有用。 例如，假设用户绘制多边形的情况。 当用户向地图添加点时，应呈现并填充多边形区域。 如果将样式的线条添加到边框，则可以在用户绘制时更轻松地看到多边形的边缘。 若要方便地编辑多边形中的单个位置，可以在每个位置的上方添加一个句柄，如 pin 或标记。

![显示单个数据源呈现数据的多个层的地图](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

在大多数映射平台中，需要一个多边形对象、一个线条对象和一个用于多边形中每个位置的 pin。 修改多边形时，您需要手动更新线条和 pin，这很快就会变得复杂。

对于 Azure Maps，只需在数据源中使用一个多边形，如以下代码所示。

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
> [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.vectortilesourceoptions?view=azure-maps-typescript-latest)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加弹出项](map-add-popup.md)

> [!div class="nextstepaction"]
> [使用数据驱动样式表达式](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [添加符号层](map-add-pin.md)

> [!div class="nextstepaction"]
> [添加气泡层](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [添加线条层](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [添加多边形层](map-add-shape.md)

> [!div class="nextstepaction"]
> [添加热度地图](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)