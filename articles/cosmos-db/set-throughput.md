---
title: 在 Azure Cosmos 容器和数据库上预配吞吐量
description: 了解如何为 Azure Cosmos 容器和数据库设置预配的吞吐量。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: dcdc9c6a22f5d291b3773da02a79cc5ceebc3db1
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73101457"
---
# <a name="provision-throughput-on-containers-and-databases"></a>在容器和数据库上预配吞吐量

Azure Cosmos 数据库是一组容器的管理单元。 数据库包含一组不限架构的容器。 Azure Cosmos 容器是吞吐量和存储的缩放单元。 容器跨 Azure 区域中的一组计算机水平分区，并分布在与 Azure Cosmos 帐户关联的所有 Azure 区域之间。

通过 Azure Cosmos DB，可以在两个粒度预配吞吐量：
 
- Azure Cosmos 容器
- Azure Cosmos 数据库

## <a name="set-throughput-on-a-container"></a>对容器设置吞吐量  

在 Azure Cosmos 容器上预配的吞吐量是专门为该容器预留的。 容器始终可获得预配的吞吐量。 对容器预配的吞吐量有 SLA 提供的经济保障。 若要了解如何配置容器的吞吐量，请参阅[在 Azure Cosmos 容器上预配吞吐量](how-to-provision-container-throughput.md)。

在容器上设置预配吞吐量是最常用的选项。 可以通过使用[请求单位（ru）](request-units.md)预配任意数量的吞吐量来弹性调整容器的吞吐量。 

假设有一个正确的分区键，它在物理分区之间平均分配逻辑分区，则吞吐量也均匀分布在容器的所有逻辑分区中。 不能有选择地为逻辑分区指定吞吐量。 由于某个容器的一个或多个逻辑分区由物理分区托管，因此，物理分区专属于该容器，并支持对该容器预配的吞吐量。 

如果逻辑分区上运行的工作负荷消耗的吞吐量超过分配给该逻辑分区的吞吐量，则操作会获得速率限制。 发生速率限制时，可以增加整个容器的预配吞吐量，也可以重试操作。 有关分区的详细信息，请参阅[逻辑分区](partition-data.md)。

如果你希望容器的性能有保证，则我们建议以容器粒度配置吞吐量。

下图显示了物理分区如何托管容器的一个或多个逻辑分区：

![物理分区](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>对数据库设置吞吐量

在 Azure Cosmos 数据库上预配吞吐量时，将在数据库中的所有容器（称为共享数据库容器）之间共享吞吐量。 例外情况是在数据库中的特定容器上指定了预配的吞吐量。 在其容器之间共享数据库级预配吞吐量类似于在计算机群集上承载数据库。 由于数据库中的所有容器共享一台计算机上的可用资源，因此，任何特定容器的性能自然不可预测。 若要了解如何配置数据库的预配吞吐量，请参阅[在 Azure Cosmos 数据库上配置预配吞吐量](how-to-provision-database-throughput.md)。

在 Azure Cosmos 数据库上设置吞吐量可确保你始终获得该数据库的预配吞吐量。 由于数据库中的所有容器共享预配的吞吐量，因此，Azure Cosmos DB 不会针对该数据库中的特定容器提供任何可预测的吞吐量保证。 特定容器可获得的吞吐量部分取决于：

* 容器数量。
* 为各个容器选择的分区键。
* 工作负荷在容器的各个逻辑分区之间的分布形式。 

若要在多个容器之间共享吞吐量，而不希望将吞吐量专门提供给任何特定的容器使用，则我们建议对数据库配置吞吐量。 

以下示例演示了最适合在数据库级别的哪个位置预配吞吐量：

* 跨一组容器共享数据库的预配吞吐量对多租户应用程序非常有帮助。 每个用户可由不同的 Azure Cosmos 容器表示。

* 将 VM 群集或本地物理服务器中托管的 NoSQL 数据库（例如 MongoDB 或 Cassandra）迁移到 Azure Cosmos DB 时，在一组容器之间共享数据库的预配吞吐量非常有利。 可将针对 Azure Cosmos 数据库配置的预配吞吐量视为在逻辑上等同于（但更具成本效益和弹性）MongoDB 或 Cassandra 群集的计算容量。  

必须使用[分区键](partition-data.md)创建在具有预配吞吐量的数据库中创建的所有容器。 在任意给定时间点，分配给数据库中的容器的吞吐量分布在该容器的所有逻辑分区中。 如果有共享在数据库上配置的预配吞吐量的容器，则不能有选择地将吞吐量应用于特定容器或逻辑分区。 

如果逻辑分区上的工作负荷消耗的吞吐量超过了分配给特定逻辑分区的吞吐量，操作将受到速率限制。 当发生速率限制时，可以增加整个数据库的吞吐量或重试操作。 有关分区的详细信息，请参阅[逻辑分区](partition-data.md)。

在数据库上预配的吞吐量可由该数据库内的容器共享。 数据库级别的共享吞吐量的每个新容器都需要 100 RU/s。 设置包含共享数据库产品/服务的容器时：

* 每25个容器都分组到一个分区集中，并且在分区集中的容器之间共享数据库吞吐量（D）。 如果数据库中最多有25个容器，并且在任何时间点，如果仅使用一个容器，则该容器可以使用最大吞吐量。

* 对于25个容器之后创建的每个新容器，将创建一个新的分区集，并在创建的新分区集之间拆分数据库吞吐量（这是2个分区集的 D/2 分区集，D/3 个分区集 ...）。 在任意时间点，如果仅使用数据库中的一个容器，则它最多可以使用（D/2，D/3，D/4 。 吞吐量）。 由于吞吐量降低，因此建议您在一个数据库中创建不超过25个容器。

**示例**

* 如果创建一个名为 "MyDB" 的数据库，其预配吞吐量为 10K RU/s。

* 如果在 "MyDB" 下预配25个容器，则所有容器都将分组到分区集中。 在任意时间点，如果只使用数据库中的一个容器，则最多可以使用 10K RU/秒（D）。

* 如果预配了26个容器，则将创建一个新的分区集，并在两个分区集之间平均拆分吞吐量。 因此，在任何时间点，如果仅使用数据库中的一个容器，则最多可以使用最多的1个 RU/秒（D/2）。 因为有两个分区集，所以吞吐量可共享性因子拆分为 D/2。

   下图以图形方式展示了前面的示例：

   ![数据库级别吞吐量的可共享性因素](./media/set-throughput/database-level-throughput-shareability-factor.png)


如果工作负荷涉及删除和重新创建数据库中的所有集合，建议删除空数据库，并在创建集合之前重新创建新数据库。 下图显示了物理分区如何托管属于数据库中不同容器的一个或多个逻辑分区：

![物理分区](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>对数据库和容器设置吞吐量

可以合并两个模型。 同时对数据库和容器预配吞吐量。 以下示例演示如何对 Azure Cosmos 数据库和容器预配吞吐量：

* 你可以创建一个名为*Z*的 Azure Cosmos 数据库，其预配吞吐量为 *"K"* ru。 
* 接下来，在数据库中创建名*为 A*、 *B*、 *C*、 *D*和*E*的五个容器。 创建容器 B 时，请确保为**此容器选项启用预配专用吞吐量**，并在此容器上显式配置预配吞吐量的 *"P"* 个 ru。 请注意，只有在创建数据库和容器时，才能配置共享和专用吞吐量。 

   ![在容器级别设置吞吐量](./media/set-throughput/coll-level-throughput.png)

* 在四个容器*A*、 *C*、 *D*和*E*之间共享 *"K"* ru 吞吐量。可用于、 *C*、 *D*或*E* *的精确*吞吐量会有所不同。 每个容器的吞吐量没有 SLA 的保障。
* 可以确保名为*B*的容器始终获取 *"P"* ru 吞吐量。 该容器有 SLA 的保障。

> [!NOTE]
> 具有预配吞吐量的容器无法转换为共享数据库容器。 与此相反，共享数据库容器不能转换为具有专用的吞吐量。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新数据库或容器的吞吐量

创建 Azure Cosmos 容器或数据库后，可以更新预配的吞吐量。 对于可以在数据库或容器上配置的最大预配吞吐量没有限制。 预配的最小吞吐量取决于以下因素： 

* 你曾经在容器中存储的最大数据大小
* 你在容器中设置的最大吞吐量
* 使用共享吞吐量在数据库中创建的 Azure Cosmos 容器的最大数目。 

您可以使用 Sdk 以编程方式检索容器或数据库的最小吞吐量，或在 Azure 门户中查看值。 使用 .NET SDK 时，使用[DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet)方法可以缩放预配的吞吐量值。 使用 Java SDK 时，可以使用[RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples)方法缩放预配的吞吐量值。 

使用 .NET SDK 时，使用[DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet)方法可以检索容器或数据库的最小吞吐量。 

你可以随时缩放容器或数据库的预配吞吐量。 执行缩放操作以增加吞吐量时，可能需要较长的时间，因为系统任务需要设置所需的资源。 可以在 Azure 门户中或使用 Sdk 以编程方式检查缩放操作的状态。 使用 .Net SDK 时，可以使用 `DocumentClient.ReadOfferAsync` 方法获取缩放操作的状态。

## <a name="comparison-of-models"></a>模型比较

|**Parameter**  |**对数据库的预配吞吐量**  |**对容器预配的吞吐量**|
|---------|---------|---------|
|最小 RU 数 |400（前四个容器之后的每个容器均需要至少每秒 100 RU 的吞吐量。） |400|
|每个容器的最小 RU 数|100|400|
|最大 RU 数|对于数据库无限。|对于容器无限。|
|分配或提供给特定容器的 RU 数|无保证。 为给定容器分配的 RU 数取决于多种属性。 属性可以是为共享吞吐量的容器选择的分区键、工作负荷的分布，以及容器的数量。 |对容器配置的所有 RU 专门保留给该容器使用。|
|容器的最大存储|不受限制。|不受限制。|
|容器的每个逻辑分区的最大吞吐量|10K RU|10K RU|
|容器的每个逻辑分区的最大存储（数据 + 索引）|10 GB|10 GB|

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

