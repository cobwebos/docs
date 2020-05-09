---
title: Azure Cosmos DB 中的分区
description: 了解 Azure Cosmos DB 中的分区、选择分区键时的最佳做法，以及如何管理逻辑分区
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: a9368e67abf3c45981cf1f85fe46a2a2799a6877
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864328"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区

Azure Cosmos DB 使用分区缩放数据库中的单个容器，以满足应用程序的性能需求。 在分区中，可将容器中的项分割成不同的子集（称作“逻辑分区”）。  逻辑分区是根据与容器中每个项关联的分区键值形成的。  逻辑分区中的所有项具有相同的分区键值。

例如，某个容器保存项。 每个项具有唯一的 `UserID` 属性值。 如果 `UserID` 充当容器中的项的分区键，并且有 1,000 个唯一的 `UserID` 值，则会为容器创建 1,000 个逻辑分区。

除了用于确定项的逻辑分区的分区键以外，容器中的每个项还有一个项 ID（在逻辑分区中保持唯一）。  合并分区键和*项 ID*将创建项的索引，该*索引*将唯一标识该项。

[分区键的选择](partitioning-overview.md#choose-partitionkey)非常重要，这会影响应用程序的性能。

## <a name="managing-logical-partitions"></a>管理逻辑分区

Azure Cosmos DB 以透明方式自动管理逻辑分区在物理分区上的位置，以有效满足容器的可伸缩性和性能需求。 随着应用程序的吞吐量和存储要求的增加，Azure Cosmos DB 移动逻辑分区，以自动将负载分散到更多的物理分区上。 你可以详细了解[物理分区](partition-data.md#physical-partitions)。

Azure Cosmos DB 使用基于哈希的分区在物理分区之间分散逻辑分区。 Azure Cosmos DB 对项的分区键值进行哈希处理。 哈希处理结果确定了物理分区。 然后，Azure Cosmos DB 在物理分区之间均匀分配分区键哈希的键空间。

只允许针对单个逻辑分区中的项执行事务（在存储过程或触发器中）。

你可以详细了解[Azure Cosmos DB 管理分区的方式](partition-data.md)。 （生成或运行应用程序不需要了解内部详细信息，添加到这里只是为了方便那些好奇的读者。）

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>选择分区键

选择分区键是 Azure Cosmos DB 中的一个简单但重要的设计选择。 选择分区键后，就不能就地更改。 如果需要更改分区键，应使用新的所需分区键将数据移动到新容器。

对于**所有**容器，分区键应：

* 是具有不更改的值的属性。 如果某个属性是您的分区键，则不能更新该属性的值。
* 基数较高。 换言之，属性应具有范围广泛的可能值。
* 跨所有逻辑分区均匀分配请求单位（RU）消耗和数据存储。 这可确保甚至跨物理分区的 RU 消耗和存储分配。

如果在 Azure Cosmos DB 中需要[多项 ACID 事务](database-transactions-optimistic-concurrency.md#multi-item-transactions)，则需要使用[存储过程或触发器](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)。 所有基于 JavaScript 的存储过程和触发器的作用域都是单个逻辑分区。

## <a name="partition-keys-for-read-heavy-containers"></a>用于读取繁重容器的分区键

对于大多数容器，在选择分区键时，需要考虑上述标准。 但对于大型的读取密集型容器，可能需要选择在查询中经常出现为筛选器的分区键。 查询可以通过在筛选器谓词中包含分区键来[有效地路由到相关的物理分区](how-to-query-container.md#in-partition-query)。

如果你的大部分工作负荷请求是查询，而大多数查询在同一属性上都有相等性筛选器，则此属性可以是一个很好的分区键选择。 例如，如果经常运行筛选器的查询`UserID`，则选择`UserID`作为分区键将减少[跨分区查询](how-to-query-container.md#avoiding-cross-partition-queries)的数量。

但是，如果容器很小，则可能没有足够的物理分区需要担心跨分区查询的性能影响。 Azure Cosmos DB 中的大多数小容器只需要一个或两个物理分区。

如果容器可能会增长到多个物理分区，则应确保选择最大程度地减少跨分区查询的分区键。 如果满足以下任一条件，则容器将需要多个物理分区：

* 容器已预配了 30000 RU
* 容器将存储超过 100 GB 的数据

## <a name="using-item-id-as-the-partition-key"></a>使用项 ID 作为分区键

如果容器具有具有各种可能值的属性，则很可能是很好的分区键选择。 此类属性的一个可能示例是*项 ID*。 对于具有任意大小的小型读繁重容器或大容量写入容器，*该项 ID*对于分区键是很好的选择。

系统属性*项 ID*保证存在于 Cosmos 容器中的每一项。 可能有其他属性，这些属性表示项的逻辑 ID。 在许多情况下，这些是很好的分区键选择，这与*项 ID*的原因相同。

*项 ID*是一个很好的分区键选择，原因如下：

* 有多种可能的值（每个项一个唯一*项 ID* ）。
* 由于每个项目都有唯一的*项 id* ，因此，*项 id*会在平均平衡 RU 消耗和数据存储时执行出色的工作。
* 你可以轻松执行有效的点读取，因为如果你知道项的*项 ID*，将始终知道项的分区键。

选择*项 ID*作为分区键时需要注意的一些事项包括：

* 如果*项 ID*是分区键，则它会成为整个容器中的唯一标识符。 你将无法具有具有重复*项 ID*的项。
* 如果有大量[物理分区](partition-data.md#physical-partitions)的读取密集型容器，则如果查询具有*项 ID*的相等筛选器，则查询将更有效。
* 不能跨多个逻辑分区运行存储过程或触发器。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Cosmos DB 中的分区和水平缩放](partition-data.md)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
