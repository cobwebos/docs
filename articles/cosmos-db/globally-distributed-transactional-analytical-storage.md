---
title: Azure Cosmos 容器的全球分布式事务和分析存储
description: 了解事务和分析存储以及它们的 Azure Cosmos 容器配置选项。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756902"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 容器的全球分布式事务和分析存储

Azure Cosmos 容器由两个存储引擎（事务存储引擎和可更新的分析存储引擎）在内部进行支持。 这两个存储引擎都进行了日志结构化和写入优化，以实现更快的更新。 但是，其中每个编码都以不同方式进行编码：

* **事务存储引擎**–以行方向格式编码，用于快速事务读取和查询。

* **分析存储引擎**-以纵栏式格式编码，用于快速分析查询和扫描。

![存储引擎和 Azure Cosmos DB API 映射](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

事务存储引擎受本地 Ssd 的支持，而分析存储则存储在群集以外的 SSD 存储上。 下表捕获了事务存储与分析存储之间的明显差异。


|Feature  |事务存储  |分析存储 |
|---------|---------|---------|
|每个 Azure Cosmos 容器的最大存储空间 |   无限制      |    无限制     |
|每个逻辑分区键的最大存储空间   |   10 GB      |   无限制      |
|存储编码  |   使用内部格式的行方向。   |   使用 Apache Parquet 格式面向列。 |
|存储区域 |   由本地/内部群集 Ssd 支持的复制存储。 |  廉价远程/脱离群集 Ssd 支持的复制存储。       |
|持续性  |    99.99999 （7-9 秒）     |  99.99999 （7-9 秒）       |
|访问数据的 Api  |   SQL、MongoDB、Cassandra、Gremlin、Tables 和 Etcd。       | Apache Spark         |
|保留期（生存时间或 TTL）   |  策略驱动，在 Azure Cosmos 容器上通过使用 `DefaultTimeToLive` 属性进行配置。       |   策略驱动，在 Azure Cosmos 容器上通过使用 `ColumnStoreTimeToLive` 属性进行配置。      |
|每 GB 的价格    |   0\.25 美元/GB      |  $ 0.02/GB       |
|存储事务的价格    | 预配的吞吐量每小时计费，每 100 RU/秒 $0.008 收费。        |  对于10000写入事务，基于消耗的吞吐量按 $0.05 收费 $0.004，为10000读取事务收费。       |

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

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>按需快照和时间行程分析

可以通过调用容器上的 `CreateSnapshot (name, timestamp)` 命令，随时获取存储在 Azure Cosmos 容器分析存储中的数据的快照。 快照在容器中的更新历史记录中命名为 "书签"。

![按需快照和时间行程分析](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

创建快照时，除了名称外，还可以指定时间戳，用于定义更新历史记录中容器的状态。 然后，可以将快照数据加载到 Spark 中并执行查询。

目前，你只能在容器上随时按需拍摄快照，但尚不支持基于计划或自定义策略自动拍摄快照的功能。

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>独立地在事务存储和分析存储之间配置和分层数据

根据你的方案，你可以单独启用或禁用两个存储引擎中的每一个。 以下是每种方案的配置：

|场景 |事务存储设置  |分析存储设置 |
|---------|---------|---------|
|以独占方式运行分析工作负荷（具有无限保留期） |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|以独占方式运行事务工作负荷（具有无限保留期）  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|同时运行事务和分析工作负荷（具有无限保留期）   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|同时运行事务和分析工作负荷（具有不同的保留间隔，也称为存储分层）  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **仅为分析工作负荷配置容器（具有无限保留期）**

   可以专门为分析工作负荷配置 Azure Cosmos 容器。 此配置的优点是无需为事务存储付费。 如果你的目标是仅将容器用于分析工作负荷，则可以通过在 Cosmos 容器上将 `DefaultTimeToLive` 设置为0来禁用事务存储，通过将 `ColumnStoreTimeToLive` 设置为-1，可以启用具有无限保留期的分析存储。

   ![具有无限保留期的分析工作负荷](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **为事务工作负荷专门配置容器（具有无限保留期）**

   可以专门为事务工作负荷配置 Azure Cosmos 容器。 可以通过在容器上将 `ColumnStoreTimeToLive` 设置为0来禁用分析存储，并通过将 `DefaultTimeToLive` 设置为-1，使分析存储具有无限的保留期。

   ![具有无限保留期的事务性工作负荷](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **为事务和分析工作负荷（具有无限保留）配置容器**

   你可以为事务和分析工作负荷配置 Azure Cosmos 容器，并在它们之间实现完全的性能隔离。 可以通过将 `ColumnStoreTimeToLive` 设置为-1 来启用分析存储，并通过将 `DefaultTimeToLive ` 设置为-1 来启用具有无限保留期的事务存储。

   ![具有无限保留期的事务性和分析工作负荷](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **通过存储分层为事务和分析工作负荷配置容器**

   你可以为事务和分析工作负荷配置 Azure Cosmos 容器，并在不同的保留间隔内对事务和分析工作负荷进行完全的性能隔离。 Azure Cosmos DB 将强制你的分析存储始终保留比事务存储更长的持续时间。

   通过将 `DefaultTimeToLive` 设置为 < 值 1 >，并通过将 `ColumnStoreTimeToLive` 设置为 < 值 2 > 来启用分析存储，可以通过将设置为值1启用分析存储。 Azure Cosmos DB 将强制 < 值 2 > 始终大于 < 值 1 >。

   ![具有存储分层的事务性和分析工作负荷](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 的生存时间](time-to-live.md)
