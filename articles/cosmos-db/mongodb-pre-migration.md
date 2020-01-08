---
title: 用于将数据迁移到 Azure Cosmos DB 的适用于 MongoDB 的 API 的预迁移步骤
description: 本文档概述了将数据从 MongoDB 迁移到 Cosmos DB 的先决条件。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445195"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>用于将数据从 MongoDB 迁移到 Azure Cosmos DB 的适用于 MongoDB 的 API 的预迁移步骤

在将数据从 MongoDB （本地或云中（IaaS））迁移到 Azure Cosmos DB 的 API for MongoDB 之前，应执行以下操作：

1. [创建 Azure Cosmos DB 帐户](#create-account)
2. [估计工作负荷所需的吞吐量](#estimate-throughput)
3. [为数据选择最佳分区键](#partitioning)
4. [了解可以对数据设置的索引策略](#indexing)

如果已完成上述迁移的先决条件，请参阅将[mongodb 数据迁移到 Azure Cosmos DB 的 API For MongoDB](../dms/tutorial-mongodb-cosmos-db.md)以获取实际的数据迁移说明。 如果不是，本文档提供了有关如何处理这些必备组件的说明。 

## <a id="create-account"></a>创建 Azure Cosmos DB 帐户 

在开始迁移之前，需要[使用 Azure Cosmos DB 的适用于 MongoDB 的 API 创建 Azure Cosmos 帐户](create-mongodb-dotnet.md)。 

创建帐户时，可以选择 "设置" 以[全局分发](distribute-data-globally.md)数据。 你还可以选择启用多区域写入（或多主配置），这允许你的每个区域都是写入和读取区域。

![帐户创建](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>估计工作负荷所需的吞吐量

使用[数据库迁移服务（DMS）](../dms/dms-overview.md)开始迁移之前，应估计为 Azure Cosmos 数据库和集合预配的吞吐量。

可以在以下任一操作中设置吞吐量：

- 集合

- 数据库

> [!NOTE]
> 你还可以组合以上，其中，数据库中的某些集合可能有专用的预配吞吐量，而另一些则可能共享吞吐量。 有关详细信息，请参阅[设置数据库和容器页上的吞吐量](set-throughput.md)。
>

你应该首先确定是否要设置数据库或集合级别的吞吐量，或两者的组合。 通常，建议在集合级别配置专用吞吐量。 在数据库级预配吞吐量可以使数据库中的集合共享预配的吞吐量。 然而，使用共享吞吐量，并不保证每个单独集合上的特定吞吐量，并且不会在任何特定集合上获得可预测的性能。

如果你不确定应该为每个单独的集合专用的吞吐量，则可以选择数据库级吞吐量。 你可以将 Azure Cosmos 数据库上配置的预配吞吐量视为逻辑等效于 MongoDB VM 或物理服务器计算容量的逻辑，但使用弹性进行缩放的能力会更具成本效益。 有关详细信息，请参阅[在 Azure Cosmos 容器和数据库上预配吞吐量](set-throughput.md)。

如果在数据库级别预配吞吐量，则在该数据库中创建的所有集合都必须使用 partition/分片键创建。 有关分区的详细信息，请参阅[Azure Cosmos DB 中的分区和水平缩放](partition-data.md)。 如果在迁移过程中未指定 partition/分片键，Azure 数据库迁移服务会自动使用自动为每个文档生成的 *_id*属性填充分片 key 字段。

### <a name="optimal-number-of-request-units-rus-to-provision"></a>要预配的请求单位（ru）的最佳数量

在 Azure Cosmos DB 中，将提前预配吞吐量，并按请求单位（RU）/秒来度量。 如果你的工作负荷是在 VM 或本地运行 MongoDB，请将 RU 视为物理资源的简单抽象，例如虚拟机的大小或本地服务器的大小以及所拥有的资源（例如，内存、CPU 和 IOPs）。 

不同于 Vm 或本地服务器，可以随时扩展和缩减 ru。 你可以在数秒内更改预配的 ru 数，并仅针对给定的一小时内的最大 ru 数进行计费。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。

下面是影响所需的 ru 数的主要因素：
- **项（即文档）大小**：当项/文档的大小增加时，读取或写入项/文档所用的 ru 数也将增加。
- **项属性计数**：假设对所有属性使用[默认索引](index-overview.md)，当项属性计数增加时，用于写入项的 ru 数将增加。 可以通过[限制索引属性的数量](index-policy.md)来减少写入操作的请求单位消耗。
- **并发操作**：所使用的请求单位还取决于执行不同 CRUD 操作（如写入、读取、更新、删除）和执行更复杂查询的频率。 你可以使用[mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/)来输出当前 MongoDB 数据的并发需求。
- **查询模式**：查询的复杂性会影响查询使用的请求单位数。

如果使用[通过 mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/)导出 JSON 文件并了解每秒发生的写入、读取、更新和删除的数量，则可以使用[Azure Cosmos DB 容量规划](https://www.documentdb.com/capacityplanner)器来估算要预配的第几个 ru。 容量规划器不会考虑更复杂的查询。 因此，如果对数据进行了复杂的查询，将会使用更多的 ru。 计算器还假定所有字段都已编制索引，并使用会话一致性。 了解查询成本的最佳方式是将数据（或示例数据）迁移到 Azure Cosmos DB，[连接到 Cosmos DB 的终结点](connect-mongodb-account.md)，并使用 `getLastRequestStastistics` 命令从 MongoDB Shell 运行示例查询，以获取请求费用，这将输出使用的 ru 数：

`db.runCommand({getLastRequestStatistics: 1})`

此命令将输出类似于下面的 JSON 文档：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

了解查询使用的 ru 数和该查询的并发需求后，可以调整预配的 ru 数。 优化 ru 并不是一次性的事件-应不断优化或纵向扩展已预配的 ru，具体取决于是否不需要繁重的流量，而不是使用繁重的工作负荷或导入数据。

## <a id="partitioning"></a>选择分区键
分区是迁移到全局分布式数据库（如 Azure Cosmos DB）之前需要注意的关键点。 Azure Cosmos DB 使用分区来缩放数据库中的各个容器，以满足应用程序的可伸缩性和性能需求。 在分区中，容器中的项被划分为多个称为逻辑分区的不同子集。 有关为数据选择正确的分区键的详细信息和建议，请参阅[选择分区键部分](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a id="indexing"></a>为你的数据编制索引
默认情况下，Azure Cosmos DB 在引入时为所有数据字段编制索引。 你可以随时在 Azure Cosmos DB 中修改[索引策略](index-policy.md)。 事实上，建议在迁移数据时关闭索引，然后在数据已 Cosmos DB 时再将其重新打开。 有关索引的详细信息，请参阅 Azure Cosmos DB 部分的[索引中](index-overview.md)的详细信息。 

[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)会自动迁移包含唯一索引的 MongoDB 集合。 但是，必须在迁移之前创建唯一索引。 当集合中已存在数据时，Azure Cosmos DB 不支持创建唯一索引。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](unique-keys.md)。

## <a name="next-steps"></a>后续步骤
* [使用数据库迁移服务将 MongoDB 数据迁移到 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和数据库上预配吞吐量](set-throughput.md)
* [Azure Cosmos DB 中的分区](partition-data.md)
* [Azure Cosmos DB 中的全局分发](distribute-data-globally.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
