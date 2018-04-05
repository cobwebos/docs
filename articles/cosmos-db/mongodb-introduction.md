---
title: Azure Cosmos DB 简介：MongoDB API | Microsoft Docs
description: 了解如何通过常用 OSS MongoDB API 使用 Azure Cosmos DB 以低延迟存储和查询大量 JSON 文档。
keywords: 什么是 MongoDB
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: anhoh
ms.openlocfilehash: cdf1404d0070b74faeaca4dab7838167e2821323
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB 简介：MongoDB API

[Azure Cosmos DB](../cosmos-db/introduction.md) 是 Microsoft 针对任务关键型应用程序提供的全球分布式多模型数据库服务。 Azure Cosmos DB 在全球范围内提供[统包全局分发](distribute-data-globally.md)、[吞吐量和存储的弹性扩展](partition-data.md)、99% 的情况下低至个位数的毫秒级延迟以及得到保证的高可用性，所有这些均由[行业领先的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 提供支持。 Azure Cosmos DB [自动为数据编制索引](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，无需客户管理架构和索引。 它是多模型的，支持文档、键-值、图和列式数据模型。 

![Azure Cosmos DB：MongoDB API](./media/mongodb-introduction/cosmosdb-mongodb.png) 

可将 Azure Cosmos DB 数据库用作针对 [MongoDB](https://docs.mongodb.com/manual/introduction/) 编写的应用的数据存储。 此功能意味着，通过使用现有[驱动程序](https://docs.mongodb.org/ecosystem/drivers/)，为 MongoDB 编写的应用程序现在可与 Azure Cosmos DB 进行通信，并可使用 Azure Cosmos DB 数据库而不是 MongoDB 数据库。 在许多情况下，只需更改连接字符串便可从使用 Azure Cosmos DB 切换到使用 MongoDB。 使用此功能，可以在 Azure 云中轻松生成和运行 MongoDB 全球分布式数据库应用程序（利用 Azure Cosmos DB 及其[行业领先的综合性 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)），并且可继续使用熟悉的技能和 MongoDB 工具。

**MongoDB 兼容性**：可使用现有 MongoDB 专业知识、应用程序代码和工具，因为 Azure Cosmos DB 实现 MongoDB 3.4（版本 5）网络协议，并支持 [MongoDB 聚合管道](mongodb-feature-support.md#aggregation-pipeline)。 可使用 MongoDB 开发应用程序，并使用完全托管的全球分布式 Azure Cosmos DB 服务将其部署到生产环境。

## <a name="what-is-the-benefit-of-using-azure-cosmos-db-for-mongodb-applications"></a>将 Azure Cosmos DB 用于 MongoDB 应用程序的好处是什么？

**可弹性缩放的吞吐量和存储：**轻松增大或减小 MongoDB 数据库规模来满足应用程序需求。 数据存储在固态硬盘 (SSD) 上，以实现可预测的低延迟。 Azure Cosmos DB 支持几乎可以扩展到无限存储大小和预配吞吐量的 MongoDB 集合。 随着应用程序规模的增长，你可以弹性无缝地扩展 Azure Cosmos DB 且其性能可以预测。 

**多区域复制：**Azure Cosmos DB 以透明方式将数据复制到与 MongoDB 帐户关联的所有区域，使用户能够开发那些对全局性数据访问有要求的应用程序，与此同时还在一致性、可用性和性能方面做出权衡，所有这些都有相应的保证。 Azure Cosmos DB 提供具有多宿主 API 的透明区域故障转移，还可以弹性缩放全局吞吐量和存储。 在[全球分布数据](distribute-data-globally.md)中了解详细信息。

无需管理服务器：无需管理和缩放 MongoDB 数据库。 Azure Cosmos DB 是完全托管的服务，这意味着无需自己管理任何基础结构或虚拟机。 Azure Cosmos DB 在 30 多个 [Azure 区域](https://azure.microsoft.com/regions/services/)中可用。

**可调整的一致性级别：**Azure Cosmos DB 当前实现了 MongoDB 3.4 版，其中具有两个一致性设置：强一致性和最终一致性。 由于 Azure Cosmos DB 是多 api，因此一致性设置在帐户级别适用，并由每个 API 控制一致性的实施。 在 MongoDB 3.6 以前，没有会话一致性的概念，因此如果将 MongoDB API 帐户设置为使用会话一致性，则使用 MongoDB API 时，该一致性会降级为最终一致性。 如果需要向某个 MongoDB API 帐户提供“读取自己的写入”保证，该帐户的默认一致性级别应设置为“强”或“有限过期”。 有关详细信息，请参阅[使用一致性级别最大化可用性和性能](consistency-levels.md)。

| Azure Cosmos DB 的默认一致性级别 |   Mongo API (3.4) |
|---|---|
|最终| 最终 |
|一致前缀| 具有一致顺序的最终 |
|会话| 具有一致顺序的最终 |
|有限过期| 非常 |
| 非常 | 非常 |

**自动编制索引：**默认情况下，Azure Cosmos DB 自动为 MongoDB 数据库文档中的所有属性编制索引，无需任何架构或创建二级索引。 此外，唯一索引功能可以针对已在 Azure Cosmos DB 中自动编制索引的所有文档字段启用唯一性约束。

**企业级**：Azure Cosmos DB 支持多个本地副本，在面对本地和区域故障时提供 99.99％ 的可用性和数据保护。 Azure Cosmos DB 具有企业级[符合性认证](https://www.microsoft.com/trustcenter)和安全功能。 

在此视频中通过 Azure Cosmos DB 高级项目经理 Aleksey Savateyev 了解详细信息。

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T136/player]
> 

## <a name="how-to-get-started"></a>如何入门

请按照 MongoDB 快速入门来创建 Azure Cosmos DB 帐户，并迁移现有 MongoDB 应用程序以使用 Azure Cosmos DB，或者生成一个新的应用程序：

* [迁移现有的 Node.js MongoDB Web 应用](create-mongodb-nodejs.md)。
* [使用 .NET 和 Azure 门户生成 MongoDB API Web 应用](create-mongodb-dotnet.md)
* [使用 Java 和 Azure 门户生成 MongoDB API 控制台应用](create-mongodb-java.md)

## <a name="next-steps"></a>后续步骤

有关 Azure Cosmos DB MongoDB API 的信息已整合到整个 Azure Cosmos DB 文档中，以下是几个入门指南：

* 若要了解如何获取帐户连接字符串信息，请参阅[连接到 MongoDB 帐户](connect-mongodb-account.md)教程。
* 要了解如何在 Studio 3T 中创建 Azure Cosmos DB 数据库和 MongoDB 应用之间的连接，请参阅[将 Studio 3T (MongoChef) 与 Azure Cosmos DB 配合使用](mongodb-mongochef.md)教程。
* 按照[将数据迁移到具有 MongoDB 协议支持的 Azure Cosmos DB](mongodb-migrate.md) 教程将数据导入到 MongoDB 的 API 数据库。
* 使用 [Robomongo](mongodb-robomongo.md) 连接到 MongoDB 的 API 帐户。
* 了解操作将多少 RU 用于 [GetLastRequestStatistics 命令和 Azure 门户指标](request-units.md#GetLastRequestStatistics)。
* 了解如何[配置全局分布的应用的读取首选项](../cosmos-db/tutorial-global-distribution-mongodb.md)。
