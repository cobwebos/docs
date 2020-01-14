---
title: 用于将数据迁移到 Azure Cosmos DB 的适用于 MongoDB 的 API 的预迁移步骤
description: 本文档概述了将数据从 MongoDB 迁移到 Cosmos DB 的先决条件。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: ef3d56b4ec7e4dbe5f6f4097fdd5d8d125b074dc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932295"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>用于将数据从 MongoDB 迁移到 Azure Cosmos DB 的适用于 MongoDB 的 API 的预迁移步骤

在将数据从 MongoDB （本地或云中）迁移到 Azure Cosmos DB 的 API for MongoDB 之前，应执行以下操作：

1. [阅读有关使用 Azure Cosmos DB 的适用于 MongoDB 的 API 的关键注意事项](#considerations)
2. [选择迁移数据的选项](#options)
3. [估计工作负荷所需的吞吐量](#estimate-throughput)
4. [为数据选择最佳分区键](#partitioning)
5. [了解可以对数据设置的索引策略](#indexing)

如果已完成上述迁移的先决条件，则可以[使用 Azure 数据库迁移服务将 mongodb 数据迁移到 Azure Cosmos DB 的适用于 MongoDB 的 API](../dms/tutorial-mongodb-cosmos-db.md)。 此外，如果尚未创建帐户，可以浏览任何[快速入门](create-mongodb-dotnet.md)。

## <a id="considerations"></a>使用 Azure Cosmos DB 的适用于 MongoDB 的 API 时的主要注意事项

下面是有关 Azure Cosmos DB 的适用于 MongoDB 的 API 的特定特征：
- **容量模型**： Azure Cosmos DB 上的数据库容量基于基于吞吐量的模型。 此模型基于[每秒请求单位](request-units.md)数，这是一个单位，表示每秒可对集合执行的数据库操作数。 此容量可在[数据库或集合级别](set-throughput.md)分配，并且可以在分配模型上或使用[AutoPilot 模型](provision-throughput-autopilot.md)进行设置。
- **请求单位**：在 Azure Cosmos DB 中，每个数据库操作都具有关联的请求单位（ru）成本。 执行时，将从给定秒的可用请求单位级别中减去这一点。 如果请求所需的 ru 数比当前分配的多个，两个选项增加了 ru 的数量，或等待下一秒开始，然后重试该操作。
- **弹性容量**：给定集合或数据库的容量随时可以更改。 这使数据库能够弹性适应工作负荷的吞吐量要求。
- **自动分片**： Azure Cosmos DB 提供只需要分片（或分区）键的自动分区系统。 [自动分区机制](partition-data.md)跨所有 Azure Cosmos DB api 共享，并且它允许无缝的数据和贯穿横向分布的整个缩放。

## <a id="options"></a>适用于 MongoDB Azure Cosmos DB API 的迁移选项

[适用于 MongoDB Azure Cosmos DB 的 API 的 Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)提供了一种机制，可通过提供完全托管的托管平台、迁移监视选项和自动限制处理来简化数据迁移。 选项的完整列表如下所示：

|**迁移类型**|**解决方案**|**注意事项**|
|---------|---------|---------|
|Offline|[数据迁移工具](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 易于设置和支持多个源 <br/>&bull; 不适用于大型数据集。|
|Offline|[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 易于设置和支持多个源 <br/>&bull; 使用 Azure Cosmos DB 批量执行器库 <br/>适用于大型数据集的 &bull; <br/>&bull; 缺少检查点，这意味着在迁移过程中出现的任何问题都需要重新启动整个迁移过程<br/>&bull; 缺少死信队列，则意味着几个错误的文件可能会停止整个迁移过程 <br/>&bull; 需要自定义代码以增加某些数据源的读取吞吐量|
|Offline|[现有的 Mongo 工具（mongodump、mongorestore、Studio3T）](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 易于设置和集成 <br/>&bull; 需要对限制进行自定义处理|
|联机|[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 完全托管的迁移服务。<br/>&bull; 提供迁移任务的宿主和监视解决方案。 <br/>&bull; 适用于大型数据集并负责复制实时更改 <br/>&bull; 仅适用于其他 MongoDB 源|


## <a id="estimate-throughput"></a>估计工作负荷所需的吞吐量

在 Azure Cosmos DB 中，将提前预配吞吐量，并按请求单位（RU）/秒来度量。 不同于 Vm 或本地服务器，可以随时扩展和缩减 ru。 可以立即更改预配的 ru 数。 有关详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。

可以使用 " [Azure Cosmos DB 容量计算器](https://cosmos.azure.com/capacitycalculator/)" 根据数据库帐户配置、数据量、文档大小以及每秒所需的读取和写入来确定请求单位的数量。

下面是影响所需的 ru 数的主要因素：
- **文档大小**：当项/文档的大小增加时，读取或写入项/文档所用的 ru 数也将增加。
- **文档属性计数**：创建或更新文档所用的 ru 数与其属性的数量、复杂性和长度相关。 可以通过[限制索引属性的数量](mongodb-indexing.md)来减少写入操作的请求单位消耗。
- **查询模式**：查询的复杂性会影响查询使用的请求单位数。 

了解查询成本的最佳方式是使用 Azure Cosmos DB 中的示例数据，并使用 "`getLastRequestStastistics`" 命令[从 MongoDB Shell 运行示例查询](connect-mongodb-account.md)，以获取请求费用，这将输出使用的 ru 数：

`db.runCommand({getLastRequestStatistics: 1})`

此命令将输出类似于下面的 JSON 文档：

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

你还可以使用[诊断设置](cosmosdb-monitor-resource-logs.md)来了解针对 Azure Cosmos DB 执行的查询的频率和模式。 诊断日志的结果可以发送到存储帐户、EventHub 实例或[Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)。  

## <a id="partitioning"></a>选择分区键
分区（也称为分片）是迁移数据前的关键点。 Azure Cosmos DB 使用完全托管的分区来增加数据库中的容量，以满足存储和吞吐量要求。 此功能不需要托管或配置路由服务器。   

与此类似，分区功能会自动添加容量，并相应地对数据进行重新平衡。 有关为数据选择正确的分区键的详细信息和建议，请参阅 "[选择分区键" 一文](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。 

## <a id="indexing"></a>为你的数据编制索引
默认情况下，Azure Cosmos DB 为插入的所有数据提供自动索引。 Azure Cosmos DB 提供的索引功能包括添加复合索引、唯一索引和生存时间（TTL）索引。 索引管理接口映射到 `createIndex()` 命令。 有关详细信息，请参阅[Azure Cosmos DB 的 API For MongoDB 中的索引](mongodb-indexing.md)。

[Azure 数据库迁移服务](../dms/tutorial-mongodb-cosmos-db.md)会自动迁移包含唯一索引的 MongoDB 集合。 但是，必须在迁移之前创建唯一索引。 当集合中已存在数据时，Azure Cosmos DB 不支持创建唯一索引。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](unique-keys.md)。

## <a name="next-steps"></a>后续步骤
* [使用数据库迁移服务将 MongoDB 数据迁移到 Cosmos DB。](../dms/tutorial-mongodb-cosmos-db.md) 
* [在 Azure Cosmos 容器和数据库上预配吞吐量](set-throughput.md)
* [Azure Cosmos DB 中的分区](partition-data.md)
* [Azure Cosmos DB 中的全局分发](distribute-data-globally.md)
* [Azure Cosmos DB 中的索引](index-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
