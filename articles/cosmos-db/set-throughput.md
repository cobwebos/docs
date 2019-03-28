---
title: 在 Azure Cosmos 容器和数据库上预配吞吐量
description: 了解如何为 Azure Cosmos 容器和数据库设置预配的吞吐量。
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: andrl
ms.openlocfilehash: 8335a235de708227136400f3af8fa7b4d0a52e29
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520898"
---
# <a name="provision-throughput-on-containers-and-databases"></a>在容器和数据库上预配吞吐量

Azure Cosmos 数据库是一组容器的管理单元。 数据库包含一组不限架构的容器。 Azure Cosmos 容器是吞吐量和存储的缩放单元。 容器跨 Azure 区域中的一组计算机水平分区，并分布在与 Azure Cosmos 帐户关联的所有 Azure 区域之间。

使用 Azure Cosmos DB 可在两个粒度级别配置吞吐量：
 
- Azure Cosmos 容器
- Azure Cosmos 数据库

## <a name="set-throughput-on-a-container"></a>对容器设置吞吐量  

对 Azure Cosmos 容器预配的吞吐量专门保留给容器使用。 容器始终可获得预配的吞吐量。 对容器预配的吞吐量有 SLA 提供的经济保障。 若要对容器配置吞吐量，请参阅[对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。

对容器设置预配吞吐量是广泛使用的选项。 可以通过使用请求单位 (RU) 预配任意数量的吞吐量来弹性缩放容器的吞吐量。 但是，无法有选择性地指定逻辑分区的吞吐量。 

如果逻辑分区上运行的工作负荷消耗的吞吐量超过了分配给特定逻辑分区的吞吐量，操作将受到速率限制。 发生速率限制时，可以增大整个容器的吞吐量，或重试操作。 有关分区的详细信息，请参阅[逻辑分区](partition-data.md)。

如果你希望容器的性能有保证，则我们建议以容器粒度配置吞吐量。

对 Azure Cosmos 容器预配的吞吐量均匀分布于该容器的所有逻辑分区之间。 由于某个容器的一个或多个逻辑分区由物理分区托管，因此，物理分区专属于该容器，并支持对该容器预配的吞吐量。 

下图显示了物理分区如何托管容器的一个或多个逻辑分区：

![物理分区](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>对数据库设置吞吐量

对 Azure Cosmos 数据库预配吞吐量时，在该数据库中的所有容器之间共享吞吐量。 一种例外情况是对特定的容器指定了预配的吞吐量。 在容器之间共享数据库吞吐量相当于在计算机群集上托管数据库。 由于数据库中的所有容器共享一台计算机上的可用资源，因此，任何特定容器的性能自然不可预测。 若要对数据库配置吞吐量，请参阅[对 Azure Cosmos 数据库配置预配吞吐量](how-to-provision-database-throughput.md)。

对 Azure Cosmos 数据库设置吞吐量可保证随时能够获得预配的吞吐量。 由于数据库中的所有容器共享预配的吞吐量，因此，Azure Cosmos DB 不会针对该数据库中的特定容器提供任何可预测的吞吐量保证。 特定容器可获得的吞吐量部分取决于：

* 容器数量。
* 为各个容器选择的分区键。
* 工作负荷在容器的各个逻辑分区之间的分布形式。 

若要在多个容器之间共享吞吐量，而不希望将吞吐量专门提供给任何特定的容器使用，则我们建议对数据库配置吞吐量。 

以下示例演示了最适合在数据库级别的哪个位置预配吞吐量：

* 跨一组容器共享数据库的预配吞吐量对多租户应用程序非常有帮助。 每个用户可由不同的 Azure Cosmos 容器表示。

* 将 VM 群集或本地物理服务器中托管的 NoSQL 数据库（例如 MongoDB 或 Cassandra）迁移到 Azure Cosmos DB 时，在一组容器之间共享数据库的预配吞吐量非常有利。 可将针对 Azure Cosmos 数据库配置的预配吞吐量视为在逻辑上等同于（但更具成本效益和弹性）MongoDB 或 Cassandra 群集的计算容量。  

必须使用分区键创建在具有预配吞吐量的数据库内创建的所有容器。 在任意给定的时间点，分配给数据库中容器的吞吐量将分布在该容器的所有逻辑分区之间。 如果有容器共享数据库的预配吞吐量，则无法选择性地将吞吐量应用到特定的容器或逻辑分区。 

如果逻辑分区上的工作负荷消耗的吞吐量超过了分配给特定逻辑分区的吞吐量，操作将受到速率限制。 发生速率限制时，可以增大整个容器的吞吐量，或重试操作。 有关分区的详细信息，请参阅[逻辑分区](partition-data.md)。

可在单个物理分区中托管多个共享数据库预配吞吐量的逻辑分区。 尽管容器的单个逻辑分区始终划归到物理分区，但可以在“R”物理分区中映射和托管“C”容器中共享数据库预配吞吐量的“L”逻辑分区。 

下图显示了物理分区如何托管属于数据库中不同容器的一个或多个逻辑分区：

![物理分区](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>对数据库和容器设置吞吐量

可以合并两个模型。 同时对数据库和容器预配吞吐量。 以下示例演示如何对 Azure Cosmos 数据库和容器预配吞吐量：

* 可以创建预配吞吐量为“K”RU 的名为“Z”的 Azure Cosmos 数据库。 
* 接下来，在该数据库中创建名为 A、B、C、D 和 E 的五个容器。
* 可对名为 B 的容器显式配置“P”RU 的预配吞吐量。
* “K”RU 吞吐量在 A、C、D 和 E 这四个容器之间共享。A、C、D 或 E 的确切可用吞吐量有所不同。 每个容器的吞吐量没有 SLA 的保障。
* 保证名为 B 的容器始终可以获得“P”RU 吞吐量。 该容器有 SLA 的保障。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新数据库或容器上的吞吐量

创建 Azure Cosmos 容器或数据库后，可以更新预配的吞吐量。 最大预配的吞吐量，可以配置对该数据库或容器中没有任何限制。 最小的预配的吞吐量取决于以下因素： 

* 曾经在容器中存储的最大数据大小
* 你曾在容器设置最大吞吐量
* 千万别创建具有共享吞吐量的数据库中的 Azure Cosmos 容器最大数目。 

可以使用 Sdk 以编程方式检索容器或数据库的最小吞吐量或在 Azure 门户中查看的值。 在使用.NET SDK [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet)方法，可缩放的预配的吞吐量值。 在使用 Java SDK [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples)方法，可缩放的预配的吞吐量值。 

在使用.NET SDK [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet)方法，可检索的容器或数据库的最小吞吐量。 

可以在任何时候缩放的容器或数据库预配的吞吐量。 可以运行缩减操作后空闲期为 4 小时。 作为时间定义空闲时间段期间不出现任何产品/服务容器或数据库上的 （其中包括纵向扩展和缩减） 替换操作时。 

## <a name="comparison-of-models"></a>模型比较

|**配额**  |**对数据库的预配吞吐量**  |**对容器预配的吞吐量**|
|---------|---------|---------|
|最小 RU 数 |400（前四个容器之后的每个容器均需要至少每秒 100 RU 的吞吐量。） |400|
|每个容器的最小 RU 数|100|400|
|使用 1 GB 存储所需的最小 RU 数|40|40|
|最大 RU 数|对于数据库无限。|对于容器无限。|
|分配或提供给特定容器的 RU 数|无保证。 为给定容器分配的 RU 数取决于多种属性。 属性可以是为共享吞吐量的容器选择的分区键、工作负荷的分布，以及容器的数量。 |对容器配置的所有 RU 专门保留给该容器使用。|
|容器的最大存储|不受限制。|不受限制。|
|容器的每个逻辑分区的最大吞吐量|10K RU|10K RU|
|容器的每个逻辑分区的最大存储（数据 + 索引）|10 GB|10 GB|

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

