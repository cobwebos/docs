---
title: Azure Cosmos DB 中的分区
description: Azure Cosmos DB 中的分区概述
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d9bd1bec6d1df6fab634c5d929cb778abbd3d13d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478400"
---
# <a name="partitioning-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的分区 - 概述

分区是 Cosmos DB 根据应用程序的性能需求，缩放数据库中的单个容器所用的方法。 使用分区可将容器中的项分割成不同的子集（称作“逻辑分区”）。 逻辑分区是基于与每个项关联的分区键属性值创建的。

逻辑分区是容器中项的不同子集。 逻辑分区中的项由分区键值标识，该值由逻辑分区中的所有项共享。  例如，假设某个容器保存文档，而每个文档具有 `UserID` 属性。  如果 `UserID` 充当容器中的项的分区键，并且有 1000 个唯一的 `UserID` 值，则会为容器创建 1000 个逻辑分区。

容器中的每个项有一个确定该项的**逻辑分区**的**分区键**，每个项还有一个**项 ID**（在逻辑分区中保持唯一）。  项的**索引**唯一标识该项，它是通过合并分区键和项 ID 构成的。

分区键的选择非常重要，这会影响应用程序的性能。  请参阅[选择分区键](partitioning-overview.md#choose-partitionkey)一文中的详细指导。

## <a name="logical-partition-management"></a>逻辑分区管理

Cosmos DB 以透明方式自动管理逻辑分区在物理分区（服务器基础结构）上的位置，以有效满足容器的可伸缩性和性能需求。 随着应用程序的吞吐量和存储要求的提高，Cosmos DB 可移动逻辑分区，以自动在更多的服务器之间分散负载。 若要详细了解 Cosmos DB 如何管理分区，请参阅[逻辑分区](partition-data.md)一文。 生成或运行应用程序不需要了解这些详细信息。

Cosmos DB 使用基于哈希的分区在物理分区之间分散逻辑分区。  项的分区键值由 Cosmos DB 进行哈希处理，哈希结果确定了物理分区。 Cosmos DB 在“N”个物理分区之间均匀分配分区键哈希的键空间。

与访问多个分区的查询相比，访问单个分区中的数据的查询更具成本效益。 只允许针对单个逻辑分区中的项执行事务（在存储过程或触发器中）。  

## <a id="choose-partitionkey"></a>选择分区键

选择分区键时，请注意以下详细信息：

* 允许为单个逻辑分区分配的存储空间上限为 10 GB。  

* 为分区的容器最少配置 400 RU/秒的吞吐量。 对同一分区键的请求不能超过分配给某个分区的吞吐量。 如果超过分配的吞吐量，则请求将受到速率限制。 请务必选择不会导致应用程序中产生“热点”的分区键。

* 选择可以持续在所有分区之间均匀分散工作负荷的分区键。  所选的分区键应该可以根据在多个分区之间分配项的目标，平衡有效分区查询和/或事务的需求，以实现可伸缩性。

* 选择具有宽广范围的值，以及在逻辑分区之间均匀分散的访问模式的分区键。 基本理念是在逻辑分区集之间分散容器中的数据和活动，以便可以在逻辑分区之间分配数据存储和吞吐量的资源。

* 分区键的候选项可能包括，经常在查询中显示为筛选器的属性。 通过在筛选器谓词中包含分区键，可以有效地路由查询。

## <a name="next-steps"></a>后续步骤

* 了解[分区](partition-data.md)
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)
