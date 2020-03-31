---
title: 添加简单的数据层 |微软 Azure 地图
description: 了解如何使用 Azure 地图 Web SDK 提供的空间 IO 模块添加简单的数据层。
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7671d07a468a9f67a4851ec828fe18896d7a6c66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334279"
---
# <a name="add-a-simple-data-layer"></a>添加简单的数据层

空间 IO 模块提供`SimpleDataLayer`类。 此类使在地图上渲染样式要素变得容易。 它甚至可以渲染具有样式属性的数据集和包含混合几何类型的数据集。 简单数据层通过包装多个呈现图层并使用样式表达式来实现此功能。 样式表达式搜索这些包装图层内要素的常见样式属性。 函数`atlas.io.read`和`atlas.io.write`函数使用这些属性将样式读取和写入受支持的文件格式。 将属性添加到受支持的文件格式后，该文件可用于各种用途。 例如，该文件可用于在地图上显示样式特征。

除了样式功能外，还提供`SimpleDataLayer`带有弹出模板的内置弹出窗口功能。 单击要素时，将显示弹出窗口。 如果需要，可以禁用默认弹出窗口功能。 此层还支持群集数据。 单击群集时，地图将放大到群集中，并将其扩展到单个点和子群集中。

该`SimpleDataLayer`类用于具有许多几何类型和许多应用于要素样式的大型数据集。 使用时，此类会增加六个包含样式表达式的图层的开销。 因此，在某些情况下，使用核心呈现图层的效率更高。 例如，使用核心图层在要素上呈现几个几何类型和几个样式

## <a name="use-a-simple-data-layer"></a>使用简单的数据层

类`SimpleDataLayer`的使用类似于使用其他呈现图层。 下面的代码显示了如何在地图中使用简单的数据图层：

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

向数据源添加要素。 然后，简单的数据层将找出如何最好地呈现要素。 单个要素的样式可以设置为要素上的属性。 以下代码显示属性设置为`color``red`的 GeoJSON 点要素。 

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

以下代码使用简单的数据层呈现上述点要素。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用简单数据层" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> 请参阅笔 在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>使用简单数据图层</a>。
</iframe>

当出现以下情况时，简单数据层的真正功能就来了：

- 数据源中有几种不同类型的功能;或
- 数据集中的要素具有多个样式属性，分别设置它们;或
- 您不确定数据集包含的内容。

例如，在分析 XML 数据馈送时，您可能不知道要素的确切样式和几何类型。 下面的示例通过呈现 KML 文件的功能来显示简单数据层的强大功能。 它还演示了简单数据层类提供的各种选项。

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="简单的数据层选项" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> 在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>简单数据图层选项</a>。
</iframe>


> [!NOTE]
> 此简单数据层使用[弹出模板](map-add-popup.md#add-popup-templates-to-the-map)类将 KML 气球或要素属性显示为表。 默认情况下，弹出窗口中呈现的所有内容都将作为安全功能在 iframe 内部装沙盒。 但是，存在限制：
>
> - 禁用所有脚本、窗体、指针锁定和顶部导航功能。 单击时，允许在新选项卡中打开链接。 
> - 不支持 iframe 上`srcdoc`参数的旧浏览器将仅限于呈现少量内容。
> 
> 如果您信任加载到弹出窗口中的数据，并且可能希望加载到弹出窗口中的这些脚本能够访问您的应用程序，则可以通过将弹出窗口模板`sandboxContent`选项设置为 false 来禁用此功能。 

## <a name="default-supported-style-properties"></a>默认支持的样式属性

如前所述，简单数据图层环绕多个核心呈现图层：气泡、符号、线、面和拉伸多边形。 然后，它使用表达式在单个要素上搜索有效的样式属性。

Azure 映射和 GitHub 样式属性是两种主要受支持的属性名称集。 不同 Azure 地图图层选项的大多数属性名称都支持为简单数据图层中要素的样式属性。 表达式已添加到某些图层选项中，以支持 GitHub 常用的样式属性名称。 这些属性名称由[GitHub 的 GeoJSON 地图支持](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)定义，它们用于设置在平台中存储和呈现的 GeoJSON 文件样式。 除了`marker-symbol`样式属性外，简单数据层中支持 GitHub 的所有样式属性。

如果读取器遇到不太常见的样式属性，它将将其转换为最近的 Azure 地图样式属性。 此外，可以使用`getLayers`简单数据图层的功能并更新任何图层上的选项来重写默认样式表达式。

以下各节提供有关简单数据层支持的默认样式属性的详细信息。 支持的属性名称的顺序也是属性的优先级。 如果为同一图层选项定义了两个样式属性，则列表中的第一个样式属性具有更高的优先级。

### <a name="bubble-layer-style-properties"></a>气泡图层样式属性

如果要素`Point`为 或`MultiPoint`，并且该要素没有用作自定义图标以将`image`点呈现为符号的属性，则该要素将使用 呈现`BubbleLayer`。

| 图层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1</sup> `marker-size` <sup>2</sup>， `scale`2 ， <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size`和`scale`值被视为标量值，它们将乘以`8`

\[2\]如果指定了`marker-size`GitHub 选项，则半径将使用以下值。

| 标记大小 | 半径 |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

群集也使用气泡图层呈现。 默认情况下，群集的半径设置为`16`。 群集的颜色因群集中的点数而异，如下所示：

| 积分 | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>符号样式属性

如果要素`Point`为 或`MultiPoint`，并且要素具有用作自定义图标以`image`将点呈现为符号的属性，则该要素将使用 呈现`SymbolLayer`。

| 图层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`， `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\]如果指定了`marker-size`GitHub 选项，则以下值将用于图标大小选项。

| 标记大小 | 符号大小 |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

如果点要素是群集，则`point_count_abbreviated`该属性将呈现为文本标签。 不会渲染任何图像。

### <a name="line-style-properties"></a>线条样式属性

如果要素`LineString`为 、、`MultiLineString``Polygon`或`MultiPolygon`，则该要素将使用 呈现。 `LineLayer`

| 图层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>多边形样式属性

`Polygon`如果要素为 或`MultiPolygon`，并且要素没有`height`属性，或者`height`该属性为零，则该要素将使用 呈现。 `PolygonLayer`

| 图层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>拉伸多边形样式属性

如果要素`Polygon`为 或`MultiPolygon`，并且具有值大于`height`0 的属性，则该要素将使用 呈现`PolygonExtrusionLayer`。

| 图层选项 | 支持的属性名称 | 默认值 |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [简单数据层](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [简单数据列表选项](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [添加 OGC 地图图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心业务](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
