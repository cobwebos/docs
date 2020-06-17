---
title: 对 Azure Cosmos 容器和数据库预配吞吐量
description: 了解如何为 Azure Cosmos 容器和数据库设置预配的吞吐量。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 910a0d9b70a63fc93aebd47896db7c3493c846b2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684030"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>Azure Cosmos DB 中的预配吞吐量简介

Azure Cosmos DB 允许对数据库和容器设置预配吞吐量。 有两种类型的预配吞吐量：标准（手动）或自动缩放。 本文概述预配吞吐量的工作原理。 

Azure Cosmos 数据库是一组容器的管理单元。 数据库包含一组不限架构的容器。 Azure Cosmos 容器是吞吐量和存储的缩放单元。 容器跨 Azure 区域中的一组计算机水平分区，并分布在与 Azure Cosmos 帐户关联的所有 Azure 区域之间。

对于 Azure Cosmos DB，可以在两个粒度级别预配吞吐量：
 
- Azure Cosmos 容器
- Azure Cosmos 数据库

## <a name="set-throughput-on-a-container"></a>对容器设置吞吐量  

对 Azure Cosmos 容器预配的吞吐量专门保留给该容器使用。 容器始终可获得预配的吞吐量。 对容器预配的吞吐量有 SLA 提供的经济保障。 若要了解如何对容器配置标准（手动）吞吐量，请参阅[对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。 若要了解如何对容器配置自动缩放吞吐量，请参阅[预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)。

对容器设置预配吞吐量是最常用的选项。 可以通过使用[请求单位 (RU)](request-units.md) 预配任意数量的吞吐量来弹性缩放容器的吞吐量。 

为容器预配的吞吐量在其物理分区之间均匀分布。假设有一个适当的分区键，它在物理分区之间均匀分配逻辑分区，那么吞吐量也会均匀分布在容器的所有逻辑分区之间。 无法选择性地指定逻辑分区的吞吐量。 由于某个容器的一个或多个逻辑分区由物理分区托管，因此，物理分区专属于该容器，并支持对该容器预配的吞吐量。 

如果逻辑分区上运行的工作负荷消耗的吞吐量超过了分配给该逻辑分区的吞吐量，操作将受到速率限制。 发生速率限制时，可以增大整个容器的预配吞吐量，或重试操作。 有关分区的详细信息，请参阅[逻辑分区](partition-data.md)。

如果你希望容器的性能有保证，则我们建议以容器粒度配置吞吐量。

下图显示了物理分区如何托管容器的一个或多个逻辑分区：

![物理分区](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>对数据库设置吞吐量

对 Azure Cosmos 数据库预配吞吐量时，会在该数据库中的所有容器（称作共享的数据库容器）之间共享吞吐量。 一种例外是在数据库中的特定容器上指定了预配的吞吐量。 在容器之间共享数据库级预配吞吐量类似于在计算机群集上托管数据库。 由于数据库中的所有容器共享一台计算机上的可用资源，因此，任何特定容器的性能自然不可预测。 若要了解如何对数据库配置预配吞吐量，请参阅[对 Azure Cosmos 数据库配置预配吞吐量](how-to-provision-database-throughput.md)。 若要了解如何对数据库配置自动缩放吞吐量，请参阅[预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)。

对 Azure Cosmos 数据库设置吞吐量可保证始终能够得到为该数据库预配的吞吐量。 由于数据库中的所有容器共享预配的吞吐量，因此，Azure Cosmos DB 不会针对该数据库中的特定容器提供任何可预测的吞吐量保证。 特定容器可获得的吞吐量部分取决于：

* 容器数量。
* 为各个容器选择的分区键。
* 工作负荷在容器的各个逻辑分区之间的分布形式。 

若要在多个容器之间共享吞吐量，而不希望将吞吐量专门提供给任何特定的容器使用，则我们建议对数据库配置吞吐量。 

以下示例演示了最适合在数据库级别的哪个位置预配吞吐量：

* 跨一组容器共享数据库的预配吞吐量对多租户应用程序非常有帮助。 每个用户可由不同的 Azure Cosmos 容器表示。

* 将 VM 群集或本地物理服务器中托管的 NoSQL 数据库（例如 MongoDB 或 Cassandra）迁移到 Azure Cosmos DB 时，在一组容器之间共享数据库的预配吞吐量会非常有用。 可将针对 Azure Cosmos 数据库配置的预配吞吐量视为在逻辑上等同于（但更具成本效益和弹性）MongoDB 或 Cassandra 群集的计算容量。  

必须使用[分区键](partition-data.md)创建在具有预配吞吐量的数据库内创建的所有容器。 在任意给定的时间点，分配给数据库中容器的吞吐量将分布在该容器的所有逻辑分区之间。 如果有容器共享对数据库配置的预配吞吐量，则无法选择性地将吞吐量应用到特定的容器或逻辑分区。 

如果逻辑分区上的工作负荷消耗的吞吐量超过了分配给特定逻辑分区的吞吐量，操作将受到速率限制。 发生速率限制时，可以增大整个数据库的吞吐量，或重试操作。 有关分区的详细信息，请参阅[逻辑分区](partition-data.md)。

共享吞吐量数据库中的容器共享分配给该数据库的吞吐量（RU/秒）。 你最多可以有四个容器，在数据库上的最低吞吐量为 400 RU/秒。 如果使用标准（手动）预配吞吐量，那么前四个容器后面的每个新容器至少需要 100 RU/秒的吞吐量。 例如，如果你有一个包含八个容器的共享吞吐量数据库，则数据库上的最小吞吐量将为 800 RU/秒。 如果使用自动缩放预配吞吐量，那么一个数据库中容器的吞吐量可自动缩放到的最大值是 4000 RU/秒（在 400 - 4000 RU/秒之间缩放）。

> [!NOTE]
> 2020 年 2 月，我们引入了一项更改，允许在一个共享吞吐量数据库中最多包含 25 个容器，以方便实现跨容器共享吞吐量。 在前 25 个容器的后面，可将更多容器添加到数据库，前提是为这些容器[预配专用吞吐量](#set-throughput-on-a-database-and-a-container)（独立于数据库的共享吞吐量）。<br>
如果 Azure Cosmos DB 帐户已包含一个共享吞吐量数据库，并且该数据库中的容器数量达到或超过了 25 个，则该帐户以及同一 Azure 订阅中的所有其他帐户不受此项更改的影响。 如果你想要提供反馈或提问，请[联系产品支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

如果工作负荷涉及到删除数据库中的所有集合并重新创建集合，则我们建议删除空数据库，再重新创建新的数据库，然后创建集合。 下图显示了物理分区如何托管属于数据库中不同容器的一个或多个逻辑分区：

![物理分区](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>对数据库和容器设置吞吐量

可以合并两个模型。 同时对数据库和容器预配吞吐量。 以下示例演示如何对 Azure Cosmos 数据库和容器预配标准（手动）预配吞吐量：

* 可以创建一个具有标准（手动）预配吞吐量（“K”RU）的名为 *Z* 的 Azure Cosmos 数据库。 
* 接下来，在该数据库中创建名为 *A*、*B*、*C*、*D* 和 *E* 的五个容器。 创建容器 B 时，请确保启用“为此容器预配专用吞吐量”选项，并在此容器上显式配置“P”RU 的预配吞吐量。 请注意，只有在创建数据库和容器时，才能配置共享吞吐量和专用吞吐量。 

   ![在容器级别设置吞吐量](./media/set-throughput/coll-level-throughput.png)

* “K”RU 吞吐量在 *A*、*C*、*D* 和 *E* 这四个容器之间共享。 *A*、*C*、*D* 或 *E* 可用的确切吞吐量因情况而异。 每个容器的吞吐量没有 SLA 的保障。
* 名为 *B* 的容器已被确保始终可以获得“P”RU 吞吐量。 该容器有 SLA 的保障。

> [!NOTE]
> 具有预配吞吐量的容器无法转换为共享的数据库容器。 反之，共享的数据库容器无法转换为具有专用吞吐量的容器。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新数据库或容器的吞吐量

创建 Azure Cosmos 容器或数据库后，可以更新预配的吞吐量。 可对数据库或容器配置的最大预配吞吐量没有限制。 [最小预配吞吐量](concepts-limits.md#storage-and-throughput)取决于以下因素： 

* 容器中当前存储的数据大小
* 曾经对容器预配的最大吞吐量
* 使用共享吞吐量的数据库中当前包含的 Azure Cosmos 容器数目。 

可以使用 SDK 以编程方式检索容器或数据库的最小吞吐量，或者在 Azure 门户中查看值。 使用 .NET SDK 时，可以通过 [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) 方法缩放预配的吞吐量值。 使用 Java SDK 时，可以通过 [RequestOptions.setOfferThroughput](sql-api-java-sdk-samples.md) 方法缩放预配的吞吐量值。 

使用 .NET SDK 时，可以通过 [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) 方法检索容器或数据库的最小吞吐量。 

可以随时缩放容器或数据库的预配吞吐量。 执行缩放操作以增加吞吐量时，由于系统任务的原因，可能需要较长的时间来预配所需的资源。 可以在 Azure 门户中检查缩放操作的状态，或使用 SDK 以编程方式检查。 使用 .NET SDK 时，可以使用 `DocumentClient.ReadOfferAsync` 方法获取缩放操作的状态。

## <a name="comparison-of-models"></a>模型比较
下表显示了对数据库与容器预配标准（手动）吞吐量时的差异比较。 

|**参数**  |**对数据库预配标准（手动）吞吐量**  |**对容器预配标准（手动）吞吐量**|**对数据库预配自动缩放吞吐量** | **对容器预配自动缩放吞吐量**|
|---------|---------|---------|---------|---------|
|入口点（最小 RU/秒） |400 RU/秒。 前四个容器后面的每个附加容器至少需要 100 RU/秒的吞吐量</li> |400| 在 400 - 4000 RU/秒之间自动缩放。 最多可以包含 25 个容器，每个容器没有最小的 RU/秒吞吐量</li> | 在 400 - 4000 RU/秒之间自动缩放。|
|每个容器的最小 RU/秒吞吐量|100|400|--|在 400 - 4000 RU/秒之间自动缩放|
|最大 RU 数|对于数据库无限。|对于容器无限。|对于数据库无限。|对于容器无限。
|分配或提供给特定容器的 RU 数|无保证。 为给定容器分配的 RU 数取决于多种属性。 属性可以是为共享吞吐量的容器选择的分区键、工作负荷的分布，以及容器的数量。 |对容器配置的所有 RU 专门保留给该容器使用。|无保证。 为给定容器分配的 RU 数取决于多种属性。 属性可以是为共享吞吐量的容器选择的分区键、工作负荷的分布，以及容器的数量。 |对容器配置的所有 RU 专门保留给该容器使用。|
|容器的最大存储|不受限制。|无限制|无限制|无限制|
|容器的每个逻辑分区的最大吞吐量|10K RU/秒|10K RU/秒|10K RU/秒|10K RU/秒|
|容器的每个逻辑分区的最大存储（数据 + 索引）|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>后续步骤

* 详细了解[逻辑分区](partition-data.md)。
* 了解如何[对 Azure Cosmos 容器预配标准（手动）吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配标准（手动）吞吐量](how-to-provision-database-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库或容器预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)。
