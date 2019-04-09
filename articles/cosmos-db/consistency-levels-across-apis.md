---
title: 一致性级别和 Azure Cosmos DB API
description: 了解 Azure Cosmos DB 中 API 的一致性级别。
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a8617ae2b01fc89a4c957b8610164a2b53a16f5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274767"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性级别和 Azure Cosmos DB API

Azure Cosmos DB 的网络提供本机支持协议兼容的受欢迎的数据库的 Api。 其中包括 MongoDB、 Apache Cassandra、 Gremlin 和 Azure 表存储。 这些数据库不提供精确地定义的一致性模型或 SLA 支持的一致性级别的保证。 它们通常仅提供 Azure Cosmos DB 提供的五个一致性模型的一个子集。 

使用 SQL API、 Gremlin API 和表 API 时，使用 Azure Cosmos 帐户上配置的默认一致性级别。 

当使用 Cassandra API 或 Azure Cosmos DB API for MongoDB，应用程序将获取一套完整的一致性级别分别提供 Apache Cassandra 和 MongoDB，、 与甚至更强的一致性和持续性保证。 本文档显示了 Apache Cassandra 和 MongoDB 的一致性级别相应的 Azure Cosmos DB 一致性级别。


## <a id="cassandra-mapping"></a>Apache Cassandra 与 Azure Cosmos DB 一致性级别之间的映射

与不同 AzureCosmos DB Apache Cassandra 不以本机方式提供精确地定义的一致性保证。  相反，Apache Cassandra 提供写一致性级别和读取的一致性级别，若要启用高可用性、 一致性和延迟权衡取舍。 在使用 Azure Cosmos DB 的 Cassandra API: 

* Apache Cassandra 的写一致性级别将映射到 Azure Cosmos 帐户上配置的默认一致性级别。 

* Azure Cosmos DB 将动态映射到一个动态配置上的读取请求的 Azure Cosmos DB 一致性级别的 Cassandra 客户端驱动程序指定的读取的一致性级别。 

下表说明了如何本机 Cassandra 的一致性级别将映射到 Azure Cosmos DB 一致性级别时使用 Cassandra API:  

[![Cassandra 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>MongoDB 和 Azure Cosmos DB 一致性级别之间映射

Azure Cosmos DB，与本机 MongoDB 不提供精确地定义的一致性保证。 相反，本机 MongoDB 允许用户配置以下的一致性保证： 写问题，读取的关注点和的 isMaster 指令的读取的操作定向到主或辅助副本，以便实现所需的一致性级别。 

当使用 Azure Cosmos DB API for MongoDB，MongoDB 驱动程序将处理的写入区域作为主副本和所有其他区域读取副本。 可以选择与你的 Azure Cosmos 帐户作为主副本关联的区域。 

使用 Azure Cosmos DB API for MongoDB 时：

* 写入问题映射到 Azure Cosmos 帐户上配置的默认一致性级别。
 
* Azure Cosmos DB 将动态映射到一个动态配置客户端的读取请求的 Azure Cosmos DB 一致性级别的 MongoDB 客户端驱动程序指定的读取的问题。 

* 你可以批注，从而第一个可写区域与区域相关联你的 Azure Cosmos 帐户作为"Master"的特定区域。 

下表说明了如何本机 MongoDB 读/写问题都映射到 Azure Cosmos 一致性级别时使用 Azure Cosmos DB API for MongoDB:

[![MongoDB 一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB API 与开源 API 之间的一致性级别和兼容性。 请参阅以下文章：

* [各种一致性级别的可用性和性能利弊](consistency-levels-tradeoffs.md)
* [MongoDB 功能适用于 MongoDB 的 Azure Cosmos DB 的 API 支持](mongodb-feature-support.md)
* [支持的 Azure Cosmos DB Cassandra API 的 Apache Cassandra 功能](cassandra-support.md)