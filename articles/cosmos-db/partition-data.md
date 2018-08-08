---
title: Azure Cosmos DB 中的分区和水平缩放 | Microsoft Docs
description: 了解分区在 Azure Cosmos DB 中的工作原理，如何配置分区和分区键以及如何为应用程序选取适当的分区键。
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cb668dba661ce05d6393aec2707b65918f0c2ac
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344124"
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中分区和缩放

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是一种全局分布的多模型数据库服务，旨在帮助用户实现快速且可预测的性能。 随着应用程序的发展，它也不断无缝扩展。 本文概述了分区如何应用于 Azure Cosmos DB 中的所有数据模型。 文中还介绍了如何配置 Azure Cosmos DB 容器以有效扩展应用程序。

此视频介绍了分区和分区键：

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区
Azure Cosmos DB 提供称作集合（适用于文档）、图形或表的容器用于存储数据。 容器是逻辑资源，可跨一个或多个物理分区或服务器。 Azure Cosmos DB 根据一个或一组容器的预配存储大小和吞吐量确定分区数。 


### <a name="physical-partition"></a>物理分区

物理分区是指支持 SSD 的保留存储容量固定但计算资源（CPU 和内存）量可变的分区。 将复制每个物理分区以实现高可用性。 每组容器可能共享一个或多个物理分区。 物理分区完全由 Azure Cosmos DB 进行管理，因此，无需编写复杂的代码或亲自管理分区。 Azure Cosmos DB 容器在存储和吞吐量方面没有限制。 物理分区是 Azure Cosmos DB 的内部概念并且是暂时的。 Azure Cosmos DB 将根据工作负荷自动缩放物理分区的数量。 因此，不应根据物理分区的数量来关联数据库设计，而应该确保选择确定逻辑分区的正确分区键。 

### <a name="logical-partition"></a>逻辑分区

逻辑分区指物理分区内用于存储与单个分区键值关联的所有数据的分区。 多个逻辑分区最终可以位于同一物理分区中。 在下图中，单个容器包含三个逻辑分区。 每个逻辑分区存储一个分区键（分别为 LAX、AMS 和 MEL）的数据。 每个 LAX、AMS 和 MEL 逻辑分区均不能超过 10 GB 的最大逻辑分区限制。 

![资源分区](./media/introduction/azure-cosmos-db-partitioning.png) 

当容器满足[分区先决条件](#prerequisites)时，分区对应用程序完全透明。 Azure Cosmos DB 处理物理和逻辑分区之间的数据分布，以及将查询请求路由到正确的分区。 

## <a name="how-does-partitioning-work"></a>分区的工作原理

每个文档必须有一个唯一标识自身的分区键和行键。 分区键充当数据的逻辑分区，为 Azure Cosmos DB 提供用于跨物理分区分布数据的自然边界。 **单个逻辑分区的数据必须驻留在单个物理分区中，物理分区管理由 Azure Cosmos DB 管理**。 

简而言之，分区在 Azure Cosmos DB 中的工作原理如下：

* 使用 **T** RU/秒（每秒请求数）吞吐量预配一组 Azure Cosmos DB 容器。  
* Azure Cosmos DB 在后台预配所需的物理分区，每秒处理 **T** 个请求。 如果 **T** 高于每个物理分区的最大吞吐量 **t**，则 Azure Cosmos DB 会预配 **N = T/t** 个物理分区。 单分区的最大吞吐量的值由 Azure Cosmos DB 配置。此值根据总的预配吞吐量以及所使用的硬件配置来分配。  
* Azure Cosmos DB 在 **N** 个物理分区中均衡分配分区键哈希的键空间。 因此，每个物理分区托管的逻辑分区数是 **1/N** * 分区键值的个数。  
* 当物理分区 **p** 达到其存储限制时，Azure Cosmos DB 会将 **p** 无缝拆分为两个新的物理分区：**p1** 和 **p2**。 它会向每个新物理分区分发对应于大约一半键的值。 此拆分操作对应用程序完全不可见。 如果物理分区达到其存储限制，但物理分区中的所有数据属于同一个逻辑分区键，则不会发生该拆分操作。 这是因为单个逻辑分区键的所有数据必须驻留在同一个物理分区中。 在这种情况下，应采用其他分区键策略。  
* 当预配高于 **t*N** 的吞吐量时，Azure Cosmos DB 会拆分一个或多个物理分区，以支持更高的吞吐量。

如下表中所示，为了与每个 API 的语义匹配，分区键的语义略有不同：

| API | 分区键 | 行键 |
| --- | --- | --- |
| SQL | 自定义分区键路径 | 固定 `id` | 
| MongoDB | 自定义分片键  | 固定 `_id` | 
| Gremlin | 自定义分区键属性 | 固定 `id` | 
| 表 | 固定 `PartitionKey` | 固定 `RowKey` | 

Azure Cosmos DB 使用基于哈希的分区。 写入某个项时，Azure Cosmos DB 将对分区键值进行哈希处理，并使用经过哈希处理的结果来确定要在其中存储该项的分区。 

> [!NOTE]
> Azure Cosmos DB 将分区键相同的所有项存储在同一个物理分区中。 

## <a name="best-practices-when-choosing-a-partition-key"></a>选择分区键时的最佳做法

选择分区键是设计时需要做出的重要决定。 选择的属性名称应具有范围较宽的值，并采用均衡的访问模式。 采用具有大量不同值（例如数百或数千个）的分区键是最佳做法。 这样就可以跨这些值均匀分配工作负荷。 理想的分区键可以作为筛选器频繁出现在查询中，并具有足够的基数，以确保解决方案可缩放。

如果物理分区达到其存储限制，但分区中的数据使用同一个分区键，则 Azure Cosmos DB 将返回“分区键达到 10 GB 的最大大小”消息，并且不会拆分分区。 选择适当的分区键非常重要。 

选择分区键，以便：

* 存储均匀分布在所有键中。  
* 在给定的时间点，请求的卷均匀分布在所有键中。  

  查看数据在不同分区之间的分布方式是个好主意。 若要在门户中查看数据分布情况，请转到 Azure Cosmos DB 帐户，在“监视”部分单击“指标”，然后单击“存储”选项卡，查看数据在不同物理分区之间的分区方式。

  ![资源分区](./media/partition-data/partitionkey-example.png)

  上面的左图显示不良分区键的结果，上面的右图显示选择了良好分区键时的结果。 从左图可以看出，数据在分区间的分布不均匀。 应该尽量选择用于分布数据的分区键，使其看上去与右图类似。

* 通过在筛选器谓词中包含分区键，可以高效地路由通过高并发性调用的查询。  
* 通常首选具有较高基数的分区键 - 因为它通常会产生更好的分布和可伸缩性。 例如，可以通过连接来自多个属性的值来形成合成键以增加基数。  

考虑上述注意事项选择分区键时，你不必担心分区数量或每个物理分区分配的吞吐量，因为 Azure Cosmos DB 会横向扩展物理分区的数量，还可以根据需要缩放单个分区。

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>分区的先决条件

可在 Azure 门户中以固定或无限制模式创建 Azure Cosmos DB 容器。 固定大小的容器最大限制为 10 GB，10,000 RU/s 吞吐量。 若要以无限制模式创建容器，必须指定一个分区键，以及最低 1,000 RU/秒的吞吐量。 也可将 Azure Cosmos DB 容器配置为在一组容器之间共享吞吐量，该组中的每个容器必须指定一个分区键，可以无限增长。 下面是分区和缩放时要考虑的先决条件：

* 在 Azure 门户中创建容器（例如集合、图形或表）时，请选择“无限制”存储容量选项，以利用无限缩放。 若要按照[分区的工作原理](#how-does-partitioning-work)中所述将物理分区自动拆分为 **p1** 和 **p2**，必须创建吞吐量为 1,000 RU/秒或更高的容器（或者在一组容器之间共享吞吐量），并且必须提供分区键。 

* 如果在 Azure 门户或以编程方式创建了容器，并且初始吞吐量为 1,000 RU/秒或更高，另外还提供了分区键，则可以利用无限缩放，且无需对容器进行更改。 这包括**固定**容器，前提是创建的初始容器至少具有 1,000 RU/秒的吞吐量，并且指定了分区键。

* 可以将一组容器中包含的所有配置为共享吞吐量的容器视为“无限制”容器。

如果创建的**固定**容器没有分区键或吞吐量小于 1,000 RU/秒，则无法自动缩放该容器。 若要将数据从固定的容器迁移到无限制容器，则需使用[数据迁移工具](import-data.md)或[更改源库](change-feed.md)。 

## <a name="partitioning-and-provisioned-throughput"></a>分区和设置的吞吐量
Azure Cosmos DB 旨在提供可预测的性能。 创建一个或一组容器时，可以根据每秒[请求单位数](request-units.md) (RU) 保留吞吐量。 每个请求会产生 RU 费用，RU 费用与系统资源（例如操作使用的 CPU、内存和 IO）的数量成比例。 读取 1kb 会话一致性文档会使用 1 RU。 读取为 1 RU 时不考虑存储的项数量或同时运行的并发请求数。 较大的项要求较高的 RU，具体取决于大小。 如果知道实体大小以及为应用程序提供支持需要的读取次数，则可以预配应用程序所需的确切吞吐量。 

> [!NOTE]
> 若要充分利用一个或一组容器的预配吞吐量，必须选择一个可用于在所有不同分区键值之间均衡分配请求的分区键。
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="create-partition-key"></a>创建分区键 
随时可以使用 Azure 门户或 Azure CLI 创建容器并对其进行缩放。 本部分介绍如何使用每个 API 创建容器及指定预配的吞吐量和分区键。


### <a name="sql-api"></a>SQL API
以下示例演示如何使用 SQL API 创建容器（集合）。 

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

有关详细信息，请参阅[使用 SQL API 在 Azure Cosmos DB 中分区](sql-api-partition-data.md)。

### <a name="mongodb-api"></a>MongoDB API
使用 MongoDB API 可以通过常用的工具、驱动程序或 SDK 创建分片集合。 此示例使用 Mongo Shell 创建集合。

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

若要使用表 API 创建表，请使用 `CreateIfNotExists` 方法。 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

预配的吞吐量设置为 `CreateIfNotExists` 的参数。 分区键隐式创建为 `PartitionKey` 值。 

可使用以下代码检索单个实体：

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
有关详细信息，请参阅[使用表 API 进行部署](tutorial-develop-table-dotnet.md)。

### <a name="gremlin-api"></a>Gremlin API

借助 Gremlin API，可以使用 Azure 门户或 Azure CLI 创建表示图形的容器。 由于 Azure Cosmos DB 是多模型服务，因此你可以使用其他 API 之一来创建和缩放图形容器。

可以在 Gremlin 中使用分区键和 ID 读取任何顶点或边缘。 例如，对于使用区域（“USA”）作为分区键，使用“Seattle”作为行键的图形，可以使用以下语法查找顶点：

```
g.V(['USA', 'Seattle'])
```

可以使用分区键和行键引用边缘。

```
g.E(['USA', 'I5'])
```

有关详细信息，请参阅[在 Azure Cosmos DB 中使用分区图形](graph-partitioning.md)。

## <a name="form-partition-key-by-concatenating-multiple-fields"></a>通过串联多个字段来构成分区键

还可以通过将多个属性值串联并填充成项的单个人工“partitionKey”属性，来构成分区键。 这些键称为合成键。

例如，你有一个如下所示的文档：

```json
{
"deviceId": "abc-123",
"date": 2018
}
```

一种做法是设置基于 /deviceId 或 /date 中的 partitionKey。 如果想要基于设备 ID 和日期构成分区键： 将这两个值串联成一个人工“partitionKey”属性，并将分区键设置为 /partitionKey。

```json
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

实时方案中可能包含数千个文档，因此，应该定义客户端逻辑，以将值串联成合成键，将合成键插入文档，然后使用合成键来指定分区键。

<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>缩放设计
若要有效地使用 Azure Cosmos DB 进行缩放，在创建容器时需要选择适当的分区键。 选择适当的分区键时需要考虑两个主要因素：

* **查询边界和事务**。 所选的分区键应该权衡使用事务的需求与将实体分布到多个分区键（以确保可缩放的解决方案）的需求。 一种极端情况是为所有项设置相同的分区键，但此选项可能会限制解决方案的可扩展性。 另一种极端情况是为每个项分配一个唯一的分区键。 此选项可扩展性高，但无法通过存储过程和触发器使用跨文档事务。 理想的分区键可以使用高效查询，并具有足够多基数来确保解决方案的可缩放性。 
* **去除存储和性能瓶颈**。 请务必选择允许将写入分布在各种相异值之间的属性。 对相同分区键的请求不能超过分配给某个分区的预配吞吐量，且速率受到限制。 请务必选择不会导致应用程序中产生“热点”的分区键。 单分区键的所有数据都必须存储在分区内，因此应避免使用具有大量相同数据值的分区键。 

让我们看看几个真实的情景，以及适合每种情景的分区键：
* 如果要实现用户配置文件后端，则用户 ID 是分区键的一个不错选择。
* 如果要存储 IoT 数据（如设备状态），则设备 ID 是分区键的一个不错选择。
* 如果正在使用 Azure Cosmos DB 记录时间序列数据，则主机名或进程 ID 是分区键的一个不错选择。
* 如果拥有多租户体系结构，则租户 ID 是分区键的一个不错选择。

在某些用例中（如 IoT 和用户配置文件），分区键可能与 *ID*（文档键）相同。 在其他用例（如时序数据）中，可能拥有与该 *ID* 不同的分区键。

### <a name="partitioning-and-loggingtime-series-data"></a>分区和记录/时间序列数据
Azure Cosmos DB 中的最常见用例之一是日志记录和遥测。 在此场景中，选择适当的分区键非常重要，因为可能需要读取/写入大量数据。 分区键的选择将取决于读取和写入的速率以及要运行的查询类型。 以下是有关如何选择适当分区键的一些提示：

* 如果用例涉及少量在较长时期累积的写入，并且你需要按时间戳范围和其他筛选器进行查询，请使用时间戳汇总。 举例来说，使用日期作为分区键是一种不错的方法。 通过此方法可以查询单个分区中在给定日期的所有数据。 
* 如果工作负荷是写入密集型的（在此场景中很常见），请使用不是基于时间戳的分区键。 这样，Azure Cosmos DB 便可将写入平均分布到各个分区。 此处，主机名、进程 ID、活动 ID 或其他具有较大基数的属性是不错的选择。 
* 还有一种方法是混合型分区键，其中包含多个容器，每个日期/月份各有一个容器，分区键是类似主机名的更高粒度的属性。 此方法的优点是可以根据时间范围以及规模和性能需求为一个或一组容器设置不同的吞吐量。 例如，可将本月的容器预配为较高的吞吐量，因为它需要处理读取和写入。 可将前几个月的容器预配为较低的吞吐量，因为其只需处理读取。

### <a name="partitioning-and-multitenancy"></a>分区和多租户
若要使用 Azure Cosmos DB 实现多租户应用程序，请考虑以下两种流行的设计：每个租户一个分区键、每个租户一个容器。 下面是每种模式的优缺点：

* **每个租户一个分区键**。 在此模型中，租户共置于单个容器中。 可以针对单个分区的单个租户执行查询和插入。 可以在属于某个租户的所有项之间实现事务逻辑。 由于多个租户共享一个容器，因此可将单个容器中所有租户的资源加入池中，而不是为每个租户预配资源，从而更好地利用存储和预配的吞吐量。 缺点是未按每个租户隔离性能。 提高吞吐量来保证性能的做法将应用到包含所有租户的整个容器，而不是针对单个租户提高。
* **每个租户一个容器**。 在此模型中，每个租户有其自身的容器，你可以保留吞吐量，并保证每个租户的性能。 此模型对于包含少量租户的多租户应用程序而言性价比更高。

也可以使用混合方法将小型租户共置在一起，并将大型租户隔离到其自身的容器中。

## <a name="next-steps"></a>后续步骤
本文概述了有关在 Azure Cosmos DB 中进行缩放和分区的概念与最佳做法。 

* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。



