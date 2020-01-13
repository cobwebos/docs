---
title: Azure Maps 中的移动服务数据结构 |Microsoft Azure 映射
description: 在本文中，你将了解通过 Microsoft Azure 映射移动服务返回的公共字段和数据结构。
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910724"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps 移动服务中的数据结构

本文介绍了[Azure Maps 移动服务](https://aka.ms/AzureMapsMobilityService)中的大都市区域的概念，以及通过服务返回的一些公共字段，以便在对公共传输停止和线路进行查询时。 建议在开始使用移动服务 Api 之前完成本文。 下面将讨论这些公共字段。

## <a name="metro-area"></a>地铁区域

移动服务数据拆分为支持的地铁区域。 大都市区不遵循 city 边界，大都市区可以包含多个城市，例如，密集填充的城市和周围的城市;并且国家/地区可以是一个大都市区。 

`metroID` 是一种大都市区的 ID，可用于调用[获取地铁区域信息 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)来请求支持的传输类型，并为地铁区域（如转口机构和活动警报）提供其他详细信息。 你可以使用 Azure Maps 获取地铁 API 来请求支持的地铁区域和 metroIDs。 地铁区域 Id 可能会更改。

**metroID：** 522 **Name：** Tacoma-Bellevue

![西雅图-地铁区域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止 Id

传输停止可以通过两种类型的 Id （[一般传输源规范（GFTS）](https://gtfs.org/) id （称为 stopKey）和 AZURE MAPS 停止 id （称为 stopId）来引用。 当引用在一段时间后停止时，建议使用 Azure Maps stop ID，因为此 ID 更稳定，只要物理停止存在，就不会发生更改。 例如，GTFS stop ID 会更频繁地进行更新，例如，当 GTFS 提供程序需要更改它或发布新的 GTFS 版本时，尽管物理停止没有变化。

若要开始，可以使用 "[获取附近的传输 API](https://aka.ms/AzureMapsMobilityNearbyTransit)" 请求附近的传输停止。

## <a name="line-groups-and-lines"></a>行组和线条

移动服务为行组和行组使用并行数据模型，以便更好地处理从[GTFS](https://gtfs.org/)路由和行程数据模型继承的更改。


### <a name="line-groups"></a>行组

"行组" 是一个实体，它将逻辑上属于同一组的所有行组合在一起。 通常，线条组将包含两个行，一条从 A 到 B，另一个从点 B 返回到 A，两者都属于同一公共传输机构并且具有相同的行号。 但是，在某些情况下，线条组具有两行以上的行或其中只有一行。


### <a name="lines"></a>线条

如上所述，每个行组都由一组行组成。 通常，每行描述一个方向，每个行组由两行组成。 但是，在某些情况下，行组中包含的行有更多的行，例如，有时会 detour 一条直线，有时不会在同一行号下进行操作，并且在其他情况下，行 gr) 由一行组成，例如一个方向为的圆。

若要开始，可以通过使用 "[获取传输行" API](https://aka.ms/AzureMapsMobilityTransitLine)请求行组，稍后向下钻取到 "行"。


## <a name="next-steps"></a>后续步骤

了解如何使用移动服务请求传输数据：

> [!div class="nextstepaction"]
> [如何请求传输数据](how-to-request-transit-data.md)

了解如何使用移动服务请求实时数据：

> [!div class="nextstepaction"]
> [如何请求实时数据](how-to-request-real-time-data.md)

浏览 Azure Maps 移动服务 API 文档

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)
