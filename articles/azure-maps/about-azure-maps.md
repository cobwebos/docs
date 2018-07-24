---
title: Azure Maps 概述 | Microsoft Docs
description: Azure Maps 简介
author: dsk-2015
ms.author: dkshir
ms.date: 07/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 93fe8dc3f8ff991cd6c48923d9e2073e4e93f1ad
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040841"
---
# <a name="what-is-azure-maps"></a>什么是 Azure Maps？
Azure Maps 是以全新地图数据支撑的一系列地理空间服务，可让你在 Web 和移动应用程序中提供准确的地理上下文。 它包含用于在定位服务中呈现地图以及搜索兴趣点、兴趣点路线、交通状况、时区和 IP 的 REST API。 可以结合熟悉的工具使用这些 API，快速开发和缩放将位置信息集成到 Azure 解决方案中的解决方案。 除 REST API 以外，Azure Maps 还提供一个基于 Web 的 JavaScript 控件，实现简单、灵活且可移植的跨媒体开发。 

以下视频深入介绍了 Azure Maps：

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="services-in-azure-maps"></a>Azure Maps 中的服务

Azure Maps 包含以下六个可在 Azure 应用程序中提供地理上下文的服务。 

### <a name="render-service"></a>呈现服务

“呈现”服务专为开发人员而设计，用于创建与映射有关的 Web 和移动应用程序。 该服务使用有 19 个缩放级别的高品质光栅图形图像或完全可自定义的矢量格式的地图图像。

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

呈现服务现在提供预览版 API，使开发人员能够使用卫星图像。 有关更多详细信息，请参阅 [Azure Maps 呈现 API](https://docs.microsoft.com/rest/api/maps/render)。


### <a name="route-service"></a>路线服务 

“路线”服务包含实际基础结构的可靠几何计算，以及多种运输模式的指示。 通过此服务，开发人员可以计算大量出行模式（例如汽车、卡车、自行车或步行）的指示。 此服务还考虑流量条件、重量限制或危险材料运输等因素。

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

“路线”服务现在提供高级功能预览版，例如，批处理多个路线请求、行程时间矩阵、一组出发地与目的地之间的距离，以及根据时间或燃料要求查找可以行驶的路线或距离。 有关路线功能的详细信息，请参阅 [Azure Maps 路线 API](https://docs.microsoft.com/rest/api/maps/route)。


### <a name="search-service"></a>搜索服务

“搜索”服务旨在让开发人员按名称或类别搜索地址、地点、商家列表和其他地理信息。 “搜索”服务还能根据纬度和经度[反向地理编码](https://en.wikipedia.org/wiki/Reverse_geocoding)地址和十字路口。 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

“搜索”服务也提供高级功能，例如，沿路线搜索、在更广泛的区域中搜索、批处理一组搜索请求，以及搜索较大区域而不是定位点。 用于批处理和区域搜索的 API 目前以预览版提供。 有关搜索功能的详细信息，请参阅 [Azure Maps 搜索 API](https://docs.microsoft.com/rest/api/maps/search) 页。


### <a name="time-zone-service"></a>时区服务

“时区”服务允许你使用纬度-经度对或 [IANA ID](http://www.iana.org/) 来查询当前、历史和将来的时区信息。 时区服务还允许将 Microsoft Windows 时区 ID 转换为 IANA 时区，提取到 UTC 的时区偏移以及获取相应时区中的当前时间。 查询时区服务的典型 JSON 响应如下例所示：

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

有关此服务的详细信息，请访问 [Azure Maps 时区 API](https://docs.microsoft.com/rest/api/maps/timezone) 页。

### <a name="traffic-service"></a>交通服务

“交通”服务是一套 Web 服务，旨在让开发人员创建交通所需的 Web 和移动应用程序。 该服务提供两种数据类型：
    * 交通流量 - 路网中所有关键道路的实时观察速度和行驶时间。 
    * 交通事件 - 有关路网周围的交通堵塞和事故的准确视图。

![Azure Maps 流量](media/about-azure-maps/Introduction_Traffic.png)

有关更多详细信息，请访问 [Azure Maps 交通 API](https://docs.microsoft.com/rest/api/maps/traffic) 页。

### <a name="ip-to-location"></a>定位 IP

“定位 IP”是一个预览版服务，用于检索给定 IP 地址的双字母国家/地区代码。 此服务可帮助你根据特殊的地缘政治约束定制应用程序，以及通过基于地理位置更改应用程序内容来增强用户体验。 


## <a name="programming-model"></a>编程模型

Azure Maps 针对移动设备设计，可为跨平台应用程序提供支持。 它使用语言无关的编程模型，支持通过 [REST API](https://docs.microsoft.com/rest/api/maps/) 返回 JSON 输出。 

此外，Azure Maps 还提供一个简单的编程模型和方便的 [JavaScript 地图控件](https://docs.microsoft.com/javascript/api/azure-maps-javascript/?view=azure-iot-typescript-latest)，用于快速轻松地开发 Web 和移动应用程序。 


## <a name="usage"></a>使用情况

若要访问 Maps 服务，只需导航到 [Azure 门户](http://portal.azure.com)并创建一个 Azure Maps 帐户即可。 

Azure Maps 使用基于密钥的身份验证方案。 你的帐户将附带为你预生成的两个密钥。 首先将这些位置功能直接集成到你的应用程序，方法是使用请求 Azure Maps 服务的任一密钥。

## <a name="supported-regions"></a>支持的区域
只有以下国家/地区尚未推出 Azure Maps API： 

* 阿根廷
* 中国
* 印度
* 摩洛哥
* 巴基斯坦
* 韩国

请检查当前的 IP 地址，并确认该 IP 地址的位置不在上面所列的某个不受支持国家/地区。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Maps 新功能的详细信息： 
    - [路线矩阵、等时线、IP 查找等](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)。 
- 请继续尝试学习展示该服务的示例应用
    - [启动演示交互式搜索地图](quick-demo-map-app.md)
