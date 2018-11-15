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
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956377"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Cosmos DB SQL API 原生支持 Azure Cosmos DB 提供的五个一致性模型，Cosmos DB SQL API 是使用 Cosmos DB 时的默认 API。 除 SQL API 外，Cosmos DB 还为常用数据库（如 MongoDB、Apache Cassandra、Gremlin 和 Azure 表）提供了对线路协议兼容的 API 的本机支持。 这些数据库既没有提供准确定义的一致性模型，也没有为一致性级别提供由 SLA 支持的保证。 这些数据库通常仅提供 Cosmos DB 提供的五个一致性模型的一个子集。 对于 SQL API、Gremlin API 和表 API，将使用你在 Cosmos 帐户上配置的默认一致性级别。

以下各部分显示了 Apache Cassandra 4.x 和 MongoDB 3.4 的 OSS 客户端驱动程序所请求的数据一致性（分别使用 Cassandra API 和 MongoDB API 时）与相应 Cosmos DB 一致性级别之间的映射。

## <a id="cassandra-mapping"></a>Apache Cassandra 与 Cosmos DB 一致性级别之间的映射

针对多区域和单区域部署，下表显示了 Apache Cassandra 4.x 客户端与 Cosmos DB 中的默认一致性级别之间的“读取一致性”映射。

| **Apache Cassandra 4.x** | **Cosmos DB（多区域）** | **Cosmos DB（单区域）** |
| - | - | - |
| ONE, TWO, THREE | 一致前缀 | 一致前缀 |
| LOCAL_ONE | 一致前缀 | 一致前缀 |
| QUORUM, ALL, SERIAL | 有限过期（默认）或非常（个人预览版中） | 非常 |
| LOCAL_QUORUM | 有限过期 | 非常 |
| LOCAL_SERIAL | 有限过期 | 非常 |

## <a id="mongo-mapping"></a>MongoDB 3.4 与 Cosmos DB 一致性级别之间的映射

针对多区域和单区域部署，下表显示了 MongoDB 3.4 与 Cosmos DB 中的默认一致性级别之间的“读取关注点”映射。

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