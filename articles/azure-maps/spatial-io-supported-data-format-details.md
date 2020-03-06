---
title: 支持的数据格式详细信息 |Microsoft Azure 映射
description: 了解如何在空间 IO 模块中分析分隔空间数据。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cbeb1ccaed6ec2c62aaa731e88fcefbe84d1df02
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370877"
---
# <a name="supported-data-format-details"></a>支持的数据格式详细信息

本文提供了对所有 XML 标记和熟知文本几何类型的读取和写入支持的详细信息。 它还详细说明了在空间 IO 模块中分析分隔空间数据的方式。

## <a name="supported-xml-namespaces"></a>支持的 XML 命名空间

空间 IO 模块支持以下命名空间中的 XML 标记。

| 命名空间前缀 | 命名空间 URI   | 注意                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS 文件中的只读支持。           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS 文件中的只读支持。                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | .GPX) 文件中的只读支持。 分析并使用 DisplayColor。 添加到形状元数据的所有其他属性。 |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | 在 .GPX) 文件中受支持。 使用线条颜色。 |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 只读。 GeoRSS 使用 Atom 格式写入。              |

## <a name="supported-xml-elements"></a>支持的 XML 元素

空间 IO 模块支持以下 XML 元素。 不受支持的任何 XML 标记将转换为 JSON 对象。 然后，每个标记将作为属性添加到父形状或层的 `properties` 字段中。

### <a name="kml-elements"></a>KML 元素

空间 IO 模块支持以下 KML 元素。

| 元素名称         | 读    | 写入   | 注意                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | partial — 部分 | 是     | 对象经过分析，但不用于定位形状。                                                                    |
| `AddressDetails`     | partial — 部分 | no      | 对象经过分析，但不用于定位形状。                                                                    |
| `atom:author`        | 是     | 是     |                                                                                                                            |
| `atom:link`          | 是     | 是     |                                                                                                                            |
| `atom:name`          | 是     | 是     |                                                                                                                            |
| `BalloonStyle`       | partial — 部分 | partial — 部分 | 不支持 `displayMode`。 转换为 `PopupTemplate`。 若要编写，请添加一个 `popupTemplate` 属性作为要为其编写的功能的属性。 |
| `begin`              | 是     | 是     |                                                                                                                            |
| `color`              | 是     | 是     | 包括 `#AABBGGRR` 和 `#BBGGRR`。 分析为 CSS 颜色字符串                                                           |
| `colorMode`          | 是     | no      |                                                                                                                            |
| `coordinates`        | 是     | 是     |                                                                                                                            |
| `Data`               | 是     | 是     |                                                                                                                            |
| `description`        | 是     | 是     |                                                                                                                            |
| `displayName`        | 是     | 是     |                                                                                                                            |
| `Document`           | 是     | 是     |                                                                                                                            |
| `drawOrder`          | partial — 部分 | no      | 阅读地面覆盖，并使用对其进行排序。 
| `east`               | 是     | 是     |                                                                                                                            |
| `end`                | 是     | 是     |                                                                                                                            |
| `ExtendedData`       | 是     | 是     | 支持非类型化 `Data`、`SimpleData` 或 `Schema`，以及窗体 `$[dataName]`的实体替换。                      |
| `extrude`            | partial — 部分 | partial — 部分 | 仅多边形支持。 具有不同高度的多边形的 MultiGeometry 将被分解为单独的功能。 不支持线条样式。 海拔高度为0的多边形将呈现为平面多边形。 在读取时，外部环中第一个坐标的海拔将作为多边形的 height 属性添加。 然后，第一个坐标的海拔将用于呈现地图上的多边形。 |
| `fill`               | 是     | 是     |                                                                                                                            |
| `Folder`             | 是     | 是     |                                                                                                                            |
| `GroundOverlay`      | 是     | 是     | 不支持 `color`                                                                                                   |
| `heading`            | partial — 部分 | no      | 分析但不是由 `SimpleDataLayer`呈现的。 仅当数据存储在形状的属性中时才写入。                 |
| `hotSpot`            | 是     | partial — 部分 | 仅当数据存储在形状的属性中时才写入。 仅将单元输出为 "像素"。                         |
| `href`               | 是     | 是     |                                                                                                                            |
| `Icon`               | partial — 部分 | partial — 部分 | 分析但不是由 `SimpleDataLayer`呈现的。 如果形状包含 URI 数据，则仅写入该形状的图标属性。 仅支持 `href`。 |
| `IconStyle`          | partial — 部分 | partial — 部分 | 解析 `icon`、`heading`、`colorMode`和 `hotspots` 值，但不会对其进行呈现 `SimpleDataLayer`         |
| `innerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `kml`                | 是     | 是     |                                                                                                                            |
| `LabelStyle`         | no      | no      |                                                                                                                            |
| `LatLonBox`          | 是     | 是     |                                                                                                                            |
| `gx:LatLonQuad`      | 是     | 是     |                                                                                                                            |
| `LinearRing`         | 是     | 是     |                                                                                                                            |
| `LineString`         | 是     | 是     |                                                                                                                            |
| `LineStyle`          | 是     | 是     | 不支持 `colorMode`。                                                                                         |
| `Link`               | 是     | no      | 网络链接只支持 `href` 属性。                                                                   |
| `MultiGeometry`      | partial — 部分 | partial — 部分 | 读取时可能会分解为单独的功能。                                                                     |
| `name`               | 是     | 是     |                                                                                                                            |
| `NetworkLink`        | 是     | no      | 链接需要位于文档所在的同一域中。                                                                  |
| `NetworkLinkControl` | no      | no      |                                                                                                                            |
| `north`              | 是     | 是     |                                                                                                                            |
| `open`               | 是     | 是     |                                                                                                                            |
| `outerBoundaryIs`    | 是     | 是     |                                                                                                                            |
| `outline`            | 是     | 是     |                                                                                                                            |
| `overlayXY`          | no      | no      |                                                                                                                            |
| `Pair`               | partial — 部分 | no      | 仅支持 `StyleMap` 中的 `normal` 样式。 不支持 `highlight`。                                   |
| `phoneNumber`        | 是     | 是     |                                                                                                                            |
| `PhotoOverlay`       | no      | no      |                                                                                                                            |
| `Placemark`          | 是     | 是     |                                                                                                                            |
| `Point`              | 是     | 是     |                                                                                                                            |
| `Polygon`            | 是     | 是     |                                                                                                                            |
| `PolyStyle`          | 是     | 是     |                                                                                                                            |
| `Region`             | partial — 部分 | partial — 部分 | 支持在文档级别 `LatLongBox`。                                                                      |
| `rotation`           | no      | no      |                                                                                                                            |
| `rotationXY`         | no      | no      |                                                                                                                            |
| `scale`              | no      | no      |                                                                                                                            |
| `Schema`             | 是     | 是     |                                                                                                                            |
| `SchemaData`         | 是     | 是     |                                                                                                                            |
| `schemaUrl`          | partial — 部分 | 是     | 不支持从不包含在 KMZ 中的外部文档加载样式。                             |
| `ScreenOverlay`      | no      | no      |                                                                                                                            |
| `screenXY`           | no      | no      |                                                                                                                            |
| `SimpleData`         | 是     | 是     |                                                                                                                            |
| `SimpleField`        | 是     | 是     |                                                                                                                            |
| `size`               | no      | no      |                                                                                                                            |
| `Snippet`            | partial — 部分 | partial — 部分 | 将忽略 `maxLines` 特性。                                                                                  |
| `south`              | 是     | 是     |                                                                                                                            |
| `Style`              | 是     | 是     |                                                                                                                            |
| `StyleMap`           | partial — 部分 | no      | 仅支持 `StyleMap` 中的 normal 样式。                                                                        |
| `styleUrl`           | partial — 部分 | 是     | 外部样式 Url 不受支持。                                                                         |
| `text`               | 是     | 是     | 不支持替换 `$[geDirections]`                                                                          |
| `textColor`          | 是     | 是     |                                                                                                                            |
| `TimeSpan`           | 是     | 是     |                                                                                                                            |
| `TimeStamp`          | 是     | 是     |                                                                                                                            |
| `value`              | 是     | 是     |                                                                                                                            |
| `viewRefreshMode`    | partial — 部分 | no      |  如果指向 WMS 服务，则基本覆盖仅支持 `onStop`。 将 `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` 追加到 URL，并在地图移动时进行更新。  |
| `visibility`         | 是     | 是     |                                                                                                                            |
| `west`               | 是     | 是     |                                                                                                                            |
| `when`               | 是     | 是     |                                                                                                                            |
| `width`              | 是     | 是     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 元素

空间 IO 模块支持以下 GeoRSS 元素。

| 元素名称             | 读    | 写入 | 注意                                                                                          |
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
| `geo:lat`                | 是     | no    | 编写为 `georss:point`。                                                                   |
| `geo:lon`                | 是     | no    | 编写为 `georss:point`。                                                                   |
| `geo:long`               | 是     | no    | 编写为 `georss:point`。                                                                   |
| `georss:box`             | 是     | no    | 作为多边形读取并给定一个 `subType` 属性 "矩形"                                |
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
| `geourl:latitude`        | 是     | no    | 编写为 `georss:point`。                                                                   |
| `geourl:longitude`       | 是     | no    | 编写为 `georss:point`。                                                                   |
| `position`               | 是     | no    | 某些 XML 源会将 GML 与位置标记一起打包，而不是使用 georss： where 标记来包装。 将读取此标记，但将使用 georss： where 标记写入。 |
| `rss`                    | 是     | no    | GeoRSS 以 ATOM 格式编写的。                                                                 |
| `rss:author`             | 是     | partial — 部分 | 编写为 `atom:author`。                                                                 |
| `rss:category`           | 是     | partial — 部分 | 编写为 `atom:category`。                                                               |
| `rss:channel`            | 是     | no    |                                                                                                |
| `rss:cloud`              | 是     | no    |                                                                                                |
| `rss:comments`           | 是     | no    |                                                                                                |
| `rss:copyright`          | 是     | partial — 部分 | 如果形状没有 `rights` `properties` 属性，则编写为 `atom:rights`。       |
| `rss:description`        | 是     | partial — 部分 | 如果形状没有 `content` `properties` 属性，则编写为 `atom:content`。      |
| `rss:docs`               | 是     | no    |                                                                                                |
| `rss:enclosure`          | 是     | no    |                                                                                                |
| `rss:generator`          | 是     | no    |                                                                                                |
| `rss:guid`               | 是     | partial — 部分 | 如果形状没有 `id` `properties` 属性，则编写为 `atom:id`。         |
| `rss:image`              | 是     | partial — 部分 | 如果形状没有 `logo` `properties` 属性，则编写为 `atom:logo`。      |
| `rss:item`               | 是     | partial — 部分 | 编写为 `atom:entry`。                                                                  |
| `rss:language`           | 是     | no    |                                                                                                |
| `rss:lastBuildDate`      | 是     | partial — 部分 | 如果形状没有 `updated` `properties` 属性，则编写为 `atom:updated`。     |
| `rss:link`               | 是     | partial — 部分 | 编写为 `atom:link`。                                                                   |
| `rss:managingEditor`     | 是     | partial — 部分 | 编写为 `atom:contributor`。                                                            |
| `rss:pubDate`            | 是     | partial — 部分 | 如果形状没有 `published` `properties` 属性，则编写为 `atom:published`。  |
| `rss:rating`             | 是     | no    |                                                                                                |
| `rss:skipDays`           | 是     | no    |                                                                                                |
| `rss:skipHours`          | 是     | no    |                                                                                                |
| `rss:source`             | 是     | partial — 部分 | 编写为包含 `atom:link`的 `atom:source`。                                       |
| `rss:textInput`          | 是     | no    |                                                                                                |
| `rss:title`              | 是     | partial — 部分 | 编写为 `atom:title`。                                                                  |
| `rss:ttl`                | 是     | no    |                                                                                                |
| `rss:webMaster`          | 是     | no    |                                                                                                |

### <a name="gml-elements"></a>GML 元素

空间 IO 模块支持以下 GML 元素。 

| 元素名称            | 读 | 写入 | 注意                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 是  | no    | 编写为 `gml:posList`。                                                              |
| `gml:curveMember`       | 是  | no    |                                                                                        |
| `gml:curveMembers`      | 是  | no    |                                                                                        |
| `gml:Box`               | 是  | no    | 编写为 `gml:Envelope`。                                                             |
| `gml:description`       | 是  | 是   |                                                                                        |
| `gml:Envelope`          | 是  | 是   |                                                                                        |
| `gml:exterior`          | 是  | 是   |                                                                                        |
| `gml:Feature`           | 是  | no    | 编写为形状。                                                                    |
| `gml:FeatureCollection` | 是  | no    | 编写为几何图形集合。                                                      |
| `gml:featureMember`     | 是  | no    | 编写为几何图形集合。                                                      |
| `gml:geometry`          | 是  | no    | 编写为形状。                                                                    |
| `gml:geometryMember`    | 是  | 是   |                                                                                        |
| `gml:geometryMembers`   | 是  | 是   |                                                                                        |
| `gml:identifier`        | 是  | 是   |                                                                                        |
| `gml:innerBoundaryIs`   | 是  | no    | 使用 `gml.interior`编写的。                                                          |
| `gml:interior`          | 是  | 是   |                                                                                        |
| `gml:LinearRing`        | 是  | 是   |                                                                                        |
| `gml:LineString`        | 是  | 是   |                                                                                        |
| `gml:lineStringMember`  | 是  | 是   |                                                                                        |
| `gml:lineStringMembers` | 是  | no    |                                                                                        |
| `gml:MultiCurve`        | 是  | no    | 仅读取 `gml:LineString` 的成员。 编写为 `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | partial — 部分  | partial — 部分   | 仅读取 FeatureCollection。                                              |
| `gml:MultiLineString`   | 是  | 是   |                                                                                        |
| `gml:MultiPoint`        | 是  | 是   |                                                                                        |
| `gml:MultiPolygon`      | 是  | 是   |                                                                                        |
| `gml:MultiSurface`      | 是  | no    | 仅读取 `gml:Polygon` 的成员。 编写为 `gml.MultiPolygon`                        |
| `gml:name`              | 是  | 是   |                                                                                        |
| `gml:outerBoundaryIs`   | 是  | no    | 使用 `gml.exterior`编写的。                                                          |
| `gml:Point`             | 是  | 是   |                                                                                        |
| `gml:pointMember`       | 是  | 是   |                                                                                        |
| `gml:pointMembers`      | 是  | no    |                                                                                        |
| `gml:Polygon`           | 是  | 是   |                                                                                        |
| `gml:polygonMember`     | 是  | 是   |                                                                                        |
| `gml:polygonMembers`    | 是  | no    |                                                                                        |
| `gml:pos`               | 是  | 是   |                                                                                        |
| `gml:posList`           | 是  | 是   |                                                                                        |
| `gml:surfaceMember`     | 是  | 是   |                                                                                        |

#### <a name="additional-notes"></a>其他说明

- 将搜索成员元素，查找可能在子元素中隐藏的几何图形。 此搜索操作是必需的，因为从 GML 扩展的 XML 格式不能将几何图形作为成员元素的直接子级。
- WGS84 坐标部分支持 `srsName`，以下代码：[EPSG： 4326](https://epsg.io/4326)）、web Mercator （[EPSG： 3857](https://epsg.io/3857)或其替代代码之一。 任何其他坐标系统都将按原样分析为 WGS84。
- 除非在读取 XML 源时指定，否则，将根据 XML 源中的提示来确定轴顺序。 为 "纬度，经度" 轴顺序提供了一个首选项。
- 除非在写入 GML 文件时为属性指定了自定义 GML 命名空间，否则将不会添加其他属性信息。

### <a name="gpx-elements"></a>.GPX) 元素

空间 IO 模块支持以下 .GPX) 元素。

| 元素名称             | 读    | 写入   | 注意                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 是     | 是     |                                                                                             |
| `gpx:author`             | 是     | 是     |                                                                                             |
| `gpx:bounds`             | 是     | 是     | 读取时转换为 LocationRect。                                                    |
| `gpx:cmt`                | 是     | 是     |                                                                                             |
| `gpx:copyright`          | 是     | 是     |                                                                                             |
| `gpx:desc`               | 是     | 是     | 在读取时复制到 description 属性以与其他 XML 格式对齐。               |
| `gpx:dgpsid`             | 是     | 是     |                                                                                             |
| `gpx:ele`                | 是     | 是     |                                                                                             |
| `gpx:extensions`         | partial — 部分 | partial — 部分 | 读取时，将提取样式信息。 所有其他扩展将平展为一个简单的 JSON 对象。 仅写入形状样式信息。 |
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
| `gpx:sym`                | 是     | 是     | 捕获值，但不用于更改图钉图标。                               |
| `gpx:text`               | 是     | 是     |                                                                                             |
| `gpx:time`               | 是     | 是     |                                                                                             |
| `gpx:trk`                | 是     | 是     |                                                                                             |
| `gpx:trkpt`              | 是     | 是     |                                                                                             |
| `gpx:trkseg`             | 是     | 是     |                                                                                             |
| `gpx:type`               | 是     | 是     |                                                                                             |
| `gpx:vdop`               | 是     | 是     |                                                                                             |
| `gpx:wpt`                | 是     | 是     |                                                                                             |
| `gpx_style:color`        | 是     | 是     |                                                                                             |
| `gpx_style:line`         | partial — 部分 | partial — 部分 | 支持 `color`、`opacity`、`width``lineCap`。                                           |
| `gpx_style:opacity`      | 是     | 是     |                                                                                             |
| `gpx_style:width`        | 是     | 是     |                                                                                             |
| `gpxx:DisplayColor`      | 是     | no      | 用于指定形状的颜色。 写入时，将改为使用 `gpx_style:line` 颜色。  |
| `gpxx:RouteExtension`    | partial — 部分 | no      | 所有属性都读入 `properties`。 仅使用 `DisplayColor`。                     |
| `gpxx:TrackExtension`    | partial — 部分 | no      | 所有属性都读入 `properties`。 仅使用 `DisplayColor`。                     |
| `gpxx:WaypointExtension` | partial — 部分 | no      | 所有属性都读入 `properties`。 仅使用 `DisplayColor`。                     |
| `gpx:keywords`           | 是     | 是     |                                                                                             |
| `gpx:fix`                | 是     | 是     |                                                                                             |

#### <a name="additional-notes"></a>其他说明

写入时;

- MultiPoints 将分解为单个 waypoints。
- 多边形和 MultiPolygons 将以磁道的形式写入。 
  
## <a name="supported-well-known-text-geometry-types"></a>支持的已知文本几何类型

| Geometry 类型 | 读 | 写入 |
|--------------|:----:|:-----:|
| 点 | x | x |
| 点 Z | x | x | 
| 点 M | x | x<sup>[2]</sup> |
| 点 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 各 | x | x |
| 多边形 Z | x | x |
| 多边形 M | x | x<sup>[2]</sup> |
| 多边形 ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| 单 | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1\] 只捕获 Z 参数，并将其作为第三个值添加到位置值中。

不捕获 \[2\] M 参数。

## <a name="delimited-spatial-data-support"></a>分隔空间数据支持

分隔空间数据（如逗号分隔值文件（CSV））通常具有包含空间数据的列。 例如，可能存在包含纬度和经度信息的列。 在已知文本格式中，可能有包含空间几何数据的列。

### <a name="spatial-data-column-detection"></a>空间数据列检测

在读取包含空间数据的带分隔符的文件时，将分析该标头以确定哪些列包含位置字段。 如果标头包含类型信息，则它将用于将单元值强制转换为适当的类型。 如果未指定标头，则将分析第一行并将其用于生成标头。 分析第一行时，将执行检查，以将具有以下名称的列名称与不区分大小写的方法匹配。 如果文件中有两个或多个名称，则名称的顺序就是优先级。

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

系统将扫描第一行数据中的字符串，以查找采用已知文本格式的字符串。 

### <a name="delimited-data-column-types"></a>分隔的数据列类型

在扫描标题行时，将提取列名中的任何类型信息，并将其用于转换列中的单元格。 下面是一个类型值为 "ColumnName （typeName）" 的列名称的示例。 支持以下不区分大小写的类型名称：

**小数**

    - edm
    - int
    - long
    - edm。 double
    - float
    - double
    - number

**布尔**

    - edm
    - bool
    - boolean

**截止**

    - edm. datetime
    - date
    - datetime

**地域**

    - edm
    - geography

**Strings**

    - edm
    - varchar
    - text
    - case "string

如果没有可以从标头中提取类型信息，并且在读取时启用了动态键入选项，则会对每个单元格进行单独分析以确定最适合转换为的数据类型。

## <a name="next-steps"></a>后续步骤

有关可向地图添加的更多代码示例，请参阅以下文章：

> [!div class="nextstepaction"]
> [读取和写入空间数据](spatial-io-read-write-spatial-data.md)
