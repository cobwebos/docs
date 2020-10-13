---
title: 排查 Azure Cosmos DB 的 API for Mongo DB 中的常见错误
description: 本文档讨论解决 Azure Cosmos DB 的 API for MongoDB 中遇到的常见问题的方法。
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: 27a9c7eb48c4a0148401c0d146a50a5197593806
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91409623"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题

下文介绍使用 Azure Cosmos DB API for MongoDB 时数据库的常见错误和解决方案。

>[!Note]
> Azure Cosmos DB 不托管 MongoDB 引擎。 它提供 MongoDB [线路协议版本 3.6](mongodb-feature-support-36.md) 的实现，以及对[线路协议版本 3.2](mongodb-feature-support.md) 的传统支持，因此其中一些错误仅可在 Azure Cosmos DB API for MongoDB 中找到。 

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 错误               | 代码  | 说明  | 解决方案  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | 请求已超过 60 秒执行时间的超时。 | 此问题的原因可能有很多。 其中一个是当前分配的请求单位容量不足，无法完成请求。 可以通过增加该集合或数据库的请求单位来解决此问题。 其他情况下，可以通过将大型请求拆分为较小请求来规避此问题。 |
| TooManyRequests     | 16500 | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户对分配给一个容器或一组容器的吞吐量进行缩放，也可以重试该操作。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系技术支持。 示例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 与指定的 order-by 项对应的索引路径将排除/order by 查询没有可以从中提供服务的对应复合索引。 | 2 | 查询请求对未建立索引的字段进行排序。 | 为所尝试的排序查询创建匹配索引（或复合索引）。 |
| MongoDB 线路版本问题 | - | 旧版本的 MongoDB 驱动程序无法在连接字符串中检测 Azure Cosmos 帐户的名称。 | 在 Cosmos DB 的 API for MongoDB 连接字符串末尾追加 appName=@**accountName**@**，其中 ***accountName*** 是 Cosmos DB 帐户名。 |

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 通过 Azure Cosmos DB 的用于 MongoDB 的 API 来浏览 MongoDB [示例](mongodb-samples.md)。

