---
title: Azure Cosmos DB 中的分区
description: 了解 Azure Cosmos DB 中的分区、选择分区键时的最佳做法，以及如何管理逻辑分区
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481827"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区

Azure Cosmos DB 使用分区缩放数据库中的单个容器，以满足应用程序的性能需求。 在分区中，可将容器中的项分割成不同的子集（称作“逻辑分区”）。 逻辑分区是根据与容器中每个项关联的分区键值形成的。 逻辑分区中的所有项具有相同的分区键值。

例如，某个容器保存项。 每个项具有唯一的 `UserID` 属性值。 如果 `UserID` 充当容器中的项的分区键，并且有 1,000 个唯一的 `UserID` 值，则会为容器创建 1,000 个逻辑分区。

除了用于确定项的逻辑分区的分区键以外，容器中的每个项还有一个项 ID（在逻辑分区中保持唯一）。 将分区键与项 ID 相结合可创建项的索引，用来唯一地标识该项。 

[分区键的选择](partitioning-overview.md#choose-partitionkey)非常重要，这会影响应用程序的性能。

## <a name="managing-logical-partitions"></a>管理逻辑分区

Azure Cosmos DB 以透明方式自动管理逻辑分区在物理分区上的位置，以有效满足容器的可伸缩性和性能需求。 随着应用程序的吞吐量和存储要求的提高，Azure Cosmos DB 会移动逻辑分区，以便自动在更多的物理分区之间分散负载。 可以详细了解[物理分区](partition-data.md#physical-partitions)。

Azure Cosmos DB 使用基于哈希的分区在物理分区之间分散逻辑分区。 Azure Cosmos DB 对项的分区键值进行哈希处理。 哈希处理结果确定了物理分区。 然后，Azure Cosmos DB 在物理分区之间均匀分配分区键哈希的键空间。

只允许针对单个逻辑分区中的项执行事务（在存储过程或触发器中）。

可以详细了解 [Azure Cosmos DB 如何管理分区](partition-data.md)。 （生成或运行应用程序不需要了解内部详细信息，添加到这里只是为了方便那些好奇的读者。）

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>选择分区键

 分区键具有两个组成部分：分区键路径和分区键值。 假设有一个项{ "userId" :"Andrew", "worksFor":"Microsoft" }，如果选择 "userId" 作为分区键，以下是分区键的两个部分：

* 分区键路径（例如 "/userId"）。 分区键路径接受字母数字和下划线 (_) 字符。 还可以通过标准路径表示法 (/) 来使用嵌套的对象。

* 分区键值（例如 "Andrew"）。 分区键值可以是字符串或数值类型。

若要了解有关吞吐量、存储和分区键长度的限制，请参阅 [Azure Cosmos DB 服务配额](concepts-limits.md)一文。

选择分区键是 Azure Cosmos DB 中的一个简单但重要的设计选择。 选择分区键后，将无法就地进行更改。 如果需要更改分区键，应将数据移动到带有所需新分区键的新容器。

对于**所有**容器，分区键应当：

* 是一个属性，并且其值不会更改。 如果某个属性是分区键，那么你不能更新该属性的值。
* 具有较高的基数。 换言之，该属性应具有范围广泛的可能值。
* 将请求单位 (RU) 消耗和数据存储均匀分配到所有逻辑分区上。 这可确保跨物理分区均匀分配 RU 消耗和存储。

如果在 Azure Cosmos DB 中需要[多项 ACID 事务](database-transactions-optimistic-concurrency.md#multi-item-transactions)，则需要使用[存储过程或触发器](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)。 所有基于 JavaScript 的存储过程和触发器的作用域都是单个逻辑分区。

## <a name="partition-keys-for-read-heavy-containers"></a>读取密集型容器的分区键

对于大多数容器，上述条件就是在选择分区键时需要考虑的全部。 但对于较大的读取密集型容器，可能需要选择在查询中经常作为筛选器出现的分区键。 通过在筛选器谓词中包含分区键，查询可以[高效地专门路由到相关的物理分区](how-to-query-container.md#in-partition-query)。

如果大多数工作负荷请求是查询，并且大多数查询在同一属性上都有一个等式筛选器，则此属性可以成为不错的分区键选择。 例如，如果经常运行在 `UserID` 上筛选的查询，则选择 `UserID` 作为分区键将减少[跨分区查询](how-to-query-container.md#avoiding-cross-partition-queries)的数目。

但是，如果容器很小，那么你的物理分区数量可能并非很多，无需你担心跨分区查询的性能影响。 Azure Cosmos DB 中的大多数小容器只需要一个或两个物理分区。

如果容器可能会增长到许多个物理分区，则应确保选择一个可以最大程度地减少跨分区查询的分区键。 如果满足以下任一条件，则容器将需要许多个物理分区：

* 容器已预配了 30000 以上的 RU
* 容器将存储超过 100 GB 的数据

## <a name="using-item-id-as-the-partition-key"></a>使用项 ID 作为分区键

如果容器具有一个属性，并且该属性的可能值范围十分广泛，则该属性很可能是非常好的分区键选择。 此类属性的一个可能示例是项 ID。 对于较小的读取密集型容器或任意大小的写入密集型容器，项 ID 自然是很好的分区键选择。

系统属性“项 ID”保证存在于 Cosmos 容器中的每一项内。 可能会有其他用于表示项逻辑 ID 的属性。 在许多情况下，出于与项 ID 相同的原因，这些属性也会是非常好的分区键选择。

项 ID 是很好的分区键选择，原因如下：

* 其可能值范围十分广泛（每个项一个唯一的项 ID）。
* 由于每个项都有一个唯一的项 ID，因此，项 ID 在均衡 RU 消耗和数据存储方面有显著作用。
* 你可以轻松执行高效的点读取，因为如果你知道项的项 ID，你将始终知道项的分区键。

选择项 ID 作为分区键时要考虑的一些事项包括：

* 如果项 ID 为分区键，则它会成为整个容器中的唯一标识符。 不同的项不能具有相同的项 ID。
* 如果一个读取密集型容器有大量[物理分区](partition-data.md#physical-partitions)，则当查询具有一个包含项 ID 的等式筛选器时，查询将更高效。
* 不能跨多个逻辑分区运行存储过程或触发器。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Cosmos DB 中的分区和水平缩放](partition-data.md)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
