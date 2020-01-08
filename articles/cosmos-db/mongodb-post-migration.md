---
title: Azure Cosmos DB 的 MongoDB API 的迁移后优化步骤
description: 此文档提供从 MongoDB 到 Azure Cosmos DB 的 APi for Mongo DB 的迁移后优化技术。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c521546bedf1ebfd42bce4c50aa79b199553fd5a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441565"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时的迁移后优化步骤 

将 MongoDB 数据库中存储的数据迁移到 Azure Cosmos DB 的 API for MongoDB 后，你可以连接到 Azure Cosmos DB 并管理数据。 本指南提供迁移后应考虑的步骤。 有关迁移步骤，请参阅将[Mongodb 迁移到 Azure Cosmos DB 适用于 MongoDB 的 API 教程](../dms/tutorial-mongodb-cosmos-db.md)。

本指南介绍如何：
- [连接应用程序](#connect-account)
- [优化索引策略](#indexing)
- [为 MongoDB Azure Cosmos DB 的 API 配置全局分发](#distribute-data)
- [设置一致性级别](#consistency)

> [!NOTE]
> 应用程序级别的唯一强制迁移步骤是将应用程序中的连接字符串更改为指向新的 Azure Cosmos DB 帐户。 所有其他迁移步骤都是推荐的优化。
>

## <a id="connect-account"></a>连接应用程序 

1. 在新窗口中，登录到[Azure 门户](https://www.portal.azure.com/)
2. 在[Azure 门户](https://www.portal.azure.com/)的左窗格中，打开 "**所有资源**" 菜单并找到已将数据迁移到的 Azure Cosmos DB 帐户。
3. 打开 "**连接字符串**" 边栏选项卡。 右侧窗格中包含成功连接到帐户所需的全部信息。
4. 使用应用程序配置（或其他相关位置）中的连接信息，在应用中反映 Azure Cosmos DB 的 API for MongoDB 连接。 
![连接-字符串](./media/mongodb-post-migration/connection-string.png)

有关更多详细信息，请参阅将[MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md)页。

## <a id="indexing"></a>优化索引策略

默认情况下，在将数据迁移到 Azure Cosmos DB 的过程中，所有数据字段都将自动编制索引。 在许多情况下，此默认索引策略是可接受的。 通常，删除索引会优化写入请求并使默认索引策略（即自动索引）优化读取请求。

有关索引的详细信息，请参阅[Azure Cosmos DB 的 API For MongoDB 中的数据索引](mongodb-indexing.md)，以及[Azure Cosmos DB 文章中的索引](index-overview.md)。

## <a id="distribute-data"></a>全局分发数据

Azure Cosmos DB 在全球所有[Azure 区域](https://azure.microsoft.com/regions/#services)中提供。 为 Azure Cosmos DB 帐户选择默认的一致性级别后，可以关联一个或多个 Azure 区域（具体取决于全局分发需求）。 为了实现高可用性和业务连续性，我们始终建议至少在2个区域中运行。 可以[在 Azure Cosmos DB 中查看有关多区域部署的成本优化](optimize-cost-regions.md)提示。

若要在全球范围内分发数据，请参阅[在 Azure Cosmos DB 的适用于 MongoDB 的 API 上全局分发数据](tutorial-global-distribution-mongodb.md)。 

## <a id="consistency"></a>设置一致性级别
Azure Cosmos DB 提供5个明确定义的[一致性级别](consistency-levels.md)。 若要了解 MongoDB 与 Azure Cosmos DB 一致性级别之间的映射，请参阅[一致性级别和 Azure Cosmos DB api](consistency-levels-across-apis.md)。 默认的一致性级别为会话一致性级别。 更改一致性级别是可选的，你可以为应用优化一致性级别。 使用 Azure 门户更改一致性级别：

1. 在 "设置" 下，中转到 "**默认一致性**" 边栏选项卡。
2. 选择[一致性级别](consistency-levels.md)

大多数用户在默认会话一致性设置下保持其一致性级别。 但[对于不同的一致性级别，存在可用性和性能的折衷](consistency-levels-tradeoffs.md)。 

## <a name="next-steps"></a>后续步骤

* [将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md)
* [使用 Studio 3T 连接到 Azure Cosmos DB 帐户](mongodb-mongochef.md)
* [如何使用 Azure Cosmos DB 的适用于 MongoDB 的 API 全局分发读取](mongodb-readpreference.md)
* [使用 Azure Cosmos DB 的用于 MongoDB 的 API 过期数据](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)





