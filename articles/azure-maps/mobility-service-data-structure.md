---
title: Azure Maps 中的移动服务数据结构 |Microsoft Azure 映射
description: 在本文中，你将了解通过 Microsoft Azure 映射移动服务返回的公共字段和数据结构。
author: farah-alyasari
ms.author: v-faalya
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 685810a6efa46c8eb3ad6cee0c2424299f0347d8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209607"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps 移动服务中的数据结构

本文介绍了[Azure Maps 移动服务](https://aka.ms/AzureMapsMobilityService)中的大都市区域的概念。 我们讨论了在为公共传输停止和行查询此服务时返回的一些公共字段。 建议在开发移动服务 Api 之前阅读本文。

## <a name="metro-area"></a>地铁区域

移动服务数据按受支持的大都市区域分组。 地铁区域不遵循城市界限。 地铁区域可包含多个城市、密集填充的城市和周围的城市。 事实上，国家/地区可以是一个大都市区。 

`metroID` 是一种大都市区的 ID，可用于调用[获取地铁区域信息 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)。 使用 Azure Maps "获取地铁" API 来请求传输类型、传输机构、活动警报，以及所选地铁的其他详细信息。 你还可以请求支持的地铁区域和 metroIDs。 地铁区域 Id 可能会更改。

**metroID：** 522 **Name：** Tacoma-Bellevue

![西雅图-地铁区域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止 Id

传输停止可由两种类型的 Id （[一般传输源规范（GFTS）](https://gtfs.org/) id 和 AZURE MAPS 停止 id）引用。 GFTS ID 称为 stopKey，而 Azure Maps stop ID 称为 stopID。 当经常提到传输停止时，建议使用 Azure Maps 停止 ID。 只要物理停止存在，stopID 就会更稳定，并且可能会保持不变。 GTFS stop ID 会更频繁地更新。 例如，可以根据 GTFS 提供程序请求或发布新的 GTFS 版本时，更新 GTFS stop ID。 尽管物理停止没有变化，但 GTFS 停止 ID 可能会更改。

若要开始，可以使用 "[获取附近的传输 API](https://aka.ms/AzureMapsMobilityNearbyTransit)" 请求附近的传输停止。

## <a name="line-groups-and-lines"></a>行组和线条

移动服务为行组和行组使用并行数据模型。 此模型用于更好地处理从[GTFS](https://gtfs.org/)路由和行程数据继承的更改。


### <a name="line-groups"></a>行组

"行组" 是一个实体，它将逻辑上属于同一组的所有行组合在一起。 通常，线条组包含两行，一个从点 A 到 B，另一个从点 B 返回到。这两行都属于同一公共传输机构，并且具有相同的行号。 但是，在某些情况下，线条组具有两行以上的行或其中只有一行。


### <a name="lines"></a>线条

如上所述，每个行组都由一组行组成。 每个行组都由两行组成，每行描述一个方向。  但是，在某些情况下，多个行组成了一个线条组。 例如，有一条线有时 detour 某个邻近，有时不会。 在这两种情况下，它在相同的行号下运行。 此外，线条组可以由单个行组成。 具有单个方向的圆线是一个具有一行的 ling 组。

首先，可以使用[获取传输线路 API](https://aka.ms/AzureMapsMobilityTransitLine)请求行组。


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
