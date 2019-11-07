---
title: Azure Cosmos 容器的全球分布式事务和分析存储
description: 了解 Azure Cosmos 容器的事务和分析存储及其配置选项。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 22bb36e3b22f65bbf9922bd31e4b2e041cdb8979
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601238"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 容器的全球分布式事务和分析存储

Azure Cosmos 容器在内部以两个存储引擎（事务存储引擎和可更新的分析存储引擎）为后盾。 这两个存储引擎采用结构化的日志，并已经过写入优化，可加快更新速度。 但是，两者的编码方式有所不同：

* **事务存储引擎**–以行方向格式编码，用于快速事务读取和查询。

* **分析存储引擎** - 以列格式进行编码，可实现快速分析查询和扫描。

![存储引擎和 Azure Cosmos DB API 映射](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

事务存储引擎以本地 SSD 为后盾，而分析存储则存储在群集外部的经济型 SSD 存储上。 下表汇总了事务存储与分析存储之间的重要差异。


|功能  |事务存储  |分析存储 |
|---------|---------|---------|
|每个 Azure Cosmos 容器的最大存储空间 |   不受限制      |    不受限制     |
|每个逻辑分区键的最大存储空间   |   10 GB      |   不受限制      |
|存储编码  |   行导向，使用内部格式。   |   列导向，使用 Apache Parquet 格式。 |
|存储区域性 |   以本地/群集内部 SSD 为后盾的复制存储。 |  以经济型远程/群集外部 SSD 为后盾的复制存储。       |
|持续性  |    99.99999（7-9 秒）     |  99.99999（7-9 秒）       |
|用于访问数据的 API  |   SQL、MongoDB、Cassandra、Gremlin、Tables 和 etcd。       | Apache Spark         |
|保留期（生存时间或 TTL）   |  由策略驱动，使用 `DefaultTimeToLive` 属性在 Azure Cosmos 容器中配置。       |   由策略驱动，使用 `ColumnStoreTimeToLive` 属性在 Azure Cosmos 容器中配置。      |
|每 GB 价格    |   请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|存储事务的价格    |  请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   请参阅[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>事务存储和分析存储的优势

### <a name="no-etl-operations"></a>无 ETL 操作

传统的分析管道比较复杂，它们包含多个阶段，而每个阶段都需要与计算和存储层之间来回执行提取-转换-加载 (ETL) 操作。 这会导致数据处理体系结构变得复杂。 这也意味着，存储和计算层的多个阶段会产生较高的成本，而且在存储和计算层的各个阶段之间传输大量数据会造成较高的延迟。  

对 Azure Cosmos DB 执行 ETL 操作不会产生任何开销。 每个 Azure Cosmos 容器以事务和分析存储引擎为后盾，事务与分析存储引擎之间的数据传输是在数据库引擎内部执行的，无需任何网络跃点。 与传统的分析解决方案相比，产生的延迟和成本要低得多。 并为事务和分析工作负荷获取一个全局分布式存储系统。  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>存储多个版本并可更新和查询分析存储

分析存储完全可更新，其中包含 Azure Cosmos 容器中发生的所有事务更新的完整版本历史记录。

保证在 30 秒内，分析存储就能看到对事务存储所做的任何更新。 

### <a name="globally-distributed-multi-master-analytical-storage"></a>全球分布式多主机分析存储

如果 Azure Cosmos 帐户的范围限定为单个区域，则容器中存储的数据（存储在事务存储和分析存储中）的范围也限定为单个区域。 另一方面，如果 Azure Cosmos 帐户是全局分布的，则存储在容器中的数据也是全局分布的。

对于配置了多个写入区域的 Azure Cosmos 帐户，始终在本地区域中执行对容器的写入操作（写入事务存储和分析存储），因此写入速度很快。

对于单区域或多区域 Azure Cosmos 帐户，无论使用单个写入区域（单主数据库）还是多个写入区域（也称为多主数据库），都会在给定区域的本地执行事务和分析读取/查询操作。

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>事务和分析工作负荷之间的性能隔离

在给定区域中，事务工作负荷针对容器的事务性/行存储进行操作。 另一方面，分析工作负荷针对容器的分析/列存储进行操作。 这两个存储引擎独立运行，在工作负荷之间提供严格的性能隔离。

事务工作负荷消耗预配的吞吐量 (RU)。 与事务工作负荷不同，分析工作负荷的吞吐量基于实际消耗量。 分析工作负荷按需消耗资源。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的生存时间](time-to-live.md)
