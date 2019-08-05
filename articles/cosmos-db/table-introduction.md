---
title: Azure Cosmos DB 表 API 简介
description: 了解如何通过 Azure Tables API 使用 Azure Cosmos DB 以低延迟存储和查询大量键值对数据。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: be6a402673fd2d3ba01451c6ea04e723cbdfa292
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597504"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB 简介：表 API

[Azure Cosmos DB](introduction.md) 提供的表 API 适用于为 Azure 表存储编写且需要以下高级功能的应用程序：

* [统包式全局分发](distribute-data-globally.md)。
* 全球范围内[专用的吞吐量](partition-data.md)。
* 99% 的情况下低至个位数的毫秒级延迟。
* 保证高可用性。
* 自动编制辅助索引。

为 Azure 表存储编写的应用程序无需更改代码便可使用表 API 迁移到 Azure Cosmos DB，并可充分利用高级功能。 表 API 包含可用于 .NET、Java、Python 和 Node.js 的客户端 SDK。

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 目前处于维护模式，不久将被弃用。 请升级到新的 .NET Standard 库 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) 来继续获取表 API 支持的最新功能。

## <a name="table-offerings"></a>表产品/服务
如果当前使用 Azure 表存储，可以通过移到 Azure Cosmos DB 表 API 获得以下好处：

| | Azure 表存储 | Azure Cosmos DB 表 API |
| --- | --- | --- |
| Latency | 快速，但对延迟没有上限。 | 读取操作和写入操作的低至个位数的毫秒级延迟（通过以下统计数据提供支持：在全球任何位置，对于任何规模，99% 的情况下读取和写入操作的延迟 < 10 毫秒）。 |
| Throughput | 可变吞吐量模型。 表的可伸缩性限制为 20,000 个操作/秒。 | 使用 SLA 支持的[每个表专用保留吞吐量](request-units.md)实现高度可缩放。 帐户没有吞吐量上限，每个表支持 >1000 万个操作/秒。 |
| 全球分布 | 具有一个可选可读辅助读取区域以实现高可用性的单一区域。 不能启动故障转移。 | 从 1 个到任意多个区域进行[统包式全局分发](distribute-data-globally.md)。 支持在全球各地随时[自动和手动故障转移](high-availability.md)。 多主数据库功能，允许任何区域接受写入操作。 |
| 索引 | 仅对 PartitionKey 和 RowKey 建立主索引。 没有辅助索引。 | 默认情况下，自动对所有属性完成编制索引，不进行索引管理。 |
| Query | 执行查询时使用主键的索引，否则进行扫描。 | 查询可以利用属性的自动索引缩短查询时间。 |
| 一致性 | 在主要区域内实现强一致性。 在辅助区域内实现最终一致性。 | [五个定义完善的一致性级别](consistency-levels.md)可基于应用程序需要权衡可用性、延迟、吞吐量和一致性。 |
| 定价 | 存储优化。 | 吞吐量优化。 |
| SLA | 可用性为 99.9% 至 99.99%，具体取决于复制策略。 | 单区域帐户的读取可用性为 99.999%、写入可用性为 99.99%，多区域帐户的写入可用性为 99.999%。 [综合性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)：涵盖可用性、延迟、吞吐量和一致性。 |

## <a name="get-started"></a>入门

在 [Azure 门户](https://portal.azure.com)中创建 Azure Cosmos DB 帐户。 然后开始使用我们的[通过 .NET 使用表 API 的快速入门](create-table-dotnet.md)。 

> [!IMPORTANT]
> 如果已在预览期间创建表 API 帐户，请[新建表 API 帐户](create-table-dotnet.md#create-a-database-account)，这样才能使用正式版表 API SDK。
>

## <a name="next-steps"></a>后续步骤

下面是一些可帮助你入门的指南：
* [使用表 API 生成 .NET 应用程序](create-table-dotnet.md)
* [在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)
* [使用表 API 查询表数据](tutorial-query-table.md)
* [了解如何使用表 API 设置 Azure Cosmos DB 全局分发](tutorial-global-distribution-table.md)
* [Azure Cosmos DB 表 .NET API](table-sdk-dotnet.md)
* [Azure Cosmos DB 表 Java API](table-sdk-java.md)
* [Azure Cosmos DB 表 Node.js API](table-sdk-nodejs.md)
* [用于 Python 的 Azure Cosmos DB 表 SDK](table-sdk-python.md)

