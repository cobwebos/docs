---
title: Azure 地图路由服务的最佳做法 |微软 Azure 地图
description: 了解如何使用 Microsoft Azure 地图中的路由服务高效地路由。
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335419"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Azure 地图路由服务的最佳做法

Azure 地图[路径服务](https://docs.microsoft.com/rest/api/maps/route)中的路由方向和路径矩阵 API 可用于计算每个请求的路由的估计到达时间 （ETA）。 路线 API 会考虑实时交通信息和历史交通数据等因素，例如一周中请求的一天和一天中请求的典型道路速度。 API 根据时间或距离，根据时间或距离，根据时间或距离，根据时间或距离，根据时间或距离，根据时间或距离，一次或按优化顺序返回可用于多个目标的最短或最快的路由。 用户还可以为步行者、骑自行车者和卡车等商用车辆请求专用路线和详细信息。 在本文中，我们将分享调用 Azure 地图[路由服务的](https://docs.microsoft.com/rest/api/maps/route)最佳做法，并学习如何操作：

* 在路由方向 API 和矩阵路由 API 之间进行选择
* 根据实时和历史流量数据请求历史和预测旅行时间
* 请求整个路线和路线各段的路线详细信息（如时间和距离）
* 请求商用车辆的路线，如卡车
* 沿路线请求交通信息，如拥堵和通行费信息
* 请求由一个或多个停靠点（航点）组成的路线
* 优化一个或多个停靠点的路径，以获得访问每个停靠点的最佳顺序（航点）
* 使用支持点优化其他路线。 例如，提供通过电动汽车充电站的替代路线。
* 将[路由服务](https://docs.microsoft.com/rest/api/maps/route)与 Azure 地图 Web SDK 一起使用

## <a name="prerequisites"></a>先决条件

要调用 Azure 地图 API，需要 Azure 地图帐户和密钥。 有关详细信息，请参阅[创建帐户](quick-demo-map-app.md#create-an-account-with-azure-maps)并[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 主密钥也称为主订阅密钥或订阅密钥。

有关 Azure 映射中的身份验证的信息，请参阅[在 Azure 映射中管理身份验证](./how-to-manage-authentication.md)。 有关路由服务覆盖范围的详细信息，请参阅[路由覆盖范围](routing-coverage.md)。

本文使用[Postman 应用](https://www.postman.com/downloads/)生成 REST 调用，但您可以选择任何 API 开发环境。

## <a name="choose-between-route-directions-and-matrix-routing"></a>在路线方向和矩阵路由之间进行选择

路径方向 API 返回说明，包括行驶时间和路径路径的坐标。 路由矩阵 API 允许您计算由起点和目标位置定义的一组路由的行驶时间和距离。 对于每个给定的源，矩阵 API 计算从该原点到每个给定目的地的路由的成本（行驶时间和距离）。 所有这些 API 都允许您指定参数，如所需的出发时间、到达时间和车辆类型（如汽车或卡车）。 它们都相应地使用实时或预测流量数据来返回最佳路线。

如果您的方案是：

* 请求两个或两个以上已知地点之间的最短或最快的行驶路线，以获得送货车辆的精确到达时间。
* 请求详细的路线指南（包括路径几何体）以可视化地图上的路径
* 给定客户位置列表，计算访问每个客户位置并返回原点的最短路线。 此方案通常称为旅行推销员问题。 在一个请求中，您最多可以传递 150 个航点（停靠点）。
* 只需使用单个 API 调用即可将批处理查询发送到路由方向批处理 API。

如果方案是：

* 计算一组起点和目的地之间的行驶时间或距离。 例如，您有 12 名司机，您需要找到最接近的可用司机来从餐厅领取食物。
* 按潜在路线的实际行驶距离或时间进行排序。 矩阵 API 仅返回每个起点和目的地组合的行程时间和距离。
* 基于行驶时间或距离的群集数据。 例如，您的公司有 50 名员工，请查找所有员工，这些员工在 20 分钟车程内从您的办公室找到。

下面是显示路由方向和矩阵 API 的一些功能的比较：

| Azure 地图 API | 请求中查询的最大数量 | 避开区域 | 卡车和电动汽车路线 | 航点和旅行推销员优化 | 支持点 |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| 获取路线方向 | 1 | | X | X | |
| 发布路线方向 | 1 | X | X | X | X |
| 发布路线方向批次 | 700 | | X | X | |
| 发布路线矩阵 | 700 | | X | | |

要了解有关电动汽车路由功能的更多信息，请参阅有关如何使用[带有 Python 的 Azure 笔记本路由电动汽车的](tutorial-ev-routing.md)教程。

## <a name="request-historic-and-real-time-data"></a>请求历史和实时数据

默认情况下，路线服务假定行驶模式为汽车，且起飞时间为现在。 它基于实时交通状况返回路由，除非路由计算请求另有指定。 固定时间相关的流量限制（如"下午 4：00 到 6：00 之间不允许左转"）将被捕获，路由引擎将考虑这些限制。 道路封闭，如道路工程，将考虑，除非你特别要求的路线，忽略目前的现场交通。 要忽略当前流量，请在`traffic`API`false`请求中设置为。

路径计算**行程时间时间秒**值包括由于流量而导致的延迟。 它是通过利用当前和历史旅行时间数据生成的，此时出发时间设置为现在。 如果出发时间设置为将来，API 会根据历史数据返回预测旅行时间。

如果在请求中包含**计算 TravelTimeFor_all**参数，则响应中的摘要元素将具有以下其他字段，包括历史流量条件：

| 元素 | 描述|
| :--- | :--- |
| 无交通旅行时间以秒为单位 | 估计行驶时间计算，例如，由于交通状况，例如，由于拥堵，路线上没有延误 |
| 历史交通旅行时间以秒为单位 | 使用时间相关的历史流量数据计算的估计行驶时间 |
| 实时交通事件旅行时间以秒为单位 | 使用实时速度数据计算的估计行驶时间 |

以下各节演示如何使用讨论的参数对路由 API 进行调用。

### <a name="sample-query"></a>示例查询

在下面的第一个示例中，出发时间设置为将来，在编写本文时。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

响应包含摘要元素，如下所示。 由于出发时间设置为将来，**因此流量延迟秒**值为零。 使用时间相关的历史流量数据计算**行驶时间（以秒为单位**）值。 因此，在这种情况下，**行驶时间秒**值等于**历史流量旅行时间秒**值。

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

在下面的第二个示例中，我们有一个实时路由请求，此时的出发时间是现在。 URL 中未显式指定它，因为它是默认值。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

响应包含摘要，如下所示。 由于拥塞，**流量延迟秒**值大于零。 它也比**历史性的交通时间在秒**。

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

## <a name="request-route-and-leg-details"></a>请求路线和腿部详细信息

默认情况下，路由服务将返回坐标数组。 响应将包含组成路径的坐标在名为`points`的列表中。 路由响应还包括从路径开始的距离和估计的经过时间。 这些值可用于计算整个路径的平均速度。

下图显示了元素`points`。

<center>

![点列表](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

展开`point`元素以查看路径的坐标列表：

<center>

![点列表](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

路由方向 API 支持不同的指令格式，可以通过指定**指令类型**参数来使用。 要格式化说明以方便计算机处理，请使用**说明类型_编码**。 使用 **"类型"标记的说明**将说明显示为用户的文本。 此外，指令可以格式化为文本，其中指令的某些元素被标记，并且说明显示的特殊格式。 有关详细信息，请参阅[受支持的说明类型列表](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)。

请求指令时，响应将返回名为`guidance`的新元素。 该`guidance`元素包含两条信息：逐圈方向和汇总说明。

<center>

![说明类型](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

元素`instructions`保存行程的逐圈方向，并且已`instructionGroups`汇总说明。 每个说明摘要都涵盖了行程的一部分，可以覆盖多条道路。 API 可以返回路由部分的详细信息。 例如，交通堵塞的坐标范围或当前交通速度。

<center>

![按转弯说明](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![总结说明](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>请求商用车辆的路线

Azure 地图路由 API 支持商用车辆路由，涵盖商用卡车路由。 API 考虑指定的限制。 如车辆的高度和重量，以及车辆是否携带危险货物。 例如，如果车辆携带易燃物品，则路由引擎会避开住宅区附近的某些隧道。

### <a name="sample-query"></a>示例查询

下面的示例请求查询了商用卡车的路线。 卡车装载的是1类危险废物。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

路线 API 返回适合卡车尺寸和危险废物的方向。 您可以通过展开`guidance`元素来读取路由说明。

<center>

![卡车与1类垃圾](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>示例查询

从上述查询更改美国 Hazmat 类将导致不同的路由以适应此更改。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

以下响应适用于装载 9 类危险材料的卡车，该危险品比 1 类危险材料危险度低。 当您展开元素`guidance`以读取方向时，您会注意到方向不同。 对于装载 1 类危险材料的卡车，还有更多的路线说明。

<center>

![卡车与9级垃圾](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>沿路线请求流量信息

使用 Azure 地图路由方向 API，开发人员可以通过在请求中包含`sectionType`参数来请求每个节类型的详细信息。 例如，您可以请求每个交通堵塞段的速度信息。 请参阅[节类型键的值列表](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype)，以了解您可以请求的各种详细信息。

### <a name="sample-query"></a>示例查询

以下查询将 集`sectionType``traffic`到 。 它请求包含从西雅图到圣地亚哥的交通信息的分区。

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

响应包含适合沿给定坐标的流量的节。

<center>

![交通部分](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

此选项可用于在渲染地图时对各节着色，如下图所示： 

<center>

![交通部分](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>计算和优化多站路径

Azure 地图当前提供两种形式的路由优化：

* 基于请求的路由类型的优化，而不更改航点的顺序。 您可以[在此处找到支持的路由类型](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* 旅行推销员优化，它改变航点的顺序，以获得访问每个停靠点的最佳顺序

对于多站路由，在单个路由请求中最多可以指定 150 个航点。 开始和结束坐标位置可以相同，往返位置也是如此。 但是，您需要提供至少一个额外的航点来计算路径。 航点可以添加到原点坐标和目标坐标之间的查询中。

如果要优化访问给定航点的最佳顺序，则需要指定**计算BestOrder_true**。 此方案也称为旅行推销员优化问题。

### <a name="sample-query"></a>示例查询

以下查询请求六个航点的路径，`computeBestOrder`参数设置为`false`。 它也是参数的`computeBestOrder`默认值。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

响应描述路径长度为 140，851 米，并且需要 9，991 秒才能行驶该路径。

<center>

![非优化响应](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

下图说明了此查询产生的路径。 此路径是一条可能的路线。 它不是基于时间或距离的最佳路径。

<center>

![非优化图像](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

此路线航点顺序为：0、1、2、3、4、5 和 6。

### <a name="sample-query"></a>示例查询

以下查询请求路径为相同的六个航点，如上例所示。 这一次，`computeBestOrder`参数设置为`true`（旅行推销员优化）。

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

响应描述路径长度为 91，814 米，并且需要 7，797 秒才能行驶该路径。 此处的行驶距离和行驶时间都较低，因为 API 返回了优化的路线。

<center>

![非优化响应](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

下图说明了此查询产生的路径。

<center>

![非优化图像](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

最佳路线的顺序如下：0、5、1、2、4、3 和 6。

>[!TIP]
> 路由服务的优化航点订单信息提供一组索引。 这些不包括原点和目标索引。 您需要将这些值递增 1 来考虑原点。 然后，将目标添加到末尾，以获得完整的有序航点列表。

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>使用支撑点计算和偏置替代路径

您可能有这样的情况是，要重建路由以计算参考路由的零个或多个替代路由。 例如，您可能希望向客户显示通过零售商店的替代路由。 在这种情况下，您需要使用支撑点偏置位置。 以下是偏置位置的步骤：

1. 按"按"计算路径并从路由响应获取路径
2. 使用路径查找路径沿线或附近的所需位置。 例如，可以使用 Azure 地图[兴趣点 API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi)或在数据库中查询自己的数据。  
3. 根据路线起点的距离对位置进行排序
4. 将这些位置作为支持点添加到新的路由请求到[后路由方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)。 要了解有关支持点的更多信息，请参阅[后路线方向 API 文档](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints)。 

调用[后路由方向 API](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)时，可以设置最小偏差时间或距离约束以及支撑点。 如果要提供替代路线，但还要限制行驶时间，请使用这些参数。 使用这些约束时，替代路由将遵循给定时间或距离的原点中的参考路线。 换句话说，其他路由与给定约束的参考路由不同。

下图是渲染具有指定偏差限制的时间和距离的替代路径的示例。

<center>

![替代路线](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>在 Web 应用中使用路由服务

Azure 映射 Web SDK 提供[服务模块](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest)。 此模块是一个帮助库，使用 JavaScript 或 TypeScript，可以轻松地在 Web 或 Node.js 应用程序中使用 Azure 映射 REST API。 服务模块可用于在地图上呈现返回的路由。 模块会自动确定与 GET 和 POST 请求一起使用的 API。

## <a name="next-steps"></a>后续步骤

要了解更多信息，请参阅：

> [!div class="nextstepaction"]
> [Azure 地图路由服务](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [如何使用服务模块](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [在地图上显示路线](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Azure 映射 NPM 包](https://www.npmjs.com/package/azure-maps-rest  )
