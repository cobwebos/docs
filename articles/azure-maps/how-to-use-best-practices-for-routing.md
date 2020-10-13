---
title: Microsoft Azure 地图中 Azure Maps 路线服务的最佳实践
description: 了解如何使用 Microsoft Azure Maps 中的路线服务来路由车辆。
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 13c7178b4a0866066dc74e409f8f4bfcd21a23f4
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874588"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure Maps 路由服务的最佳做法

Azure Maps [路线服务](https://docs.microsoft.com/rest/api/maps/route) 中的路由方向和路由矩阵 api 可用于计算每个请求的路由 (eta) 的预计到达时间。 路由 Api 考虑诸如实时流量信息和历史流量数据之类的因素，如一周中请求日期和时间的典型路上。 这些 API 会根据时间或距离依次或按最佳顺序返回可用于多个目的地的最短或最快路线。 用户还可以请求查看器、骑车和商业汽车（如卡车）的专用路线和详细信息。 在本文中，我们将分享 Azure Maps [路线服务](https://docs.microsoft.com/rest/api/maps/route)的最佳实践，并了解操作方法：

 * 在路线方向 Api 和矩阵路由 API 之间进行选择
 * 根据实时和历史交通数据请求历史和预测行程时间
 * 为整个路由和路由的每个阶段请求路由详细信息，如时间和距离
 * 商业车辆的请求路线，如卡车
 * 按路线请求流量信息，如堵塞和收费信息
 * 请求包含一个或多个停止 (waypoints 的路由) 
 * 优化一个或多个停止的路由，获取访问每个 stop (waypoint 的最佳顺序) 
 * 使用支持点优化备选路由。 例如，提供通过电力公司收费工作站的备用路由。
 * 将 [路线服务](https://docs.microsoft.com/rest/api/maps/route) 与 AZURE MAPS Web SDK 一起使用

## <a name="prerequisites"></a>先决条件

1. [创建 Azure Maps 帐户](quick-demo-map-app.md#create-an-azure-maps-account)
2. [获取主订阅密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)（亦称为“主密钥”或“订阅密钥”）。

有关路线服务的覆盖范围的详细信息，请参阅 [路由覆盖面](routing-coverage.md)。

本文使用 [Postman 应用](https://www.postman.com/downloads/) 来构建 REST 调用，但你可以选择任何 API 开发环境。

## <a name="choose-between-route-directions-and-matrix-routing"></a>选择路线方向和矩阵路由

路由方向 Api 将返回说明，其中包括行程时间和路由路径的坐标。 使用路由矩阵 API 可以计算由源位置和目标位置定义的一组路由的行程时间和距离。 对于每个给定的源，Matrix API 会计算从该原点到每个给定目标的路由所需的成本 (行程时间和距离) 。 所有这些 Api 都允许您指定参数，例如所需的出发时间、到达时间和车辆类型（如汽车或卡车）。 它们都使用实时或预测流量数据来返回最佳路由。

如果你的方案是，请考虑调用路线方向 Api：

* 请求两个或更多个已知位置之间的最短或最快驱动路线，以获得交付车辆的精确到达时间。
* 请求详细路由指南（包括路由几何图形）来可视化地图上的路由
* 给定客户位置的列表，计算访问每个客户位置并返回到源的最短路由。 这种情况通常称为旅行推销员问题。 你最多可以传递 150 waypoints (停止一个请求) 。
* 仅使用单个 API 调用将查询批次发送到路由方向批处理 API。

如果你的方案是，请考虑调用矩阵路由 API：

* 计算一组源和目标之间的行程时间或距离。 例如，您有12个驱动程序，并且您需要找到最接近的可用驱动程序，以便从餐馆中选取食物交付。
* 按实际行程距离或时间对潜在路由进行排序。 Matrix API 只为每个源和目标组合返回旅行时间和距离。
* 基于行程时间或距离的群集数据。 例如，你的公司的员工数为50，查找从你的办公室到20分钟的时间段内的所有员工。

下面是用于显示路线方向和矩阵 Api 的某些功能的比较：

| Azure Maps API | 请求中的最大查询数 | 避免区域 | 卡车和电车布线 | Waypoints 和旅游推销员优化 | 支持点 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 获取路线方向 | 1 | | X | X | |
| 后敷设方向 | 1 | X | X | X | X |
| 后期路由方向批处理 | 700 | | X | X | |
| 发布路线矩阵 | 700 | | X | | |

若要详细了解电动机布线功能，请参阅本教程，了解如何 [通过 Python 使用 Azure Notebooks 来路由电动汽车](tutorial-ev-routing.md)。

## <a name="request-historic-and-real-time-data"></a>请求历史数据和实时数据

默认情况下，路由服务假定旅行模式为汽车，并现在出发时间为。 它会根据实时流量条件返回路由，除非路由计算请求另外指定。 捕获与时间相关的流量限制，如 "在 4:00 PM 之间不允许使用左移到 6:00 PM"，并将由路由引擎考虑。 除非您专门请求了忽略当前实时流量的路由，否则将会考虑 roadworks （如）。 若要忽略当前流量，请 `traffic` `false` 在 API 请求中设置为。

路由计算 **travelTimeInSeconds** 值包括流量导致的延迟。 当出发时间设置为 "现在" 时，它通过利用当前和历史行程时间数据来生成。 如果你的出发时间设置为将来的时间，则 Api 会根据历史数据返回预测的行程时间。

如果你的请求中包含 **computeTravelTimeFor = all** 参数，则响应中的 summary 元素将包含以下附加字段，其中包括历史流量条件：

| 元素 | 说明|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | 预计的预计旅行时间，就像由于流量情况而导致的路由没有延迟，例如由于拥塞 |
| historicTrafficTravelTimeInSeconds | 使用依赖时间的历史流量数据计算的预计旅行时间 |
| liveTrafficIncidentsTravelTimeInSeconds | 使用实时速度数据计算的预计旅行时间 |

下一部分演示如何使用讨论的参数对路由 Api 进行调用。

### <a name="sample-query"></a>示例查询

在下面的第一个示例中，在编写时将出发时间设置为将来。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

响应包含一个摘要元素，如下所示。 由于将出发时间设置为未来，因此 **trafficDelayInSeconds** 值为零。 **TravelTimeInSeconds**值是使用依赖时间的历史流量数据计算的。 因此，在这种情况下， **travelTimeInSeconds** 值等于 **historicTrafficTravelTimeInSeconds** 值。

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>示例查询

在下面的第二个示例中，我们有一个实时路由请求，即现在的出发时间。 它不是在 URL 中显式指定的，因为它是默认值。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

响应包含摘要，如下所示。 由于拥塞， **trafficDelaysInSeconds** 值大于零。 它还大于 **historicTrafficTravelTimeInSeconds**。

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>请求路由和支线详细信息

默认情况下，路由服务将返回一个坐标数组。 响应将包含在名为的列表中构成路径的坐标 `points` 。 路由响应还包括从路由开始到预计运行时间的距离。 这些值可用于计算整个路线的平均速度。

下图显示了 `points` 元素。

![Points 元素](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

展开 `point` 元素可查看路径的坐标列表：

![展开点元素](media/how-to-use-best-practices-for-routing/points-list-img.png)

路由方向 Api 支持可以通过指定 **instructionsType** 参数来使用的不同格式的指令。 若要格式化说明以便轻松进行计算机处理，请使用 **instructionsType = 编码**。 使用 **instructionsType = 标记** 为用户显示文本说明。 此外，还可以将指令的格式设置为文本，其中指令的某些元素将被标记出来，并且说明中提供了特殊的格式设置。 有关详细信息，请参阅 [支持的指令类型的列表](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)。

请求指令时，响应将返回名为的新元素 `guidance` 。 `guidance`元素包含两条信息：依次旋转方向和摘要说明。

![指令类型](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions`元素为行程保留轮流方向，并提供 `instructionGroups` 摘要说明。 每个说明摘要都涵盖了可能涵盖多个道路的行程段。 Api 可以返回路由各部分的详细信息。 例如，流量堵塞的坐标范围或流量的当前速度。

![打开指令](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![摘要说明](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>为商业车辆请求路由

Azure Maps 路由 Api 支持商用车路由，涵盖了商业卡车路线。 Api 考虑指定的限制。 例如，车辆的高度和权重，并且如果车辆携带有害货物。 例如，如果车辆携带 flammable，则路由引擎将避免使用位于居住区附近的某些隧道。

### <a name="sample-query"></a>示例查询

下面的示例请求查询商业卡车的路线。 卡车携带第1类危险废物材料。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

路由 API 返回可容纳卡车尺寸和危险废物的方向。 可以通过展开元素来读取路由说明 `guidance` 。

![具有类 1 hazwaste 的卡车](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>示例查询

更改上述查询中的 US Hazmat 类将导致不同的路由来容纳此更改。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

下面的响应适用于带有2类危险性材料的卡车，这种情况比 class 1 有害材料的危险更小。 当你展开 `guidance` 元素以阅读方向时，你会注意到，方向并不相同。 对于携带 class 1 物质材料的卡车有更多路线说明。



![类 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>沿路由请求流量信息

使用 Azure Maps 路由方向 Api，开发人员可以通过在请求中包含参数来请求每个部分类型的详细信息 `sectionType` 。 例如，您可以为每个交通堵塞段请求速度信息。 请参阅 [sectionType 键的值列表](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) ，了解你可以请求的各种详细信息。

### <a name="sample-query"></a>示例查询

下面的查询将设置 `sectionType` 为 `traffic` 。 它将包含来自西雅图的流量信息的部分请求到圣地亚哥。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

响应包含适用于沿给定坐标的流量的部分。

![流量部分](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

在呈现地图时，此选项可用于着色部分，如下图所示： 

![在地图上呈现的彩色部分](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>计算和优化包含多个停靠站的路线

Azure Maps 当前提供两种形式的路由优化：

* 基于请求的路由类型进行优化，而不会更改 waypoints 的顺序。 可在此处找到 [受支持的路由类型](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* 旅行式销售商优化，它更改 waypoints 的顺序，以获得每个停止的最佳顺序

对于多停止路由，在单个路由请求中最多可以指定 150 waypoints。 起始坐标位置和结束坐标位置可以相同，这与往返行程的情况相同。 但需要至少提供一个额外的 waypoint 来进行路由计算。 Waypoints 可以在源坐标和目标坐标之间的位置添加到查询中。

如果要优化访问给定 waypoints 的最佳顺序，则需要指定 **computeBestOrder = true**。 此方案也称为旅行推销员优化问题。

### <a name="sample-query"></a>示例查询

下面的查询请求六个 waypoints 的路径， `computeBestOrder` 参数设置为 `false` 。 它也是该参数的默认值 `computeBestOrder` 。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

响应将路径长度描述为140851米，并使用9991秒来传送该路径。

![非优化响应](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

下图说明了此查询生成的路径。 此路径是一种可能的路由。 它不是基于时间或距离的最佳路径。

![非优化映像](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



此路由 waypoint 顺序为：0、1、2、3、4、5和6。

### <a name="sample-query"></a>示例查询

下面的查询请求相同六个 waypoints 的路径，如上述示例中所示。 这次， `computeBestOrder` 参数设置为 `true` (旅行推销员优化) 。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

响应将路径长度描述为91814米，并使用7797秒来传送该路径。 在这里，旅行距离和旅行时间都很低，因为 API 返回了优化的路由。

![优化的响应](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

下图说明了此查询生成的路径。

![优化的映像](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

最佳路由具有以下 waypoint 顺序：0、5、1、2、4、3和6。

>[!TIP]
> 路由服务的优化 waypoint 订单信息提供了一组索引。 这会排除源和目标索引。 需要将这些值按1递增，以考虑源的值。 然后，将目标添加到末尾，以获取完整的有序 waypoint 列表。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>使用支持点计算和偏向备用路由

在某些情况下，您可能需要重新构建路由，以便为引用路由计算零个或更多备用路由。 例如，你可能想要显示传递零售商店的客户备用路由。 在这种情况下，需要使用支持点来偏向位置。 下面是用于偏向位置的步骤：

1. 按原样计算路由，并从路由响应中获取路径
2. 使用路由路径来查找路由路径或路由路径附近所需的位置。 例如，你可以使用 Azure Maps [兴趣点 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) 或在数据库中查询你自己的数据。  
3. 根据与路线起点的距离对位置排序
4. 将这些位置作为新的路由请求中的支持点添加到 [发布路由方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)。 若要了解有关支持点的详细信息，请参阅 [后路线方向 API 文档](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)。 

在调用 " [后处理路线方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)" 时，可以设置最小偏差时间或距离约束以及支持点。 如果希望提供备用路由，但又想要限制旅行时间，请使用这些参数。 当使用这些约束时，备选路由将在给定时间或距离所在的原始点处跟随引用路由。 换句话说，其他路由根据给定的约束从引用路由分叉。

下图是一个示例，其中显示了具有指定的时间和距离的偏差限制的备用路由。

![备用路由](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>在 web 应用中使用路由服务

Azure Maps Web SDK 提供 [服务模块](https://docs.microsoft.com/javascript/api/azure-maps-rest/)。 此模块是一个帮助程序库，使用 JavaScript 或 TypeScript 可以轻松地在 web 或 Node.js 应用程序中使用 Azure Maps REST Api。 服务模块可用于在地图上呈现返回的路由。 模块自动确定要与 GET 和 POST 请求一起使用的 API。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅：

> [!div class="nextstepaction"]
> [Azure Maps 路线服务](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [如何使用服务模块](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [在地图上显示路线](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure Maps NPM 包](https://www.npmjs.com/package/azure-maps-rest  )
