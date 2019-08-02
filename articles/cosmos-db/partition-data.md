---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解分区在 Azure Cosmos DB 中的工作原理，分区和分区键的配置方式以及应用程序分区键的选择方法。
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 67ad37491f71ac82ff52331d19ea92a646c80a52
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716984"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍 Azure Cosmos DB 中的物理分区和逻辑分区。 此外，介绍有关缩放和分区的最佳做法。 

## <a name="logical-partitions"></a>逻辑分区

逻辑分区由一组具有相同分区键的项构成。 例如，在所有项都包含一个 `City` 属性的容器中，可以使用 `City` 作为该容器的分区键。 具有特定 `City` 值（例如 `London`、`Paris` 和 `NYC`）的项组构成了独立的逻辑分区。 删除基础数据时，无需担心是否会删除分区。

在 Azure Cosmos DB 中，容器是基本的缩放单元。 添加到容器的数据以及针对容器预配的吞吐量将自动在一组逻辑分区之间（水平）分区。 数据和吞吐量是根据为 Azure Cosmos 容器指定的分区键分区的。 有关详细信息，请参阅[创建 Azure Cosmos 容器](how-to-create-container.md)。

逻辑分区也定义数据库事务的范围。 可以使用[支持快照隔离的事务](database-transactions-optimistic-concurrency.md)来更新逻辑分区中的项。 当向容器中添加新项时，系统将透明地创建新的逻辑分区。

## <a name="physical-partitions"></a>物理分区

通过将数据和吞吐量分配到大量逻辑分区上来缩放 Azure Cosmos 容器。 在内部，一个或多个逻辑分区将映射到由一组副本（也称为[副本集](global-dist-under-the-hood.md)）构成的物理分区。 每个副本集托管 Azure Cosmos DB 数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 物理分区支持最大数量的存储和请求单位 (RU)。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 

下图显示了逻辑分区如何映射到全局分配的物理分区：

![演示 Azure Cosmos DB 分区的插图](./media/partition-data/logical-partitions.png)

为容器预配的吞吐量在物理分区之间均匀划分。 不会均匀分配吞吐量请求的分区键设计可能会产生“热”分区。 热分区可能导致速率限制、预配吞吐量的低效使用，以及更高的成本。

与逻辑分区不同，物理分区是系统的内部实现。 无法控制物理分区的大小、位置或计数，也无法控制逻辑分区与物理分区之间的映射。 但是，可以通过[选择适当的逻辑分区键](partitioning-overview.md#choose-partitionkey)来控制逻辑分区的数目以及数据、工作负荷和吞吐量的分配。

## <a name="next-steps"></a>后续步骤

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
