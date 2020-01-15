---
title: 排查 Azure Cosmos DB 的 Mongo DB API 的常见错误
description: 此文档讨论了解决 Azure Cosmos DB 的 MongoDB API 中遇到的常见问题的方法。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: lbosq
ms.openlocfilehash: d9a4e336f582e866fd057f6c281f892ce07b34fc
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941847"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>排查 Azure Cosmos DB 的 MongoDB API 中的常见问题

Azure Cosmos DB 实现常见 NoSQL 数据库的网络协议，其中包括 MongoDB。 由于线路协议实现，你可以使用现有的客户端 Sdk、驱动程序和使用 NoSQL 数据库的工具以透明方式与 Azure Cosmos DB 交互。 Azure Cosmos DB 不使用数据库的任何源代码为任何 NoSQL 数据库提供与网络兼容的 Api。 任何了解线路协议版本的 MongoDB 客户端驱动程序都可以连接到 Azure Cosmos DB。

尽管 Azure Cosmos DB 的 MongoDB API 与 MongoDB 的网络协议版本3.2 兼容（查询运算符和在版本3.4 中添加的功能当前以预览版提供），但有一些对应于 Azure Cosmos DB 的自定义错误代码特定的错误。 本文介绍了不同的错误、错误代码以及解决这些错误的步骤。

## <a name="common-errors-and-solutions"></a>常见错误和解决方案

| 错误               | 代码  | Description  | 解决方案  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的请求单位总数超过了集合的预配请求单位率，已达到限制。 | 请考虑从 Azure 门户缩放分配给容器或容器集的吞吐量，或重试该操作。 |
| ExceededMemoryLimit | 16501 | 作为一种多租户服务，操作已超出客户端的内存配额。 | 通过限制性更强的查询条件缩小操作的作用域，或者通过 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)联系支持人员。 示例： `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 排除了与指定的 order by 项对应的索引路径/order by 查询不具有可供其提供服务的相应组合索引。 | 2 | 查询请求对未建立索引的字段进行排序。 | 为尝试的排序查询创建匹配的索引（或复合索引）。 |
| MongoDB 线路版本问题 | - | 更早版本的 MongoDB 驱动程序无法在连接字符串中检测到 Azure Cosmos 帐户的名称。 | 向 MongoDB 连接字符串的 Cosmos DB 的 API 末尾追加*appName = @**accountName**@* ，其中***accountName***是你的 Cosmos DB 帐户名称。 |


## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 使用 Azure Cosmos DB 的用于 MongoDB 的 API 浏览 MongoDB [示例](mongodb-samples.md)。

