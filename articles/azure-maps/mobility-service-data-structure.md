---
title: Azure Maps 中的移动服务数据结构 |Microsoft Docs
description: Azure Maps 移动服务中的数据结构
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735551"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps 移动服务中的数据结构

本文介绍了在 Metro 区的概念[Azure Maps 移动服务](https://aka.ms/AzureMapsMobilityService)的一些常见字段返回并通过在服务中，查询时公共传输停止行。 我们建议在出开始使用移动服务 Api 之前开始阅读本文。 我们将讨论以下这些公共字段。

## <a name="metro-area"></a>Metro 区域

移动服务数据拆分为支持 metro 的区域。 大都市区不遵循市/县边界，metro 区域可以包含多个城市，例如，密集填充的 city 和其周围的城市;和国家/地区/区域可以是一个 metro 区域。 

`metroID`是大都市区域的 ID，可用于调用[获取 Metro 区域信息 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)请求支持传输类型和其他详细信息，如传输机构和活动警报的 metro 区域。 可以使用 Azure 地图获取 Metro API 请求支持 metro 的区域和 metroIDs。 Metro 区域 Id 会有所更改。

**metroID:** 522**名称：** Seattle-Tacoma-Bellevue

![西雅图大都市区域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止 Id

可以通过两种类型的 Id，称为传输停止[常规传输源规范 (GFTS)](https://gtfs.org/) ID （称为 stopKey） 和 Azure Maps 停止 ID （也称为 stopId）。 当指时停止随着时间的推移，建议使用 Azure Maps 停止 ID，因为此 ID 是更稳定，不会有可能更改，只要物理停止存在。 GTFS 停止 ID 是更频繁地更新，例如，如果 GTFS 提供程序需要对其进行更改或发布新 GTFS 版本后，尽管物理停止发生不了任何更改。

若要开始，可以请求通过使用来停止附近传输[获取附近传输 API](https://aka.ms/AzureMapsMobilityNearbyTransit)。

## <a name="line-groups-and-lines"></a>行组和行

移动服务使用的并行数据模型行，而且要更好地处理的更改的行组继承自[GTFS](https://gtfs.org/)路由和相应的数据模型。


### <a name="line-groups"></a>行组

行组是组的一个实体，它集中在一起以逻辑方式是组的相同的一部分的所有行。 通常一个行组将包含两行、 一个将从 A 点到 B，和其他从 B 点返回到 A，这两个属于相同的公共交通机构和具有相同的行号。 但是，可能是在其中一个行组具有两个以上的行或单个行中的情况。


### <a name="lines"></a>行

如上文所述，每个行组由一系列行组成。 通常，每行描述方向和每个行组包含两行。 但是有些情况下的多个行中包含行组，例如存在是一个行，有时会绕过通过某些邻居，有时不是，请和运营的相同的行号，在这两种情况，并且没有的其他情况下一行 g组组成单个行，例如在单方向上的循环行。

若要开始，可以请求行组通过使用[获取传输行 API](https://aka.ms/AzureMapsMobilityTransitLine)和更高版本的向下钻取行。


## <a name="next-steps"></a>后续步骤

了解如何请求使用的移动服务的传输数据：

> [!div class="nextstepaction"]
> [如何请求传输数据](how-to-request-transit-data.md)

了解如何请求使用的移动服务的实时数据：

> [!div class="nextstepaction"]
> [如何对请求的实时数据](how-to-request-real-time-data.md)

浏览 Azure Maps 移动服务 API 文档

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)
