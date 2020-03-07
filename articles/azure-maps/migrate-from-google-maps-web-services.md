---
title: 教程：从 Google Maps 迁移 Web 服务 | Microsoft Azure Maps
description: 如何将 Web 服务从 Google Maps 迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: fae9b8a2101329383cc90c8f7f0ff225e3a9059c
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913812"
---
# <a name="migrate-web-service-from-google-maps"></a>从 Google Maps 迁移 Web 服务

Azure Maps 和 Google Maps 都通过 REST Web 服务提供对空间 API 的访问。 这些平台的 API 接口执行类似的功能。 但是，每个接口使用不同的命名约定和响应对象。

下表显示了功能与所列 Google Maps 服务 API 类似的 Azure Maps 服务 API。

| Google Maps 服务 API | Azure Maps 服务 API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| 方向              | [Route](https://docs.microsoft.com/rest/api/maps/route)                                     |
| 距离矩阵         | [路线矩阵](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| 地理编码               | [搜索](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 地点搜索           | [搜索](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 地点自动完成      | [搜索](https://docs.microsoft.com/rest/api/maps/search)                                   |
| 对齐道路            | 请参阅[计算路线和方向](#calculate-routes-and-directions)部分。            |
| 速度限制            | 请参阅[对坐标进行反向地理编码](#reverse-geocode-a-coordinate)部分。                  |
| 静态地图              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| 时区               | [时区](https://docs.microsoft.com/rest/api/maps/timezone)                              |

以下服务 API 目前在 Azure Maps 中不可用：

- Elevation
- 地理位置
- 位置详细信息和照片 - 电话号码和网站 URL 在 Azure Maps 搜索 API 中可用。
- 地图 URL
- 最近的道路 - 这可以使用 Web SDK 实现（如[此处](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
)所示），但目前不作为服务提供。
- 静态街道视图

Azure Maps 提供其他几个你可能会感兴趣的 REST Web 服务：

- [空间操作](https://docs.microsoft.com/rest/api/maps/spatial)：将复杂的空间计算和操作（例如地理围栏）负载分散到某个服务。
- [交通](https://docs.microsoft.com/rest/api/maps/traffic)：访问实时交通流量和事件数据。

## <a name="geocoding-addresses"></a>地理编码地址

地理编码是将地址转换为坐标的过程。 例如，将“1 Microsoft way, Redmond, WA”转换为经度：-122.1298，纬度：47.64005。 然后，坐标可用于不同的目的，例如，在地图上定位并居中某个标记。

Azure Maps 提供多种方法用于对地址进行地理编码：

- [**自由格式的地址地理编码**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定单个地址字符串并立即处理请求。 “1 Microsoft way, Redmond, WA”是单个地址字符串的示例。 如果需要快速对各个地址进行地理编码，我们建议使用此 API。
- [**结构化地址地理编码**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定单个地址的各个部分（例如街道名称、城市、国家/地区和邮政编码），并立即处理请求。 如果需要快速对单个地址进行地理编码，并且数据已分析成各个地址组成部分，则我们建议使用此 API。
- [**批量地址地理编码**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：创建最多包含 10,000 个地址的请求，并在一段时间内对其进行处理。 所有地址将在服务器上同时进行地理编码，完成后，可以下载已完成的结果集。 若要对大型数据集进行地理编码，建议使用此方法。
- [**模糊搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串。 此字符串可以是地址、地点、地标、兴趣点或兴趣点类别。 此 API 以近实时的速度处理请求。 对于其用户可在同一文本框中搜索地址或兴趣点的应用程序，建议使用此 API。
- [**模糊批量搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多 10, 000 个地址、地点、地标或兴趣点的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。

下表对 Google Maps API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| Google Maps API 参数 | 类似的 Azure Maps API 参数  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` 和 `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - 城市/城镇<br/>`municipalitySubdivision` – 周边区域，城市副中心/超级城市<br/>`countrySubdivision` - 州或省/自治区/直辖市<br/>`countrySecondarySubdivision` - 县<br/>`countryTertiarySubdivision` - 区<br/>`countryCode` - 双字母国家/地区代码 |
| `key`                       | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `language`                  | `language` – 请参阅[支持的语言](supported-languages.md)文档。  |
| `region`                    | `countrySet`                       |

[此处](how-to-search-for-address.md)提供了演示如何使用搜索服务的示例。 请务必查看[搜索最佳做法](how-to-use-best-practices-for-search.md)。

> [!TIP]
> 可以通过将 `&amp;typeahead=true` 添加到请求 URL，在自动完成模式下使用自由格式的地址地理编码和模糊搜索 API。 这可以让服务器知道输入文本可能是不完整的，搜索将进入预测模式。

## <a name="reverse-geocode-a-coordinate"></a>对坐标进行反向地理编码

反向地理编码是将地理坐标转换为大致地址的过程。 例如，将坐标“经度：-122.1298，纬度：47.64005”转换为“1 Microsoft way, Redmond, WA”。

Azure Maps 提供多种反向地理编码方法：

- [**地址反向地理编码器**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定单个地理坐标以获取对应于此坐标的大致地址。 以近实时的速度处理请求。
- [**十字街反向地理编码器**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定单个地理坐标以获取附近的十字街信息，并立即处理请求。 例如，可以获取第 1 大道与主街的十字街位置。
- [**批量地址反向地理编码器**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：创建最多包含 10,000 个坐标的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理。 请求完成后，可以下载整个结果集。

下表对 Google Maps API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| Google Maps API 参数   | 类似的 Azure Maps API 参数   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `language`                  | `language` – 请参阅[支持的语言](supported-languages.md)文档。  |
| `latlng`                    | `query`  |
| `location_type`             | *空值*     |
| `result_type`               | `entityType`    |

查看[搜索最佳做法](how-to-use-best-practices-for-search.md)。

Azure Maps 反向地理编码 API 提供 Google Maps 所不能提供的其他一些功能。 在迁移应用时，这些功能可能对集成应用程序有帮助：

- 检索限速数据
- 检索道路使用信息：地方道路、干道、限制进入、匝道等
- 检索坐标所在的街道一侧

## <a name="search-for-points-of-interest"></a>搜索兴趣点

在 Google Maps 中可以使用地点搜索 API 搜索兴趣点数据。 此 API 提供三种不同的方式来搜索兴趣点：

- **从文本中查找地点：** 基于兴趣点的名称、地址或电话号码搜索该兴趣点。
- **附近搜索：** 搜索位于某个位置特定距离内的兴趣点。
- **文本搜索：** 使用包含兴趣点和位置信息的自由格式文本来搜索地点。 例如，“纽约的披萨店”或“主街附近的餐厅”。

Azure Maps 提供多个搜索 API 用于搜索兴趣点：

- [**POI 搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：按名称搜索兴趣点。 例如“星巴克”。
- [**POI 类别搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：按类别搜索兴趣点。 例如“餐厅”。
- [**附近搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：搜索位于某个位置特定距离内的兴趣点。
- [**模糊搜索**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串（可以是地址、地点、地标、兴趣点或兴趣点类别）。 它以近实时的速度处理请求。 对于其用户可在同一文本框中搜索地址或兴趣点的应用程序，建议使用此 API。
- [**在几何图形中搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜索指定几何图形中的兴趣点。 例如，在多边形中搜索兴趣点。
- [**沿路线搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜索指定路线上的兴趣点。
- [**模糊批量搜索**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多 10, 000 个地址、地点、地标或兴趣点的请求。 在一段时间内处理请求。 所有数据将在服务器上同时进行处理。 请求完成处理后，可以下载整个结果集。

目前，Azure Maps 没有与 Google Maps 中的文本搜索 API 类似的 API。

> [!TIP]
> 可以通过将 `&amp;typeahead=true` 添加到请求 URL，在自动完成模式下使用 POI 搜索、POI 类别搜索和模糊搜索 API。 这可以让服务器知道输入文本可能是不完整的。API 将在预测模式下执行搜索。

查看[搜索最佳做法](how-to-use-best-practices-for-search.md)文档。

### <a name="find-place-from-text"></a>从文本中查找地点

使用 Azure Maps [POI 搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和[模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)可按名称或地址搜索兴趣点。

下表对 Google Maps API 参数与类似的 Azure Maps API 参数做了交叉比较。

| Google Maps API 参数 | 类似的 Azure Maps API 参数 |
|---------------------------|-------------------------------------|
| `fields`                  | *空值*                               |
| `input`                   | `query`                             |
| `inputtype`               | *空值*                               |
| `key`                     | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `language`                | `language` – 请参阅[支持的语言](supported-languages.md)文档。  |
| `locationbias`            | `lat`、`lon` 和 `radius`<br/>`topLeft` 和 `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>附近搜索

在 Azure Maps 中，使用[附近搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API 来检索附近的兴趣点。

下表显示了 Google Maps API 参数与类似的 Azure Maps API 参数。

| Google Maps API 参数 | 类似的 Azure Maps API 参数  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `keyword`                   | `categorySet` 和 `brandSet`        |
| `language`                  | `language` – 请参阅[支持的语言](supported-languages.md)文档。  |
| `location`                  | `lat` 和 `lon`                     |
| `maxprice`                  | *空值*                               |
| `minprice`                  | *空值*                               |
| `name`                      | `categorySet` 和 `brandSet`        |
| `opennow`                   | *空值*                               |
| `pagetoken`                 | `ofs` 和 `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *空值*                               |
| `type`                      | `categorySet –` 请参阅[支持的搜索类别](supported-search-categories.md)文档。   |

## <a name="calculate-routes-and-directions"></a>计算路线和方向

使用 Azure Maps 计算路线和方向。 Azure Maps 具有许多与 Google Maps 路线服务相同的功能，例如：

- 抵达和出发时间。
- 实时和基于预测的交通路线。
- 不同的交通方式。 例如驾车、步行、自行车。

> [!NOTE]
> Azure Maps 要求以坐标提供中途点。 必须先对地址进行地理编码。

Azure Maps 路线服务提供以下 API 来计算路线：

- [**计算路线**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：计算路线并立即处理请求。 此 API 支持 GET 和 POST 请求。 指定大量中途点或使用大量路线选项时，建议使用 POST 请求，以确保 URL 请求不会太长并导致出现问题。 Azure Maps 中的“POST 路线方向”有一个选项，该选项可以接受数千个[支持点](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)，并将使用这些支持点在它们之间重新创建一个逻辑路线路径（对齐到道路）。 
- [**批处理路线**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：创建最多包含 1,000 个路线请求的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。
- [**移动服务**](https://docs.microsoft.com/rest/api/maps/mobility)：使用公共交通计算路线和方向。

下表对 Google Maps API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| Google Maps API 参数    | 类似的 Azure Maps API 参数  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – 采用 `"lat0,lon0:lat1,lon1…."` 格式的坐标  |
| `key`                          | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `language`                     | `language` – 请参阅[支持的语言](supported-languages.md)文档。   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *不适用* – 此功能与地理编码相关。 使用 Azure Maps 地理编码 API 时，请使用 *countrySet* 参数。  |
| `traffic_model`               | *不适用* – 仅当交通数据应与 *traffic* 参数结合使用时才能指定。 |
| `transit_mode`                | 请参阅[移动服务文档](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | 请参阅[移动服务文档](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *不适用* – Azure Maps 仅使用指标系统。  |
| `waypoints`                    | `query`                            |

> [!TIP]
> 默认情况下，Azure Maps 路线 API 仅返回摘要。 它返回距离和时间，以及路径的坐标。 使用 `instructionsType` 参数可检索转弯提示说明。 使用 `routeRepresentation` 参数筛选出摘要和路径。

Azure Maps 路线 API 提供 Google Maps 所不能提供的其他许多功能。 在迁移应用时，请考虑使用这些功能，你可能会发现它们很有用。

- 路线类型支持：最短、最快、trilling 和最省油。
- 其他交通方式支持：公共汽车、摩托车、出租车、货车和面包车。
- 支持 150 个中途点。
- 计算单个请求中的多个旅行时间；历史交通状况、实时交通状况、无交通状况。
- 避开其他道路类型：共乘车道、土路、已占用的道路。
- 指定要避开的自定义区域。
- 限制路线可以提升到的高度。
- 基于发动机规格规划路线。 根据发动机规格和剩余燃料或电量计算燃料汽车或电动车的路线。
- 支持商务车路线参数。 例如车辆尺寸、重量、轴数和货物类型。
- 指定最大车速。

除此之外，Azure Maps 中的路线服务还支持[计算可达范围](https://docs.microsoft.com/rest/api/maps/route/getrouterange)。 可达范围也称为等时线。 这种计算会生成一个多边形，其中涵盖了在指定的时间内或根据剩余的燃料/电量， 从某个原点朝任意方向可以行进到的区域。

## <a name="retrieve-a-map-image"></a>检索地图图像

Azure Maps 提供一个 API 用于呈现包含叠加数据的静态地图图像。 Azure Maps 中的[地图图像呈现](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API 类似于 Google Maps 中的静态地图 API。

> [!NOTE]
> Azure Maps 要求以“经度,纬度”格式的坐标提供中心点、所有标记和路径位置。 而 Google Maps 则使用“纬度,经度”格式。 首先需要对地址进行地理编码。

下表对 Google Maps API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| Google Maps API 参数 | 类似的 Azure Maps API 参数  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – 指定为 URL 路径的一部分。 目前仅支持 PNG。 |
| `key`                       | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `language`                  | `language` – 请参阅[支持的语言](supported-languages.md)文档。  |
| `maptype`                   | `layer` 和 `style` – 请参阅[支持的地图样式](supported-map-styles.md)文档。 |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *不适用* – 这是地理编码相关的功能。 使用 Azure Maps 地理编码 API 时，请使用 `countrySet` 参数。  |
| `scale`                     | *空值*                              |
| `size`                      | `width` 和 `height` – 最大大小可为 8192x8192。 |
| `style`                     | *空值*                              |
| `visible`                   | *空值*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> 在 Azure Maps 图块系统中，图块大小是 Google Maps 中所用地图图块的两倍。 因此，相比 Google Maps，Azure Maps 中的缩放级别值看上去要更近一个级别。 若要补偿这种差异，请要迁移的请求中降低缩放级别。

有关详细信息，请参阅[有关地图图像呈现 API 的操作指南](how-to-render-custom-data.md)。

除了能够生成静态地图图像以外，Azure Maps 呈现服务还提供直接访问光栅 (PNG) 和矢量格式的地图图块的功能：

- [**地图图块**](https://docs.microsoft.com/rest/api/maps/render/getmaptile)：检索基础地图的光栅 (PNG) 和矢量图块（道路、边界、背景）。
- [**地图航拍图块**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)：检索航空和卫星航拍图块。

> [!TIP]
> 许多 Google Maps 应用程序在几年前就已从交互式地图体验改换到了静态地图图像。 这是一种成本节省的方法。 在 Azure Maps 中，使用 Web SDK 中的交互式地图控件可以提高性价比。 交互式地图控件是按照图块加载次数收费的。 Azure Maps 中的地图图块较大。 通常只需添加少量的图块即可重新创建与静态地图相同的地图视图。 浏览器会自动缓存地图图块。 因此，在再现静态地图视图时，交互式地图控件通常只生成一部分事务。 平移和缩放会加载更多图块，但是，可以使用地图控件中的选项来禁用此行为。 此外，交互式地图控件提供的可视化选项比静态地图服务要多得多。

### <a name="marker-url-parameter-format-comparison"></a>标记 URL 参数格式比较

**前者：Google Maps**

可以通过在 URL 中使用 `markers` 参数来添加标记。 `markers` 参数采用某种样式以及要在地图上呈现的位置列表，其中，样式如下所示：

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

若要添加更多的样式，请在包含不同样式和位置集的 URL 中使用 `markers` 参数。

使用“纬度,经度”格式指定标记位置。

添加采用 `optionName:value` 格式的标记样式，多个样式以竖线 (\|) 字符分隔，例如 “optionName1:value1\|optionName2:value2”。 请注意，选项名称和值以冒号(:) 分隔。 使用以下样式选项名称来为 Google Maps 中的标记设置样式：

- `color` – 默认标记图标的颜色。 可以是 24 位十六进制颜色 (`0xrrggbb`) 或以下值之一：`black`、`brown`、`green`、`purple`、`yellow`、`blue`、`gray`、`orange`、`red`、`white`。
- `label` – 要在图标顶部显示的单个大写字母数字字符。
- `size` - 标记的大小。 可以是 `tiny`、`mid` 或 `small`。

对 Google Maps 中的自定义图标使用以下样式选项名称：

- `anchor` – 指定如何将图标图像与坐标对齐。 可以是像素 (x,y) 值或以下值之一：`top`、`bottom`、`left`、`right`、`center`、`topleft`、`topright`、`bottomleft` 或 `bottomright`。
- `icon` - 指向图标图像的 URL。

例如，让我们将中等大小的红色标记添加到地图中的经度：-110，纬度：45：

```
&markers=color:red|size:mid|45,-110
```

<center>

![Google Maps 标记](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**后者：Azure Maps**

通过在 URL 中指定 `pins` 参数，将标记添加到静态地图图像。 与 Google Maps 一样，可以在此参数中指定样式和位置列表。 可以指定 `pins` 参数多次，以支持采用不同样式的标记。

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

若要使用更多的样式，请在包含不同样式和位置集的 URL 中添加 `pins` 参数。

在 Azure Maps 中，图钉位置需要采用“经度 纬度”格式。 Google Maps 使用“纬度,经度”格式。 使用空格而不是逗号来分隔 Azure Maps 格式中的经度和纬度。

`iconType` 指定要创建的图钉类型。 该选项可具有以下值：

- `default` – 默认的图钉图标。
- `none` – 不显示图标，只呈现标签。
- `custom` – 指定要使用的自定义图标。 可将指向图标图像的 URL 添加到图钉位置信息后的 `pins` 参数的末尾。
- `{udid}` – 存储在 Azure Maps 数据存储平台中的图标的唯一数据 ID (UDID)。

添加采用 `optionNameValue` 格式的图钉样式。 使用竖线 (\|) 字符分隔多个样式。 例如：`iconType|optionName1Value1|optionName2Value2`。 不用分隔选项名称和值。 使用以下样式选项名称来为标记设置样式：

- `al` – 指定标记的不透明度 (alpha)。 选择 0 到 1 的数字。
- `an` – 指定图钉定位点。 以“x y”格式指定 X 和 Y 轴值。
- `co` – 图钉的颜色。 指定 24 位十六进制颜色：`000000` 到 `FFFFFF`。
- `la` – 指定标签定位点。 以“x y”格式指定 X 和 Y 轴值。
- `lc` – 标签的颜色。 指定 24 位十六进制颜色：`000000` 到 `FFFFFF`。
- `ls` – 标签的大小（以像素为单位）。 选择大于 0 的数字。
- `ro` – 图标的旋转度数值。 选择 -360 到 360 的数字。
- `sc` – 图钉图标的比例值。 选择大于 0 的数字。

为每个图钉位置指定标签值。 此方法比向位置列表中的所有标记应用单个标签值更加高效。 标签值可以是包含多个字符的字符串。 将字符串括在单引号中，以确保不会错误地将它视为样式或位置值。

让我们添加一个具有标签“Space Needle”、定位在 (15 50) 下的红色 (`FF0000`) 默认图标。 该图标的位置为经度：-122.349300，纬度：47.620180：

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Azure Maps 标记](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

添加具有标签值“1”、“2”和“3”的三个图钉：

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps 多个标记](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>路径 URL 参数格式比较

**前者：Google Maps**

通过在 URL 中使用 `path` 参数，将线条和多边形添加到静态地图图像。 `path` 参数采用某种样式以及要在地图上呈现的位置列表，如下所示：

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

通过将其他 `path` 参数添加到包含不同样式和位置集的 URL 来使用更多样式。

以 `latitude1,longitude1|latitude2,longitude2|…` 格式指定路径位置。 路径可以编码，或包含点的地址。

添加采用 `optionName:value` 格式的路径样式，使用竖线 (\|) 字符分隔多个样式。 使用冒号(:) 分隔选项名称和值。 例如：`optionName1:value1|optionName2:value2`。 可使用以下样式选项名称来为 Google Maps 中的路径设置样式：

- `color` – 路径或多边形轮廓的颜色。 可以是 24 位十六进制颜色 (`0xrrggbb`)、32 位十六进制颜色 (`0xrrggbbbaa`) 或以下值之一：black、brown、green、purple、yellow、blue、gray、orange、red、white。
- `fillColor` – 用于填充路径区域（多边形）的颜色。 可以是 24 位十六进制颜色 (`0xrrggbb`)、32 位十六进制颜色 (`0xrrggbbbaa`) 或以下值之一：black、brown、green、purple、yellow、blue、gray、orange、red、white。
- `geodesic` – 指示路径是否应为遵循地球曲率的线条。
- `weight` – 路径线条的粗细（以像素为单位）。

在 URL 参数中，将红色线条不透明度和像素粗细添加到地图上的坐标之间。 在以下示例中，线条不透明度为 50%，粗细为 4 像素。 坐标为经度：-110，纬度：45）和（经度：-100，纬度：50。

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Google Maps 折线](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**后者：Azure Maps**

通过在 URL 中指定 `path` 参数，将线条和多边形添加到静态地图图像。 与 Google Maps 一样，可以在此参数中指定样式和位置列表。 指定 `path` 参数多次，以呈现采用不同样式的多个圆、线条和多边形。

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

涉及到路径位置时，Azure Maps 要求坐标采用“经度 纬度”格式。 Google Maps 使用“纬度,经度”格式。 使用空格而不是逗号来分隔 Azure Maps 格式中的经度和纬度。 Azure Maps 不支持编码的路径或点地址。 根据[此文档](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)所述，将较大的数据集作为 GeoJSON 填充内容上传到 Azure Maps 数据存储 API。

添加采用 `optionNameValue` 格式的路径样式。 使用竖线 (\|) 字符分隔多个样式，例如 `optionName1Value1|optionName2Value2`。 不用分隔选项名称和值。 使用以下样式选项名称来为 Azure Maps 中的路径设置样式：

- `fa` - 呈现多边形时使用的填充颜色不透明度 (alpha)。 选择 0 到 1 的数字。
- `fc` - 用于呈现多边形区域的填充颜色。
- `la` – 在呈现多边形的线条和轮廓时使用的线条颜色不透明 (alpha)。 选择 0 到 1 的数字。
- `lc` – 用于呈现多边形的线条和轮廓的线条颜色。
- `lw` – 线条的宽度（以像素为单位）。
- `ra` – 指定圆的半径（以米为单位）。

在 URL 参数中，将红色线条不透明度和像素粗细添加到坐标之间。 在以下示例中，线条不透明度为 50%，粗细为 4 像素。 坐标值如下：经度：-110，纬度：45）和（经度：-100，纬度：50。

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps 折线](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>计算距离矩阵

Azure Maps 提供距离矩阵 API。 使用此 API 可以计算一组位置之间的行程时间和距离以及距离矩阵。 它类似于 Google Maps 中的距离矩阵 API。

- [**路线矩阵**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：以异步方式计算一组来源和目标的行程时间与距离。 每个请求最多支持 700 个单元。 该数字为出发地数乘以目的地数。 考虑到这一限制，可能的矩阵维度示例如下：700x1、50x10、10x10、28x25、10x70。

> [!NOTE]
> 只能使用在正文中包含来源和目标信息的 POST 请求向距离矩阵 API 发出请求。 此外，Azure Maps 要求以坐标形式指定所有来源和目标。 首先需要对地址进行地理编码。

下表对 Google Maps API 参数与类似的 Azure Maps API 参数做了交叉比较。

| Google Maps API 参数      | 类似的 Azure Maps API 参数  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – 在 POST 请求正文中以 GeoJSON 形式指定。 |
| `key`                          | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。 |
| `language`                     | `language` – 请参阅[支持的语言](supported-languages.md)文档。  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – 在 POST 请求正文中以 GeoJSON 形式指定。  |
| `region`                       | *不适用* – 此功能与地理编码相关。 使用 Azure Maps 地理编码 API 时，请使用 `countrySet` 参数。 |
| `traffic_model`                | *不适用* – 仅当交通数据应与 `traffic` 参数结合使用时才能指定。 |
| `transit_mode`                 | *不适用* - 目前不支持基于交通方式的距离矩阵。  |
| `transit_routing_preference`   | *不适用* - 目前不支持基于交通方式的距离矩阵。  |
| `units`                        | *不适用* – Azure Maps 仅使用指标系统。 |

> [!TIP]
> Azure Maps 路线 API 中提供的所有高级路线选项在 Azure Maps 距离矩阵 API 中均受支持。 高级路线选项包括：货车路线、发动机规格，等等。

## <a name="get-a-time-zone"></a>获取时区

Azure Maps 提供一个 API 用于检索坐标所在的时区。 Azure Maps 时区 API 相当于 Google Maps 中的时区 API：

- [**按坐标获取时区信息**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)：指定坐标并接收该坐标的时区详细信息。

下表对 Google Maps API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| Google Maps API 参数 | 类似的 Azure Maps API 参数   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](azure-maps-authentication.md)文档。       |
| `language`                  | `language` – 请参阅[支持的语言](supported-languages.md)文档。    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

除此 API 以外，Azure Maps 还提供了许多时区 API。 这些 API 可根据时区的名称或 ID 转换时间：

- [**按 ID 获取时区信息**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)：返回指定 IANA 时区 ID 的当前、历史和将来时区信息。
- [**时区枚举 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：返回 IANA 时区 ID 的完整列表。 对 IANA 服务所做的更新在一天内即会反映在系统中。
- [**时区枚举 Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：返回 Windows 时区 ID 的完整列表。
- [**时区 IANA 版本**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：返回 Azure Maps 使用的当前 IANA 版本号。
- [**与 Windows 时区对应的 IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：在指定有效 Windows 时区 ID 的情况下，返回对应的 IANA ID。 可返回单个 Windows ID 的多个 IANA ID。

## <a name="client-libraries"></a>客户端库

Azure Maps 为以下编程语言提供客户端库：

- JavaScript、TypeScript、Node.js – [文档](how-to-use-services-module.md) \| [NPM 包](https://www.npmjs.com/package/azure-maps-rest)

这些开源客户端库适用于其他编程语言：

- .NET Standard 2.0 – [GitHub 项目](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 包](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>其他资源

下面是有关 Azure Maps REST 服务的其他文档和资源。

- [有关搜索的最佳做法](how-to-use-best-practices-for-search.md)
- [搜索地址](how-to-search-for-address.md)
- [Azure Maps REST 服务 API 参考文档](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps REST 服务。

> [!div class="nextstepaction"]
> [有关使用搜索服务的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [如何使用服务模块 (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [示例代码](https://docs.microsoft.com/samples/browse/?products=azure-maps)
