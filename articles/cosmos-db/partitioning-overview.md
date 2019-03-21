---
title: Azure Cosmos DB 中的分区
description: Azure Cosmos DB 中的分区概述。
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: a8db510bea57fa3d6ee873571e586bcef7508b26
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961629"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区

Azure Cosmos DB 使用分区缩放数据库中的单个容器，以满足应用程序的性能需求。 在分区中，可将容器中的项分割成不同的子集（称作“逻辑分区”）。 逻辑分区是根据与容器中每个项关联的分区键值形成的。 逻辑分区中的所有项具有相同的分区键值。

例如，某个容器保存文档。 每个文档具有唯一的 `UserID` 属性值。 如果 `UserID` 充当容器中的项的分区键，并且有 1,000 个唯一的 `UserID` 值，则会为容器创建 1,000 个逻辑分区。

确定项的逻辑分区的分区键，除了在容器中的每个项还具有*项目中的 ID* （逻辑分区中唯一）。 将分区键与项 ID 相结合可以创建项的索引用于唯一标识该项。

[选择分区键](partitioning-overview.md#choose-partitionkey)是一项重要决策将影响应用程序的性能。

## <a name="managing-logical-partitions"></a>管理逻辑分区

Azure Cosmos DB 以透明方式自动管理逻辑分区在物理分区（服务器基础结构）上的位置，以有效满足容器的可伸缩性和性能需求。 随着应用程序的吞吐量和存储要求的提高，Azure Cosmos DB 可移动逻辑分区，以自动在更多的服务器之间分散负载。 

Azure Cosmos DB 使用基于哈希的分区在物理分区之间分散逻辑分区。 Azure Cosmos DB 对项的分区键值进行哈希处理。 哈希处理结果确定了物理分区。 然后，Azure Cosmos DB 在物理分区之间均匀分配分区键哈希的键空间。

与访问多个分区的查询相比，访问单个分区中的数据的查询更具成本效益。 只允许针对单个逻辑分区中的项执行事务（在存储过程或触发器中）。

若要详细了解 Azure Cosmos DB 如何管理分区，请参阅[逻辑分区](partition-data.md)。 （生成或运行应用程序不需要了解这些详细信息。）

## <a id="choose-partitionkey"></a>选择分区键

选择分区键时，请注意以下详细信息：

* 单个逻辑分区的存储空间上限为 10 GB。  

* 已分区容器的最小吞吐量为每秒 400 个请求单位 (RU/s)。 对同一分区键的请求不能超过分配给某个分区的吞吐量。 如果请求超过分配的吞吐量，则请求将受到速率限制。 请务必选择不会导致应用程序中产生“热点”的分区键。

* 选择可以持续在所有分区之间均匀分散工作负荷的分区键。 所选的分区键应该可以根据在多个分区之间分配项的目标，平衡有效分区查询和事务的需求，以实现可伸缩性。

* 选择具有宽广范围的值，以及在逻辑分区之间均匀分散的访问模式的分区键。 这有助于在逻辑分区集之间分散容器中的数据和活动，以便可以在逻辑分区之间分配数据存储和吞吐量的资源。

* 分区键的候选项可能包括经常在查询中显示为筛选器的属性。 通过在筛选器谓词中包含分区键，可以有效地路由查询。

## <a name="next-steps"></a>后续步骤

* 了解[分区](partition-data.md)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
