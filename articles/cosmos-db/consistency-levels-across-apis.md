---
title: 一致性级别和 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 和 Apache Cassandra 中不同 Api 之间的一致性级别映射，MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 819929fb157444ae53df113c0318dd18146156c9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246833"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB 为流行的数据库提供与有线协议兼容的 Api 的本机支持。 其中包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 这些数据库不提供准确定义的一致性模型或一致性级别的 SLA 支持保证。 它们通常仅提供 Azure Cosmos DB 提供的五个一致性模型的一个子集。 

使用 SQL API、Gremlin API 和表 API 时，将使用在 Azure Cosmos 帐户上配置的默认一致性级别。 

使用 Cassandra API 或 Azure Cosmos DB 的适用于 MongoDB 的 API 时，应用程序将分别获取 Apache Cassandra 和 MongoDB 提供的完整一致性级别，同时提供更强的一致性和持久性保证。 本文档显示了 Apache Cassandra 和 MongoDB 一致性级别的相应 Azure Cosmos DB 一致性级别。

## <a id="cassandra-mapping"></a>Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的映射

与 Azure Cosmos DB 不同的是，Apache Cassandra 本身不提供准确定义的一致性保证。  Apache Cassandra 提供了写入一致性级别和读取一致性级别，以实现高可用性、一致性和延迟折衷。 使用 Azure Cosmos DB 的 Cassandra API 时： 

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到在读取请求上动态配置的 Azure Cosmos DB 一致性级别之一。 

下表说明了在使用 Cassandra API 时，如何将本机 Cassandra 一致性级别映射到 Azure Cosmos DB 的一致性级别：  

[![Cassandra 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB 与 Azure Cosmos DB 一致性级别之间的映射

与 Azure Cosmos DB 不同，本机 MongoDB 不提供准确定义的一致性保证。 相反，native MongoDB 允许用户配置以下一致性保证：写入问题、读取关注性和 isMaster 指令-将读取操作定向到主副本或辅助副本以实现所需的一致性级别。 

使用 Azure Cosmos DB 的 MongoDB API 时，MongoDB 驱动程序将写入区域视为主副本，而所有其他区域都是读取副本。 你可以选择与你的 Azure Cosmos 帐户关联的区域作为主要副本。 

使用 Azure Cosmos DB 的 MongoDB API 时：

* 写入问题映射到在 Azure Cosmos 帐户上配置的默认一致性级别。
 
* Azure Cosmos DB 会将 MongoDB 客户端驱动程序指定的读取问题动态映射到在读取请求上动态配置的 Azure Cosmos DB 一致性级别之一。 

* 您可以通过将区域作为第一个可写区域来为与 Azure Cosmos 帐户关联的特定区域添加注释。 

下表说明了在使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时，如何将本机 MongoDB 写入/读取问题映射到 Azure Cosmos 一致性级别：

[![MongoDB 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB API 与开源 API 之间的一致性级别和兼容性。 请参阅以下文章：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB 的 API for MongoDB 支持的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)
