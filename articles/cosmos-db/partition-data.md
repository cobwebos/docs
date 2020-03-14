---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解分区在 Azure Cosmos DB 中的工作原理，如何配置分区和分区键，以及如何为应用程序选择适当的分区键。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: cbd171e10cc1a8b27de98d9d4d779f345ac5a3ed
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246612"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍 Azure Cosmos DB 中的物理和逻辑分区。 还讨论了缩放和分区的最佳做法。 

## <a name="logical-partitions"></a>逻辑分区

逻辑分区包含一组具有相同分区键的项。 例如，在所有项都包含 `City` 属性的容器中，可以将 `City` 用作容器的分区键。 具有特定值 `City`的项的组，如 `London`、`Paris`和 `NYC`，构成不同的逻辑分区。 删除基础数据时，不必担心删除分区。

在 Azure Cosmos DB 中，容器是基本的缩放单元。 添加到容器中的数据以及在容器上预配的吞吐量会自动（水平）分区在一组逻辑分区中。 数据和吞吐量根据你为 Azure Cosmos 容器指定的分区键进行分区。 有关详细信息，请参阅[创建 Azure Cosmos 容器](how-to-create-container.md)。

逻辑分区还定义数据库事务的作用域。 您可以通过使用[带有快照隔离的事务](database-transactions-optimistic-concurrency.md)来更新逻辑分区内的项。 将新项添加到容器时，系统将以透明方式创建新的逻辑分区。

## <a name="physical-partitions"></a>物理分区

通过跨大量逻辑分区分配数据和吞吐量，可对 Azure Cosmos 容器进行扩展。 在内部，一个或多个逻辑分区映射到由一组副本（也称为[*副本集*](global-dist-under-the-hood.md)）组成的物理分区。 每个副本集托管 Azure Cosmos 数据库引擎的一个实例。 副本集使存储在物理分区中的数据持久、高度可用且一致。 物理分区支持最大存储量和请求单位（ru）。 构成物理分区的每个副本都继承分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 

下图显示了逻辑分区如何映射到全局分配的物理分区：

![演示 Azure Cosmos DB 分区的图像](./media/partition-data/logical-partitions.png)

为容器预配的吞吐量在物理分区之间平均划分。 不均匀分配吞吐量请求的分区键设计可能会创建 "热" 分区。 热分区可能会导致速率限制和预配的吞吐量的利用率低下，同时降低成本。

与逻辑分区不同，物理分区是系统的内部实现。 不能控制物理分区的大小、位置或计数，也不能控制逻辑分区与物理分区之间的映射。 但是，可以通过[选择适当的逻辑分区键](partitioning-overview.md#choose-partitionkey)来控制逻辑分区数以及数据、工作负荷和吞吐量的分布。

## <a name="next-steps"></a>后续步骤

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
