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
ms.openlocfilehash: 8092cd169f93a6815e52517d805941ac7ddcbbc0
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807512"
---
# <a name="what-is-azure-maps"></a>什么是 Azure Maps？

Azure Maps 是通过可用的全新地图数据解锁的一系列地理空间服务，为 Web 和移动应用程序提供准确的地理上下文。 Azure Maps 包含多个 REST API，用于以多种样式和卫星成像来呈现**地图**；在全球**搜索**地址、地点和兴趣点；**路线**功能，其中包括点到点路线、多点路线、多点优化、等时路线、商用车路线、交通受影响的路线，以及矩阵路线；查看行业领先的交通流量和交通事件；**移动服务**，可用于请求公共交通、单车共享、滑板车共享和汽车共享信息，以利用备选的交通方式和实时数据规划路线；通过**地理定位**功能确定用户位置；将位置转换为**时区**；以及获取某个位置的时间。 另外，Azure Maps 还提供用于**地理围栏**的服务、地图**数据**存储 - 在 Azure 中托管位置信息；使用**空间操作**通过地理空间分析提供位置智能。 Azure Maps 服务以 REST API 形式直接提供，也可以通过功能强大的 **Web SDK** 或 **Android SDK** 来使用它。 在 Azure 云中，开发人员可以通过这些工具快速开发和缩放将位置信息集成到 Azure 解决方案中的解决方案。 立即注册获取免费的 [Azure Maps 帐户](https://azure.microsoft.com/services/azure-maps/)，开始开发！

以下视频深入介绍了 Azure Maps：

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>地图控件

### <a name="web-sdk"></a>Web SDK

Azure Maps Web SDK 允许你使用自己的内容和图像自定义交互式地图，以便在 Web 或移动应用程序中显示。 此控件使用 WebGL，因此可以渲染大型数据集，同时保持很高的性能。 使用 JavaScript 或 TypeScript 通过此 SDK 进行开发。

![Azure Maps Web SDK](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

使用 Azure Maps Android SDK 可以创建功能强大的移动映射应用程序。 

![Azure Maps Android SDK](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure Maps 中的服务

Azure Maps 包含以下九个可在 Azure 应用程序中提供地理上下文的服务。

### <a name="data-service"></a>数据服务

数据对于地图而言是不可或缺的，使客户数据靠近 Azure Maps 服务可以降低延迟、提高工作效率，并创建强大的新方案来为应用程序增加亮点。 使用数据服务可以上传和存储地理空间数据（供空间操作或图像合成使用），以降低延迟、提高生产力，并在应用程序内实现新的方案。 有关此服务的详细信息，请访问[数据服务 API](https://docs.microsoft.com/rest/api/maps/data) 页。

### <a name="mobility-service"></a>移动服务

Azure Maps 移动服务提供附近公共交通服务的实时定位智能，包括车站、路线信息和行程时长预估。 使用该服务可以搜索特定的对象类型，例如公交停靠站、给定位置周边的共享单车/滑板板/汽车，并返回一组交通对象和对象详细信息。 此外，该服务可让开发人员请求交通路线详细信息，包括基本信息和其他详细信息，例如路线几何图形、停靠站列表、交通工具的排班和实时抵达时间，以及服务警报。 用户还可以通过请求停放站信息，来了解最近的停放站还剩下多少辆可租用的共享单车。 移动服务还能搜索可租用的共享汽车，并返回将来的可租用性和当前燃油量等详细信息。
使用 Azure Maps 移动服务可以实时规划行程，返回可能最佳的路线选项，并提供各种旅行方式，包括步行、骑车和市区提供的公交。 此外，开发人员可以请求交通路线详细信息，以及路线几何图形和详细路线安排等其他信息。

若要详细了解该服务和各种功能，请参阅我们的 [API 文档](https://docs.microsoft.com/rest/api/maps/mobility)

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

### <a name="spatial-operations"></a>空间操作

Azure Maps 空间操作将提取位置信息并即时分析这些信息，以帮助告知客户即将发生的事件的时间和空间，使近实时分析与事件预测建模成为可能。 Azure Maps 客户可在本地使用该服务通过通用地理空间数学计算库（包括最近点、大圆距离和缓冲区等服务）来增强其定位智能。 若要详细了解该服务和各种功能，请参阅我们的 [API 文档](https://docs.microsoft.com/rest/api/maps/spatial)。

### <a name="time-zone-service"></a>时区服务

“时区”服务允许你使用纬度-经度对或 [IANA ID](https://www.iana.org/) 来查询当前、历史和将来的时区信息。 时区服务还允许将 Microsoft Windows 时区 ID 转换为 IANA 时区，提取到 UTC 的时区偏移以及获取相应时区中的当前时间。 查询时区服务的典型 JSON 响应如下例所示：

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
* 交通事件 - 有关路网周围的交通堵塞和事故的最新视图。

![Azure Maps 流量](media/about-azure-maps/Introduction_Traffic.png)

有关更多详细信息，请访问 [Azure Maps 交通 API](https://docs.microsoft.com/rest/api/maps/traffic) 页。

### <a name="ip-to-location"></a>定位 IP

“定位 IP”服务可用于预留检索的给定 IP 地址的双字母国家/地区代码。 此服务可基于地理位置通过提供自定义的应用程序内容帮助改善和增强用户体验。

有关适用于位置服务 IP 的 REST API 的信息，请访问 [Azure Maps 地理位置 API](https://docs.microsoft.com/rest/api/maps/geolocation) 页面。

## <a name="programming-model"></a>编程模型

Azure Maps 针对移动设备设计，可为跨平台应用程序提供支持。 它使用语言无关的编程模型，支持通过 [REST API](https://docs.microsoft.com/rest/api/maps/) 返回 JSON 输出。

此外，Azure Maps 还提供一个简单的编程模型和方便的 [JavaScript 地图控件](https://docs.microsoft.com/javascript/api/azure-maps-control)，用于快速轻松地开发 Web 和移动应用程序。

## <a name="usage"></a>使用情况

若要访问 Maps 服务，只需导航到 [Azure 门户](https://portal.azure.com)并创建一个 Azure Maps 帐户即可。

Azure Maps 使用基于密钥的身份验证方案。 你的帐户将附带为你预生成的两个密钥。 通过使用任一密钥并向 Azure Maps 服务发出请求，开始将这些位置功能集成到你的应用程序。

## <a name="supported-regions"></a>支持的区域

Azure Maps API 目前在除以下区域外的所有国家/地区都可用：

* 阿根廷
* 中国
* 印度
* 摩洛哥
* 巴基斯坦
* 韩国

确认当前 IP 地址的位置不在上面所列的某个不受支持国家/地区。

## <a name="next-steps"></a>后续步骤

有关 Azure Maps 新功能的详细信息：

> [!div class="nextstepaction"]
> [路线矩阵、等时线、IP 查找等](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

试用一个展示 Azure Maps 的示例应用：

> [!div class="nextstepaction"]
> [快速入门：创建 Web 应用](quick-demo-map-app.md)
