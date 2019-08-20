---
title: 在 Azure Maps 中创建数据源 |Microsoft Docs
description: 如何创建数据源并将其与 Azure Maps Web SDK 一起使用。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c225aa5f821925247d27890e9ee75f3eac4d9eb9
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977347"
---
# <a name="create-a-data-source"></a>创建数据源

Azure Maps Web SDK 将数据存储在数据源中, 用于优化用于查询和呈现的数据。 目前有两种类型的数据源:

**GeoJSON 数据源**

基于 GeoJSON 的数据源可以使用类在`DataSource`本地加载和存储数据。 可以使用 [atlas.data](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data) 命名空间中的帮助器类手动创建或创建 GeoJSON 数据。 `DataSource`类提供用于导入本地或远程 GeoJSON 文件的函数。 远程 GeoJSON 文件必须托管在已启用 CORs 的终结点上。 `DataSource`类为聚集点数据提供功能。 

**矢量磁贴源**

矢量图块源介绍了如何访问矢量图块层, 并可使用[VectorTileSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.vectortilesource)类进行创建。 Azure Maps 与[Mapbox 矢量磁贴规范](https://github.com/mapbox/vector-tile-spec)(即开放式标准) 对齐。 矢量图块层类似于图块层, 但是, 它们是一个压缩文件 (PBF 格式), 其中包含向量图数据和一个或多个层, 可以根据每个层的样式在客户端上呈现并设置其样式。 矢量磁贴中的数据包含点、线条和多边形格式的地理功能。 矢量图块层通过光栅图块层有多个优点;

 - 矢量磁贴的文件大小通常比等效的光栅磁贴小得多。 因此, 使用较少的带宽, 这意味着较低的延迟和更快的地图。 这将创建更好的用户体验。
 - 由于矢量磁贴是在客户端上呈现的, 因此它们可以适应正在显示的设备的分辨率。 这使得呈现的地图更好地定义并带有 crystal 清晰标签。 
 - 更改矢量地图中数据的样式不需要再次下载数据, 因为新样式可应用于客户端。 与此相反, 更改光栅图块层的样式通常需要从应用了新样式的服务器加载磁贴。
 - 由于数据是以矢量形式传递的, 因此, 在准备数据时需要进行更少的服务器端处理, 这意味着可以更快地提供较新的数据。

使用向量源的所有层都必须指定一个`sourceLayer`值。 

创建后, 可以通过`map.sources`属性将数据源添加到地图中, 这是一个[SourceManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.sourcemanager)。 下面的代码演示如何创建`DataSource`并将其添加到地图中。

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);
```

## <a name="connecting-a-data-source-to-a-layer"></a>将数据源连接到层

使用呈现层在地图上呈现数据。 一个或多个呈现层可以引用单个数据源。 以下呈现层要求数据源能够为它供电:

- [气泡图层](map-add-bubble-layer.md)-将点数据呈现为地图上的缩放圆圈。
- [符号层](map-add-pin.md)-将点数据呈现为图标和/或文本。
- [热度地图层](map-add-heat-map-layer.md)-将点数据呈现为密度热度地图。
- [线条层](map-add-shape.md)-可用于呈现线条和或多边形的轮廓。 
- [多边形层](map-add-shape.md)-使用纯色或图像图案填充多边形的面积。

下面的代码演示如何创建数据源、将其添加到地图中并将其连接到气泡图层, 然后将 GeoJSON 点数据从远程位置导入到该数据源。 

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(datasource));

//Load the earthquake data.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

还有其他呈现层未连接到这些数据源, 而是加载它们直接呈现的数据。 

- [图像层](map-add-image-layer.md)-将单个图像叠加到地图的顶部, 并将其角部绑定到一组指定坐标。
- 图[块层](map-add-tile-layer.md)-superimposes 在地图的顶部。

## <a name="one-data-source-with-multiple-layers"></a>一个具有多个层的数据源

多个层可以连接到单个数据源。 这听起来可能很奇怪, 但在许多不同的情况下, 这会很有用。 例如, 创建多边形绘图体验的方案。 允许用户绘制多边形时, 应在用户向地图添加点时呈现填充多边形区域。 添加轮廓多边形的带样式的线条将可以更轻松地查看绘制多边形的边缘。 最后, 在多边形中的每个位置上方添加某种控点 (如旋转或标记) 可以更轻松地编辑每个单独位置。 下面是演示此方案的图像。

![显示单个数据源呈现数据的多个层的地图](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

若要在大多数映射平台中完成此方案, 需要为多边形中的每个位置创建多边形对象、线条对象和 pin。 当修改多边形时, 您需要手动更新线条和 pin, 这会很快变得复杂。

使用 Azure Maps 你只需一个数据源中的多边形, 如以下代码所示。

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
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)