---
title: Azure Maps 概述 | Microsoft Docs
description: Azure Maps 简介
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: dbcb0e87159efc7cc33101cdd5eee55afbdf3c10
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516636"
---
# <a name="what-is-azure-maps"></a>什么是 Azure Maps？

Azure Maps 是一系列地理空间服务，它使用全新地图绘制数据为 Web 和移动应用程序提供准确的地理环境。 Azure Maps 具有以下功能：

* 提供以多种样式和卫星图像呈现地图的 REST API。
* 搜索世界各地的地址、地点和兴趣点。
* 提供两地路线和多地点路线，优化多地点路线，提供等时线、商务车、受影响的交通和路网路线；查看交通流量和事故。
* 提供用于请求公共交通和其他交通模式（例如共享单车、共享摩托车和共享汽车）以及实时规划路线的移动服务。 
* 通过地理位置建立用户位置，并将位置转换为时区。 
* 提供地理围栏和地图数据存储服务，其中的位置信息存储在 Azure 中。 
* 通过地理空间分析进行智能定位。 

除了 REST API 之外，还可以通过 Web SDK 或 Android SDK 提供 Azure Maps 服务。 这些工具可以帮助开发人员快速开发和缩放将位置信息集成到 Azure 解决方案中的解决方案。 

可以注册获取免费的 [Azure Maps 帐户](https://azure.microsoft.com/services/azure-maps/)并开始开发。

以下视频深入介绍了 Azure Maps：

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>地图控件

### <a name="web-sdk"></a>Web SDK

Azure Maps Web SDK 允许你使用自己的内容和图像自定义交互式地图，以便在 Web 或移动应用程序中显示。 此控件使用 WebGL，因此可以呈现大型数据集，同时保持高性能。 使用 JavaScript 或 TypeScript 通过此 SDK 进行开发。

![人口变化的地图示例](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

使用 Azure Maps Android SDK 创建移动地图绘制应用程序。 

![移动设备上的地图示例](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure Maps 中的服务

Azure Maps 包含以下九个可在 Azure 应用程序中提供地理上下文的服务。

### <a name="data-service"></a>数据服务

数据对于地图至关重要。 使用数据服务上传和存储地理空间数据（供空间操作使用）或图像组成部分。  使客户数据靠近 Azure Maps 服务可以降低延迟、提高工作效率，并在应用程序中创建新方案。 有关此服务的详细信息，请访问[数据服务 API 文档](https://docs.microsoft.com/rest/api/maps/data)。

### <a name="mobility-service"></a>移动服务

Azure Maps 移动服务可以实现实时行程规划。 它返回最佳可能的路线选项，并提供各种出行模式。 对于地铁（城市）区域，这些模式可能包括步行、单车和公共交通。 开发人员可以请求交通路线的详细信息（例如，线路几何图形、站点列表、预计和实时到达时间以及服务警报）。

该服务还可以搜索特定的对象类型，如位置周围的共享单车、摩托车或汽车。 用户可以请求查看最近的停车点还有多少可用的共享单车。 他们可以搜索可用的共享汽车和查找未来供应情况和当前燃油水平等详细信息。

若要了解有关服务的更多信息，请参阅[移动 API 文档](https://docs.microsoft.com/rest/api/maps/mobility)。

### <a name="render-service"></a>呈现服务

呈现服务帮助开发人员创建与地图绘制有关的 Web 和移动应用程序。 该服务使用有 19 个缩放级别的高品质光栅图形图像或完全可自定义的矢量格式的地图图像。

![呈现服务中的地图示例](media/about-azure-maps/Introduction_Map.png)

呈现服务现在提供预览版 API，使开发人员能够使用卫星图像。 有关更多详细信息，请阅读[呈现 API 文档](https://docs.microsoft.com/rest/api/maps/render)。

### <a name="route-service"></a>路线服务

“路线”服务包含实际基础结构的可靠几何计算，以及多种运输模式的指示。 通过此服务，开发人员可以计算大量出行模式（例如汽车、卡车、自行车或步行）的指示。 此服务还考虑流量条件、重量限制或危险材料运输等因素。

![路线服务中的地图示例](media/about-azure-maps/Introduction_Route.png)

路线服务可以预览高级功能，例如： 

* 批处理多个路线请求。
* 提供一组起点和目的地之间的出行时间和距离表格。
* 根据时间或燃料需求，找出用户可以行驶的路线或距离。 

有关路线功能的详细信息，请阅读[路线 API 文档](https://docs.microsoft.com/rest/api/maps/route)。

### <a name="search-service"></a>搜索服务

搜索服务帮助开发人员按名称或类别搜索地址、地点、商家列表和其他地理信息。 搜索服务能根据纬度和经度将地址和十字路口进行[反向地理编码](https://en.wikipedia.org/wiki/Reverse_geocoding)。

![地图上的搜索示例](media/about-azure-maps/Introduction_Search.png)

搜索服务还提供高级功能，例如：

* 沿路线搜索。
* 在更大的区域内搜索。
* 批处理一组搜索请求。
* 搜索更大的区域，而不是定位点。 

用于批处理和区域搜索的 API 目前以预览版提供。 有关搜索功能的详细信息，请阅读[搜索 API 文档](https://docs.microsoft.com/rest/api/maps/search)。

### <a name="spatial-operations-service"></a>空间操作服务

Azure Maps 空间操作服务获取位置信息并对其进行即时分析，以帮助客户了解某时某地正在发生的事件。 它可以实现近乎实时地分析事件并对事件进行预测性建模。 

该服务使客户可以借助一系列常用的地理空间数学计算（包括最近点、大圆距离和缓冲区）来增强其智能定位能力。 若要详细了解该服务和各种功能，请阅读[空间操作 API 文档](https://docs.microsoft.com/rest/api/maps/spatial)。

### <a name="time-zone-service"></a>时区服务

通过时区服务可以使用纬度/经度对或 [IANA ID](https://www.iana.org/) 来查询当前的、历史的和将来的时区信息。 通过时区服务还可以实现：

* 将 Microsoft Windows 时区 ID 转换为 IANA 时区。
* 提取与 UTC 的时区偏移量。
* 获取时区中的当前时间。 

查询时区服务的典型 JSON 响应如下例所示：

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

有关此服务的详细信息，请阅读[时区 API 文档](https://docs.microsoft.com/rest/api/maps/timezone)。

### <a name="traffic-service"></a>交通服务

交通服务是一套 Web 服务，开发人员可用于创建交通所需的 Web 和移动应用程序。 该服务提供两种数据类型：

* 交通流量：在路网中对所有关键道路进行实时观察获得的车速和行驶时间。
* 交通事故：有关路网周围的交通堵塞和事故的最新视图。

![含交通信息的地图示例](media/about-azure-maps/Introduction_Traffic.png)

有关详细信息，请参阅[交通 API 文档](https://docs.microsoft.com/rest/api/maps/traffic)。

### <a name="ip-to-location-service"></a>IP to Location 服务

使用 IP to Location 服务预览检索的 IP 地址的双字母国家/地区代码。 此服务可基于地理位置通过提供自定义的应用程序内容帮助定制和增强用户体验。

有关 IP to Location 服务的 REST API 的详细信息，请阅读 [Azure Maps 地理位置 API 文档](https://docs.microsoft.com/rest/api/maps/geolocation)。

## <a name="programming-model"></a>编程模型

Azure Maps 针对移动设备设计，可帮助你开发跨平台应用程序。 它使用语言无关的编程模型，并通过 [REST API](https://docs.microsoft.com/rest/api/maps/) 支持 JSON 输出。

此外，Azure Maps 还提供一个简单的编程模型和方便的 [JavaScript 地图控件](https://docs.microsoft.com/javascript/api/azure-maps-control)，用于快速轻松地开发 Web 和移动应用程序。

## <a name="usage"></a>使用情况

若要访问 Azure Maps 服务，只需转到 [Azure 门户](https://portal.azure.com)并创建一个 Azure Maps 帐户即可。

Azure Maps 使用基于密钥的身份验证方案。 你的帐户将附带已为你生成的两个密钥。 通过使用任一密钥并向 Azure Maps 服务发出请求，开始将这些位置功能集成到你的应用程序。

## <a name="supported-regions"></a>支持的区域

Azure Maps API 目前在除以下区域外的所有国家/地区都可用：

* 中国
* 韩国

确认当前 IP 地址的位置不在上面所列的某个不受支持国家/地区。

## <a name="next-steps"></a>后续步骤

试用展示 Azure Maps 的示例应用：

> [!div class="nextstepaction"]
> [快速入门：创建 Web 应用](quick-demo-map-app.md)

随时掌握 Azure Maps 的最新信息： 

> [!div class="nextstepaction"]
> [Azure Maps 博客](https://azure.microsoft.com/blog/topics/azure-maps/)
