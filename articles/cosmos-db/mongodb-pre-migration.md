---
title: '预迁移步骤对于数据迁移从 MongoDB Azure Cosmos DB: MongoDB API'
description: 此文档提供有关到 Cosmos DB 从 MongoDB 数据迁移的先决条件的概述。
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330735"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>预迁移步骤对于数据迁移从 MongoDB Azure Cosmos DB: MongoDB API

将数据从 MongoDB 迁移之前 (在本地还是在云中 (IaaS)) 到 Azure Cosmos DB 的 mongodb 的 API，您应该：

1. [创建 Azure Cosmos DB 帐户](#create-account)
2. [估算所需的工作负荷的吞吐量](#estimate-throughput)
3. [选择你的数据的最佳分区密钥](#partitioning)
4. [了解可以设置对数据的索引策略](#indexing)

如果你已完成迁移上述的先决条件，请参阅[到 Azure Cosmos DB API for MongoDB 迁移 MongoDB 数据](../dms/tutorial-mongodb-cosmos-db.md)的实际数据迁移说明。 如果没有，本文档介绍如何处理这些系统必备组件。 

## <a id="create-account"></a> 创建 Azure Cosmos DB 帐户 

开始迁移之前，需要[创建 Azure Cosmos 帐户使用 Azure Cosmos DB API for MongoDB](create-mongodb-dotnet.md)。 

在帐户创建时，可以选择设置应用于[全局分配](distribute-data-globally.md)你的数据。 此外可以选择对启用多区域写入 （或多主配置），允许每个应用的区域，以将这两个写入和读取区域。

![Account-Creation](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> 估计工作负荷的吞吐量需要

通过使用开始迁移之前[数据库迁移服务 (DMS)](../dms/dms-overview.md)，应当估计的预配 Azure Cosmos 数据库和集合的吞吐量。

可以在预配的吞吐量：

- 集合

- 数据库

> [!NOTE]
> 此外可以组合使用以上，其中可能有专用数据库中的某些集合预配的吞吐量和其他人可能会共享吞吐量。 有关详细信息，请参阅[数据库和容器上的设置吞吐量](set-throughput.md)页。
>

首先应确定您是否要预配数据库或集合级别的吞吐量或这两者的组合。 一般情况下，建议在集合级别上配置专用的吞吐量。 在数据库级别的预配吞吐量使共享预配的吞吐量在数据库中的集合。 为共享的吞吐量，但是，对于每个单独的集合上的特定吞吐量不能保证并不针对任何特定集合中获取可预测的性能。

如果您不确定应将多少吞吐量专用于每个单独的集合，可以选择数据库级别的吞吐量。 您可以将预配的吞吐量为逻辑上相当于一个 MongoDB 的虚拟机或物理服务器的计算能力的 Azure Cosmos 数据库上配置，但更具成本效益能够灵活缩放。 有关详细信息，请参阅[Azure Cosmos 容器和数据库上的预配吞吐量](set-throughput.md)。

如果你设置在数据库级别的吞吐量时，必须使用分区/分片键创建该数据库中创建的所有集合。 有关分区的详细信息，请参阅[的分区和水平缩放 Azure Cosmos DB 中](partition-data.md)。 如果在迁移期间未指定的分区/分片键，Azure 数据库迁移服务将自动填充具有的分片键字段 *_id*自动生成的每个文档的属性。

### <a name="optimal-number-of-request-units-rus-to-provision"></a>预配的最优数目的请求单位 (Ru)

Azure Cosmos DB 中吞吐量预先预配，以每秒请求单位 (RU)。 如果必须在本地或 VM 运行 MongoDB 的工作负荷，看作 RU 的物理资源的简单抽象如 VM 或上的本地服务器和它们拥有，例如，资源的大小为内存、 CPU、 IOPs。 

与不同的 Vm 或本地服务器，RUs 很容易在任何时候向上和向下缩放。 你可以预配的 Ru 数秒钟内，并仅为的最大为给定的 1 小时内预配的 Ru 数进行计费。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。

下面是会影响所需的 Ru 数的关键因素：
- **项 （即，文档） 大小**:项/文档的大小增加时，使用的 Ru 数量来读取或写入项/文档也会增加。
- **项属性计数**：假设[默认索引](index-overview.md)上的所有属性，Ru 数用来编写项会作为项属性计数的增加而增加。 可以减少写入操作的请求单位消耗[限制的数量的索引属性](index-policy.md)。
- **并发操作**:请求单位数还取决于哪些不同的 CRUD 操作 （如写入、 读取、 更新、 删除） 的频率和执行更复杂的查询。 可以使用[mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/)以输出当前 MongoDB 数据的并发性需求。
- **查询模式**：查询的复杂性会影响请求单位数由该查询。

如果您将使用的 JSON 文件导出[mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/)并且了解多少写入、 读取、 更新和删除每秒发生，则可以使用[Azure Cosmos DB 容量规划器](https://www.documentdb.com/capacityplanner)来估计预配的 Ru 的初始数量。 在更复杂的查询的成本未纳入的容量规划器。 因此，如果对数据有复杂的查询，将占用更多的 Ru。 计算器还假定所有字段编制都索引，并且使用会话一致性。 了解查询的成本是将数据 （或示例数据） 迁移到 Azure Cosmos DB 的最好办法[连接到 Cosmos DB 的终结点](connect-mongodb-account.md)并使用 MongoDB Shell 中运行示例查询`getLastRequestStastistics`命令来获取请求费用，会输出消耗的 ru 来讲数目：

`db.runCommand({getLastRequestStatistics: 1})`

此命令将输出类似于以下的 JSON 文档：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

了解查询占用的 Ru 数和并发性需要为该查询后，您可以调整预配的 Ru 的数。 优化 Ru 不是一次性事件-应持续优化或纵向扩展 Ru 预配，具体取决于您是不应为大量流量，而不是繁重的工作负荷还是导入数据。

## <a id="partitioning"></a>选择分区键
分区是在迁移到 Azure Cosmos DB 等全球分布式数据库之前要考虑的一个关键点。 Azure Cosmos DB 使用分区来缩放以满足你的应用程序的可伸缩性和性能需求的数据库中单个容器。 在分区中，在容器中的项划分为称为逻辑分区的不同子集。 有关详细信息和建议选择适当的分区键为你的数据，请参阅[选择分区键部分](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a id="indexing"></a>你的数据编制索引
默认情况下，Azure Cosmos DB 索引后引入的所有数据字段。 您可以修改[索引策略](index-policy.md)在任何时间的 Azure Cosmos DB 中。 实际上，通常建议关闭索引迁移数据时，然后将其重新打开数据时已经在 Cosmos DB 中。 有关编制索引的详细信息，您可以阅读有关它在[Azure Cosmos DB 中的索引编制](index-overview.md)部分。 

[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)会自动将 MongoDB 与唯一索引的集合的迁移。 但是，必须在迁移之前创建唯一索引。 Azure Cosmos DB 不支持创建唯一索引时已有数据在集合中。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](unique-keys.md)。

## <a name="next-steps"></a>后续步骤
* [将 MongoDB 数据迁移到 Cosmos DB 中使用数据库迁移服务。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和数据库上的预配吞吐量](set-throughput.md)
* [Azure Cosmos DB 中的分区](partition-data.md)
* [Azure Cosmos DB 中的全局分发](distribute-data-globally.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
