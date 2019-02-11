---
title: 一致性级别和 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 中 API 的一致性级别。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806991"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB SQL API 本机支持 Azure Cosmos DB 提供的五个一致性模型。 使用 Azure Cosmos DB 时，SQL API 是默认 API。 

Azure Cosmos DB 还为常用数据库提供对与线路协议兼容的 API 的本机支持。 数据库包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 这些数据库既没有提供准确定义的一致性模型，也没有为一致性级别提供由 SLA 支持的保证。 它们通常仅提供 Azure Cosmos DB 提供的五个一致性模型的一个子集。 对于 SQL API、Gremlin API 和表 API，会使用 Azure Cosmos DB 帐户上配置的默认一致性级别。 

以下部分介绍了由 Apache Cassandra 4.x 和 MongoDB 3.4 的 OSS 客户端驱动程序请求的数据一致性之间的映射。 本文档还介绍了 Apache Cassandra 和 MongoDB 对应的 Azure Cosmos DB 一致性级别。

## <a id="cassandra-mapping"></a>Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的映射

下表显示了 Apache Cassandra 与 Azure Cosmos DB 中的一致性级别之间的一致性映射。 对于每个 Cassandra 读写一致性级别，对应的 Cosmos DB 一致性级别提供更强的（即更严格的）保证。

下表显示了 Azure Cosmos DB 和 Cassandra 之间的“写一致性映射”：

| Cassandra | Azure Cosmos DB | 保证 |
| - | - | - |
|ALL|非常  | 可线性化 |
| EACH_QUORUM   | 非常    | 可线性化 | 
| QUORUM, SERIAL |  非常 |    可线性化 |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | 一致前缀 |全局一致前缀 |
| EACH_QUORUM   | 非常    | 可线性化 |
| QUORUM, SERIAL |  非常 |    可线性化 |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | 一致前缀 | 全局一致前缀 |
| QUORUM, SERIAL | 非常   | 可线性化 |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | 一致前缀 | 全局一致前缀 |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | 有限过期 | <ul><li>有限过期。</li><li>至多到 K 版本或在 t 时间之后。</li><li>读取区域中提交的最新值。</li></ul> |
| ONE, LOCAL_ONE, ANY   | 一致前缀 | 各区域一致前缀 |

下表显示了 Azure Cosmos DB 和 Cassandra 之间的“读一致性映射”：

| Cassandra | Azure Cosmos DB | 保证 |
| - | - | - |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE | 非常  | 可线性化|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |非常 |   可线性化 |
|LOCAL_ONE, ONE | 一致前缀 | 全局一致前缀 |
| ALL, QUORUM, SERIAL   | 非常    | 可线性化 |
| LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  一致前缀   | 全局一致前缀 |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM |    一致前缀   | 全局一致前缀 |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |非常 |   可线性化 |
| LOCAL_ONE, ONE    | 一致前缀 | 全局一致前缀|
| ALL, QUORUM, SERIAL   Strong  Linearizability
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |一致前缀  | 全局一致前缀 |
|ALL    |非常 |可线性化 |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  |一致前缀  |全局一致前缀|
|ALL, QUORUM, SERIAL    Strong  Linearizability
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |一致前缀  |全局一致前缀 |
|ALL    |非常 | 可线性化 |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | 一致前缀 | 全局一致前缀 |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  有限过期   | <ul><li>有限过期。</li><li>至多到 K 版本或在 t 时间之后。 </li><li>读取区域中提交的最新值。</li></ul>
| LOCAL_ONE, ONE |一致前缀 | 各区域一致前缀 |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | 一致前缀 | 各区域一致前缀 |


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
* [Azure Cosmos DB 的 API for MongoDB 支持的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)