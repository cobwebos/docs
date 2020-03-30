---
title: 排查 Azure Cosmos DB 的 API for Mongo DB 中的常见错误
description: 本文档讨论解决 Azure Cosmos DB 的 API for MongoDB 中遇到的常见问题的方法。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941847"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>解决 Azure Cosmos DB 的 API for MongoDB 中的常见问题

Azure Cosmos DB 可实现常用 NoSQL 数据库（包括 MongoDB）的线路协议。 由于线路协议的实现，你可以通过使用与 NoSQL 数据库一起工作的现有客户端 SDK、驱动程序和工具来透明地与 Azure Cmoss DB 进行交互。 Azure Cosmos DB 不使用数据库的任何源代码为任何 NoSQL 数据库提供与线路兼容的 API。 任何理解线路协议版本的 MongoDB 客户端驱动程序都可以连接到 Azure Cosmos DB。

虽然 Azure Cosmos DB 的 API for MongoDB 与 3.2 版 MongoDB 线路协议兼容（版本 3.4 中添加的查询运算符和功能目前以预览版的形式提供），但有一些自定义错误代码与 Azure Cosmos DB 特定错误相对应。 本文介绍了不同的错误、错误代码以及解决这些错误的步骤。

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 错误               | 代码  | 描述  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户对分配给一个容器或一组容器的吞吐量进行缩放，也可以重试该操作。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系支持人员。 示例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 与指定的 order-by 项对应的索引路径将排除/order by 查询没有可以从中提供服务的对应复合索引。 | 2 | 查询请求对未建立索引的字段进行排序。 | 为所尝试的排序查询创建匹配索引（或复合索引）。 |
| MongoDB 线路版本问题 | - | 旧版本的 MongoDB 驱动程序无法在连接字符串中检测 Azure Cosmos 帐户的名称。 | 在 Cosmos DB 的 API for MongoDB 连接字符串末尾追加 appName=@**accountName**@**，其中 ***accountName*** 是 Cosmos DB 帐户名。 |


## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 使用 Azure Cosmos DB 的用于 MongoDB 的 API 浏览 MongoDB [示例](mongodb-samples.md)。

