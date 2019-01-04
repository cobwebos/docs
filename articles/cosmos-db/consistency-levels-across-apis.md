---
title: 一致性级别和 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 中 API 的一致性级别。
keywords: 一致性, azure cosmos db, azure, 模型, mongodb, cassandra, 图, 表, Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 277a064d93e2ebcea82f3909b3fd16328a775105
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832490"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB SQL API 本机支持 Azure Cosmos DB 提供的五个一致性模型。 使用 Azure Cosmos DB 时，SQL API 是默认 API。 

Azure Cosmos DB 还为常用数据库提供对与线路协议兼容的 API 的本机支持。 数据库包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 这些数据库既没有提供准确定义的一致性模型，也没有为一致性级别提供由 SLA 支持的保证。 它们通常仅提供 Azure Cosmos DB 提供的五个一致性模型的一个子集。 对于 SQL API、Gremlin API 和表 API，会使用 Azure Cosmos DB 帐户上配置的默认一致性级别。 

以下部分介绍了由 Apache Cassandra 4.x 和 MongoDB 3.4 的 OSS 客户端驱动程序请求的数据一致性之间的映射。 本文档还介绍了 Apache Cassandra 和 MongoDB 对应的 Azure Cosmos DB 一致性级别。

## <a id="cassandra-mapping"></a>Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的映射

下表显示了 Apache Cassandra 4.x 客户端与 Azure Cosmos DB 中的默认一致性级别之间的“读取一致性”映射。 下表显示了多区域和单区域部署。

| **Apache Cassandra 4.x** | **Azure Cosmos DB（多区域）** | **Azure Cosmos DB（单区域）** |
| - | - | - |
| ONE, TWO, THREE | 一致前缀 | 一致前缀 |
| LOCAL_ONE | 一致前缀 | 一致前缀 |
| QUORUM, ALL, SERIAL | “有限过期”为默认值。 “非常”在个人预览版中提供。 | 非常 |
| LOCAL_QUORUM | 有限过期性 | 非常 |
| LOCAL_SERIAL | 有限过期性 | 非常 |

## <a id="mongo-mapping"></a>MongoDB 3.4 与 Azure Cosmos DB 一致性级别之间的映射

下表显示了 MongoDB 3.4 与 Azure Cosmos DB 中的默认一致性级别之间的“读取关注点”映射。 下表显示了多区域和单区域部署。

| **MongoDB 3.4** | **Azure Cosmos DB（多区域）** | **Azure Cosmos DB（单区域）** |
| - | - | - |
| 线性化 | 非常 | 非常 |
| 多数 | 有限过期性 | 非常 |
| Local | 一致前缀 | 一致前缀 |

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB API 与开源 API 之间的一致性级别和兼容性。 请参阅以下文章：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB MongoDB API 支持的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)