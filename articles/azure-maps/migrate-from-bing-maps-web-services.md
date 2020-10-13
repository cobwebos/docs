---
title: 教程：从必应地图迁移 Web 服务 | Microsoft Azure Maps
description: 如何将 Web 服务从必应地图迁移到 Microsoft Azure Maps。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 14e0998b75e0e5bd3ae996f5f5010ecc50180f14
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741663"
---
# <a name="migrate-web-service-from-bing-maps"></a>从必应地图迁移 Web 服务

Azure Maps 和必应地图都通过 REST Web 服务提供对空间 API 的访问。 这些平台的 API 接口执行类似的功能，但使用不同的命名约定和响应对象。

下表提供了功能与所列必应地图服务 API 类似的 Azure Maps 服务 API。

| 必应地图服务 API                 | Azure Maps 服务 API      |
|---------------------------------------|-----------------------------|
| 自动建议                           | [搜索](https://docs.microsoft.com/rest/api/maps/search)     |
| 走向（包括卡车）          | [路线走向](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)                          |
| 距离矩阵                       | [路线矩阵](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)                          |
| 图像 - 静态地图                  | [呈现](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                                   |
| 等时线                            | [路线范围](https://docs.microsoft.com/rest/api/maps/route/getrouterange)                                    |
| 本地见解                        | [搜索](https://docs.microsoft.com/rest/api/maps/search) + [路线范围](https://docs.microsoft.com/rest/api/maps/route/getrouterange)    |
| 本地搜索                          | [搜索](https://docs.microsoft.com/rest/api/maps/search)     |
| 位置识别 (POI)           | [搜索](https://docs.microsoft.com/rest/api/maps/search)     |
| 位置（正向/反向地理编码） | [搜索](https://docs.microsoft.com/rest/api/maps/search)                                               |
| 对齐道路                          | [POST 路线走向](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)                         |
| 空间数据服务 (SDS)           | [搜索](https://docs.microsoft.com/rest/api/maps/search) + [路线](https://docs.microsoft.com/rest/api/maps/route) + 其他 Azure 服务 |
| 时区                             | [时区](https://docs.microsoft.com/rest/api/maps/timezone)  |
| 交通事故                     | [交通事件详细信息](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)                     |

以下服务 API 目前在 Azure Maps 中不可用：

-   高程 - 已计划
-   优化的路线 - 已计划。 Azure Maps 路由 API 支持单个车辆的旅行推销员优化。
-   图像元数据 - 主要用于在必应地图中获取图块 URL。 Azure Maps 具有用于直接访问地图图块的独立服务。

Azure Maps 提供其他几个你可能会感兴趣的 REST Web 服务；

-   [Azure Maps Creator](https://docs.microsoft.com/azure/azure-maps/creator-indoor-maps) - 创建建筑和空间的自定义专有数字孪生。
-   [空间操作](https://docs.microsoft.com/rest/api/maps/spatial) - 将复杂的空间计算和操作（例如地理围栏）负载分散到某个服务。
-   [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile) - 通过 Azure Maps 访问光栅和矢量图块形式的道路和图像图块。
-   [批处理路线](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview) - 支持在一段时间内在单个批处理中发出多达 1,000 个路线请求。 路线在服务器上并行计算，以提高处理速度。
-   [交通](https://docs.microsoft.com/rest/api/maps/traffic)流量 - 访问光栅和矢量图块形式的实时交通流量数据。
-   [地理位置 API](https://docs.microsoft.com/rest/api/maps/geolocation/getiptolocationpreview) - 获取 IP 地址的位置。
-   [天气服务](https://docs.microsoft.com/rest/api/maps/weather) - 获取实时和预报天气数据。

请务必查看以下最佳做法指南：

-   [有关搜索的最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)
-   [路线规划最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)

## <a name="geocoding-addresses"></a>地理编码地址

地理编码是将地址（例如 `"1 Microsoft way, Redmond, WA"`）转换为坐标（例如经度：-122.1298，纬度：47.64005）的过程。 坐标通常用于在地图上定位某个图钉，或者设置地图的中心点。

Azure Maps 提供多种方法用于对地址进行地理编码；

-   [自由格式的地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定单个地址字符串（如 `"1 Microsoft way, Redmond, WA"`）并立即处理请求。 如果需要快速对各个地址进行地理编码，我们建议使用此服务。
-   [结构化地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定单个地址的各个部分（例如街道名称、城市、国家/地区和邮政编码），并立即处理请求。 如果需要快速对单个地址进行地理编码，并且数据已分析成各个地址组成部分，则我们建议使用此服务。
-   [批量地址地理编码](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：创建最多包含 10,000 个地址的请求，并在一段时间内对其进行处理。 所有地址将在服务器上同时进行地理编码，完成后，可以下载已完成的结果集。 若要对大型数据集进行地理编码，建议使用此服务。
-   [模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串（可以是地址、地点、地标、兴趣点或兴趣点类别），并立即处理请求。 对于其用户可以从同一文本框搜索地址或兴趣点的应用程序，建议使用此 API。
-   [模糊批量搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多 10, 000 个地址、地点、地标或兴趣点的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。

下表将必应地图 API 参数和 Azure Maps 中的可比较的 API 参数交叉引用，用于结构化和自由格式地址地理编码。

按地址（结构化地址）划分的位置

| 必应地图 API 参数              | 类似的 Azure Maps API 参数                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`、`streetName` 或 `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` 和 `countryCode`                     |
| `locality`                         | `municipality` 或 `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N/A - 始终由 Azure Maps 返回（如果可用）。   |
| `include` (`incl`)               | N/A - 国家/地区 ISO2 代码始终由 Azure Maps 返回。 |
| `key`                              | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)                  | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。 |
| `userRegion` (`ur`)              | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

Azure Maps 也支持；

-   `countrySecondarySubdivision` - 县、地区
-   `countryTertiarySubdivision` - 已命名的区域；自治市镇、行政区、公社
-   `ofs` - 包含结果和 `maxResults` 参数的页面。

按查询划分的位置（自由格式的地址字符串）

| 必应地图 API 参数              | 类似的 Azure Maps API 参数      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N/A - 始终由 Azure Maps 返回（如果可用）。  |
| `include` (`incl`)               | N/A - 国家/地区 ISO2 代码始终由 Azure Maps 返回。  |
| `key`                              | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)                  | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。  |
| `userRegion` (`ur`)              | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

Azure Maps 也支持；

-   `typeahead` - 如果查询将解释为部分输入，并且搜索将进入预测模式（自动建议/自动完成），则会指定。
-   `countrySet` - 用于搜索限制的 ISO2 国家/地区代码的逗号分隔列表。
-   `lat`/`lon`、`topLeft`/`btmRight`、`radius` - 指定用户位置和区域，以提高结果的本地相关性。
-   `ofs` - 包含结果和 `maxResults` 参数的页面。

[此处](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)提供了演示如何使用搜索服务的示例。 请务必查看[搜索最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)文档。

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>对坐标进行反向地理编码（按点查找位置）

反向地理编码是将地理坐标（例如经度：-122.1298，纬度：47.64005）转换为大致地址（如 `"1 Microsoft way, Redmond, WA"`）的过程。

Azure Maps 提供多种反向地理编码方法；

-   [地址反向地理编码器](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)：指定单个地理坐标以获取其大致地址，并立即处理请求。
-   [十字街反向地理编码器](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)：指定单个地理坐标以获取附近的十字街信息（例如，第一个十字街和主街），并立即处理请求。
-   [批量地址反向地理编码器](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview)：创建最多包含 10,000 个坐标的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数              | 类似的 Azure Maps API 参数       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` - 请参阅下面的实体类型比较表。    |
| `includeNeighborhood` (`inclnb`)     | N/A - 始终由 Azure Maps 返回（如果可用）。         |
| `include` (`incl`)                   | N/A - 国家/地区 ISO2 代码始终由 Azure Maps 返回。    |
| `key`                                | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)                      | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。   |
| `userRegion` (`ur`)                  | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

请务必查看[搜索最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)文档。

Azure Maps 反向地理编码 API 提供必应地图所不能提供的其他一些功能，在迁移应用时，集成这些功能可能会有所帮助：

-   检索限速数据。
-   检索道路使用信息；地方道路、干道、限制进入、匝道等。
-   坐标所在的街道一侧。

实体类型比较表

下表交叉引用必应地图实体类型值到 Azure Maps 中的等效属性名称。

| 必应地图实体类型 | 类似的 Azure Maps 实体类型               | 说明                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Address*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | 邻近区域                             |
| `PopulatedPlace`      | `Municipality` 或 `MunicipalitySubdivision`     | 城市、城镇或郊区或者特大城市       |
| `Postcode1`           | `PostalCodeArea`                                | 邮政编码                 |
| `AdminDivision1`      | `CountrySubdivision`                            | 州或省                       |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | 县或地区                     |
| `CountryRegion`       | `Country`                                       | *国家/地区名称*                             |
|                       | `CountryTertiarySubdivision`                    | 自治市镇、行政区、公社            |

## <a name="get-location-suggestions-autosuggest"></a>获取位置建议（自动建议）

可用于自动建议场景的几个 Azure Maps 搜索 API 的支持预测模式。 Azure Maps [模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API 与必应地图自动建议 API 最相似。 以下 API 还支持预测模式，向查询添加 `&typeahead=true`；

-   [自由格式的地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定单个地址字符串（如 `"1 Microsoft way, Redmond, WA"`）并立即处理请求。 如果需要快速对各个地址进行地理编码，我们建议使用此服务。
-   [模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串（可以是地址、地点、地标、兴趣点或兴趣点类别），并立即处理请求。 对于其用户可以从同一文本框搜索地址或兴趣点的应用程序，建议使用此 API。
-   [POI 搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：按名称搜索兴趣点。 例如，`"starbucks"`。
-   [POI 类别搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：按类别搜索兴趣点。 例如，“餐厅”。

## <a name="calculate-routes-and-directions"></a>计算路线和方向

Azure Maps 可用于计算路线和方向。 Azure Maps 具有许多与必应地图路线服务相同的功能，例如；

-   抵达和出发时间
-   实时和基于预测的交通路线
-   不同的交通方式；驾车、步行、卡车
-   航点顺序优化（旅行销售员）

> [!NOTE]
> Azure Maps 要求以坐标提供中途点。 首先需要对地址进行地理编码。

Azure Maps 路线服务提供以下 API 来计算路线；

-   [计算路线](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)：计算路线并立即处理请求。 此 API 支持 GET 和 POST 请求。 指定大量中途点或使用大量路线选项时，建议使用 POST 请求，以确保 URL 请求不会太长并导致出现问题。
-   [批处理路线](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview)：创建最多包含 1,000 个路线请求的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。
-   [移动服务](https://docs.microsoft.com/rest/api/maps/mobility)：使用公共交通计算路线和方向。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数                                    | 类似的 Azure Maps API 参数               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` 或 `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | 空值                                               |
| `distanceUnit` (`du`)                                      | 不适用 – Azure Maps 仅使用指标系统。     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`、`alternativeType`、`minDeviationDistance` 和 `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` 和 `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` 或 `arriveAt`                          |
| `tolerances` (`tl`)                                        | 空值                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) 或 `viaWaypoint.n` (`vwp.n`)         | `query` – 采用 `lat0,lon0:lat1,lon1….` 格式的坐标   |
| `key`                                                      | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)                                            | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。 |
| `userRegion` (`ur`)                                        | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

Azure Maps 路线 API 还支持同一 API 中的卡车路线。 下表对其他必应地图卡车路线参数与 Azure Maps 中的类似 API 参数作了交叉引用。

| 必应地图 API 参数                  | 类似的 Azure Maps API 参数        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N/A - 仅支持以米为单位的尺寸。 |
| `weightUnit` (`wu`)                      | N/A - 仅支持以千克为单位的重量。 |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **不适用**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **不适用**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **不适用**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **不适用**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **不适用**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> 默认情况下，Azure Maps 路由 API 仅返回摘要（距离和时间）以及路线的坐标。 使用 `instructionsType` 参数可检索转弯提示说明。 可以使用 `routeRepresentation` 参数筛选出摘要和路线。

此外，请务必查看[路线规划最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)文档。

Azure Maps 路线 API 提供必应地图所不能提供的其他许多功能，在迁移应用时，集成这些功能可能会有所帮助：

-   路线类型支持：最短、最快、trilling 和最省油。
-   其他交通方式支持：自行车、公共汽车、摩托车、出租车、货车和面包车。
-   支持 150 个中途点。
-   计算单个请求中的多个旅行时间；历史交通状况、实时交通状况、无交通状况。
-   避开其他道路类型：共乘车道、土路、已占用的道路。
-   基于引擎规格规划路线。 根据剩余燃料/电量和引擎规格计算燃料汽车或电动车的路线。
-   指定最大车速。

## <a name="snap-coordinates-to-road"></a>将坐标与道路对齐

在 Azure Maps 中，有几种方法可以将坐标与道路对齐。

-   使用路线方向 API 以沿道路网络将坐标对齐到逻辑路线。
-   使用 Azure Maps Web SDK 将各坐标与矢量图块中最近的道路对齐。
-   直接使用 Azure Maps 矢量图块对齐各个坐标。

使用路线方向 API 对齐坐标

Azure Maps 可以通过使用[路线方向](https://docs.microsoft.com/rest/api/maps/route/postroutedirections) API 将坐标与道路对齐。 此服务可用于在一组坐标之间重建逻辑路线，并且与必应地图对齐道路 API 相差无几。

可以通过两种不同的方法使用路线方向 API 将坐标与道路对齐。

-   如果坐标为 150 个或更少，则可以作为 GET 路线方向 API 中的航点传递。 使用这种方法可以检索两种不同类型的对齐数据；路径指令将包含各个对齐的航点，而路线路径将具有一组用于填充坐标之间的完整路径的内插坐标。
-   如果坐标超过 150 个，可以使用 POST 路线方向 API。 开始坐标和结束坐标必须传递到查询参数中，但所有坐标都可以传递到 POST 请求正文中的 `supportingPoints` 参数，并格式化为点的 GeoJSON 几何集合。 使用此方法的唯一可用的对齐数据将是路线路径，该路径是一组用于填充坐标之间的完整路径的内插坐标。 下面是使用 Azure Maps Web SDK 中的服务模块的此方法的[示例](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path)。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数    | 类似的 Azure Maps API 参数                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` - 将这些点传递到 post 请求的正文中  |
| `interpolate`              | 空值                                                                 |
| `includeSpeedLimit`        | 空值                                                                 |
| `includeTruckSpeedLimit`   | 空值                                                                 |
| `speedUnit`                | 空值                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)            | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。   |
| `userRegion` (`ur`)        | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。   |

Azure Maps 路线 API 还支持同一 API 中的卡车路线参数，以确保计算逻辑路径。 下表对其他必应地图卡车路线参数与 Azure Maps 中的类似 API 参数作了交叉引用。

| 必应地图 API 参数                 | 类似的 Azure Maps API 参数        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N/A - 仅支持以米为单位的尺寸。 |
| `weightUnit` (`wu`)                     | N/A - 仅支持以千克为单位的重量。 |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **不适用**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **不适用**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **不适用**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **不适用**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **不适用**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

由于此方法使用路线方向 API，因此该服务中的完整选项集可用于自定义用于将坐标与道路对齐的逻辑。 例如，指定出发时间将导致历史交通数据被纳入考虑范围。

Azure Maps 路线方向 API 当前不返回速度限制数据，但可以使用 Azure Maps 反向地理编码 API 检索这些数据。

使用 Web SDK 对齐坐标

Azure Maps Web SDK 使用矢量图块来呈现地图。 这些矢量图块包含原始道路几何信息，可用于计算距离坐标最近的道路，以轻松对齐各个坐标。 如果你希望坐标在道路上直观地显示，并且已经在使用 Azure Maps Web SDK 来可视化数据，这非常有用。

但是，此方法只会对齐到在地图视图中加载的路段。 在国家/地区级别缩小时，可能没有道路数据，因此无法进行对齐，但是在该缩放级别，单个像素可以表示几个城市街区的区域，因此不需要对齐。 为了解决这个问题，可以在每次地图移动完成后应用对齐逻辑。 [下面的代码示例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)演示了这一点。

直接使用 Azure 地图矢量图块对齐坐标

Azure Maps 矢量图块包含原始道路几何数据，可用于计算道路上距离某个坐标最近的点，以执行各个坐标的基本对齐。 所有路段都在缩放级别为 15 的扇区中显示，因此你需要从那里检索图块。 然后，可以使用[四叉树图块棱锥数学](https://docs.microsoft.com/azure/azure-maps/zoom-levels-and-tile-grid)来确定需要的图块，并将图块转换为几何图形。 由此，可以使用空间数学库（如 [turf js](http://turfjs.org/) 或 [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)）计算最近的路径段。

## <a name="retrieve-a-map-image-static-map"></a>检索地图图像（静态地图）

Azure Maps 提供一个 API 用于呈现包含叠加数据的静态地图图像。 Azure Maps [地图图像呈现](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API 相当于必应地图中的静态地图 API。

> [!NOTE]
> Azure Maps 要求中心、所有图钉和路径位置都采用 `longitude,latitude` 格式的坐标，而必应地图则使用 `latitude,longitude` 格式。</p>
<p>首先需要对地址进行地理编码。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数  | 类似的 Azure Maps API 参数            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – 指定为 URL 路径的一部分。 目前仅支持 PNG。  |
| `heading`                | N/A - 不支持街景。                |
| `imagerySet`             | `layer` 和 `style` – 请参阅[支持的地图样式](https://docs.microsoft.com/azure/azure-maps/supported-map-styles)文档。   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | 空值                                            |
| `mapSize` (`ms`)         | `width` 和 `height` – 最大大小可为 8192x8192。 |
| `declutterPins` (`dcl`)  | 空值                                            |
| `dpi`                    | 空值                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | 空值                                            |
| `pitch`                  | N/A - 不支持街景。                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/A - 必须使用中心或边界框。     |
| `highlightEntity` (`he`) | 空值                                            |
| `style`                  | 空值                                            |
| 路线参数         | 空值                                            |
| `key`                    | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)          | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。   |
| `userRegion` (`ur`)      | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

> [!NOTE]
> Azure Maps 使用图块系统，其中的图块大小是必应地图中所用地图图块的两倍。 因此，相比必应地图，Azure Maps 中的缩放级别值看上去要更近一个级别。 在要迁移的请求中将缩放级别减 1 即可补偿这种差异。

有关详细信息，请参阅[有关地图图像呈现 API 的操作指南](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data)。

除了能够生成静态地图图像以外，Azure Maps 呈现服务还提供直接访问光栅 (PNG) 和矢量格式的地图图块的功能；

-   [地图图块](https://docs.microsoft.com/rest/api/maps/render/getmaptile) - 检索基础地图的光栅 (PNG) 和矢量图块（道路、边界、背景）。
-   [地图图像图块](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) - 检索航空和卫星图像图块。

### <a name="pushpin-url-parameter-format-comparison"></a>图钉 URL 参数格式比较

**前者：必应地图**

在必应地图中，可以通过在 URL 中使用 `pushpin` 参数将图钉添加到静态地图图像。 `pushpin` 参数采用 `latitude,longitude` 格式的位置、图标样式和文本标签（最多三个字符），如下所示：

> `&pushpin=latitude,longitude;iconStyle;label`

可以通过将其他 `pushpin` 参数添加到包含一组不同值的 URL 来添加更多图钉。 图钉图标样式只能是必应地图 API 中可用的预定义样式之一。

例如，在必应地图中，可使用以下 URL 参数，将标签为“AB”的红色图钉添加到地图中的坐标（经度：-110，纬度：45）：

> `&pushpin=45,-110;7;AB`

<center>

![必应地图静态地图图钉](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，还可以通过在 URL 中指定 `pins` 参数将图钉添加到静态地图图像。 Azure Maps 中的图钉是通过指定图标样式和使用该图标样式的位置列表来定义的。 此信息随后会传递到可以多次指定的 `pins` 参数，以支持采用不同样式的图钉。

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

可以通过将其他 `pins` 参数添加到包含不同样式和位置集的 URL 来使用更多样式。

对于图钉位置，Azure Maps 要求坐标采用 `longitude latitude` 格式，而必应地图采用 `latitude,longitude` 格式。 另请注意，在 Azure Maps 中使用空格而不是逗号来分隔经度和纬度。

`iconType` 值指定要创建的图钉类型，可使用以下值：

-   `default` – 默认的图钉图标。
-   `none` – 不显示图标，只呈现标签。
-   `custom` – 指定要使用的自定义图标。 可将指向图标图像的 URL 添加到图钉位置信息后的 `pins` 参数的末尾。
-   `{udid}` - 存储在 Azure Maps 数据存储平台中的图标的唯一数据 ID (UDID)。

Azure Maps 中添加的图钉样式采用 `optionNameValue` 格式，多个样式以竖线 (`|`) 字符分隔，例如 `iconType|optionName1Value1|optionName2Value2`。 请注意，不用分隔选项名称和值。 可使用以下样式选项名称来为 Azure Maps 中的图钉设置样式：

-   `al` - 指定图钉的不透明度 (alpha)。 可以是 0 到 1 的数字。
-   `an` – 指定图钉定位点。 x 和 y 像素值以 `x y` 格式指定。
-   `co` – 图钉的颜色。 必须是 24 位十六进制颜色：`000000` 到 `FFFFFF`。
-   `la` – 指定标签定位点。 x 和 y 像素值以 `x y` 格式指定。
-   `lc` – 标签的颜色。 必须是 24 位十六进制颜色：`000000` 到 `FFFFFF`。
-   `ls` – 标签的大小（以像素为单位）。 可以是大于 0 的数字。
-   `ro` – 图标的旋转度数值。 可以是 -360 到 360 的数字。
-   `sc` – 图钉图标的比例值。 可以是大于 0 的数字。

需为每个图钉位置指定标签值，而不是使用应用于位置列表中所有图钉的单个标签值。 标签值可以是包含多个字符的字符串，并括在单引号中，以确保不会错误地将它视为样式或位置值。

例如，在 Azure Maps 中，可以使用以下 URL 参数，添加一个具有标签“Space Needle”、定位在坐标（经度：-122.349300，纬度：47.620180）处的图标下 (15 50) 的红色 (`FF0000`)默认图标：

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Azure Maps 静态地图图钉](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

以下示例添加具有标签值“1”、“2”和“3”的三个图钉：

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Azure Maps 静态地图多个图钉](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>绘制曲线 URL 参数格式比较

**前者：必应地图**

在必应地图中，可以通过在 URL 中使用 `drawCurve` 参数将线条和多边形添加到静态地图图像。 `drawCurve` 参数采用形状类型、样式类型以及要在地图上呈现的位置列表，如下所示：

> `&drawCurve=shapeType,styleType,location1,location2...`

可以通过将其他 `drawCurve` 参数添加到包含不同样式和位置集的 URL 来使用更多样式。

必应地图中的位置以 `latitude1,longitude1_latitude2,longitude2_…` 格式指定。 还可以对位置进行编码。

必应地图中的形状类型包括直线、多边形、圆和曲线。 样式类型包括线条颜色、线条粗细、轮廓颜色、填充颜色、轮廓粗细和圆弧半径。

例如，在必应地图中，可以使用以下 URL 参数，将不透明度为 50%、粗细为 4 像素的蓝色线条添加到地图上的坐标（经度：-110，纬度：45）和（经度：-100，纬度：50）之间：

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![必应地图静态地图线条](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**后者：Azure Maps**

在 Azure Maps 中，还可以通过在 URL 中指定 path 参数将线条和多边形添加到静态地图图像。 与必应地图一样，可以在此参数中指定样式和位置列表，并且可以指定 path 参数多次，以呈现采用不同样式的多个圆、线条和多边形。

> `&path=pathStyles||pathLocation1|pathLocation2|...`

关于路径位置，Azure Maps 要求坐标采用 `longitude latitude` 格式，而必应地图采用 `latitude,longitude` 格式。 另请注意，在 Azure Maps 中使用空格而不是逗号来分隔经度和纬度。 Azure Maps 当前不支持编码的路径。 可根据[此文档](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data#get-data-from-azure-maps-data-storage)所述，将较大的数据集作为 GeoJSON 填充内容上传到 Azure Maps 数据存储 API。

Azure Maps 中添加的路径样式采用 `optionNameValue` 格式，多个样式以竖线 (`|`) 字符分隔，例如 `optionName1Value1|optionName2Value2`。 请注意，不用分隔选项名称和值。 可使用以下样式选项名称来为 Azure Maps 中的路径设置样式：

-   `fa` - 呈现多边形时使用的填充颜色不透明度 (alpha)。 可以是 0 到 1 的数字。
-   `fc` - 用于呈现多边形区域的填充颜色。
-   `la` – 在呈现多边形的线条和轮廓时使用的线条颜色不透明 (alpha)。 可以是 0 到 1 的数字。
-   `lc` – 用于呈现多边形的线条和轮廓的线条颜色。
-   `lw` – 线条的宽度（以像素为单位）。
-   `ra` – 指定圆的半径（以米为单位）。

例如，在 Azure Maps 中，可以使用以下 URL 参数，将不透明度为 50%、粗细为 4 像素的蓝色线条添加到地图上的坐标（经度：-110，纬度：45）和（经度：-100，纬度：50）之间：

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Azure Maps 静态地图线条](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>计算距离矩阵

Azure Maps 提供一个 API 用于计算一组的位置之间的行程时间和距离（距离矩阵）。 Azure Maps 距离矩阵 API 相当于必应地图中的距离矩阵 API；

-   [路线矩阵](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)：以异步方式计算一组来源和目标的行程时间与距离。 对于每个请求最多支持 700 个单元（来源数乘以目标数）。 考虑到这一限制，可能的矩阵维度示例如下：`700x1`、`50x10`、`10x10`、`28x25`、`10x70`。

> [!NOTE]
> 只能使用在正文中包含来源和目标信息的 POST 请求向距离矩阵 API 发出请求。</p>
<p>此外，Azure Maps 要求以坐标形式指定所有来源和目标。 首先需要对地址进行地理编码。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数 | 类似的 Azure Maps API 参数                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – 在 POST 请求正文中以 GeoJSON 形式指定。    |
| `destinations`          | `destination` – 在 POST 请求正文中以 GeoJSON 形式指定。 |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | 空值                                                         |
| `distanceUnit`          | N/A - 所有距离（以米为单位）。                              |
| `timeUnit`              | N/A - 所有时间（以秒为单位）。                                 |
| `key`                   | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)         | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。  |
| `userRegion` (`ur`)     | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。     |

> [!TIP]
> Azure Maps 路线 API 中提供的所有高级路线选项（货车路线、引擎规格、避开...）在 Azure Maps 距离矩阵 API 中均受支持。

## <a name="calculate-an-isochrone"></a>计算等时线

Azure Maps 提供了用于计算等时线的 API，这是一个多边形，其中包含一个区域（可在指定的时间内或燃油/电量内从原点沿任意方向前往）。 Azure Maps 路线范围 API 与必应地图中的等时线 API 相差无几；

-   [路线](https://docs.microsoft.com/rest/api/maps/route/getrouterange)范围**：计算一个多边形，其中包含一个区域（可在指定的时间、距离内或可用的燃油/电量内从原点沿任意方向前往）。

> [!NOTE]
> Azure Maps 要求查询原点是坐标。 首先需要对地址进行地理编码。</p>
<p>此外，必应地图可以基于时间或距离计算等时线，而 Azure Maps 可以根据时间、距离或可用的燃油/电量来计算等时线。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数      | 类似的 Azure Maps API 参数            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/A - 所有时间（以秒为单位）。                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N/A - 所有距离（以米为单位）。                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)              | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。  |
| `userRegion` (`ur`)          | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

> [!TIP]
> Azure Maps 路线 API 中提供的所有高级路线选项（货车路线、引擎规格、避开...）在 Azure Maps 等时线 API 中均受支持。

## <a name="search-for-points-of-interest"></a>搜索兴趣点

在必应地图中可以使用以下 API 搜索兴趣点数据：

-   本地搜索：按名称或按实体类型（类别）搜索附近兴趣点（径向搜索）。 Azure Maps [POI 搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)和 [POI 类别搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) API 与此 API 的相似度最高。
-   位置识别：搜索位于某个位置特定距离内的兴趣点。 Azure Maps [附近搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) API 与此 API 相似度最高。
-   本地见解：搜索在指定的最大驾驶时间内或距特定坐标的最大距离内的兴趣点。 借助 Azure Maps，通过首先计算等时线，然后将其传递到[在几何图形中搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) API 中来实现这一点。

Azure Maps 提供多个搜索 API 用于搜索兴趣点：

-   [POI 搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)：按名称搜索兴趣点。 例如，`"starbucks"`。
-   [POI 类别搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)：按类别搜索兴趣点。 例如，“餐厅”。
-   [附近搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)：搜索位于某个位置特定距离内的兴趣点。
-   [模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串（可以是地址、地点、地标、兴趣点或兴趣点类别），并立即处理请求。 对于其用户可以从同一文本框搜索地址或兴趣点的应用程序，建议使用此 API。
-   [在几何图形中搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)：搜索指定几何图形（多边形）中的兴趣点。
-   [沿路线搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute)：搜索指定路线上的兴趣点。
-   [模糊批量搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多 10, 000 个地址、地点、地标或兴趣点的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。

请务必查看[搜索最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)文档。

## <a name="get-traffic-incidents"></a>获取交通事件数据

Azure Maps 提供了几个用于检索交通数据的 API。 可以使用两种类型的交通数据；

-   流量数据 - 提供有关道路各部分交通流量的指标。 这通常用于对道路设置颜色编码。 该数据每 2 分钟更新一次。
-   事件数据 - 提供有关施工、道路封闭、事故和其他可能影响交通的事件的数据。 该数据每分钟更新一次。

必应地图在其交互式地图控件中提供交通流量和事件数据，并将事件数据作为服务提供。

交通数据也整合到 Azure Maps 交互式地图控件中。 Azure Maps 还提供以下交通服务 API；

-   [交通流量段](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowsegment)：提供有关距给定坐标最近的路段的速度和行程时间的信息。
-   [交通流量图块](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile)：提供包含交通流量数据的光栅和矢量图块。 这些图块可以与 Azure Maps 控件或第三方地图控件（如 Leaflet）结合使用。 矢量图块还可用于高级数据分析。
-   [交通事件详细信息](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)：提供边界框、缩放级别和交通模型内的交通事件详细信息。
-   [交通事件图块](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile)：提供包含交通事件数据的光栅和矢量图块。
-   [交通事件视区](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentviewport)：检索请求中所述视区的法律和技术信息，如交通模型 ID。

下表交叉引用必应地图交通 API 参数与 Azure Maps 中的类似交通事件详细信息 API 参数。

| 必应地图 API 参数  | 类似的 Azure Maps API 参数   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` 和 `boundingZoom`      |
| `includeLocationCodes`   | 空值                                   |
| `severity` (`s`)         | N/A - 返回的所有数据               |
| `type` (`t`)             | N/A - 返回的所有数据               |
| `key`                    | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)          | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。 |
| `userRegion` (`ur`)      | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。 |

## <a name="get-a-time-zone"></a>获取时区

Azure Maps 提供一个 API 用于检索坐标所在的时区。 Azure Maps 时区 API 相当于必应地图中的时区 API；

-   [按坐标获取时区信息](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates)：指定一个坐标，并获取其所在时区的详细信息。

下表对必应地图 API 参数与 Azure Maps 中的类似 API 参数做了交叉比较。

| 必应地图 API 参数 | 类似的 Azure Maps API 参数          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/A - 必须先对位置进行地理编码。      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N/A - 始终由 Azure Maps 包含在响应中。 |
| `key`                   | `subscription-key` – 另请参阅[使用 Azure Maps 进行身份验证](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication)文档。 |
| `culture` (`c`)         | `language` – 请参阅[支持的语言](https://docs.microsoft.com/azure/azure-maps/supported-languages)文档。  |
| `userRegion` (`ur`)     | `view` - 请参阅[支持的视图](https://aka.ms/AzureMapsLocalizationViews)文档。  |

除此之外，Azure Maps 平台还提供其他许多时区 API 来帮助对时区名称和 ID 进行转换；

-   [按 ID 获取时区信息](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)：返回指定 IANA 时区 ID 的当前、历史和将来时区信息。
-   [时区枚举 IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana)：返回 IANA 时区 ID 的完整列表。 对 IANA 服务所做的更新在一天内即会反映在系统中。 
-   [时区枚举 Windows](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows)：返回 Windows 时区 ID 的完整列表。
-   [时区 IANA 版本](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)：返回 Azure Maps 使用的当前 IANA 版本号。 
-   [与 Windows 时区对应的 IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)：在指定有效 Windows 时区 ID 的情况下，返回对应的 IANA ID。 可返回单个 Windows ID 的多个 IANA ID。

## <a name="spatial-data-services-sds"></a>空间数据服务 (SDS)

必应地图中的空间数据服务提供三个关键功能：

-   批处理地理编码 - 使用单个请求处理大量地址地理编码。
-   检索管理边界数据 - 使用坐标并获取指定实体类型的相交边界。
-   托管和查询空间业务数据 - 上传一个简单的 2D 数据表，并使用一些简单的空间查询访问它。

### <a name="batch-geocode-data"></a>批处理地理编码数据

批处理地理编码是处理大量地址或位置，在单个请求中将它们全部传递到服务，并行进行地理编码，并在单个响应中返回结果的过程。

必应地图支持在单个批处理地理编码请求中传递多达 200,000 个地址。 此请求进入队列，通常在一段时间（从几分钟到几个小时不等）内被处理，具体取决于数据集的大小和服务的负载。 请求中的每个地址都生成了一个事务。

Azure Maps 具有批处理地理编码服务，但它支持在单个请求中传递多达 10,000 个地址，并且根据数据集的大小和服务的负载，在几秒钟到几分钟内处理。 请求中的每个地址都生成了一个事务。 在 Azure Maps 中，批处理地理编码服务仅可用于 S1 层。

使用 Azure Maps 对大量地址进行地理编码的另一个方式是向标准搜索 API 发出并行请求。 这些服务对于每个请求只接受一个地址，但可以与 S0 层一起使用，S0 层也提供免费使用限制。 S0 层支持从单个帐户每秒最多向 Azure Maps 平台发出 50 个请求。 因此，如果你处理限制以保持在该限制内，则每小时可以对多达 180,000 个地址进行地理编码。 S1 层对可以从帐户进行的每秒查询数没有记录的限制，因此在使用该定价层时可以更快地处理更多数据，但是使用批处理地理编码服务将有助于减少传输的数据总量，并大大减少网络流量。

-   [自由格式的地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)：指定单个地址字符串（如 `"1 Microsoft way, Redmond, WA"`）并立即处理请求。 如果需要快速对各个地址进行地理编码，我们建议使用此服务。
-   [结构化地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)：指定单个地址的各个部分（例如街道名称、城市、国家/地区和邮政编码），并立即处理请求。 如果需要快速对单个地址进行地理编码，并且数据已分析成各个地址组成部分，则我们建议使用此服务。
-   [批量地址地理编码](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)：创建最多包含 10,000 个地址的请求，并在一段时间内对其进行处理。 所有地址将在服务器上同时进行地理编码，完成后，可以下载已完成的结果集。 若要对大型数据集进行地理编码，建议使用此服务。
-   [模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)：此 API 将地址地理编码与兴趣点搜索组合在一起。 此 API 采用自由格式的字符串（可以是地址、地点、地标、兴趣点或兴趣点类别），并立即处理请求。 对于其用户可以从同一文本框搜索地址或兴趣点的应用程序，建议使用此 API。
-   [模糊批量搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)：创建包含最多 10, 000 个地址、地点、地标或兴趣点的请求，并在一段时间内对其进行处理。 所有数据将在服务器上同时进行处理，完成后，可以下载已完成的结果集。

### <a name="get-administrative-boundary-data"></a>获取管理边界数据

在必应地图中，通过 Geodata API 提供国家/地区、州、县、城市和邮政编码的管理边界。 此 API 采取坐标或查询进行地理编码。 如果将查询传入，查询会被地理编码，并使用第一个结果中的坐标。 此 API 会采用这些坐标并检索与坐标相交的指定实体类型的边界。 请注意，此 API 不一定返回传入的查询的边界。 如果传入了 `"Seattle, WA"` 的查询，但实体类型值设置为国家/地区，则将返回美国的边界。

Azure Maps 还提供对管理边界（国家、州、县、城市和邮政编码）的访问。 若要检索边界，必须为所需的边界（即 `Seattle, WA`）查询其中一个搜索 API。 如果搜索结果具有关联的边界，将在结果响应中提供几何 ID。 然后，可以使用搜索多边形 API 检索一个或多个几何 ID 的确切边界。 这与必应地图有点不同，因为 Azure Maps 返回所搜索内容的边界，而必应地图返回指定坐标处指定实体类型的边界。 此外，Azure Maps 返回的边界数据采用 GeoJSON 格式。

概括而言：

1.  将要接收的边界的查询传递到以下搜索 API 之一。
    -   [自由格式的地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
    -   [结构化地址地理编码](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)
    -   [批量地址地理编码](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [模糊搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
    -   [模糊批量搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  如果所需的结果具有几何 ID，请将其传递到[搜索多边形 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)。

### <a name="host-and-query-spatial-business-data"></a>托管和查询空间业务数据

必应地图中的空间数据服务提供了一个简单的空间数据存储解决方案，用于托管业务数据，并将其公开为空间 REST 服务。 此服务提供四种主要查询：按属性查找，在附近查找，在边界框中查找，以及在路线 1 英里内查找。 许多使用此服务的公司通常已经将其业务数据存储在某处的数据库中，并且一直在将其中一小部分上传到此服务中，以支持存储定位器等应用程序。 由于基于密钥的身份验证提供基本安全性，因此建议此服务仅用于面向公众的数据。

大多数业务位置数据的起点是数据库。 因此，建议使用现有的 Azure 存储解决方案，如 Azure SQL 或 Azure PostgreSQL（使用 PostGIS 插件）。 这两种存储解决方案都支持空间数据，并提供一组丰富的空间查询功能。 一旦数据位于合适的存储解决方案中，则可以通过创建自定义 Web 服务或使用框架（如 ASP.NET 或实体框架）将其集成到应用程序中。 此方法提供了更多的查询功能以及更高的安全选项。

Azure Cosmos DB 还提供一组有限的空间功能，这些功能可能够用了（具体取决于你的场景）。

以下是有关在 Azure 中托管和查询空间数据的一些有用资源。

-   [Azure SQL 空间数据类型概述](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview)
-   [ - 查询最近的邻近区域](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Azure Cosmos DB 地理空间功能概述](https://docs.microsoft.com/azure/cosmos-db/geospatial)

## <a name="client-libraries"></a>客户端库

Azure Maps 为以下编程语言提供客户端库；

-   JavaScript、TypeScript、Node.js – [文档](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module) \| [NPM 包](https://www.npmjs.com/package/azure-maps-rest)

其他编程语言的开源客户端库；

-   .NET Standard 2.0 – [GitHub 项目](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet 包](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps REST 服务。

> [!div class="nextstepaction"]
> [有关使用搜索服务的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [有关使用路由服务的最佳做法](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [如何使用服务模块 (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Azure Maps REST 服务 API 参考文档](https://docs.microsoft.com/rest/api/maps/)

> [!div class="nextstepaction"]
> [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
