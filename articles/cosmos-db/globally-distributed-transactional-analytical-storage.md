---
title: Azure Cosmos 容器的全球分布式事务和分析（在个人预览版中）存储
description: 了解事务和分析存储以及它们的 Azure Cosmos 容器配置选项。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623385"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 容器的全球分布式事务和分析存储

Azure Cosmos 容器由两个存储引擎（在个人预览版中）内部支持。 这两个存储引擎都进行了日志结构化和写入优化，以实现更快的更新。 但是，其中每个编码都以不同方式进行编码：

* **事务存储引擎**–以行方向格式编码，用于快速事务读取和查询。

* **分析存储引擎**-以纵栏式格式编码，用于快速分析查询和扫描。

![存储引擎和 Azure Cosmos DB API 映射](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

事务存储引擎受本地 Ssd 的支持，而分析存储则存储在群集以外的 SSD 存储上。 下表捕获了事务存储与分析存储之间的明显差异。


|Feature  |事务存储  |分析存储 |
|---------|---------|---------|
|每个 Azure Cosmos 容器的最大存储空间 |   无限制      |    无限制     |
|每个逻辑分区键的最大存储空间   |   20 GB      |   无限制      |
|存储编码  |   使用内部格式的行方向。   |   使用 Apache Parquet 格式面向列。 |
|存储区域 |   由本地/内部群集 Ssd 支持的复制存储。 |  廉价远程/脱离群集 Ssd 支持的复制存储。       |
|持续性  |    99.99999 （7-9 秒）     |  99.99999 （7-9 秒）       |
|访问数据的 Api  |   SQL、MongoDB、Cassandra、Gremlin、Tables 和 etcd。       | Apache Spark         |
|保留期（生存时间或 TTL）   |  策略驱动，在 Azure Cosmos 容器上通过使用 `DefaultTimeToLive` 属性进行配置。       |   策略驱动，在 Azure Cosmos 容器上通过使用 `ColumnStoreTimeToLive` 属性进行配置。      |
|每 GB 价格    |   请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|存储事务的价格    |  请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>事务性和分析存储的优点

### <a name="no-etl-operations"></a>无 ETL 操作

传统分析管道具有多个阶段，每个阶段都需要对计算层和存储层进行提取-转换-加载（ETL）操作。 这会导致复杂的数据处理体系结构。 这意味着，多个存储和计算阶段需要高昂的成本，并在存储和计算的各个阶段之间传输大量数据时造成高延迟。  

Azure Cosmos DB 执行 ETL 操作无开销。 每个 Azure Cosmos 容器均由事务和分析存储引擎支持，并在数据库引擎中执行事务和分析存储引擎之间的数据传输，无需任何网络跃点。 与传统的分析解决方案相比，产生的延迟和成本明显较低。 并为事务和分析工作负荷获取一个全局分布式存储系统。  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>存储多个版本、更新和查询分析存储

分析存储是完全可更新的，它包含 Azure Cosmos 容器上发生的所有事务更新的完整版本历史记录。

在30秒内，将保证对事务存储的任何更新对分析存储可见。 

### <a name="globally-distributed-multi-master-analytical-storage"></a>全球分布式多主机分析存储

如果你的 Azure Cosmos 帐户的作用域限定为单个区域，则容器中存储的数据（在事务存储和分析存储中）也将作用域限定为单个区域。 另一方面，如果 Azure Cosmos 帐户是全局分布的，则存储在容器中的数据也是全局分布的。

对于配置了多个写入区域的 Azure Cosmos 帐户，将始终在本地区域中执行对容器（对于事务存储和分析存储的写入）的写入操作，因此它们速度很快。

对于单区域或多区域 Azure Cosmos 帐户，不管是单个写入区域（单主机）还是多个写入区域（也称为多主机），都将在给定区域本地执行事务读取/查询。

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>事务性和分析工作负载之间的性能隔离

在给定区域中，事务工作负荷针对容器的事务性/行存储进行操作。 另一方面，分析工作负荷针对容器的分析/列存储进行操作。 这两个存储引擎独立运行，并在工作负荷之间提供严格的性能隔离。

事务工作负荷使用预配的吞吐量（ru）。 与事务工作负载不同，分析工作负荷的吞吐量取决于实际消耗情况。 分析工作负荷按需使用资源。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 的生存时间](time-to-live.md)
