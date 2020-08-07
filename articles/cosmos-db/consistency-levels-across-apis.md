---
title: 一致性级别和 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 与 Apache Cassandra、MongoDB 中不同 API 之间的一致性级别映射
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: af777efda769315019ecee41d4053f5ab82f3047
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920426"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB 为常用数据库提供对与线路协议兼容的 API 的本机支持。 这些数据库包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 表存储。 这些数据库既没有提供准确定义的一致性模型，也没有为一致性级别提供由 SLA 支持的保证。 它们通常仅提供 Azure Cosmos DB 提供的五个一致性模型的一个子集。

使用 SQL API、Gremlin API 和表 API 时，会使用 Azure Cosmos 帐户上配置的默认一致性级别。 

使用 Cassandra API 或 Azure Cosmos DB 的适用于 MongoDB 的 API 时，应用程序将分别获取 Apache Cassandra 和 MongoDB 提供的完整一致性级别，同时提供更强的一致性和持久性保证。 本文档介绍了与 Apache Cassandra 和 MongoDB 一致性级别对应的 Azure Cosmos DB 一致性级别。

> [!NOTE]
> Azure Cosmos DB 的默认一致性模型是 Session。 会话是以客户端为中心的一致性模型，不受 Cassandra 或 MongoDB 的本机支持。 有关所选的一致性模型的详细信息，请参阅[Azure Cosmos DB 中的一致性级别](consistency-levels.md)

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的映射

与 Azure Cosmos DB 不同，Apache Cassandra 本身并不提供精确定义的一致性保证。  与之相反，Apache Cassandra 提供一个写入一致性级别和一个读取一致性级别，以便进行高可用性、一致性和延迟方面的权衡。 使用 Azure Cosmos DB 的 Cassandra API 时： 

* Apache Cassandra 的写入一致性级别映射到在 Azure Cosmos 帐户上配置的默认一致性级别。 写入操作 (CL) 的一致性不能因请求的不同而（即按请求）更改。

* Azure Cosmos DB 会将 Cassandra 客户端驱动程序指定的读取一致性级别动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。 

下表说明了在使用 Cassandra API 时，如何将本机 Cassandra 一致性级别映射到 Azure Cosmos DB 的一致性级别：  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra 一致性模型映射" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>MongoDB 与 Azure Cosmos DB 一致性级别之间的映射

与 Azure Cosmos DB 不一样，本机 MongoDB 并不提供精确定义的一致性保证。 与之相反，本机 MongoDB 允许用户配置下述一致性保证：写入关注、读取关注以及 isMaster 指令 - 目的是将读取操作定向到主副本或辅助副本，以便实现所需的一致性级别。

使用 Azure Cosmos DB 的 MongoDB API 时，MongoDB 驱动程序将写入区域视为主副本，而所有其他区域都是读取副本。 可以选择将哪个与 Azure Cosmos 帐户关联的区域作为主副本。 

使用 Azure Cosmos DB 的 MongoDB API 时：

* 写入关注映射到在 Azure Cosmos 帐户上配置的默认一致性级别。

* Azure Cosmos DB 会将 MongoDB 客户端驱动程序指定的读取关注动态映射到根据读取请求动态配置的某个 Azure Cosmos DB 一致性级别。  

* 可以将与 Azure Cosmos 帐户关联的特定区域批注为“主区域”，方法是将该区域设置为第一个可写区域。 

下表说明了在使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时，如何将本机 MongoDB 写入/读取问题映射到 Azure Cosmos 一致性级别：

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB 一致性模型映射" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB API 与开源 API 之间的一致性级别和兼容性。 请参阅以下文章：

* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB 的用于 MongoDB 的 API 支持的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)
