---
title: Azure 地图中的移动服务数据结构*微软 Azure 地图
description: 在本文中，您将了解通过 Microsoft Azure 地图移动服务返回的常见字段和数据结构。
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334447"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure 地图移动服务中的数据结构

本文介绍了[Azure 地图移动服务](https://aka.ms/AzureMapsMobilityService)中的地铁区域概念。 我们将讨论在查询此服务以查询公共交通站点和线路时返回的一些常见字段。 我们建议您在使用移动服务 API 开发之前阅读本文。

## <a name="metro-area"></a>地铁区

移动服务数据按支持的大都市区分组。 地铁区域不遵循城市边界。 大都市区可以包含多个城市、人口稠密的城市和周边城市。 事实上，国家/地区可以是一个大都市区。 

是`metroID`一个大都市区的ID，可以用来调用[获取地铁区域信息API。](https://aka.ms/AzureMapsMobilityMetroAreaInfo) 使用 Azure 地图的"获取地铁"API 请求所选地铁的公交类型、中转机构、活动警报和其他详细信息。 您也可以请求支持的大都市区和地铁。 地铁区区号的年号可能会有所更改。

**地铁ID：** 522**名称：** 西雅图-塔科马-贝尔维尤

![西雅图-地铁区](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止指示

中转站可以由两种类型的 ID（[一般传输馈送规范 （GFTS）](https://gtfs.org/) ID 和 Azure 映射停止 ID）来引用。 GFTS ID 称为停止密钥，Azure 映射停止 ID 称为 stopID。 当经常提到中转站时，鼓励您使用 Azure 映射停止 ID。 只要存在物理停止，停止 ID 就越稳定，并且可能保持不变。 GTFS 停止 ID 更新频率更大。 例如，GTFS 停止 ID 可以根据 GTFS 提供程序请求或发布新的 GTFS 版本时更新。 虽然物理停止没有变化，但 GTFS 停止 ID 可能会更改。

要开始，您可以使用[获取附近的中转 API](https://aka.ms/AzureMapsMobilityNearbyTransit)请求附近的中转站。

## <a name="line-groups-and-lines"></a>线组和行

移动服务对行和行组使用并行数据模型。 此模型用于更好地处理从[GTFS](https://gtfs.org/)路由和行程数据继承的更改。


### <a name="line-groups"></a>行组

行组是一个实体，它将所有逻辑上属于同一组一部分的行组合在一起。 通常，线组包含两条线，一条从 A 点到 B 点，另一条从点 B 返回 A。两条线路将属于同一公共交通机构，并且具有相同的线路号。 但是，在某些情况下，行组中可能有两行以上或其中只有一行。


### <a name="lines"></a>线条

如上所述，每个行组由一组行组成。 每行组由两行组成，每行描述一个方向。  但是，在某些情况下，更多的行组成一个行组。 例如，有一条线有时绕道穿过某个社区，有时没有。 在这两种情况下，它在同一行号下运行。 此外，线组可以由单行组成。 具有单个方向的圆线是具有一条线的 ling 组。

首先，您可以使用["获取传输线 API"](https://aka.ms/AzureMapsMobilityTransitLine)请求行组。


## <a name="next-steps"></a>后续步骤

了解如何使用移动服务请求传输数据：

> [!div class="nextstepaction"]
> [如何请求传输数据](how-to-request-transit-data.md)

了解如何使用移动服务请求实时数据：

> [!div class="nextstepaction"]
> [如何请求实时数据](how-to-request-real-time-data.md)

浏览 Azure 地图移动服务 API 文档

> [!div class="nextstepaction"]
> [移动服务 API 文档](https://aka.ms/AzureMapsMobilityService)
