---
title: 读取和写入空间数据 |微软 Azure 地图
description: 了解如何使用 Azure 地图 Web SDK 提供的空间 IO 模块读取和写入数据。
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334159"
---
# <a name="read-and-write-spatial-data"></a>读取和写入空间数据

下表列出了空间 IO 模块的读取和写入操作所支持的空间文件格式。

| 数据格式       | 读取 | 写入 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| 地质学            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 空间 CSV       | ✓  |  ✓  |
| 知名文本   | ✓  |  ✓  |

以下各节概述了使用空间 IO 模块读取和写入空间数据的所有不同工具。

## <a name="read-spatial-data"></a>读取空间数据

该`atlas.io.read`函数是用于读取常用空间数据格式的主要函数，例如 KML、GPX、GeoRSS、GeoJSON 和包含空间数据的 CSV 文件。 此功能还可以读取这些格式的压缩版本，作为 zip 文件或 KMZ 文件。 KMZ 文件格式是 KML 的压缩版本，还可以包括图像等资产。 或者，读取函数可以采用以任何这些格式指向文件的 URL。 URL 应托管在启用 CORS 的终结点上，或者应在读取选项中提供代理服务。 代理服务用于加载未启用 CORS 的域上的资源。 读取函数返回将图像图标添加到地图的承诺，并异步处理数据，以尽量减少对 UI 线程的影响。

读取压缩文件（作为 zip 或 KMZ）时，将解压缩并扫描第一个有效文件。 例如，doc.kml 或其他有效扩展名的文件，例如：.kml、.xml、geojson、.json、.csv、.tsv 或 .txt。 然后，预先加载 KML 和 GeoRSS 文件中引用的图像，以确保它们易于访问。 无法访问的图像数据可能会加载备用回退图像，或将从样式中删除。 从 KMZ 文件中提取的图像将转换为数据 URI。

读取函数的结果是一个`SpatialDataSet`对象。 此对象扩展 GeoJSON 要素集合类。 它可以很容易地传递到一个`DataSource`正样，以呈现其要素在地图上。 不仅`SpatialDataSet`包含功能信息，而且还可能包括下表中概述的 KML 地面叠加、处理指标和其他详细信息。

| 属性名称 | 类型 | 说明 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 数据集中所有数据的边界框。 |
| `features` | `Feature[]` | 数据集中的 GeoJSON 功能。 |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML 接地叠加阵列。 |
| `icons` | 记录&lt;字符串，字符串&gt; | 一组图标 URL。 键 = 图标名称，值 = URL。 |
| properties | any | 在空间数据集的文档级别提供的属性信息。 |
| `stats` | `SpatialDataSetStats` | 有关空间数据集的内容和处理时间的统计信息。 |
| `type` | `'FeatureCollection'` | 只读 GeoJSON 类型值。 |

## <a name="examples-of-reading-spatial-data"></a>读取空间数据的示例

以下代码演示如何读取空间数据集，以及如何使用 类`SimpleDataLayer`在地图上呈现它。 该代码使用 URL 指向的 GPX 文件。

<br/>

<iframe height='500' scrolling='no' title='加载空间数据简单' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 显示笔<a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>加载空间数据简单</a>。
</iframe>

下一个代码演示演示如何读取 KML 或 KMZ 到地图。 KML 可以包含接地叠加，其形式为`ImageLyaer`或`OgcMapLayer`。 这些叠加必须与要素分开添加到地图上。 此外，如果数据集具有自定义图标，则这些图标需要在加载要素之前加载到地图资源。

<br/>

<iframe height='500' scrolling='no' title='将 KML 加载到地图上' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，请参阅按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 显示笔<a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>加载 KML 到地图</a>。
</iframe>

您可以选择提供代理服务来访问可能未启用 CORS 的跨域资产。 读取函数将首先尝试使用 CORS 访问另一个域上的文件。 首次使用 CORS 无法访问其他域上的任何资源后，它仅在提供代理服务时才会请求其他文件。 读取函数将文件 URL 追加到提供的代理 URL 的末尾。 此代码段演示如何将代理服务传递到读取函数：

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

下面的演示演示如何读取已划定的文件并在地图上呈现该文件。 在这种情况下，代码使用具有空间数据列的 CSV 文件。

<br/>

<iframe height='500' scrolling='no' title='添加已限制的文件' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>请参阅在 CodePen 上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）<a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>添加"已限制文件</a>"<a href='https://codepen.io'>的笔</a>。
</iframe>

## <a name="write-spatial-data"></a>写入空间数据

空间 IO 模块中有两个主要写入函数。 函数`atlas.io.write`生成字符串，`atlas.io.writeCompressed`而函数生成压缩 zip 文件。 压缩的 zip 文件将包含一个基于文本的文件，其中包含空间数据。 这两个函数都返回将数据添加到文件中的承诺。 并且，它们都可以编写以下任何数据`SpatialDataSet`：、、、、`DataSource``ImageLayer``OgcMapLayer`特征、几何或这些数据类型的任意组合数组。 使用任一函数写入时，可以指定想要的文件格式。 如果未指定文件格式，则数据将写入 KML。

下面的工具演示了可用于`atlas.io.write`函数的大多数写入选项。

<br/>

<iframe height='700' scrolling='no' title='空间数据写入选项' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看笔<a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>空间数据写入选项</a>。
</iframe>

## <a name="example-of-writing-spatial-data"></a>编写空间数据的示例

以下示例允许您拖放，然后在地图上加载空间文件。 可以从地图导出 GeoJSON 数据，并将其以受支持的空间数据格式之一写成字符串或压缩文件。

<br/>

<iframe height='700' scrolling='no' title='将空间文件拖放到地图上' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，通过 Azure 地图 （）<a href='https://codepen.io/azuremaps'>@azuremaps</a>查看笔<a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>拖放空间文件到地图上</a>。
</iframe>

您可以选择提供代理服务来访问可能未启用 CORS 的跨域资产。 此代码段显示您可以合并代理服务：

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>阅读和写入知名文本 （WKT）

[众所周知的文本](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry)（WKT） 是开放地理空间联盟 （OGC） 标准，用于将空间几何表示为文本。 许多地理空间系统都支持 WKT，例如使用 PostGIS 插件的 Azure SQL 和 Azure PostgreSQL。 与大多数 OGC 标准一样，坐标被格式化为"经纬度"，以符合"x y"约定。 例如，经度 -110 和纬度 45 处的点`POINT(-110 45)`可以编写为使用 WKT 格式。

可以使用 函数读取`atlas.io.ogc.WKT.read`已知文本，并使用 函数`atlas.io.ogc.WKT.write`编写。

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>阅读和编写知名文本的示例 （WKT）

以下代码演示如何读取已知文本字符串`POINT(-122.34009 47.60995)`并使用气泡图层在地图上呈现它。

<br/>

<iframe height='500' scrolling='no' title='阅读已知文本' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 CodePen 上，请参阅按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/XWbabLd/'>读取已知文本</a><a href='https://codepen.io'>的笔</a>。
</iframe>

以下代码演示了来回阅读和编写众所周知的文本。

<br/>

<iframe height='700' scrolling='no' title='阅读和写入知名文本' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 CodePen 上，请参阅按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（）<a href='https://codepen.io/azuremaps/pen/JjdyYav/'>读取和写入已知文本</a><a href='https://codepen.io'>的笔</a>。
</iframe>

## <a name="read-and-write-gml"></a>读取和写入 GML

GML 是一种空间 XML 文件规范，通常用作其他 XML 规范的扩展名。 使用 函数可以使用 GML 标记`atlas.io.core.GmlWriter.write`将 GeoJSON 数据编写为 XML。 可以使用 函数读取`atlas.io.core.GmlReader.read`包含 GML 的 XML。 读取函数有两个选项：

- 选项`isAxisOrderLonLat`- 坐标"纬度、经度"或"经度、纬度"的轴顺序可能因数据集而异，并且并不总是定义良好。 默认情况下，GML 读取器将坐标数据读取为"纬度、经度"，但将此选项设置为 true 会将其读取为"经度、纬度"。
- 选项`propertyTypes`- 此选项是键值查找表，其中键是数据集中属性的名称。 该值是分析时要将值转换为的对象类型。 支持的类型值是： `string`、、`boolean``date``number`和 。 如果属性不在查找表中或未定义类型，则该属性将作为字符串进行解析。

当`atlas.io.read``atlas.io.core.GmlReader.read`函数检测到输入数据是 XML 时，它将默认为函数，但数据不是其他支持空间 XML 格式之一。

## <a name="next-steps"></a>后续步骤

详细了解本文中使用的类和方法：

> [!div class="nextstepaction"]
> [atlas.io静态函数](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [空间数据集](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [空间数据集统计](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [格姆阅读器](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [格姆作家](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [阿特拉斯.io.ogc.WKT 功能](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [添加 OGC 地图图层](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [连接到 WFS 服务](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心业务](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支持的数据格式详细信息](spatial-io-supported-data-format-details.md)
