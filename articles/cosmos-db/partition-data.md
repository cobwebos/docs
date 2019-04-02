---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解分区在 Azure Cosmos DB 中的工作原理，分区和分区键的配置方式以及应用程序分区键的选择方法。
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.openlocfilehash: cf454d6504f0433d7ac7ca883982ae317b14f814
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762527"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍 Azure Cosmos DB 中的物理分区和逻辑分区。 此外，介绍有关缩放和分区的最佳做法。 

## <a name="logical-partitions"></a>逻辑分区

逻辑分区由一组具有相同分区键的项构成。 例如，在所有项都包含一个 `City` 属性的容器中，可以使用 `City` 作为该容器的分区键。 具有特定值的项的组`City`，如`London`， `Paris`，和`NYC`，形成不同的逻辑分区。 删除基础数据时，无需担心是否会删除分区。

在 Azure Cosmos DB 中，容器是基本的缩放单元。 添加到容器的数据以及针对容器预配的吞吐量将自动在一组逻辑分区之间（水平）分区。 数据和吞吐量是根据 Azure Cosmos 容器的分区键指定分区的。 有关详细信息，请参阅[创建 Azure Cosmos 容器](how-to-create-container.md)。

逻辑分区还定义了数据库事务的作用域。 可以通过使用更新的逻辑分区内的项[使用快照隔离事务](database-transactions-optimistic-concurrency.md)。 当新项添加到容器中时，系统以透明方式创建新的逻辑分区。

## <a name="physical-partitions"></a>物理分区

Azure Cosmos 容器进行缩放跨大量的逻辑分区分布数据和吞吐量。 在内部，将一个或多个逻辑分区映射到物理分区包含一组副本，也称为[*副本集*](global-dist-under-the-hood.md)。 每个副本集托管 Azure Cosmos DB 数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 物理分区支持最大数量的存储和请求单位 (RU)。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 

下图显示了逻辑分区如何映射到全局分配的物理分区：

![演示 Azure Cosmos DB 分区的插图](./media/partition-data/logical-partitions.png)

为容器预配的吞吐量在物理分区之间均匀划分。 不会均匀分配吞吐量请求的分区键设计可能会产生“热”分区。 在速率限制和预配的吞吐量和更高的成本的使用效率低下，可能会导致热分区。

与逻辑分区不同，物理分区是系统的内部实现。 无法控制物理分区的大小、位置或计数，也无法控制逻辑分区与物理分区之间的映射。 但是，您可以控制逻辑分区和分布数据、 工作负荷和吞吐量的数目[选择正确逻辑分区键](partitioning-overview.md#choose-partitionkey)。

## <a name="next-steps"></a>后续步骤

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
