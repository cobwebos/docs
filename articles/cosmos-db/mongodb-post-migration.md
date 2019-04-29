---
title: 迁移后优化步骤时使用 Azure Cosmos DB API for MongoDB
description: 此文档提供有关迁移后优化技术从 MongoDB 到 Azure Cosmos DB APi 的 Mongo DB。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: roaror
ms.openlocfilehash: c0c761fef481a1fdaa027f1329e9a4e72d62985a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331192"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>迁移后优化步骤时使用 Azure Cosmos DB API for MongoDB 

将 MongoDB 数据库中存储的数据迁移到 Azure Cosmos DB 的 API for MongoDB 后，你可以连接到 Azure Cosmos DB 并管理数据。 本指南提供应考虑在迁移后的步骤。 请参阅[迁移到 Azure Cosmos DB API for MongoDB 教程中的 MongoDB](../dms/tutorial-mongodb-cosmos-db.md)的迁移步骤。

本指南介绍如何：
- [连接你的应用程序](#connect-account)
- [优化的索引策略](#indexing)
- [配置全局分发的 Azure Cosmos DB API for MongoDB](#distribute-data)
- [将一致性级别设置](#consistency)

> [!NOTE]
> 在您的应用程序级别上唯一的必填的迁移后步骤正在改变你的应用程序以指向新的 Azure Cosmos DB 帐户中的连接字符串。 其他所有迁移步骤，都建议使用优化。
>

## <a id="connect-account"></a>连接你的应用程序 

1. 在新窗口中登录到[Azure 门户](https://www.portal.azure.com/)
2. 从[Azure 门户](https://www.portal.azure.com/)，在左窗格中打开**的所有资源**菜单并找到你的数据已迁移到 Azure Cosmos DB 帐户。
3. 打开**连接字符串**边栏选项卡。 右侧窗格中包含成功连接到帐户所需的全部信息。
4. 使用 MongoDB 连接应用程序中的应用程序的配置 （或其他相关的位置） 以反映 Azure Cosmos DB 的 API 中的连接信息。 
![Connection-String](./media/mongodb-post-migration/connection-string.png)

有关更多详细信息，请参阅[连接到 Azure Cosmos DB 的 MongoDB 应用程序](connect-mongodb-account.md)页。

## <a id="indexing"></a>优化的索引策略

所有数据字段都自动编制都索引，默认情况下，迁移到 Azure Cosmos DB 的数据的过程。 在许多情况下，此默认索引策略是可接受的。 一般情况下，删除索引优化写入请求，并具有默认索引策略 （即，自动索引） 来优化读取的请求。

有关索引的详细信息，请参阅[数据中的索引编制 Azure Cosmos DB API for MongoDB](mongodb-indexing.md)并将[Azure Cosmos DB 中的索引编制](index-overview.md)文章。

## <a id="distribute-data"></a>全局分发数据

Azure Cosmos DB 是可用于所有[Azure 区域](https://azure.microsoft.com/regions/#services)全球范围内。 选择你的 Azure Cosmos DB 帐户默认一致性级别后，可以将一个或多个 Azure 区域 （具体取决于您的全局分发需求） 相关联。 有关高可用性和业务连续性，我们始终建议在至少 2 个区域中运行。 你可以查看的提示[优化 Azure Cosmos DB 中的多区域部署的成本](optimize-cost-regions.md)。

若要全局分发数据，请参阅[将 Azure Cosmos DB API 上的全局数据分发适用于 MongoDB](tutorial-global-distribution-mongodb.md)。 

## <a id="consistency"></a>将一致性级别设置
Azure Cosmos DB 提供 5 定义完善[一致性级别](consistency-levels.md)。 若要了解 MongoDB 和 Azure Cosmos DB 一致性级别之间的映射信息，请阅读[一致性级别和 Azure Cosmos DB Api](consistency-levels-across-apis.md)。 默认一致性级别为会话一致性级别。 更改一致性级别是可选的可以优化它为你的应用。 若要更改使用 Azure 门户的一致性级别：

1. 转到**默认一致性**下设置边栏选项卡。
2. 选择你[一致性级别](consistency-levels.md)

大多数用户将保留为默认会话一致性设置其一致性级别。 但是，有[各种一致性级别的可用性与性能折](consistency-levels-tradeoffs.md)。 

## <a name="next-steps"></a>后续步骤

* [将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md)
* [连接到 Azure Cosmos DB 帐户使用 Studio 3T](mongodb-mongochef.md)
* [如何全局分配读取使用 Azure Cosmos DB API for MongoDB](mongodb-readpreference.md)
* [使用 Azure Cosmos DB API 的数据过期 mongodb](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)





