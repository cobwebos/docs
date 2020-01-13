---
title: 从 Google Maps 迁移 web 服务 |Microsoft Docs
description: 本教程介绍如何将 web 服务从 Google Maps 迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480016"
---
# <a name="migrate-web-service-from-google-maps"></a>从 Google Maps 迁移 web 服务

Azure 和 Google Maps 通过 REST web 服务提供对空间 Api 的访问。 这些平台的 API 接口执行类似的功能，但使用不同的命名约定和响应对象。

下表提供了 Azure Maps 服务 Api，这些 Api 为列出的 Google Maps 服务 Api 提供类似的功能。

| Google Maps 服务 API | Azure Maps 服务 API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 方向              | Route[](https://docs.microsoft.com/rest/api/maps/route)                               |
| 距离矩阵         | [路由矩阵](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| 地理编码               | [搜索](https://docs.microsoft.com/rest/api/maps/search)                             |
| 位置搜索           | [搜索](https://docs.microsoft.com/rest/api/maps/search)                             |
| 放置自动完成      | [搜索](https://docs.microsoft.com/rest/api/maps/search)                             |
| 静态映射              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| 时区               | [时区](https://docs.microsoft.com/rest/api/maps/timezone)                        |

以下服务 Api 当前在 Azure Maps 中不可用：

- Elevation
- 地理位置
- 放置详细信息和照片。 Azure Maps 搜索 API 中可用的电话号码和网站 URL。
- 映射 Url
- 通过 Azure Maps 中的路由和反向地理编码 Api 可以使用公路-速度限制数据。
- 静态街道视图

Azure Maps 具有几个可能会受到关注的其他 REST web 服务：

- [空间操作](https://docs.microsoft.com/rest/api/maps/spatial)：将复杂的空间计算和操作（如地理围栏）卸载到服务。
- [流量](https://docs.microsoft.com/rest/api/maps/traffic)：访问实时流量流和事件数据。

## <a name="geocoding-addresses"></a>地理编码地址

地理编码是将地址（如 "1 Microsoft 方式，Redmond，WA"）转换为坐标（如经度：-122.1298、纬度：47.64005）的过程。 然后，坐标通常用于在地图或中心地图上放置标记。

Azure Maps 提供了用于地理编码地址的多种方法：

- [**自由格式的 address 地理编码**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定单个地址字符串（如 "1 Microsoft 方法，REDMOND，WA"）并立即处理请求。 如果需要快速地理编码各个地址，建议使用此建议。
- [**结构化地址地理编码**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定单个地址的各个部分，例如街道名称、城市、国家/地区和邮政编码，并立即处理请求。 如果需要快速地理编码各个地址，并且数据已分析到其单独的地址部分，则建议这样做。
- [**批处理地址地理编码**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：创建包含最多10000地址的请求，并在一段时间内处理它们。 所有地址将在服务器上并行地理编码，完成后，可以下载完整的结果集。 对于地理编码大型数据集，建议使用此设置。
- [**模糊搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串，该字符串可以是地址、位置、路标、感兴趣点或兴趣点类别，并立即处理请求。 对于用户可以在同一文本框中搜索感兴趣的地址或点的应用程序，建议使用此 API。
- [**模糊批处理搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多10000个地址、位置、特征点或兴趣点的请求，并在一段时间内处理它们。 所有数据将在服务器上并行处理，完成后，可以下载完整的结果集。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数 | 可比较 Azure Maps API 参数  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 和 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`-市/镇<br/>`municipalitySubdivision` –邻居，子/超级城市<br/>`countrySubdivision`-省或直辖市<br/>`countrySecondarySubdivision`-县<br/>`countryTertiarySubdivision`-地区<br/>`countryCode`-两个字母的国家/地区代码 |
| `key`                       | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `language`                  | `language` –请参阅[支持的语言](supported-languages.md)文档。  |
| `region`                    | `countrySet`                       |

[此处](how-to-search-for-address.md)介绍了如何使用搜索服务的示例。 务必查看搜索文档的[最佳实践](how-to-use-best-practices-for-search.md)。

> [!TIP]
> 可以通过将 `&amp;typeahead=true` 添加到请求 URL 来在自动完成模式下使用自由格式的地址地理编码和模糊搜索 Api。 这将告知服务器输入文本可能是部分内容，并将进入预测模式。

## <a name="reverse-geocode-a-coordinate"></a>反向地理编码坐标

反向地理编码是将地理坐标（如经度：-122.1298，纬度：47.64005）转换为其近似地址（如 "1 Microsoft 方法，Redmond，WA"）的过程。

Azure Maps 提供多种反向地理编码方法：

- [**Address 反向 geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定单个地理坐标以获取其大致地址并立即处理请求。
- [**交叉街道反向 geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定单个地理坐标以获取附近的交叉街道信息（例如，第一个 & main）并立即处理请求。
- [**批处理地址反向 geocoder**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：创建包含最多10000坐标的请求，并在一段时间内处理它们。 所有数据将在服务器上并行处理，完成后，可以下载完整的结果集。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数   | 可比较 Azure Maps API 参数   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `language`                  | `language` –请参阅[支持的语言](supported-languages.md)文档。  |
| `latlng`                    | `query`  |
| `location_type`             | *空值*     |
| `result_type`               | `entityType`    |

务必查看搜索文档的[最佳实践](how-to-use-best-practices-for-search.md)。

Azure Maps 反向地理编码 API 有一些在 Google Maps 中不可用的附加功能，这些功能在迁移应用时可能会很有用：

- 检索速度限制数据。
- 检索道路使用情况信息：本地公路、arterial、有限的访问和斜坡，等等。
- 坐标所属的街道的一侧。

## <a name="search-for-points-of-interest"></a>搜索兴趣点

可以通过使用其位置搜索 API 在 Google Maps 中搜索兴趣数据点。 此 API 提供了三种不同的方法来搜索兴趣点：

- **从文本中查找位置：** 基于其名称、地址或电话号码搜索兴趣点。
- **邻近搜索**：在某个位置的特定距离内搜索兴趣点。
- **文本搜索：** 使用包含兴趣点和位置信息的自由格式文本搜索位置。 例如，"纽约的比萨饼" 或 "在主 st 附近的餐厅"。

Azure Maps 为感兴趣的点提供了若干搜索 Api：

- [**POI 搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：按名称搜索兴趣点。 例如 "Starbucks"。
- [**POI 类别搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：按类别搜索兴趣点。 例如，"餐馆"。
- [**邻近搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：在某个位置的特定距离内搜索兴趣点。
- [**模糊搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串，该字符串可以是地址、位置、路标、感兴趣点或兴趣点类别，并立即处理请求。 对于用户可以在同一文本框中搜索感兴趣的地址或点的应用程序，建议使用此 API。
- [**在几何图形内搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜索指定几何（多边形）内的感兴趣点。
- [**沿路由搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜索沿指定路由路径的兴趣点。
- [**模糊批处理搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多10000个地址、位置、特征点或兴趣点的请求，并在一段时间内处理它们。 所有数据将在服务器上并行处理，完成后，可以下载完整的结果集。

当前 Azure Maps 没有与 Google Maps 文本搜索 API 相当的 API。

> [!TIP]
> 通过向请求 URL 添加 `&amp;typeahead=true`，可以在自动完成模式下使用 POI 搜索、POI 类别搜索和模糊搜索 Api。 这将告知服务器输入文本可能是部分内容，并将进入预测模式。

务必查看搜索文档的[最佳实践](how-to-use-best-practices-for-search.md)。

### <a name="find-place-from-text"></a>从文本查找位置

若要按名称或地址搜索兴趣点，可以使用 Azure Maps [POI 搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和[模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)api。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数 | 可比较 Azure Maps API 参数 |
|---------------------------|-------------------------------------|
| `fields`                  | *空值*                               |
| `input`                   | `query`                             |
| `inputtype`               | *空值*                               |
| `key`                     | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `language`                | `language` –请参阅[支持的语言](supported-languages.md)文档。  |
| `locationbias`            | `lat`、`lon` 和 `radius`<br/>`topLeft` 和 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>邻近搜索

附近的兴趣点可以使用[附近的搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)API Azure Maps 检索。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数 | 可比较 Azure Maps API 参数  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `keyword`                   | `categorySet` 和 `brandSet`        |
| `language`                  | `language` –请参阅[支持的语言](supported-languages.md)文档。  |
| `location`                  | `lat` 和 `lon`                     |
| `maxprice`                  | *空值*                               |
| `minprice`                  | *空值*                               |
| `name`                      | `categorySet` 和 `brandSet`        |
| `opennow`                   | *空值*                               |
| `pagetoken`                 | `ofs` 和 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *空值*                               |
| `type`                      | `categorySet –` 参阅[支持的搜索类别](supported-search-categories.md)文档。   |

## <a name="calculate-routes-and-directions"></a>计算路线和方向

Azure Maps 可用于计算路由和方向。 Azure Maps 具有与 Google Maps 路由服务相同的许多功能，例如：

- 到达和出发时间。
- 基于实时和预测的流量路由。
- 不同运输模式;推动、审核、bicycling。

> [!NOTE]
> Azure Maps 要求所有 waypoints 都是坐标。 首先需要地理编码地址。

Azure Maps 路由服务提供以下 Api 来计算路由：

- [**计算路线**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：计算路线并立即处理请求。 此 API 支持 GET 和 POST 请求。 当指定大量 waypoints 或使用大量路由选项来确保 URL 请求不会太长并导致问题时，建议使用 POST 请求。
- [**批处理路由**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：创建包含最多1000个路由请求的请求，并在一段时间内处理这些请求。 所有数据将在服务器上并行处理，完成后，可以下载完整的结果集。
- [**移动服务**](https://docs.microsoft.com/rest/api/maps/mobility)：使用公共传输来计算路由和方向。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数    | 可比较 Azure Maps API 参数  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` –格式的坐标 `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `language`                     | `language` –请参阅[支持的语言](supported-languages.md)文档。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | 不*适用–这*是一个地理编码的相关功能。 使用 Azure Maps 地理编码 API 时，请使用*countrySet*参数。  |
| `traffic_model`               | 不*适用–只能*指定流量数据是否应与*流量*参数一起使用。 |
| `transit_mode`                | 请参阅[移动服务文档](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | 请参阅[移动服务文档](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | 不*适用– Azure Maps*仅使用指标系统。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 默认情况下，Azure Maps 路由 API 只返回摘要（距离和时间）和路由路径的坐标。 使用 `instructionsType` 参数可检索轮流说明。 `routeRepresentation` 参数可用于筛选出摘要和路由路径。

Azure Maps 路由 API 在 Google Maps 中提供了许多其他功能，这些功能在迁移应用时可能会很有用：

- 支持路由类型：最短、最快、最 trilling 和最大燃料效率。
- 支持其他行程模式：总线、摩托车、出租车、卡车和 van。
- 150 waypoints 的支持。
- 在单个请求中计算多个旅行时间;历史流量、实时流量、无流量。
- 避免其他道路类型：合用公路，unpaved 公路，已使用道路。
- 指定要避免的自定义区域。
- 限制路由可以递增到的提升。
- 基于引擎规范的路由。 基于其剩余燃料/费用和引擎规格，计算 combustion 或电子车辆的路线。
- 商业汽车工艺路线参数支持;车辆维度、重量、axels 数和货物类型。
- 指定最大车辆速度。

除此之外，Azure Maps 中的路由服务还支持[计算可路由的范围](https://docs.microsoft.com/rest/api/maps/route/getrouterange)（也称为等时线），该服务会生成一个多边形，其中包含可在指定的时间段内或燃料/充电量内从原点沿任意方向向后传递到的区域。

## <a name="retrieve-a-map-image"></a>检索地图图像

Azure Maps 提供了一个 API，用于呈现包含重叠数据的静态地图图像。 Azure Maps[地图图像呈现](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Api 与 Google Maps 中的静态映射 api 相当。

> [!NOTE]
> Azure Maps 要求中心、所有标记和路径位置都以 "经度，纬度" 格式进行坐标，而 Google Maps 使用 "纬度，经度" 格式。 首先需要地理编码地址。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数 | 可比较 Azure Maps API 参数  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` –指定为 URL 路径的一部分。 目前仅支持 PNG。 |
| `key`                       | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `language`                  | `language` –请参阅[支持的语言](supported-languages.md)文档。  |
| `maptype`                   | `layer` 和 `style` –请参阅[支持的地图样式](supported-map-styles.md)文档。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | 不*适用–这*是一个地理编码的相关功能。 使用 Azure Maps 地理编码 API 时，请使用 `countrySet` 参数。  |
| `scale`                     | *空值*                              |
| `size`                      | `width` 和 `height` –最大可为8192x8192。 |
| `style`                     | *空值*                              |
| `visible`                   | *空值*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps 使用磁贴系统，其磁贴大小为 Google Maps 中使用的地图磁贴大小的两倍。 因此，Azure Maps 中的 "缩放级别" 值将在与 Google Maps 相比 Azure Maps 中显示一个缩放级别。 降低要迁移的请求中的缩放级别，以弥补这一情况。

有关详细信息，请参阅[地图图像呈现 API 中的操作方法指南](how-to-render-custom-data.md)。

除了能够生成静态地图图像以外，Azure Maps 呈现服务还提供直接访问光栅（PNG）和矢量格式中地图图块的能力：

- [**地图磁贴**](https://docs.microsoft.com/rest/api/maps/render/getmaptile)：检索基本地图（道路、边界、背景）的光栅（PNG）和矢量图块。
- [**地图图像磁贴**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)：检索高空图磁贴和卫星图像磁贴。

> [!TIP]
> 许多 Google 地图应用程序，在这些应用程序中，从交互地图到静态地图图像的体验，只需几年前就可以节省成本。 在 Azure Maps 中，使用 Web SDK 中的交互式地图控件时，使用交互式地图控件的成本更高，因为它基于地图磁贴加载进行收费。 Azure Maps 中的地图磁贴很大，通常只需几次即可重新创建同一地图视图，因为它会自动缓存一个静态地图，而地图图块会自动缓存。 因此，在复制静态映射视图时，交互式地图控件通常只生成事务的一部分。 平移和缩放将加载更多图块，但是，如果需要，地图控件中会有一些选项来禁用此行为。 与静态映射服务相比，交互式地图控件还提供了更多的可视化选项。

### <a name="marker-url-parameter-format-comparison"></a>标记 URL 参数格式比较

**之前： Google Maps**

在中，可以通过使用 URL 中的 `markers` 参数将 Google Maps 标记添加到静态映射图像中。 `markers` 参数采用样式和在地图上呈现的位置列表，如下所示：

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

可以通过将其他 `markers` 参数添加到具有不同样式和位置集的 URL 来使用其他样式。

用 "纬度，经度" 格式指定标记位置。

Google Maps 中的标记样式添加了格式 `optionName:value`，其中多个样式由竖线（\|）字符（如此 "optionName1： value1\|optionName2： value2"）分隔。 请注意，选项名称和值用冒号（:) 分隔。 以下样式选项名称可用于在 Google Maps 中的样式标记：

- `color` –默认标记图标的颜色。 可以是24位十六进制颜色（`0xrrggbb`）或以下值之一;`black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white`。
- `label` –在图标顶部显示的单个大写字母数字字符。
- `size`-标记的大小。 可以是 `tiny`、`mid` 或 `small`。

还可以使用以下样式选项名称在 Google Maps 中使用自定义图标：

- `anchor` –指定如何将图标图像与坐标对齐。 可以是像素（x，y）值或以下值之一;`top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft`或 `bottomright`。
- `icon` –指向图标图像的 URL。

例如，在 Google Maps 中，可以使用以下 URL 参数将红色的中等大小标记添加到地图坐标（经度：-110，纬度：45）：

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps 标记](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**后： Azure Maps**

在中，还可以通过在 URL 中指定 `pins` 参数，将 Azure Maps 标记添加到静态映射图像中。 与 Google Maps 类似，可以在此参数中指定样式和位置列表，并且可以多次指定 `pins` 参数以支持具有不同样式的标记。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

可以通过将其他 `pins` 参数添加到具有不同样式和位置集的 URL 来使用其他样式。

当涉及固定位置时，Azure Maps 要求坐标为 "经度纬度" 格式，而 Google Maps 使用 "纬度，经度" 格式。 另请注意，在 Azure Maps 中有一个空格，而不是用逗号分隔经度和纬度。

`iconType` 值指定要创建的 pin 类型，可以具有以下值：

- `default` –默认的固定图标。
- `none` –未显示图标，将只呈现标签。
- `custom` –指定要使用的自定义图标。 指向图标图像的 URL 可以在 pin 位置信息后添加到 `pins` 参数的末尾。
- `{udid}` –存储在 Azure Maps 数据存储平台中的图标的唯一数据 ID （UDID）。

使用 `optionNameValue`格式添加了 Azure Maps 中的固定样式，其中有多个样式用竖线（\|）字符（如此 `iconType|optionName1Value1|optionName2Value2`）分隔。 请注意，不会分隔选项名称和值。 以下样式选项名称可用于在 Azure Maps 中的样式标记：

- `al` –指定标记的不透明度（alpha）。 可以是0到1之间的数字。
- `an` –指定 pin 锚点。 以 "x y" 格式指定的 X 和 y 像素值。
- `co` – pin 的颜色。 必须为24位十六进制颜色： `000000` `FFFFFF`。
- `la` –指定标签定位点。 以 "x y" 格式指定的 X 和 y 像素值。
- `lc` –标签的颜色。 必须为 24-但十六进制颜色： `000000` `FFFFFF`。
- `ls` –标签的大小（以像素为单位）。 可以是大于0的数字。
- `ro` –旋转图标的度数值。 可以是介于-360 和360之间的数字。
- `sc` –固定图标的刻度值。 可以是大于0的数字。

为每个 pin 位置指定标签值，而不是将单个标签值应用于位置列表中的所有标记。 标签值可以是多个字符的字符串，并以单引号括起来，以确保它不被误认为为样式或位置值。

例如，在 Azure Maps 中，添加一个标签为 "Space 针" 的红色（`FF0000`）默认图标（15 50），可以使用以下 URL 参数完成坐标（经度：-122.349300，纬度：47.620180）的图标：

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

Azure Maps 标记 ![](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

下面的示例添加了具有标签值 "1"、"2" 和 "3" 的三个 pin：

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

Azure Maps 多个标记的 ![](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>路径 URL 参数格式比较

**之前： Google Maps**

在 Google Maps 中，可以通过使用 URL 中的 `path` 参数向静态地图图像添加多边形。 `path` 参数采用样式和在地图上呈现的位置列表，如下所示：

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

可以通过将其他 `path` 参数添加到具有不同样式和位置集的 URL 来使用其他样式。

Google Maps 中的路径位置以 `latitude1,longitude1|latitude2,longitude2|…`格式指定。 路径可以编码，也可以包含点地址。

Google Maps 中的路径样式以 `optionName:value`格式添加，多个样式用竖线（\|）字符隔开，如此 `optionName1:value1|optionName2:value2`。 请注意，选项名称和值用冒号（:) 分隔。 以下样式选项名称可用于在 Google Maps 中样式路径：

- `color` –路径或多边形轮廓的颜色。 可以为24位十六进制颜色（`0xrrggbb`）、32位十六进制颜色（`0xrrggbbbaa`）或以下值之一;黑色、棕色、绿色、紫色、黄色、蓝色、灰色、橙色、红色和白色。
- `fillColor` –用（多边形）填充路径区的颜色。 可以为24位十六进制颜色（`0xrrggbb`）、32位十六进制颜色（`0xrrggbbbaa`）或以下值之一;黑色、棕色、绿色、紫色、黄色、蓝色、灰色、橙色、红色和白色。
- `geodesic` –指示路径是否应为跟在地球曲率后的直线。
- `weight` –路径线条的粗细（以像素为单位）。

例如，在 Google Maps 中，具有 50% opacity 的红线和宽度为4像素的可通过以下 URL 参数添加到坐标（经度：-110，纬度：45和经度：-100，纬度：50）之间的地图：

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps 折线](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**后： Azure Maps**

在 Azure Maps 线条和多边形还可以通过在 URL 中指定 `path` 参数添加到静态地图图像。 与 Google Maps 类似，可以在此参数中指定样式和位置列表，并且可以多次指定 `path` 参数以呈现具有不同样式的多个圆圈、线条和多边形。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

当涉及到路径位置时，Azure Maps 要求坐标为 "经度纬度" 格式，而 Google Maps 使用 "纬度，经度" 格式。 另请注意，在 Azure Maps 中有一个空格，而不是用逗号分隔经度和纬度。 Azure Maps 不支持编码路径或点地址。 较大的数据集可以作为 GeoJSON 填充到 Azure Maps 数据存储 API，如[此处](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)所述。

Azure Maps 中的路径样式添加了格式 `optionNameValue`，多个样式用竖线（\|）字符（如此 `optionName1Value1|optionName2Value2`）分隔。 请注意，不会分隔选项名称和值。 以下样式选项名称可用于在 Azure Maps 中的路径样式：

- `fa`-呈现多边形时使用的填充色不透明度（alpha）。 可以是0到1之间的数字。
- `fc`-用于渲染多边形区域的填充颜色。
- `la` –在呈现线条和多边形的轮廓时使用的线条颜色不透明（alpha）。 可以是0到1之间的数字。
- `lc` –线条颜色，用于呈现线条和多边形的轮廓。
- `lw` –线条的宽度（以像素为单位）。
- `ra` –指定圆的半径，以米为单位。

例如，在 Azure Maps 中，具有 50% opacity 的红线和宽度为4像素的可通过以下 URL 参数添加到坐标（经度：-110，纬度：45和经度：-100，纬度：50）之间的地图：

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps 折线](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>计算距离矩阵

Azure Maps 提供了一个 API，用于计算一组位置与距离矩阵之间的行程时间和距离。 Azure Maps 距离矩阵 API 相当于 Google Maps 中的距离矩阵 API;

- [**路由矩阵**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：异步计算一组来源和目标的行程时间和距离。 每个请求最多支持700个单元（源数乘以目标数）。 考虑到这一限制，可能的矩阵维度示例包括：700x1、50x10、10x10、28x25、10x70。

> [!NOTE]
> 只能使用 POST 请求，对距离矩阵 API 发出请求，并在请求正文中使用源和目标信息。 此外，Azure Maps 要求所有源和目标都是坐标。 首先需要地理编码地址。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数      | 可比较 Azure Maps API 参数  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` –在 POST 请求正文中指定 GeoJSON。 |
| `key`                          | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。 |
| `language`                     | `language` –请参阅[支持的语言](supported-languages.md)文档。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` –在 POST 请求正文中指定 GeoJSON。  |
| `region`                       | 不*适用–这*是一个地理编码的相关功能。 使用 Azure Maps 地理编码 API 时，请使用 `countrySet` 参数。 |
| `traffic_model`                | *N/A* –只能指定流量数据是否应与 `traffic` 参数一起使用。 |
| `transit_mode`                 | 当前不支持基于非传输*的*距离矩阵。  |
| `transit_routing_preference`   | 当前不支持基于非传输*的*距离矩阵。  |
| `units`                        | 不*适用– Azure Maps*仅使用指标系统。 |

> [!TIP]
> Azure Maps 路由 API 中可用的所有高级路由选项（"卡车路线"、"引擎规范"、"规避 ..."）在 Azure Maps 距离矩阵 API 中都是支持的。

## <a name="get-a-time-zone"></a>获取时区

Azure Maps 提供了一个 API，用于检索坐标所在的时区。 Azure Maps 时区 API 相当于 Google Maps 中的时区 API：

- 时区（[**按坐标**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)）：指定坐标并获取其所属时区的详细信息。

下表交叉引用 Azure Maps 中具有可比较 API 参数的 Google Maps API 参数。

| Google Maps API 参数 | 可比较 Azure Maps API 参数   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` –另请参阅[Azure Maps 文档的身份验证](azure-maps-authentication.md)。       |
| `language`                  | `language` –请参阅[支持的语言](supported-languages.md)文档。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

除此之外，Azure Maps 平台还提供了一些额外的时区 Api，可帮助使用时区名称和 Id 进行转换：

- 时区（[**按 ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)）：返回指定的 IANA 时区 ID 的当前、历史和将来时区信息。
- 时区[**枚举 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：返回 IANA 时区 id 的完整列表。 IANA 服务的更新在一天内反映在系统中。
- 时区[**枚举窗口**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：返回 Windows 时区 id 的完整列表。
- 时区[**IANA 版本**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：返回 Azure Maps 使用的当前 IANA 版本号。
- 时区[**窗口到 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：返回对应的 iana id （给定有效的 WINDOWS 时区 id）。 可以为单个 Windows ID 返回多个 IANA Id。

## <a name="client-libraries"></a>客户端库

Azure Maps 提供以下编程语言的客户端库：

- JavaScript、TypeScript、node.js –[文档](how-to-use-services-module.md)\| [NPM 包](https://www.npmjs.com/package/azure-maps-rest)

其他编程语言的开源客户端库：

- .NET Standard 2.0 – [GitHub 项目](https://github.com/perfahlen/AzureMapsRestServices)\| [NuGet 包](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>其他资源

下面是有关 Azure Maps REST 服务的一些其他文档和资源。

- [搜索的最佳实践](how-to-use-best-practices-for-search.md)
- [搜索地址](how-to-search-for-address.md)
- [Azure Maps REST 服务 API 参考文档](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps REST 服务。

> [!div class="nextstepaction"]
> [使用搜索服务的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [如何使用服务模块（Web SDK）](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)