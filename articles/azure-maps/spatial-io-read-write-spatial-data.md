---
title: 读取和写入空间数据 |Microsoft Azure 映射
description: 了解如何使用 Azure Maps Web SDK 提供的空间 IO 模块来读取和写入数据。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 458b307cf1158c467100e032e3f789462e8fdcca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370903"
---
# <a name="read-and-write-spatial-data"></a>读取和写入空间数据

下表列出了在空间 IO 模块中执行读取和写入操作时支持的空间文件格式。

| 数据格式       | 读 | 写入 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| .GPX）               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空间 CSV       | ✓  |  ✓  |
| 熟知文本   | ✓  |  ✓  |

以下各节概述了使用空间 IO 模块读取和写入空间数据的所有不同工具。

## <a name="read-spatial-data"></a>读取空间数据

`atlas.io.read` 函数是用于读取通用空间数据格式（例如 KML、.GPX)、GeoRSS、GeoJSON 和具有空间数据的 CSV 文件）的主要函数。 此函数还可以读取这些格式的压缩版本，如 zip 文件或 KMZ 文件。 KMZ 文件格式是 KML 的压缩版本，还可以包含诸如图像之类的资产。 或者，read 函数可以采用指向任意格式的文件的 URL。 Url 应托管在已启用 CORs 的终结点上，或者应在读取选项中提供代理服务。 代理服务用于在未启用 CORs 的域上加载资源。 Read 函数返回向地图添加图像图标的承诺，并异步处理数据，以最大程度地减小对 UI 线程的影响。

读取压缩文件（作为 zip 或 KMZ）时，将对第一个有效文件进行解压缩和扫描。 例如，kml 或具有其他有效扩展名的文件，例如：. kml、.xml、geojson、json、.csv、tsv 或 .txt。 然后，预加载 KML 和 GeoRSS 文件中引用的图像，以确保它们可访问。 不可访问的图像数据可能会加载备用回退映像，或将从样式中删除。 从 KMZ 文件中提取的图像将转换为数据 Uri。

Read 函数的结果是一个 `SpatialDataSet` 对象。 此对象扩展 GeoJSON FeatureCollection 类。 它可以轻松地按原样传递到 `DataSource`，以在地图上呈现其功能。 `SpatialDataSet` 不仅包含功能信息，还包含下表中所述的 KML 地面覆盖、处理度量值和其他详细信息。

| 属性名称 | 类型 | 说明 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 数据集中所有数据的边界框。 |
| `features` | `Feature[]` | GeoJSON 数据集内的功能。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlays 的数组。 |
| `icons` | 记录&lt;字符串，string&gt; | 一组图标 Url。 Key = icon name，Value = URL。 |
| 属性 | 任意 | 在空间数据集的文档级提供的属性信息。 |
| `stats` | `SpatialDataSetStats` | 有关空间数据集的内容和处理时间的统计信息。 |
| `type` | `'FeatureCollection'` | 只读 GeoJSON 类型值。 |

## <a name="examples-of-reading-spatial-data"></a>读取空间数据的示例

下面的代码演示如何读取简单的空间数据集，并使用 `SimpleDataLayer` 类在地图上呈现该数据集。 该代码使用 URL 指向的 .GPX) 文件。

<br/>

<iframe height='500' scrolling='no' title='简单加载空间数据' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>上的 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>），查看笔<a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>加载空间数据的简单</a>方法。
</iframe>

下面的代码演示演示如何读取 KML 或 KMZ，并将其加载到映射。 KML 可以包含地面覆盖，其形式为 `ImageLyaer` 或 `OgcMapLayer`。 这些叠加必须与功能分开添加到地图中。 此外，如果数据集包含自定义图标，则在加载功能之前需要将这些图标加载到映射资源。

<br/>

<iframe height='500' scrolling='no' title='将 KML 加载到地图上' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 KML （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）将笔<a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>加载到 Azure Maps 地图</a>上。
</iframe>

你可以选择提供代理服务，用于访问可能未启用 CORs 的跨域资产。 此代码片段演示了如何提供代理服务：

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

//Read a KML file from a URL or pass in a raw KML string.
atlas.io.read('https://s3-us-west-2.amazonaws.com/s.cdpn.io/1717245/2007SanDiegoCountyFires.kml', {
    //Provide a proxy service
    proxyService: proxyServiceUrl
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

以下演示如何读取带分隔符的文件并将其呈现在地图上。 在这种情况下，代码使用具有空间数据列的 CSV 文件。

<br/>

<iframe height='500' scrolling='no' title='添加分隔文件' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 "通过 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）<a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>添加带分隔符的文件</a>。
</iframe>

## <a name="write-spatial-data"></a>写入空间数据

空间 IO 模块中有两个主要写入函数。 `atlas.io.write` 函数将生成一个字符串，而 `atlas.io.writeCompressed` 函数会生成压缩的 zip 文件。 压缩的 zip 文件将包含一个基于文本的文件，其中包含空间数据。 这两个函数都返回一项承诺，以将数据添加到文件。 而且，它们都可以编写以下任何数据： `SpatialDataSet`、`DataSource`、`ImageLayer`、`OgcMapLayer`、功能集合、功能、几何或这些数据类型的任意组合的数组。 使用任一函数编写时，可以指定所需的文件格式。 如果未指定文件格式，则数据将以 KML 的形式写入。

下图演示了可与 `atlas.io.write` 函数一起使用的大多数写入选项。

<br/>

<iframe height='700' scrolling='no' title='空间数据写入选项' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的笔<a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空间数据写入选项</a>Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="example-of-writing-spatial-data"></a>写入空间数据的示例

下面的示例允许您在地图上拖放并加载空间文件。 可以从映射中导出 GeoJSON 数据，并将其作为字符串或压缩文件写入其中一种受支持的空间数据格式。

<br/>

<iframe height='700' scrolling='no' title='将空间文件拖放到地图上' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的<a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>将空间文件拖放到 map</a> by Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

你可以选择提供代理服务，用于访问可能未启用 CORs 的跨域资产。 此代码片段演示了如何合并代理服务：

```javascript

//Set the location of your proxyServiceUrl file 
var proxyServiceUrl = window.location.origin + '/CorsEnabledProxyService.ashx?url=';

function readData(data, fileName) {
    loadingIcon.style.display = '';
    fileCount++;
    //Attempt to parse the file and add the shapes to the map.
    atlas.io.read(data, {
        //Provide a proxy service
        proxyService: proxyServiceUrl
    }).then(
        //Success
        function(r) {
            //some code goes here ...
        }
    );
}
```

## <a name="read-and-write-well-known-text-wkt"></a>读取和写入已知文本（WKT）

[熟知文本](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry)（WKT）是一种用于将空间几何表示为文本的开放地理空间信息联盟（OGC）标准。 许多地理空间系统使用 PostGIS 插件支持 WKT，例如 Azure SQL 和 Azure PostgreSQL。 与大多数 OGC 标准一样，坐标的格式设置为 "经度纬度"，以与 "x y" 约定保持一致。 例如，可以使用 WKT 格式将介于110和纬度45的点编写为 `POINT(-110 45)`。

可以使用 `atlas.io.ogc.WKT.read` 函数读取已知文本，并使用 `atlas.io.ogc.WKT.write` 函数编写该文本。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>读取和写入熟知文本（WKT）的示例

下面的代码演示如何读取已知的文本字符串 `POINT(-122.34009 47.60995)` 并使用气泡图层在地图上呈现该字符串。

<br/>

<iframe height='500' scrolling='no' title='读取已知文本' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 "<a href='https://codepen.io/azuremaps/pen/XWbabLd/'>读取已知文本</a>" 一 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

下面的代码演示如何读取和写入已知文本。

<br/>

<iframe height='700' scrolling='no' title='读取和写入已知文本' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅<a href='https://codepen.io'>CodePen</a>上的 "<a href='https://codepen.io/azuremaps/pen/JjdyYav/'>读取和写入已知文本</a>" 一 Azure Maps （<a href='https://codepen.io/azuremaps'>@azuremaps</a>）。
</iframe>

## <a name="read-and-write-gml"></a>读取和写入 GML

GML 是一个空间 XML 文件规范，通常用作其他 XML 规范的扩展。 GeoJSON 数据可使用 `atlas.io.core.GmlWriter.write` 函数编写为具有 GML 标记的 XML。 可以使用 `atlas.io.core.GmlReader.read` 函数读取包含 GML 的 XML。 Read 函数有两个选项：

- `isAxisOrderLonLat` 选项-坐标 "纬度、经度" 或 "经度，纬度" 的轴顺序在数据集之间可能会有所不同，并且它不是很明确的定义。 默认情况下，GML 读取器将坐标数据作为 "纬度，经度" 读取，但将此选项设置为 true 会将其读取为 "经度，纬度"。
- `propertyTypes` 选项-此选项是键值查找表，其中的键是数据集中属性的名称。 值是在分析时将值强制转换为的对象类型。 支持的类型值为： `string`、`number`、`boolean`和 `date`。 如果某个属性不在查找表中或未定义该类型，则该属性将作为字符串进行分析。

当 `atlas.io.core.GmlReader.read` 函数检测到输入数据为 XML 时，该函数将默认为 `atlas.io.read` 函数，但数据不是其他支持空间 XML 格式之一。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [atlas.io 静态函数](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [ogc. WKT 函数](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加 OGC 地图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WF 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心操作](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
