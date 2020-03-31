---
title: 支持的数据格式详细信息 |微软 Azure 地图
description: 了解如何在空间 IO 模块中解析空间数据。
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3353620f1751e939a04543115fe704555fb3bc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334082"
---
# <a name="supported-data-format-details"></a>支持的数据格式详细信息

本文详细介绍了对所有 XML 标记和已知文本几何类型的读写支持。 它还详细介绍了如何在空间 IO 模块中分析分隔的空间数据。

## <a name="supported-xml-namespaces"></a>支持的 XML 命名空间

空间 IO 模块支持来自以下命名空间的 XML 标记。

| 命名空间前缀 | 命名空间 URI   | 说明                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | 只读取 GeoRSS 文件中的支持。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | 只读取 GeoRSS 文件中的支持。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | 只读取 GPX 文件中的支持。 分析并使用显示颜色。 添加到形状元数据的所有其他属性。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | GPX 文件中支持。 使用线颜色。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 只读。 使用 Atom 格式写入 GeoRSS。              |

## <a name="supported-xml-elements"></a>支持的 XML 元素

空间 IO 模块支持以下 XML 元素。 不支持的任何 XML 标记都将转换为 JSON 对象。 然后，每个标记将添加为`properties`父形状或图层字段中的属性。

### <a name="kml-elements"></a>KML 元件

空间 IO 模块支持以下 KML 元素。

| 元素名称         | 读取    | 写入   | 说明                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | 部分 | 是     | 对象已解析，但不用于定位形状。                                                                    |
| `AddressDetails`     | 部分 | 否      | 对象已解析，但不用于定位形状。                                                                    |
| `atom:author`        | 是     | 是     |                                                                                                                            |
| `atom:link`          | 是     | 是     |                                                                                                                            |
| `atom:name`          | 是     | 是     |                                                                                                                            |
| `BalloonStyle`       | 部分 | 部分 | 不支持 `displayMode`。 转换为 。 `PopupTemplate` 要编写，请添加`popupTemplate`属性作为要为其编写的要素的属性。 |
| `begin`              | 是     | 是     |                                                                                                                            |
| `color`              | 是     | 是     | 包括`#AABBGGRR``#BBGGRR`和 。 解析为 CSS 颜色字符串                                                           |
| `colorMode`          | 是     | 否      |                                                                                                                            |
| `coordinates`        | 是     | 是     |                                                                                                                            |
| `Data`               | 是     | 是     |                                                                                                                            |
| `description`        | 是     | 是     |                                                                                                                            |
| `displayName`        | 是     | 是     |                                                                                                                            |
| `Document`           | 是     | 是     |                                                                                                                            |
| `drawOrder`          | 部分 | 否      | 读取地面叠加并用于对其进行排序。 
| `east`               | 是     | 是     |                                                                                                                            |
| `end`                | 是     | 是     |                                                                                                                            |
| `ExtendedData`       | 是     | 是     | 支持窗体 的`Data`未`SimpleData`键入`Schema`或 和实体替换`$[dataName]`。                      |
| `extrude`            | 部分 | 部分 | 仅支持面。 具有不同高度的多边形的多几何体将分解为单个要素。 不支持行样式。 高度为 0 的多边形将呈现为平面多边形。 读取时，外部环中第一个坐标的高度将添加为多边形的高度属性。 然后，第一个坐标的高度将用于渲染地图上的多边形。 |
| `fill`               | 是     | 是     |                                                                                                                            |
| `Folder`             | 是     | 是     |                                                                                                                            |
| `GroundOverlay`      | 是     | 是     | `color`不受支持                                                                                                   |
| `heading`            | 部分 | 否      | 已分析但未由`SimpleDataLayer`呈现。 仅当数据存储在形状的属性中时，才写入。                 |
| `hotSpot`            | 是     | 部分 | 仅当数据存储在形状的属性中时，才写入。 单位仅输出为"像素"。                         |
| `href`               | 是     | 是     |                                                                                                                            |
| `Icon`               | 部分 | 部分 | 已分析但未由`SimpleDataLayer`呈现。 仅当形状包含 URI 数据时，才写入形状的图标属性。 仅支持 `href`。 |
| `IconStyle`          | 部分 | 部分 | `icon``heading`、、`colorMode`和`hotspots`值进行解析，但它们不由`SimpleDataLayer`         |
| `innerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `kml`                | 是     | 是     |                                                                                                                            |
| `LabelStyle`         | 否      | 否      |                                                                                                                            |
| `LatLonBox`          | 是     | 是     |                                                                                                                            |
| `gx:LatLonQuad`      | 是     | 是     |                                                                                                                            |
| `LinearRing`         | 是     | 是     |                                                                                                                            |
| `LineString`         | 是     | 是     |                                                                                                                            |
| `LineStyle`          | 是     | 是     | 不支持 `colorMode`。                                                                                         |
| `Link`               | 是     | 否      | 网络链接`href`仅支持该属性。                                                                   |
| `MultiGeometry`      | 部分 | 部分 | 读取时可能会分解为单个功能。                                                                     |
| `name`               | 是     | 是     |                                                                                                                            |
| `NetworkLink`        | 是     | 否      | 链接必须与文档位于同一域上。                                                                  |
| `NetworkLinkControl` | 否      | 否      |                                                                                                                            |
| `north`              | 是     | 是     |                                                                                                                            |
| `open`               | 是     | 是     |                                                                                                                            |
| `outerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `outline`            | 是     | 是     |                                                                                                                            |
| `overlayXY`          | 否      | 否      |                                                                                                                            |
| `Pair`               | 部分 | 否      | 仅支持`normal`中的`StyleMap`样式。 不支持 `highlight`。                                   |
| `phoneNumber`        | 是     | 是     |                                                                                                                            |
| `PhotoOverlay`       | 否      | 否      |                                                                                                                            |
| `Placemark`          | 是     | 是     |                                                                                                                            |
| `Point`              | 是     | 是     |                                                                                                                            |
| `Polygon`            | 是     | 是     |                                                                                                                            |
| `PolyStyle`          | 是     | 是     |                                                                                                                            |
| `Region`             | 部分 | 部分 | `LatLongBox`在文档级别支持。                                                                      |
| `rotation`           | 否      | 否      |                                                                                                                            |
| `rotationXY`         | 否      | 否      |                                                                                                                            |
| `scale`              | 否      | 否      |                                                                                                                            |
| `Schema`             | 是     | 是     |                                                                                                                            |
| `SchemaData`         | 是     | 是     |                                                                                                                            |
| `schemaUrl`          | 部分 | 是     | 不支持从 KMZ 中未包括的外部文档加载样式。                             |
| `ScreenOverlay`      | 否      | 否      |                                                                                                                            |
| `screenXY`           | 否      | 否      |                                                                                                                            |
| `SimpleData`         | 是     | 是     |                                                                                                                            |
| `SimpleField`        | 是     | 是     |                                                                                                                            |
| `size`               | 否      | 否      |                                                                                                                            |
| `Snippet`            | 部分 | 部分 | `maxLines`属性将被忽略。                                                                                  |
| `south`              | 是     | 是     |                                                                                                                            |
| `Style`              | 是     | 是     |                                                                                                                            |
| `StyleMap`           | 部分 | 否      | 仅支持 中`StyleMap`的正常样式。                                                                        |
| `styleUrl`           | 部分 | 是     | 不支持外部样式 URL。                                                                         |
| `text`               | 是     | 是     | 不支持替换`$[geDirections]`                                                                          |
| `textColor`          | 是     | 是     |                                                                                                                            |
| `TimeSpan`           | 是     | 是     |                                                                                                                            |
| `TimeStamp`          | 是     | 是     |                                                                                                                            |
| `value`              | 是     | 是     |                                                                                                                            |
| `viewRefreshMode`    | 部分 | 否      |  如果指向 WMS 服务，则仅`onStop`支持接地覆盖。 将追加`BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]`到 URL 并随着地图移动而更新。  |
| `visibility`         | 是     | 是     |                                                                                                                            |
| `west`               | 是     | 是     |                                                                                                                            |
| `when`               | 是     | 是     |                                                                                                                            |
| `width`              | 是     | 是     |                                                                                                                            |

### <a name="georss-elements"></a>地理RSS元素

空间 IO 模块支持以下地理 RSS 元素。

| 元素名称             | 读取    | 写入 | 说明                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | 是     | 是   |                                                                                                |
| `atom:category`          | 是     | 是   |                                                                                                |
| `atom:content`           | 是     | 是   |                                                                                                |
| `atom:contributor`       | 是     | 是   |                                                                                                |
| `atom:email`             | 是     | 是   |                                                                                                |
| `atom:entry`             | 是     | 是   |                                                                                                |
| `atom:feed`              | 是     | 是   |                                                                                                |
| `atom:icon`              | 是     | 是   |                                                                                                |
| `atom:id`                | 是     | 是   |                                                                                                |
| `atom:link`              | 是     | 是   |                                                                                                |
| `atom:logo`              | 是     | 是   |                                                                                                |
| `atom:name`              | 是     | 是   |                                                                                                |
| `atom:published`         | 是     | 是   |                                                                                                |
| `atom:rights`            | 是     | 是   |                                                                                                |
| `atom:source`            | 是     | 是   |                                                                                                |
| `atom:subtitle`          | 是     | 是   |                                                                                                |
| `atom:summary`           | 是     | 是   |                                                                                                |
| `atom:title`             | 是     | 是   |                                                                                                |
| `atom:updated`           | 是     | 是   |                                                                                                |
| `atom:uri`               | 是     | 是   |                                                                                                |
| `geo:lat`                | 是     | 否    | 写成 . `georss:point`                                                                   |
| `geo:lon`                | 是     | 否    | 写成 . `georss:point`                                                                   |
| `geo:long`               | 是     | 否    | 写成 . `georss:point`                                                                   |
| `georss:box`             | 是     | 否    | 作为多边形读取，`subType`并给出"矩形"的属性                                |
| `georss:circle`          | 是     | 是   |                                                                                                |
| `georss:elev`            | 是     | 是   |                                                                                                |
| `georss:featurename`     | 是     | 是   |                                                                                                |
| `georss:featuretypetag`  | 是     | 是   |                                                                                                |
| `georss:floor`           | 是     | 是   |                                                                                                |
| `georss:line`            | 是     | 是   |                                                                                                |
| `georss:point`           | 是     | 是   |                                                                                                |
| `georss:polygon`         | 是     | 是   |                                                                                                |
| `georss:radius`          | 是     | 是   |                                                                                                |
| `georss:relationshiptag` | 是     | 是   |                                                                                                |
| `georss:where`           | 是     | 是   |                                                                                                |
| `geourl:latitude`        | 是     | 否    | 写成 . `georss:point`                                                                   |
| `geourl:longitude`       | 是     | 否    | 写成 . `georss:point`                                                                   |
| `position`               | 是     | 否    | 某些 XML 源将用位置标记包装 GML，而不是用`georss:where`标记包装它。 将读取此标记，但将使用`georss:where`标记写入。 |
| `rss`                    | 是     | 否    | 以 ATOM 格式编写的地理RSS。                                                                 |
| `rss:author`             | 是     | 部分 | 写成 . `atom:author`                                                                 |
| `rss:category`           | 是     | 部分 | 写成 . `atom:category`                                                               |
| `rss:channel`            | 是     | 否    |                                                                                                |
| `rss:cloud`              | 是     | 否    |                                                                                                |
| `rss:comments`           | 是     | 否    |                                                                                                |
| `rss:copyright`          | 是     | 部分 | 编写为`atom:rights`if 形状已没有`rights``properties`属性。       |
| `rss:description`        | 是     | 部分 | 编写为`atom:content`if 形状已没有`content``properties`属性。      |
| `rss:docs`               | 是     | 否    |                                                                                                |
| `rss:enclosure`          | 是     | 否    |                                                                                                |
| `rss:generator`          | 是     | 否    |                                                                                                |
| `rss:guid`               | 是     | 部分 | 编写为`atom:id`if 形状已没有`id``properties`属性。         |
| `rss:image`              | 是     | 部分 | 编写为`atom:logo`if 形状已没有`logo``properties`属性。      |
| `rss:item`               | 是     | 部分 | 写成 . `atom:entry`                                                                  |
| `rss:language`           | 是     | 否    |                                                                                                |
| `rss:lastBuildDate`      | 是     | 部分 | 编写为`atom:updated`if 形状已没有`updated``properties`属性。     |
| `rss:link`               | 是     | 部分 | 写成 . `atom:link`                                                                   |
| `rss:managingEditor`     | 是     | 部分 | 写成 . `atom:contributor`                                                            |
| `rss:pubDate`            | 是     | 部分 | 编写为`atom:published`if 形状已没有`published``properties`属性。  |
| `rss:rating`             | 是     | 否    |                                                                                                |
| `rss:skipDays`           | 是     | 否    |                                                                                                |
| `rss:skipHours`          | 是     | 否    |                                                                                                |
| `rss:source`             | 是     | 部分 | 写成包含`atom:source`的`atom:link`。                                       |
| `rss:textInput`          | 是     | 否    |                                                                                                |
| `rss:title`              | 是     | 部分 | 写成 . `atom:title`                                                                  |
| `rss:ttl`                | 是     | 否    |                                                                                                |
| `rss:webMaster`          | 是     | 否    |                                                                                                |

### <a name="gml-elements"></a>GML 元素

空间 IO 模块支持以下 GML 元素。 

| 元素名称            | 读取 | 写入 | 说明                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 是  | 否    | 写成`gml:posList`.                                                              |
| `gml:curveMember`       | 是  | 否    |                                                                                        |
| `gml:curveMembers`      | 是  | 否    |                                                                                        |
| `gml:Box`               | 是  | 否    | 写成`gml:Envelope`.                                                             |
| `gml:description`       | 是  | 是   |                                                                                        |
| `gml:Envelope`          | 是  | 是   |                                                                                        |
| `gml:exterior`          | 是  | 是   |                                                                                        |
| `gml:Feature`           | 是  | 否    | 写成形状。                                                                    |
| `gml:FeatureCollection` | 是  | 否    | 编写为几何集合。                                                      |
| `gml:featureMember`     | 是  | 否    | 编写为几何集合。                                                      |
| `gml:geometry`          | 是  | 否    | 写成形状。                                                                    |
| `gml:geometryMember`    | 是  | 是   |                                                                                        |
| `gml:geometryMembers`   | 是  | 是   |                                                                                        |
| `gml:identifier`        | 是  | 是   |                                                                                        |
| `gml:innerBoundaryIs`   | 是  | 否    | 使用`gml.interior`编写。                                                          |
| `gml:interior`          | 是  | 是   |                                                                                        |
| `gml:LinearRing`        | 是  | 是   |                                                                                        |
| `gml:LineString`        | 是  | 是   |                                                                                        |
| `gml:lineStringMember`  | 是  | 是   |                                                                                        |
| `gml:lineStringMembers` | 是  | 否    |                                                                                        |
| `gml:MultiCurve`        | 是  | 否    | 只读取`gml:LineString`成员。 写为`gml.MultiLineString`                  |
| `gml:MultiGeometry`     | 部分  | 部分   | 仅作为功能集合读取。                                              |
| `gml:MultiLineString`   | 是  | 是   |                                                                                        |
| `gml:MultiPoint`        | 是  | 是   |                                                                                        |
| `gml:MultiPolygon`      | 是  | 是   |                                                                                        |
| `gml:MultiSurface`      | 是  | 否    | 只读取`gml:Polygon`成员。 写为`gml.MultiPolygon`                        |
| `gml:name`              | 是  | 是   |                                                                                        |
| `gml:outerBoundaryIs`   | 是  | 否    | 使用`gml.exterior`编写。                                                          |
| `gml:Point`             | 是  | 是   |                                                                                        |
| `gml:pointMember`       | 是  | 是   |                                                                                        |
| `gml:pointMembers`      | 是  | 否    |                                                                                        |
| `gml:Polygon`           | 是  | 是   |                                                                                        |
| `gml:polygonMember`     | 是  | 是   |                                                                                        |
| `gml:polygonMembers`    | 是  | 否    |                                                                                        |
| `gml:pos`               | 是  | 是   |                                                                                        |
| `gml:posList`           | 是  | 是   |                                                                                        |
| `gml:surfaceMember`     | 是  | 是   |                                                                                        |

#### <a name="additional-notes"></a>附加说明

- 将搜索成员元素，查找可能埋在子元素中的几何体。 此搜索操作是必需的，因为许多从 GML 扩展的 XML 格式可能不会将几何体作为成员元素的直接子级放置。
- `srsName`WGS84 坐标和以下代码部分支持[：EPSG：4326](https://epsg.io/4326)）和 Web Mercator[（EPSG：3857](https://epsg.io/3857)或其替代代码之一）。 任何其他坐标系将解析为 WGS84。
- 除非在读取 XML 源时指定，否则轴顺序将根据 XML 源中的提示确定。 对"纬度、经度"轴顺序给予首选项。
- 除非在写入 GML 文件时为属性指定了自定义 GML 命名空间，否则不会添加其他属性信息。

### <a name="gpx-elements"></a>GPX 元素

空间 IO 模块支持以下 GPX 元素。

| 元素名称             | 读取    | 写入   | 说明                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 是     | 是     |                                                                                             |
| `gpx:author`             | 是     | 是     |                                                                                             |
| `gpx:bounds`             | 是     | 是     | 读取时转换为位置重新使用。                                                    |
| `gpx:cmt`                | 是     | 是     |                                                                                             |
| `gpx:copyright`          | 是     | 是     |                                                                                             |
| `gpx:desc`               | 是     | 是     | 读取时复制到描述属性以与其他 XML 格式对齐。               |
| `gpx:dgpsid`             | 是     | 是     |                                                                                             |
| `gpx:ele`                | 是     | 是     |                                                                                             |
| `gpx:extensions`         | 部分 | 部分 | 读取时，将提取样式信息。 所有其他扩展都将拼合成一个简单的 JSON 对象。 只写入形状样式信息。 |
| `gpx:geoidheight`        | 是     | 是     |                                                                                             |
| `gpx:gpx`                | 是     | 是     |                                                                                             |
| `gpx:hdop`               | 是     | 是     |                                                                                             |
| `gpx:link`               | 是     | 是     |                                                                                             |
| `gpx:magvar`             | 是     | 是     |                                                                                             |
| `gpx:metadata`           | 是     | 是     |                                                                                             |
| `gpx:name`               | 是     | 是     |                                                                                             |
| `gpx:pdop`               | 是     | 是     |                                                                                             |
| `gpx:rte`                | 是     | 是     |                                                                                             |
| `gpx:rtept`              | 是     | 是     |                                                                                             |
| `gpx:sat`                | 是     | 是     |                                                                                             |
| `gpx:src`                | 是     | 是     |                                                                                             |
| `gpx:sym`                | 是     | 是     | 值被捕获，但它不用于更改图钉图标。                               |
| `gpx:text`               | 是     | 是     |                                                                                             |
| `gpx:time`               | 是     | 是     |                                                                                             |
| `gpx:trk`                | 是     | 是     |                                                                                             |
| `gpx:trkpt`              | 是     | 是     |                                                                                             |
| `gpx:trkseg`             | 是     | 是     |                                                                                             |
| `gpx:type`               | 是     | 是     |                                                                                             |
| `gpx:vdop`               | 是     | 是     |                                                                                             |
| `gpx:wpt`                | 是     | 是     |                                                                                             |
| `gpx_style:color`        | 是     | 是     |                                                                                             |
| `gpx_style:line`         | 部分 | 部分 | `color``opacity` `width` `lineCap`                                           |
| `gpx_style:opacity`      | 是     | 是     |                                                                                             |
| `gpx_style:width`        | 是     | 是     |                                                                                             |
| `gpxx:DisplayColor`      | 是     | 否      | 用于指定形状的颜色。 书写时，`gpx_style:line`将改为使用颜色。  |
| `gpxx:RouteExtension`    | 部分 | 否      | 所有属性都读入`properties`。 仅使用 `DisplayColor`。                     |
| `gpxx:TrackExtension`    | 部分 | 否      | 所有属性都读入`properties`。 仅使用 `DisplayColor`。                     |
| `gpxx:WaypointExtension` | 部分 | 否      | 所有属性都读入`properties`。 仅使用 `DisplayColor`。                     |
| `gpx:keywords`           | 是     | 是     |                                                                                             |
| `gpx:fix`                | 是     | 是     |                                                                                             |

#### <a name="additional-notes"></a>附加说明

写作时;

- 多点将分为单独的航点。
- 多边形和多多边形将写入轨道。 
  
## <a name="supported-well-known-text-geometry-types"></a>支持的已知文本几何类型

| 几何类型 | 读取 | 写入 |
|--------------|:----:|:-----:|
| 点 | x | x |
| 点 Z | x | x | 
| 点 M | x | x<sup>[2]</sup> |
| 点 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 线串 | x | x |
| 线林斯林茨 | x | x | 
| 线林斯林 M | x | x<sup>[2]</sup> |
| 线林茨林兹姆 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多边形 | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 点 | x | x |
| 多点 Z | x | x | 
| 多点 M | x | x<sup>[2]</sup> |
| 波多点丁茨姆 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多线字符串 | x | x |
| 多林斯林茨茨 | x | x | 
| 多林斯特林格 M | x | x<sup>[2]</sup> |
| 多林斯林茨 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 多多隆 | x | x |
| 多波利贡 Z | x | x | 
| 多波利贡 M | x | x<sup>[2]</sup> |
| 多波利贡兹姆 | x<sup>[1]</sup><sup>[2]</sup> | | 
| 几何 | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\]在"位置"值中仅捕获 Z 参数并作为第三个值添加。

\[2\] M 参数未捕获。

## <a name="delimited-spatial-data-support"></a>空间数据有限

分隔空间数据（如逗号分隔的值文件 （CSV））通常具有包含空间数据的列。 例如，可能有包含纬度和经度信息的列。 在"已知文本"格式中，可能有一个列包含空间几何数据。

### <a name="spatial-data-column-detection"></a>空间数据列检测

读取包含空间数据的分隔文件时，将分析标头以确定哪些列包含位置字段。 如果标头包含类型信息，则它将用于将单元格值转换为适当的类型。 如果未指定标头，则将分析第一行并用于生成标头。 分析第一行时，将执行一项检查，以不区分大小写的方式将列名称与以下名称匹配。 名称的顺序是优先级，如果文件中存在两个或多个名称。

#### <a name="latitude"></a>纬度

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>经度

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Elevation

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>地理位置

第一行数据将扫描为已知文本格式的字符串。 

### <a name="delimited-data-column-types"></a>分隔的数据列类型

扫描标题行时，将提取列名称中的任何类型信息，并用于在该行中强制转换单元格。 下面是具有类型值的列名称的示例："列名（类型名称）"。 支持以下不区分大小写的类型名称：

#### <a name="numbers"></a>数字

- edm.int64
- int
- long
- edm.double
- FLOAT
- double
- 数字

#### <a name="booleans"></a>布尔型

- 埃德姆.布尔
- bool
- boolean

#### <a name="dates"></a>日期

- edm.日期时间
- date
- datetime

#### <a name="geography"></a>地理位置

- edm.地理
- geography

#### <a name="strings"></a>字符串

- edm.字符串
- varchar
- text
- 字符串

如果无法从标头中提取类型信息，并且读取时启用了动态键入选项，则将单独分析每个单元格以确定最适合将其转换为哪种数据类型。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)
