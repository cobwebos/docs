---
title: 添加简单的数据层 |Microsoft Azure 映射
description: 了解如何使用 Azure Maps Web SDK 提供的空间 IO 模块添加简单的数据层。
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370981"
---
# <a name="add-a-simple-data-layer"></a>添加简单的数据层

空间 IO 模块提供了一个 `SimpleDataLayer` 类。 通过此类可以轻松地在地图上呈现样式的功能。 它甚至可以呈现具有样式属性和包含混合几何图形类型的数据集的数据集。 简单数据层通过包装多个呈现层并使用样式表达式来实现此功能。 样式表达式搜索这些已包装层内功能的通用样式属性。 `atlas.io.read` 函数和 `atlas.io.write` 函数使用这些属性来读写样式，并将其写入支持的文件格式。 将属性添加到支持的文件格式后，该文件可用于各种目的。 例如，该文件可用于显示地图上的样式功能。

除了设置功能的样式以外，`SimpleDataLayer` 还提供了一个带有 popup 模板的内置弹出功能。 单击该功能时，将显示弹出窗口。 如果需要，可以禁用默认的弹出功能。 该层还支持群集数据。 单击群集后，地图会放大到该群集，并将其扩展到各个点并 subclusters。

`SimpleDataLayer` 类旨在用于具有多个几何图形类型的大型数据集和应用于这些功能的许多样式。 使用此类时，会增加包含样式表达式的六个层的开销。 因此，在某些情况下，使用核心呈现层会更有效。 例如，使用核心层呈现几个几何图形类型和一个功能上的几个样式

## <a name="default-supported-style-properties"></a>默认支持的样式属性

如前文所述，简单的数据层包装多个核心呈现层：气泡图、符号图层、直线图、多边形和延伸多边形。 然后，它使用表达式搜索各个功能的有效样式属性。

Azure Maps 和 GitHub 样式属性是受支持的两个属性名称集。 不同 azure 地图层选项的大多数属性名称都支持作为简单数据层中功能的样式属性。 已将表达式添加到一些层选项，以支持 GitHub 通常使用的样式属性名称。 这些属性名称由[GitHub 的 GeoJSON 映射支持](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)定义，它们用于对在平台中存储和呈现的 GeoJSON 文件进行样式。 除 `marker-symbol` 样式属性外，所有 GitHub 的样式属性在简单数据层中都受支持。

如果读取器遇到不太常见的样式属性，它会将其转换为最接近的 Azure Maps 样式属性。 此外，可以通过使用简单数据层的 `getLayers` 函数并更新任何层上的选项来重写默认样式表达式。

下一部分提供了有关简单数据层支持的默认样式属性的详细信息。 支持的属性名称的顺序也是属性的优先级。 如果为同一层选项定义了两个样式属性，则列表中第一个属性的优先级更高。

## <a name="simple-data-layer-options"></a>简单的数据层选项

### <a name="bubble-layer-style-properties"></a>气泡图层样式属性

如果某个功能是 `Point` 或 `MultiPoint`，并且该功能没有 `image` 属性，该属性将用作将该点呈现为符号的自定义图标，则该功能将使用 `BubbleLayer`进行呈现。

| 层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `color` | `color`、`marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup>，`marker-size`<sup>2</sup>，`scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`、`stroke` | `'#FFFFFF'` |

\[1\] `size`，`scale` 值被视为标量值，它们将乘以 `8`

\[2\] 如果指定了 GitHub `marker-size` 选项，则将使用以下值作为半径。

| 标记大小 | 半径 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

还会使用气泡层呈现分类。 默认情况下，群集的 radius 设置为 `16`。 群集的颜色因群集中的点数而异，如下所示：

| 点数 | 颜色    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>符号样式属性

如果某个功能是 `Point` 或 `MultiPoint`，并且具有 `image` 属性，该属性将用作自定义图标以将该点呈现为符号，则该功能将使用 `SymbolLayer`进行呈现。

| 层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`，`marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] 如果指定 GitHub `marker-size` 选项，则以下值将用于 "图标大小" 选项。

| 标记大小 | 符号大小 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

如果点功能是一个分类，则 `point_count_abbreviated` 属性将呈现为一个文本标签。 不会呈现图像。

### <a name="line-style-properties"></a>线条样式属性

如果该功能是 `LineString`、`MultiLineString`、`Polygon`或 `MultiPolygon`，则该功能将使用 `LineLayer`进行呈现。

| 层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`、`stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`、`stroke-width`、`stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`、`stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>多边形样式属性

如果该功能是 `Polygon` 或 `MultiPolygon`，并且该功能不具有 `height` 属性或 `height` 属性为零，则该功能将使用 `PolygonLayer`进行呈现。

| 层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`、`fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`"`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>延伸多边形样式属性

如果该功能是 `Polygon` 或 `MultiPolygon`，并且其 `height` 属性的值大于0，则该功能将使用 `PolygonExtrusionLayer`进行呈现。

| 层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`、`fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>使用简单的数据层

`SimpleDataLayer` 类的使用方式类似于使用其他呈现层。 下面的代码演示如何在映射中使用简单的数据层：

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

向数据源中添加功能。 然后，简单的数据层将找出呈现功能的最佳方式。 单个功能的样式可设置为功能的属性。 下面的代码演示了一个 GeoJSON 点功能，其中 `color` 属性设置为 `red`。 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

下面的代码使用简单数据层呈现上述点功能。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用简单数据层" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> 请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>使用简单数据层</a>。
</iframe>

简单数据层的真正强大之处在于：

- 数据源中有几种不同类型的功能;或
- 数据集中的功能分别设置了几个样式属性;或
- 不确定数据集的确切包含内容。

例如，在分析 XML 数据馈送时，您可能不知道这些功能的确切样式和几何图形类型。 下面的示例通过呈现 KML 文件的功能，显示了简单数据层的强大功能。 它还演示了简单数据层类提供的各种选项。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="简单的数据层选项" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> 请参阅<a href='https://codepen.io'>CodePen</a>上的 "笔<a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>简单数据层" 选项</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>


## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [添加 OGC 地图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WF 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
