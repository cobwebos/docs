---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解分区在 Azure Cosmos DB 中的工作原理，如何配置分区和分区键以及如何为应用程序选取适当的分区键。
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: andrl
ms.openlocfilehash: 5dd1926496351f5bbfe8e5b3e4d1e0b68e82d272
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283387"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍 Azure Cosmos DB 中的物理分区和逻辑分区，以及有关缩放和分区的最佳做法。 

## <a name="logical-partitions"></a>逻辑分区

逻辑分区由一组具有相同分区键的项构成。 例如，假设某个容器中的所有项都包含一个 `City` 属性，则你可以使用 `City` 作为该容器的分区键。 具有特定 `City` 值（例如“London”、“Paris”、“NYC”等）的项组将构成一个非重复性逻辑分区。

在 Azure Cosmos DB 中，容器是基本的缩放单元。 添加到容器的数据以及针对容器预配的吞吐量将自动在一组逻辑分区之间（水平）分区。 它们是根据为 Cosmos 容器指定的分区键分区的。 有关详细信息，请参阅[如何为 Cosmos 容器指定分区键](how-to-create-container.md)一文。

逻辑分区定义数据库事务的范围。 可以使用支持快照隔离的事务来更新逻辑分区中的项。

将新项添加到容器或者提高针对容器预配的吞吐量时，系统将以透明方式创建新的逻辑分区。

## <a name="physical-partitions"></a>物理分区

通过跨大量逻辑分区分配数据和吞吐量来缩放 Cosmos 容器。 在内部，一个或多个逻辑分区将映射到由一组副本（也称为副本集）构成的**资源分区**。 每个副本集托管 Cosmos 数据库引擎的一个实例。 副本集使资源分区中存储的数据具有持久性、高可用性和一致性。 资源分区支持最大数量固定的存储和 RU。 构成资源分区的每个副本继承存储配额。 资源分区的所有副本共同支持分配给资源分区的吞吐量。 下图显示了逻辑分区如何映射到全局分配的物理分区：

![Azure Cosmos DB 分区](./media/partition-data/logical-partitions.png)

为容器预配的吞吐量在物理分区之间均匀划分。 因此，不会均匀分配吞吐量请求的分区键设计可能会产生“热”分区。 热分区可能导致速率限制以及预配吞吐量的低效使用。

与逻辑分区不同，物理分区是系统的内部实现。 无法控制其大小、位置、计数，或者逻辑分区与物理分区之间的映射。 但是，可以通过选择适当的分区键来控制逻辑分区的数目以及数据和吞吐量的分配。

## <a name="next-steps"></a>后续步骤

本文概述了数据分区，以及有关 Azure Cosmos DB 中的缩放和分区的最佳做法。 

* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)
* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)
* 了解[如何对 Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)
* 了解[如何对 Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)
