---
title: 排查在 Azure Cosmos DB API 的常见错误的 Mongo DB
description: 本文档讨论对适用于 MongoDB 在 Azure Cosmos DB 的 API 时遇到的常见问题进行故障排除方法。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735701"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Mongodb 对 Azure Cosmos DB 的 API 中常见的问题进行故障排除

Azure Cosmos DB 实现常见的 NoSQL 数据库，包括 MongoDB 网络的协议。 由于网络协议的实施，您可以以透明方式进行交互使用 Azure Cosmos DB 通过使用现有客户端 Sdk、 驱动程序和工具来使用 NoSQL 数据库。 Azure Cosmos DB 不使用网络兼容的 Api 提供的 NoSQL 数据库的任何数据库的任何源代码。 了解网络协议版本任何 MongoDB 客户端驱动程序可以连接到 Azure Cosmos DB。

虽然 Azure Cosmos DB 的 MongoDB 的 API 与 MongoDB 的传输协议 （查询运算符和 3.4 版中新增功能是目前以预览版形式提供） 3.2 版本兼容，有一些自定义错误代码对应于 Azure Cosmos DB特定错误。 此文章介绍了不同的错误、 错误代码和步骤来解决这些错误。

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 错误               | 代码  | 描述  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑扩展从 Azure 门户分配给一个容器或一组容器的吞吐量或可重试该操作。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系支持人员。 示例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| MongoDB 在线版本问题 | - | MongoDB 驱动程序的较旧版本不能在连接字符串中检测 Azure Cosmos 帐户的名称。 | 追加*appName = @**accountName** @* 末尾的 Cosmos DB 的 API for MongoDB 连接字符串，其中***accountName***是 Cosmos DB 帐户名称. |


## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 使用 Azure Cosmos DB 的用于 MongoDB 的 API 浏览 MongoDB [示例](mongodb-samples.md)。

