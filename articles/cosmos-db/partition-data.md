---
title: Azure Cosmos DB 中的分区和水平缩放 | Microsoft Docs
description: 了解分区在 Azure Cosmos DB 中的工作原理，如何配置分区和分区键以及如何为应用程序选取适当的分区键。
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 149d2ba5108fb49741203fbe5c50add6c0d523ae
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中分区和缩放

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是一种全局分布的多模型数据库服务，旨在帮助用户实现快速且可预测的性能。 随着应用程序的发展，它也不断无缝扩展。 本文概述了分区如何应用于 Azure Cosmos DB 中的所有数据模型。 文中还介绍了如何配置 Azure Cosmos DB 容器以有效扩展应用程序。

在此视频中，Azure Cosmos DB 计划经理 Andrew Liu 谈论了分区和分区键：

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区
在 Azure Cosmos DB 中，可在任何规模以毫秒级响应时间对无架构数据进行存储和查询。 Azure Cosmos DB 提供称作集合（适用于文档）、图形或表的容器用于存储数据。 

容器是逻辑资源，可跨一个或多个物理分区或服务器。 Azure Cosmos DB 根据存储大小以及容器的预配吞吐量确定分区数。 

物理分区指大小固定且受 SSD 支持的保留存储。 将复制每个物理分区以实现高可用性。 一个或多个物理分区即构成一个容器。 物理分区完全由 Azure Cosmos DB 进行管理，因此，无需编写复杂的代码或亲自管理分区。 Azure Cosmos DB 容器在存储和吞吐量方面没有限制。 

逻辑分区指物理分区内用于存储与单个分区键值关联的所有数据的分区。 逻辑分区最大可达 10 GB。下图中，单个容器有三个逻辑分区。 每个逻辑分区存储一个分区键（分别为 LAX、AMS 和 MEL）的数据。 每个 LAX、AMS 和 MEL 逻辑分区均不能超过 10 GB 的最大逻辑分区限制。 

![资源分区](./media/introduction/azure-cosmos-db-partitioning.png) 

当集合满足[分区先决条件](#prerequisites)时，分区行为对应用程序是透明的。 Azure Cosmos DB 通过对单一容器资源调用方法/API 来支持快速读取和写入、查询、事务逻辑、一致性级别和精细访问控制。 该服务处理跨物理和逻辑分区分布数据并将查询请求路由到正确的分区。 

## <a name="how-does-partitioning-work"></a>分区的工作原理

分区的工作原理 每个项必须有一个唯一标识自身的分区键和行键。 分区键充当数据的逻辑分区，为 Azure Cosmos DB 提供用于跨物理分区分布数据的自然边界。 请记住，单个逻辑分区的数据必须驻留在单个物理分区中，但物理分区管理由 Azure Cosmos DB 管理。 

简而言之，分区在 Azure Cosmos DB 中的工作原理如下：

* 使用每秒请求数吞吐量 **T** 预配 Azure Cosmos DB 容器。
* Azure Cosmos DB 在后台预配所需的分区来提供每秒请求数 **T**。 如果 **T** 高于每个分区的最大吞吐量 **t**，则 Azure Cosmos DB 会预配 **N = T/t** 个分区。
* Azure Cosmos DB 在 **N** 个分区之间均衡分配分区键哈希的键空间。 因此，每个分区（物理分区）托管 **1/N** 个分区键值（逻辑分区）。
* 当物理分区 **p** 达到其存储限制时，Azure Cosmos DB 会将 **p** 无缝拆分为两个新的分区 **p1** 和 **p2**。 它会向每个分区分发对应于大约一半键的值。 此拆分操作对应用程序不可见。 如果物理分区达到其存储限制，但物理分区中的所有数据属于同一个逻辑分区键，则不会发生该拆分操作。 这是由于单个逻辑分区键的所有数据必须驻留在同一个物理分区中，因此无法将物理分区拆分为 p1 和 p2。 在这种情况下，应采用其他分区键策略。
* 当预配高于 **t*N** 的吞吐量时，Azure Cosmos DB 会拆分一个或多个分区，以支持更高的吞吐量。

如下表中所示，为了与每个 API 的语义匹配，分区键的语义略有不同：

| API | 分区键 | 行键 |
| --- | --- | --- |
| Azure Cosmos DB | 自定义分区键路径 | 固定 `id` | 
| MongoDB | 自定义共享密钥  | 固定 `_id` | 
| 图形 | 自定义分区键属性 | 固定 `id` | 
| 表 | 固定 `PartitionKey` | 固定 `RowKey` | 

Azure Cosmos DB 使用基于哈希的分区。 写入某个项时，Azure Cosmos DB 将对分区键值进行哈希处理，并使用经过哈希处理的结果来确定要在其中存储该项的分区。 Azure Cosmos DB 将分区键相同的所有项存储在同一个物理分区中。 选择分区键是设计时需要做出的重要决定。 选择的属性名称必须具有范围较宽的值，并采用均匀的访问模式。 如果物理分区达到其存储限制，但分区中的所有数据使用同一个分区键，则 Azure Cosmos DB 返回“分区键达到 10 GB 的最大大小”错误并且不拆分分区，因此选择分区键是一个非常重要的决策。

> [!NOTE]
> 采用具有大量不同值（最少几百到几千个）的分区键是最佳做法。
>

可在 Azure 门户中以*固定*或*无限制*模式创建 Azure Cosmos DB 容器。 固定大小的容器最大限制为 10 GB，10,000 RU/s 吞吐量。 若要以无限制模式创建容器，必须指定 1,000 RU/s 的吞吐量下限以及一个分区键。

查看数据在分区中的分布方式是个好主意。 要在门户中进行查看，请转到 Azure Cosmos DB 帐户，在“监视”部分单击“指标”，然后在右窗格中单击“存储”选项卡，查看数据在不同物理分区中的分区方式。

![资源分区](./media/partition-data/partitionkey-example.png)

左图显示不良分区键的结果，右图显示良好分区键的结果。 从左图可看出，数据在分区间的分布不均匀。 应尽可能分布数据以使图的外观类似右图。

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>分区的先决条件

若要像[分区的工作原理](#how-does-partitioning-work)中所述将物理分区自动拆分为 **p1** 和 **p2**，必须创建具有 1,000 RU/s 或更高吞吐量的容器，并且必须提供分区键。 在 Azure 门户中创建容器时，选择“无限制”存储容量选项，以利用分区和自动缩放。 

如果是在 Azure 门户中或以编程方式创建的容器，其初始吞吐量为 1,000 RU/s 或更高，且数据包含分区键，则可以利用分区，而不必对容器进行更改，这包括**固定**大小的容器，前提是所创建的初始容器的吞吐量不小于 1,000 RU/s 且数据中存在分区键。

如果创建的是没有分区键的**固定**大小容器，或吞吐量小于 1,000 RU/s 的**固定**大小容器，则无法如本文所述自动拆分容器。 若要将数据从这类容器迁移到无限制的容器（吞吐量至少为 1000 RU/s，并具有分区键），则需使用[数据迁移工具](import-data.md)或[更改源库](change-feed.md)迁移更改。 

## <a name="partitioning-and-provisioned-throughput"></a>分区和设置的吞吐量
Azure Cosmos DB 旨在提供可预测的性能。 创建容器时，可以根据*每秒[请求单位](request-units.md) (RU) 数*保留吞吐量。 每个请求将分配有一个 RU 计费，RU 计费与系统资源（如操作使用的 CPU、内存和 IO）的数量成比例。 读取 1kb 会话一致性文档会使用 1 RU。 读取为 1 RU 时不考虑存储的项数量或同时运行的并发请求数。 较大的项要求较高的 RU，具体取决于大小。 如果知道实体大小及为应用程序提供支持需要的读取次数，则可以设置应用程序读取所需的那个吞吐量。 

> [!NOTE]
> 若要实现容器的全部吞吐量，必须选择分区键，可用于在某些不同的分区键值之间均匀分配请求。
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>使用 Azure Cosmos DB API
随时可以使用 Azure 门户或 Azure CLI 创建容器并对其进行缩放。 本部分介绍如何在每个支持的 API 中创建容器，并指定吞吐量和分区键定义。

### <a name="azure-cosmos-db-api"></a>Azure Cosmos DB API
以下示例演示如何通过使用 Azure Cosmos DB API 创建容器（集合）。 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

可以使用 REST API 中的 `GET` 方法或使用某个 SDK 中的 `ReadDocumentAsync` 读取项（文档）。

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>MongoDB API
使用 MongoDB API 可以通过常用的工具、驱动程序或 SDK 创建分片集合。 在此示例中，我们将使用 Mongo Shell 创建集合。

在 Mongo Shell 中：

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
结果：

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>表 API

若要使用 Azure Cosmos DB 表 API 创建表，请使用 CreateIfNotExists 方法。 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

吞吐量设置为 CreateIfNotExists 的参数。

分区键隐式创建为 `PartitionKey` 值。 

可以使用以下代码片段检索单个实体：

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
有关详细信息，请参阅[使用表 API 进行部署](tutorial-develop-table-dotnet.md)。

### <a name="graph-api"></a>图形 API

使用图形 API 时，必须使用 Azure 门户或 Azure CLI 创建容器。 由于 Azure Cosmos DB 是多模型服务，因此也可以使用其他模型之一来创建和缩放图形容器。

可以在 Gremlin 中使用分区键和 ID 读取任何顶点或边缘。 例如，对于使用区域（“USA”）作为分区键，使用“Seattle”作为行键的图形，可以使用以下语法查找顶点：

```
g.V(['USA', 'Seattle'])
```

可以使用分区键和行键引用边缘。

```
g.E(['USA', 'I5'])
```

有关详细信息，请参阅 [Azure Cosmos DB 的 Gremlin 支持](gremlin-support.md)。


<a name="designing-for-partitioning"></a>
## <a name="design-for-partitioning"></a>设计分区
若要有效地使用 Azure Cosmos DB 进行缩放，在创建容器时需要选择适当的分区键。 选择分区键时主要需考虑两个因素：

* **查询和事务的边界**。 所选的分区键应该权衡启用事务的需求与将实体分布到多个分区键（以确保可缩放的解决方案）的需求。 一种极端情况是为所有项设置相同的分区键，但此选项可能会限制解决方案的可扩展性。 另一种极端情况是为每个项分配一个唯一的分区键。 此选项可扩展性高，但无法通过存储过程和触发器使用跨文档事务。 理想的分区键可以使用高效查询，并具有足够多基数来确保解决方案的可缩放性。 
* **去除存储和性能瓶颈**。 请务必选择允许将写入分布在各种相异值之间的属性。 对相同分区键的请求不能超过单个分区的吞吐量，且受到限制。 请务必选择不会导致应用程序中产生“热点”的分区键。 单分区键的所有数据都必须存储在分区内，因此应避免使用具有大量相同数据值的分区键。 

让我们看看几个真实的情景，以及适合每种情景的分区键：
* 如果要实现用户配置文件后端，则用户 ID 是分区键的一个不错选择。
* 如果要存储 IoT 数据（如设备状态），则设备 ID 是分区键的一个不错选择。
* 如果正在使用 Azure Cosmos DB 记录时间序列数据，则主机名或进程 ID 是分区键的一个不错选择。
* 如果拥有多租户体系结构，则租户 ID 是分区键的一个不错选择。

在某些用例中（如 IoT 和用户配置文件），分区键可能与 ID（文档键）相同。 在其他用例（如时间序列数据）中，可能拥有与该 ID 不同的分区键。

### <a name="partitioning-and-loggingtime-series-data"></a>分区和记录/时间序列数据
Azure Cosmos DB 最常见的用例之一是日志记录和遥测。 选取恰当的分区键非常重要，因为你可能需要读取/写入大量数据。 选择将取决于读取和写入的速率以及要运行的查询类型。 以下是有关如何选择适当分区键的一些提示：

* 如果用例涉及少量在较长时期累积的写入，并且你需要按时间戳范围和其他筛选器进行查询，可使用时间戳汇总。 举例来说，使用日期作为分区键是一种不错的方法。 通过此方法可以查询某日单个分区中的所有数据。 
* 如果需要大量写入工作负载（这种情况更常见），可使用不基于时间戳的分区键。 通过这种方法，Azure Cosmos DB 可将写入平均分布到各个分区。 此处，主机名、进程 ID、活动 ID 或其他具有较大基数的属性是不错的选择。 
* 还有一种方法是混合型分区键，其中包含多个容器，每个日期/月份各有一个容器，分区键是类似主机名的粒度属性。 此方法的优点在于可根据时间范围设置不同的吞吐量。 例如，可将本月的容器预配为较高的吞吐量，因为它需要处理读取和写入。 将前几个月的容器预配为较低的吞吐量，因为其只需处理读取。

### <a name="partitioning-and-multitenancy"></a>分区和多租户
若要使用 Azure Cosmos DB 实现多租户应用程序，可以采用两种常用模式 – 每个租户一个分区键，每个租户一个容器。 下面是每种模式的优缺点：

* **每个租户一个分区键**。 在此模型中，租户共置于单个容器中。 但是，可以针对单个分区执行单个租户内的项查询和插入。 可以在租户中的所有项之间实现事务逻辑。 由于多个租户共享一个容器，可以通过在单个容器内集中租户资源而不是为每个租户配置额外的多余空间来节约存储和吞吐量成本。 缺点是未按每个租户隔离性能。 区别在于适用于整个容器的性能/吞吐量增加与适用于租户的针对性增加。
* **每个租户一个容器**。 在此模型中，每个租户都有其自己的容器，可以保留每个租户的性能。 根据 Azure Cosmos DB 新的预配定价，此模型对于拥有少量租户的多租户应用程序而言，性价比更高。

也可以使用组合/分层的方法来共置小型租户并将较大的租户迁移到它们自身的容器中。

## <a name="next-steps"></a>后续步骤
本文概述了有关使用任何 Azure Cosmos DB API 进行分区的概念和最佳做法。 

* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。



