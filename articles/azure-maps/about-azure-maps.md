---
title: Microsoft Azure Maps 概述
description: 了解 Microsoft Azure Maps 中的服务和功能，以及如何在应用程序中使用这些服务和功能。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/31/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 11a0ddc5c7b297d0700e6fd07d60f8efe0e55a8f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91285326"
---
# <a name="what-is-azure-maps"></a>什么是 Azure Maps？

Azure Maps 是一系列地理空间服务和 SDK，它使用全新地图绘制数据为 Web 和移动应用程序提供地理上下文。 Azure Maps 具有以下功能：

* REST API，用于以多种样式和卫星图像呈现矢量地图和光栅地图。
* Creator 服务可根据专用室内地图数据创建和呈现地图。
* 搜索服务，用于查找全世界的地址、地点和兴趣点。
* 各种路线规划选项，例如地点到地点、多地点、多地点优化、等时线、电动车、商务车、受影响的交通，以及路网路线。
* 交通情况视图和事故视图，适用于需要实时交通信息的应用程序。
* 出行服务，用于通过结合不同的出行模式和实时到达时间来请求公共交通信息和规划路线。
* 时区和地理位置服务。
* 地理围栏和地图数据存储，其中的位置信息托管在 Azure 中。
* 通过地理空间分析进行智能定位。

此外，还可以通过 Web SDK 和 Android SDK 提供 Azure Maps 服务。 这些工具可以帮助开发人员快速开发和缩放将位置信息集成到 Azure 解决方案中的解决方案。

可以注册获取免费的 [Azure Maps 帐户](https://azure.microsoft.com/services/azure-maps/)并开始开发。

以下视频深入介绍了 Azure Maps：

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>地图控件

### <a name="web-sdk"></a>Web SDK

Azure Maps Web SDK 允许你使用自己的内容和图像自定义交互式地图。 可以将此交互式地图用于 Web 或移动应用程序。 地图控件使用 WebGL，因此可以呈现大型数据集，同时保持高性能。 你可以使用 JavaScript 或 TypeScript 通过此 SDK 进行开发。

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="使用 Azure Maps Web SDK 创建的人口变化地图示例":::

### <a name="android-sdk"></a>Android SDK

使用 Azure Maps Android SDK 创建移动地图绘制应用程序。

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="使用 Azure Maps Web SDK 创建的人口变化地图示例":::

## <a name="services-in-azure-maps"></a>Azure Maps 中的服务

Azure Maps 包含以下可在 Azure 应用程序中提供地理上下文的服务。

### <a name="data-service"></a>数据服务

数据对于地图至关重要。 使用数据服务上传和存储地理空间数据，以用于空间操作或图像合成。  使客户数据靠近 Azure Maps 服务可以降低延迟、提高工作效率，并在应用程序中创建新方案。 有关此服务的详细信息，请访问[数据服务文档](https://docs.microsoft.com/rest/api/maps/data)。

### <a name="geolocation-service"></a>地理位置服务

使用地理位置服务预览检索的 IP 地址的双字母国家/地区代码。 此服务可基于地理位置通过提供自定义的应用程序内容帮助你增强用户体验。

有关更多详细信息，请参阅[地理位置服务文档](https://docs.microsoft.com/rest/api/maps/geolocation)。

### <a name="mobility-service"></a>移动服务

Azure Maps 出行服务缩短了具有公共交通功能（例如公交路线规划和搜索附近的公交站点）的应用程序的开发时间。 用户可以检索有关公交站点、线路和时刻表的详细信息。 通过出行服务，用户还可以检索停靠站点和线路的几何形状，停靠站点、线路和服务区的警报以及实时的公交到达时间和服务警报。 此外，出行服务还提供具有多式联运行程规划选项的路线规划功能。 多式联运行程规划在一个行程中融合了步行、骑行和公共交通选项。 用户还可以访问详细的多式联运分步行程。

若要详细了解服务，请参阅[出行服务文档](https://docs.microsoft.com/rest/api/maps/mobility)。

### <a name="render-service"></a>呈现服务

现处于预览阶段的[呈现服务 V2](https://docs.microsoft.com/rest/api/maps/renderv2) 引入了 [Get Map Tile V2 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 的新版本。 现在，客户可以使用 Get Map Tile V2 API 请求 Azure Maps 道路图块、天气图块或使用 Azure Maps Creator 创建的地图图块。 建议使用新的 Get Map Tile V2 API。  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="使用 Azure Maps Web SDK 创建的人口变化地图示例":::

有关更多详细信息，请参阅[呈现服务 V2 文档](https://docs.microsoft.com/rest/api/maps/renderv2)。

若要详细了解现提供 GA（正式发布版）的呈现服务 V1，请参阅[呈现服务 V1 文档](https://docs.microsoft.com/rest/api/maps/render)。  

### <a name="route-service"></a>路线服务

路线服务可用于计算每个请求路线的估计到达时间 (ETA)。 路线 API 考虑多种因素，例如实时交通信息和历史交通数据（如请求的星期几某一时间的典型道路速度）。 这些 API 会根据时间或距离依次或按最佳顺序返回可用于多个目的地的最短或最快路线。 使用该服务，开发人员可以计算跨几种出行模式（例如汽车、卡车、自行车、步行或电动车）的方向。 该服务还会考虑出发时间、重量限制或危险品运输等因素。

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="使用 Azure Maps Web SDK 创建的人口变化地图示例":::

路线服务提供高级设置功能，例如：

* 批处理多个路线请求。
* 提供一组起点和目的地之间的出行时间和距离表格。
* 根据时间或燃料需求，找出用户可以行驶的路线或距离。

有关路线规划功能的详细信息，请参阅[路线服务文档](https://docs.microsoft.com/rest/api/maps/route)。

### <a name="search-service"></a>搜索服务

搜索服务帮助开发人员按名称或类别搜索地址、地点、商家列表和其他地理信息。 此外，服务可以根据纬度和经度对地址和十字路口进行[反向地理编码](https://en.wikipedia.org/wiki/Reverse_geocoding)。

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="使用 Azure Maps Web SDK 创建的人口变化地图示例":::

搜索服务还提供高级功能，例如：

* 沿路线搜索。
* 在更大的区域内搜索。
* 批处理一组搜索请求。
* 根据品牌名称搜索电动车充电站和兴趣点 (POI) 数据。

有关搜索功能的更多详细信息，请参阅[搜索服务文档](https://docs.microsoft.com/rest/api/maps/search)。

### <a name="spatial-service"></a>空间服务

空间服务可以快速分析位置信息，以帮助客户了解某时某地正在发生的事件。 它可以实现近乎实时地分析事件并对事件进行预测性建模。

该服务使客户可以借助一系列常用的地理空间数学计算来增强其智能定位能力。 常用计算包括最近点、大圆圈航线距离和缓冲区。 若要详细了解服务和各种功能，请参阅[空间服务文档](https://docs.microsoft.com/rest/api/maps/spatial)。

### <a name="timezone-service"></a>时区服务

使用时区服务，你可以查询当前的、历史的和将来的时区信息。 可使用经纬度对或 [IANA ID](https://www.iana.org/) 用作输入。 通过时区服务还可以实现以下操作：

* 将 Microsoft Windows 时区 ID 转换为 IANA 时区。
* 提取与 UTC 的时区偏移量。
* 获取选定时区的当前时间。

查询时区服务的典型 JSON 响应如下例所示：

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

有关此服务的详细信息，请参阅[时区服务文档](https://docs.microsoft.com/rest/api/maps/timezone)。

### <a name="traffic-service"></a>交通服务

交通服务是一套 Web 服务，开发人员可将其用于需要交通信息的 Web 或移动应用程序。 该服务提供两种数据类型：

* 交通流量：在路网中对所有关键道路进行实时观察获得的车速和行驶时间。
* 交通事故：有关路网周围的交通堵塞和事故的最新视图。

![含交通信息的地图示例](media/about-azure-maps/intro_traffic.png)

有关详细信息，请参阅[交通服务文档](https://docs.microsoft.com/rest/api/maps/traffic)。

### <a name="weather-service"></a>天气服务

天气服务提供了 API，开发人员可以使用这些 API 来检索特定位置的天气信息。 天气信息包含以下详细信息：观测日期和时间、天气状况的简要说明、天气图标、降水量指示标志、温度和风速信息。 还会返回 RealFeel™ 温度和 UV 指数等其他详细信息。

开发人员可以使用[获取沿路天气 API](https://docs.microsoft.com/rest/api/maps/weather/getweatheralongroutepreview) 来检索沿特定路线的天气信息。 此外，该服务还支持为受洪水或暴雨等天气灾害影响的中途点生成天气通知。

使用 [Get Map Tile V2 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)，你可以请求过去、当前和将来的雷达图块和卫星图块。

![带有实时天气雷达图块的地图示例](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service"></a>地图创建者服务

Maps Creator 服务是一套 Web 服务，开发人员可以使用这些服务基于室内地图数据创建具有地图功能的应用程序。

Maps Creator 提供三个核心服务：

* [数据集服务](https://docs.microsoft.com/rest/api/maps/dataset)。 使用数据集服务，基于转换后的绘图包数据创建数据集。 有关绘图包要求的信息，请参阅绘图包要求。

* [转换服务](https://docs.microsoft.com/rest/api/maps/dataset)。 使用转换服务将 DWG 设计文件转换为室内地图的绘图包数据。

* [图块集服务](https://docs.microsoft.com/rest/api/maps/tileset)。 使用图块集服务可以创建数据集的基于向量的表示形式。 应用程序可以使用图块集来呈现基于视觉对象图块的数据集视图。

* [功能状态服务](https://docs.microsoft.com/rest/api/maps/featurestate)。 使用功能状态服务来支持动态地图样式。 动态定位样式允许应用程序在 IoT 系统提供的空间上反映实时事件。

* [WFS 服务](https://docs.microsoft.com/rest/api/maps/featurestate)。 使用 WFS 服务查询室内地图数据。 WFS 服务遵循[开放地理空间信息联盟 API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) 标准来查询单个数据集。

## <a name="programming-model"></a>编程模型

Azure Maps 针对移动设备设计，可帮助你开发跨平台应用程序。 它使用语言无关的编程模型，并通过 [REST API](https://docs.microsoft.com/rest/api/maps/) 支持 JSON 输出。

此外，Azure Maps 还通过简单的编程模型提供了一个便利的 [JavaScript 地图控件](https://docs.microsoft.com/javascript/api/azure-maps-control)。 Web 和移动应用程序的开发都快速而简单。

## <a name="power-bi-visual"></a>Power BI 视觉对象

Power BI 的 Azure Maps 视觉对象提供了一组丰富的数据可视化效果，适用于地图上的空间数据。 据估计，超过 80% 的业务数据具有位置上下文。 Azure Maps 视觉对象提供了一个无代码解决方案，可用于深入了解此位置上下文与业务数据有何关系以及对业务数据有何影响。

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="使用 Azure Maps Web SDK 创建的人口变化地图示例":::

有关详细信息，请参阅 [Azure Maps Power BI 视觉对象入门](power-bi-visual-getting-started.md)文档。

## <a name="usage"></a>使用情况

若要访问 Azure Maps 服务，请转到 [Azure 门户网站](https://portal.azure.com)并创建 Azure Maps 帐户。

Azure Maps 使用基于密钥的身份验证方案。 创建帐户时，将生成两个密钥。 若要验证 Azure Maps 服务，可以使用其中任一密钥。

注意：Azure Maps 将与第三方 TomTom 共享客户提供的地址/位置查询（简称“查询”），以实现地图功能。 与 TomTom 共享时，查询不会链接到任何客户或最终用户，也不能用于识别个人。 与 Moovit 和 AccuWeather 集成的出行服务和天气服务目前提供[预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Microsoft 目前正在向“联机服务分包商”列表添加 TomTom、Moovit 和 AccuWeather。

## <a name="supported-regions"></a>支持的区域

除以下国家外，当前在所有国家/地区提供 Azure Maps 服务：

* 中国
* 韩国

验证当前 IP 地址的位置是否位于受支持的国家/地区。

## <a name="next-steps"></a>后续步骤

试用展示 Azure Maps 的示例应用：

[快速入门：创建 Web 应用](quick-demo-map-app.md)

随时掌握 Azure Maps 的最新信息：

[Azure Maps 博客](https://azure.microsoft.com/blog/topics/azure-maps/)
