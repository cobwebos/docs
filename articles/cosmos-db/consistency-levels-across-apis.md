---
title: 一致性级别和 Azure Cosmos DB API | Microsoft Docs
description: 了解 Azure Cosmos DB 中 API 的一致性级别。
keywords: 一致性, azure cosmos db, azure, 模型, mongodb, cassandra, 图, 表, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243992"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>一致性级别和 Cosmos DB API

SQL API 在本机状态下支持五种一致性模型，SQL API 是使用 Cosmos DB 时的默认 API。 除 SQL API 外，Cosmos DB 还为常用数据库（如 MongoDB、Apache Cassandra、Gremlin 和 Azure 表）提供了对线路协议兼容的 API 的本机支持。 这些数据库既不提供精确定义的一致性模型，也不提供 SLA 支持的一致性级别保证，并且通常仅提供 Cosmos DB 所提供五种一致性模型的子集。 对于 SQL API、Gremlin API 和表 API，会使用 Cosmos 帐户上配置的默认一致性级别。

下表显示了 Apache Cassandra 4.x 和 MongoDB 3.4 的 OSS 客户端驱动程序所请求的数据一致性（分别使用 Cassandra API 和 MongoDB API 时）与相应 Cosmos DB 一致性级别之间的映射。

## <a id="cassandra-mapping"> </a>映射 Apache Cassandra 和 Cosmos DB 一致性级别

下表显示了 Apache Cassandra 4.x 客户端与 Cosmos DB“默认”一致性级别之间的读取一致性映射（对于多区域和单区域部署）。

| **Apache Cassandra 4.x** | **Cosmos DB（多区域）** | **Cosmos DB（单区域）** |
| - | - | - |
| ONE, TWO, THREE | 一致前缀 | 一致前缀 |
| LOCAL_ONE | 一致前缀 | 一致前缀 |
| QUORUM, ALL, SERIAL | 有限过期（默认）或非常（个人预览版中） | 非常 |
| LOCAL_QUORUM | 有限过期 | 非常 |
| LOCAL_SERIAL | 有限过期 | 非常 |

## <a id="mongo-mapping"></a>MongoDB 3.4 与 Cosmos DB 一致性级别之间的映射

下表显示了 MongoDB 3.4 和 Cosmos DB“默认”一致性级别的“读取问题”的映射（对于多区域和单区域部署）。

| **MongoDB 3.4** | **Cosmos DB（多区域）** | **Cosmos DB（单区域）** |
| - | - | - |
| 线性化 | 非常 | 非常 |
| 多数 | 有限过期 | 非常 |
| Local | 一致前缀 | 一致前缀 |

## <a name="next-steps"></a>后续步骤

参阅以下文章，详细了解 Cosmos DB API 与开源 API 之间的一致性级别和兼容性：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [Cosmos DB MongoDB API 支持的 MongoDB 功能](mongodb-feature-support.md)
* [Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)