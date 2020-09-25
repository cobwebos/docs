---
title: Azure Cosmos DB 的 MongoDB API 的迁移后优化步骤
description: 本文档提供从 MongoDB 迁移到 Azure Cosmos DB MongoDB API 后的优化方法。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: 93a9058dec66ffe2e1c7ad4b15d9bf4eba12c647
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358519"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB MongoDB API 时的迁移后优化步骤

将 MongoDB 数据库中存储的数据迁移到 Azure Cosmos DB 的 API for MongoDB 后，你可以连接到 Azure Cosmos DB 并管理数据。 本指南提供迁移后应考虑的步骤。 有关迁移步骤，请参阅将 [Mongodb 迁移到 Azure Cosmos DB 适用于 MongoDB 的 API 教程](../dms/tutorial-mongodb-cosmos-db.md) 。

本指南介绍如何：

- [连接应用程序](#connect-your-application)
- [优化索引策略](#optimize-the-indexing-policy)
- [为 MongoDB Azure Cosmos DB 的 API 配置全局分发](#globally-distribute-your-data)
- [设置一致性级别](#set-consistency-level)

> [!NOTE]
> 必须在应用程序级别执行的唯一一个迁移后步骤是将应用程序中的连接字符串更改为指向新的 Azure Cosmos DB 帐户。 所有其他迁移步骤都是建议的优化措施。
>

## <a name="connect-your-application"></a>连接应用程序

1. 在新窗口中，登录到 [Azure 门户](https://www.portal.azure.com/)
2. 在 [Azure 门户](https://www.portal.azure.com/)的左窗格中打开“所有资源”菜单，找到已将数据迁移到的 Azure Cosmos DB 帐户。 
3. 打开“连接字符串”边栏选项卡。  右侧窗格中包含成功连接到帐户所需的全部信息。
4. 使用应用程序配置（或其他相关位置）中的连接信息来反映应用中的 Azure Cosmos DB MongoDB API 连接。
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="屏幕截图显示连接字符串的设置。":::

有关更多详细信息，请参阅[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md) 页。

## <a name="optimize-the-indexing-policy"></a>优化索引策略

在将数据迁移到 Azure Cosmos DB 的过程中，默认将为所有数据字段自动编制索引。 在许多情况下，此默认索引策略是可接受的。 一般情况下，删除索引可以优化写入请求，而使用默认索引策略（即自动编制索引）可以优化读取请求。

有关索引的详细信息，请参阅文章 [Azure Cosmos DB MongoDB API 中的数据索引](mongodb-indexing.md)和 [Azure Cosmos DB 中的索引](index-overview.md)。

## <a name="globally-distribute-your-data"></a>全局分发数据

Azure Cosmos DB 在全球范围的所有 [Azure 区域](https://azure.microsoft.com/regions/#services)中推出。 为 Azure Cosmos DB 帐户选择默认的一致性级别后，可以将一个或多个 Azure 区域关联 (，具体取决于你的全局分布需要) 。 为了实现高可用性和业务连续性，我们始终建议至少在 2 个区域中运行 Azure Cosmos DB。 可以查看有关 [Azure Cosmos DB 中多区域部署的优化成本](optimize-cost-regions.md)的提示。

若要在全球范围内分发数据，请参阅 [在 Azure Cosmos DB 的适用于 MongoDB 的 API 上全局分发数据](tutorial-global-distribution-mongodb.md)。

## <a name="set-consistency-level"></a>设置一致性级别

Azure Cosmos DB 提供 5 个妥善定义的[一致性级别](consistency-levels.md)。 若要了解 MongoDB 与 Azure Cosmos DB 一致性级别之间的映射，请参阅[一致性级别和 Azure Cosmos DB API](consistency-levels-across-apis.md)。 默认一致性级别是会话一致性级别。 更改一致性级别是可选操作，可为应用优化一致性级别。 若要使用 Azure 门户更改一致性级别：

1. 转到“设置”下的“默认一致性”边栏选项卡。 
2. 选择所需的[一致性级别](consistency-levels.md)

大多数用户会将其一致性级别保留为默认的会话一致性设置。 但是，[不同一致性级别的可用性和性能各有利弊](consistency-levels-tradeoffs.md)。

## <a name="next-steps"></a>后续步骤

* [将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md)
* [使用 Studio 3T 连接到 Azure Cosmos DB 帐户](mongodb-mongochef.md)
* [如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 全局分发读取](mongodb-readpreference.md)
* [使用 Azure Cosmos DB 的用于 MongoDB 的 API 过期数据](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
