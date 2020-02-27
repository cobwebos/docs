---
title: Azure Cosmos DB 中的分区
description: 了解 Azure Cosmos DB 中的分区、选择分区键时的最佳实践，以及如何管理逻辑分区
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621871"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区

Azure Cosmos DB 使用分区来缩放数据库中的各个容器，以满足应用程序的性能需求。 在分区中，容器中的项被划分为多个称为*逻辑分区*的不同子集。 逻辑分区基于与容器中每一项关联的*分区键*的值形成。 逻辑分区中的所有项都具有相同的分区键值。

例如，容器保存项。 每个项都具有 `UserID` 属性的唯一值。 如果 `UserID` 用作容器中项的分区键，并且存在1000个唯一 `UserID` 值，则为容器创建1000个逻辑分区。

除了确定项的逻辑分区的分区键外，容器中的每一项都有一个*项 ID* （在逻辑分区中是唯一的）。 合并分区键和项 ID 将创建项的索引，该*索引*将唯一标识该项。

[选择分区键](partitioning-overview.md#choose-partitionkey)是将影响应用程序性能的一项重要决定。

## <a name="managing-logical-partitions"></a>管理逻辑分区

透明地 Azure Cosmos DB 自动管理物理分区上的逻辑分区位置，以有效地满足容器的可伸缩性和性能需求。 随着应用程序的吞吐量和存储要求的增加，Azure Cosmos DB 移动逻辑分区，以自动将负载分散到更多的服务器上。 

Azure Cosmos DB 使用基于哈希的分区将逻辑分区分散到物理分区。 Azure Cosmos DB 对项的分区键值进行哈希处理。 哈希结果确定物理分区。 然后，Azure Cosmos DB 跨物理分区平均分配分区键哈希的键空间。

访问单个逻辑分区中的数据的查询比访问多个分区的查询更经济高效。 仅允许对单个逻辑分区中的项执行事务（在存储过程或触发器中）。

若要详细了解 Azure Cosmos DB 如何管理分区，请参阅[逻辑分区](partition-data.md)。 （无需了解内部详细信息即可构建或运行你的应用程序，但在此处添加了用于好奇的读者。）

## <a id="choose-partitionkey"></a>选择分区键

下面是有关选择分区键的好指导：

* 单个逻辑分区的存储上限为 20 GB。  

* Azure Cosmos 容器的最小吞吐量为400个请求单位/秒（RU/s）。 如果在数据库上设置了吞吐量，则每个容器的最小每个 ru 为100个请求单位/秒（RU/s）。 对相同分区键的请求不能超过分配给分区的吞吐量。 如果请求超出分配的吞吐量，请求会受到速率限制。 请务必选择不会导致应用程序中产生“热点”的分区键。

* 选择具有宽广范围的值，以及在逻辑分区之间均匀分散的访问模式的分区键。 这有助于将容器中的数据和活动分散到一组逻辑分区中，以便可以跨逻辑分区分布数据存储和吞吐量的资源。

* 选择一个分区键，用于在所有分区之间均匀分布工作负荷，并在一段时间内均匀分布。 所选的分区键应根据跨多个分区分配项以实现可伸缩性的目的，平衡有效分区查询和事务的需求。

* 分区键的候选项可能包含经常作为查询中的筛选器显示的属性。 通过在筛选器谓词中包含分区键，可以有效地路由查询。

## <a name="next-steps"></a>后续步骤

* 了解[Azure Cosmos DB 中的分区和水平缩放](partition-data.md)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
