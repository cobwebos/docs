---
title: "Azure Cosmos DB 的表 API 简介 | Microsoft Docs"
description: "了解如何通过常用 OSS MongoDB API 使用 Azure Cosmos DB 以低延迟存储和查询大量键值对数据。"
services: cosmosdb
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8226ecfb5fa8c70b162ae776679302db6cfdcdbc
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB：表 API 简介

[Azure Cosmos DB](introduction.md) 是 Microsoft 针对任务关键型应用程序提供的全球分布的多模型数据库服务。 Azure Cosmos DB 在全球范围内提供[统包全局分发](../documentdb/documentdb-distribute-data-globally.md)、[吞吐量和存储的弹性扩展](partition-data.md)、99% 的情况下低至个位数的毫秒级延迟、[五个妥善定义的一致性级别](../documentdb/documentdb-consistency-levels.md)以及得到保证的高可用性，所有这些均由[行业领先的 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) 提供支持。 Azure Cosmos DB [自动对数据进行索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，而无需你进行架构和索引管理。 它是多模型的，支持文档、键-值、图和列式数据模型。 

![Azure 表存储 API 和 Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB 通过灵活架构为需要键/值存储的应用程序提供表 API。 [Azure 表存储](../storage/storage-introduction.md) SDK 和 REST API 可用于处理 Azure Cosmos DB。 可以使用 Azure Cosmos DB 创建具有高吞吐量要求的表。 Azure Cosmos DB 当前在公共预览版中支持吞吐量优化表（非正式地称为“高级表”）。 

可以继续对具有高存储和低吞吐量要求的表使用 Azure 表存储。 Azure Cosmos DB 会在将来更新中引入对存储优化表的支持，并且现有和新的 Azure 表存储帐户将升级到 Azure Cosmos DB。

## <a name="premium-and-standard-table-apis"></a>高级表 API 和标准表 API
如果你当前使用 Azure 表存储，可以通过迁移到 Azure Cosmos DB 的“高级表”预览获得以下好处：

|  | Azure 表存储 | Azure Cosmos DB：表存储（预览版） |
| --- | --- | --- |
| 延迟 | 快速，但对延迟没有上限 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在全球任何位置，对于任何规模，99% 的情况下读取操作的延迟 < 10 毫秒、写入操作的延迟 < 15 毫秒） |
| 吞吐量 | 高度可缩放，但没有专用吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒 | 使用 SLA 支持的[每个表专用保留吞吐量](../documentdb/documentdb-request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒 |
| 全局分发 | 具有一个可选可读辅助读取区域以实现 HA 的单一区域。 不能启动故障转移 | 从 1 个到 30 多个区域进行[统包全局分发](../documentdb/documentdb-distribute-data-globally.md)，支持在全球各地随时[自动和手动故障转移](../documentdb/documentdb-regional-failovers.md) |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引 | 自动对所有属性完成编制索引，没有索引管理 |
| 查询 | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 Azure Cosmos DB 数据库引擎能够支持聚合查询、地理空间查询和排序查询。 |
| 一致性 | 在主区域中实现强一致性，在辅助区域中实现最终一致性 | [五个定义完善的一致性级别](../documentdb/documentdb-consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性 |
| 定价 | 存储优化  | 吞吐量优化 |
| SLA | 99.9% 可用性 | 在单个区域中达到 99.99% 可用性，并能够添加更多区域以实现更高的可用性。 针对通用版本的[行业领先综合 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) |

## <a name="how-to-get-started"></a>如何入门

在 [Azure 门户](https://portal.azure.com)中创建 Azure Cosmos DB 帐户，并通过我们的[使用 .NET 的表 API 快速入门](create-table-dotnet.md)开始使用。 

## <a name="next-steps"></a>后续步骤

下面是一些可帮助你入门的指南：
* 使用现有的 NET 表 SDK 开始使用 [Azure Cosmos DB 的表 API](create-table-dotnet.md)。
* 了解[使用 Azure Cosmos DB 进行全局分发](../documentdb/documentdb-distribute-data-globally.md)。
* 了解 [Azure Cosmos DB 中预配的吞吐量](../documentdb/documentdb-request-units.md)。
