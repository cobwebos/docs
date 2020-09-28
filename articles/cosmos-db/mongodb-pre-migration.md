---
title: 将数据迁移到 Azure Cosmos DB's API for MongoDB 的迁移前步骤
description: 本文档概述将数据从 MongoDB 迁移到 Cosmos DB 的先决条件。
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/01/2020
ms.author: jasonh
ms.openlocfilehash: 01bfff6db7827218dafa539b8bc2c7ce0a7869f2
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409640"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>将数据从 MongoDB 迁移到 Azure Cosmos DB's API for MongoDB 的迁移前步骤

在将数据从 MongoDB（本地或云中）迁移到 Azure Cosmos DB's API for MongoDB 之前，应执行以下操作：

1. [阅读有关使用 Azure Cosmos DB’s API for MongoDB 的重要注意事项](#considerations)
2. [选择迁移数据的选项](#options)
3. [估算工作负荷所需的吞吐量](#estimate-throughput)
4. [为数据选择最佳的分区键](#partitioning)
5. [了解可对数据设置的索引策略](#indexing)

如果已完成上述迁移先决条件，可以[使用 Azure 数据库迁移服务将 MongoDB 数据迁移到 Azure Cosmos DB’s API for MongoDB](../dms/tutorial-mongodb-cosmos-db.md)。 此外，如果尚未创建帐户，可以浏览任何介绍了帐户创建步骤的[快速入门](create-mongodb-dotnet.md)。

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>使用 Azure Cosmos DB’s API for MongoDB 时的注意事项

下面是有关 Azure Cosmos DB’s API for MongoDB 的具体特征：

- **容量模型**：Azure Cosmos DB 上的数据库容量基于吞吐量模型。 此模型基于[每秒请求单位数](request-units.md)，此单位表示每秒可对集合执行的数据库操作次数。 可以在[数据库或集合级别](set-throughput.md)分配此容量，也可以在分配模型中进行预配，或者使用[自动缩放预配的吞吐量](provision-throughput-autoscale.md)。

- **请求单位**：在 Azure Cosmos DB 中，每个数据库操作都有关联的请求单位 (RU) 成本。 执行操作时，将从在给定的秒可用的请求单位级别中减去此成本。 如果请求所需的 RU 数超过了当前分配的每秒 RU 数，可以使用两个选项来解决此问题 - 增加 RU 数量，或等待下一秒开始，然后重试操作。

- **弹性容量**：给定集合或数据库的容量随时可以更改。 这样，数据库就能弹性适应工作负荷的吞吐量要求。

- **自动分片**：Azure Cosmos DB 提供一个仅需要分片（或分区键）的自动分区系统。 [自动分区机制](partition-data.md)在所有 Azure Cosmos DB API 之间共享，允许通过水平分配进行无缝的数据缩放和全面缩放。

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>适用于 Azure Cosmos DB’s API for MongoDB 的迁移选项

[适用于 Azure Cosmos DB’s API for MongoDB 的 Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)提供一个机制来简化数据迁移。该机制提供完全托管的主机平台、迁移监视选项和自动限制处理。 下面是完整的选项列表：

|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|联机|[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集，负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|
|Offline|[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集，负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|
|Offline|[Azure 数据工厂](../data-factory/connector-azure-cosmos-db.md)|&bull; 易于设置且支持多个源 <br/>&bull; 利用 Azure Cosmos DB 批量执行程序库 <br/>&bull; 适合用于大型数据集 <br/>&bull; 缺少检查点，这意味着，在迁移过程中出现任何问题都需要重启整个迁移过程<br/>&bull; 缺少死信队列，这意味着，出现几个有错误的文件就可能会停止整个迁移过程。 <br/>&bull; 需要编写自定义代码来增大某些数据源的读取吞吐量|
|Offline|[现有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 易于设置和集成 <br/>&bull; 需要对限制进行自定义处理|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>估算工作负荷的吞吐量需求

在 Azure Cosmos DB 中，吞吐量是提前预配的，按每秒请求单位 (RU) 数计量。 不同于 VM 或本地服务器，RU 随时可以轻松纵向扩展和缩减。 可以即时更改预配的 RU 数。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。

可以使用 [Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/)根据数据库帐户配置、数据量、文档大小以及每秒所需的读取和写入次数，来确定请求单位数量。

下面是影响所需 RU 数的关键因素：
- **文档大小**：随着项/文档大小的增大，读取或写入该项/文档所要消耗的 RU 数也会增加。

- **文档属性计数**：创建或更新文档所消耗的 RU 数与该文档的属性数目、复杂性和长度相关。 可以通过[限制已编制索引的属性数目](mongodb-indexing.md)，来减少写入操作的请求单位消耗量。

- **查询模式**：查询的复杂性会影响查询消耗的请求单位数。 

了解查询成本的最佳方式是使用 Azure Cosmos DB 中的示例数据，[并在 MongoDB Shell 中使用 `getLastRequestStastistics` 命令运行示例查询](connect-mongodb-account.md)以获取请求开销，此命令将输出消耗的 RU 数：

`db.runCommand({getLastRequestStatistics: 1})`

此命令将输出类似于以下内容的 JSON 文档：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

也可以使用[诊断设置](cosmosdb-monitor-resource-logs.md)来了解针对 Azure Cosmos DB 执行的查询的频率和模式。 可将诊断日志中的结果发送到存储帐户、事件中心实例或 [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md)。  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>选择分区键
分区（也称为分片）是迁移数据之前要考虑的一个要点。 Azure Cosmos DB 使用完全托管的分区来提高数据库中的容量，以满足存储和吞吐量要求。 此功能不需要托管或配置路由服务器。   

分区功能以类似方式自动增加容量，并相应地重新均衡数据。 有关为数据选择适当分区键的详细信息和建议，请参阅[选择分区键](partitioning-overview.md#choose-partitionkey)一文。 

## <a name="index-your-data"></a><a id="indexing"></a>为数据编制索引

Azure Cosmos DB API for MongoDB 服务器版本 3.6 仅自动为 `_id` 字段编制索引。 无法删除此字段。 它会自动强制确保每个分片密钥的 `_id` 字段的唯一性。 若要为其他字段编制索引，请应用 MongoDB 索引管理命令。 此默认索引编制策略不同于 Azure Cosmos DB SQL API，后者在默认情况下会为所有字段编制索引。

Azure Cosmos DB 提供的索引编制功能包括添加复合索引、唯一索引和生存时间 (TTL) 索引。 索引管理接口映射到 `createIndex()` 命令。 有关详细信息，请参阅 [Azure Cosmos DB 的 API For MongoDB 中的索引](mongodb-indexing.md)。

[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)自动迁移具有唯一索引的 MongoDB 集合。 但是，必须在迁移之前创建唯一索引。 如果集合中已包含数据，Azure Cosmos DB 将不支持创建唯一索引。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](unique-keys.md)。

## <a name="next-steps"></a>后续步骤
* [使用数据库迁移服务将 MongoDB 数据迁移到 Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md) 
* [对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)
* [Azure Cosmos DB 中的分区](partition-data.md)
* [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
