---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解分区在 Azure Cosmos DB 中的工作原理，分区和分区键的配置方式以及应用程序分区键的选择方法。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 98cd28e8b770ebfb7ab395fbe7fff16a078e3529
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826847"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍了逻辑分区与物理分区之间的关系。 还讨论了用于分区的最佳做法，并且深入介绍了横向缩放在 Azure Cosmos DB 中的工作方式。 并非一定要了解这些内部详细信息才能[选择分区键](partitioning-overview.md#choose-partitionkey)，但我们还是介绍了这些内容，以便清晰说明 Azure Cosmos DB 的工作方式。

## <a name="logical-partitions"></a>逻辑分区

逻辑分区由一组具有相同分区键的项构成。 例如，在包含食物营养相关数据的容器中，所有项都包含 `foodGroup` 属性。 可以使用 `foodGroup` 作为该容器的分区键。 具有特定 `foodGroup` 值（例如 `Beef Products`、`Baked Products` 和 `Sausages and Luncheon Meats`）的项组构成了独立的逻辑分区。 无需担心在删除基础数据时是否会删除逻辑分区。

逻辑分区也定义数据库事务的范围。 可以使用[支持快照隔离的事务](database-transactions-optimistic-concurrency.md)来更新逻辑分区中的项。 当向容器中添加新项时，系统将透明地创建新的逻辑分区。

容器中逻辑分区的数量是没有限制的。 每个逻辑分区最多可以存储 20GB 数据。 如果分区键的可能值范围广泛，那么这些分区键是良好的分区键选择。 例如，在所有项都包含属性的容器中 `foodGroup` ，逻辑分区内的数据 `Beef Products` 最多可增长到 20 GB。 [选择具有多种可能值的分区键](partitioning-overview.md#choose-partitionkey)会确保容器能够缩放。

## <a name="physical-partitions"></a>物理分区

Azure Cosmos 容器是通过在物理分区间分配数据和吞吐量来缩放的。 在内部，一个或多个逻辑分区映射到一个物理分区。 大多数小型 Cosmos 容器都会有许多逻辑分区，但只需要一个物理分区。 与逻辑分区不同，物理分区是系统的内部实现，并且全部由 Azure Cosmos DB 管理。

Cosmos 容器中的物理分区数依赖于以下各项：

- 预配的吞吐量（每个单独的物理分区最多可以提供每秒 10,000 个请求单位的吞吐量）
- 总数据存储量（每个单独的物理分区最多可以存储 50GB）

容器中物理分区的总数是没有限制的。 随着预配的吞吐量或数据量规模的增长，Azure Cosmos DB 将会通过拆分现有物理分区来自动创建新物理分区。 物理分区拆分不影响应用程序可用性。 物理分区拆分后，单个逻辑分区内的所有数据仍将存储在同一个物理分区中。 物理分区拆分只是创建逻辑分区到物理分区的新映射。

为容器预配的吞吐量在物理分区之间均匀划分。 不均匀分配请求的分区键设计可能会导致过多的请求被定向到成为 "热" 的一小部分分区。 热分区导致使用预配的吞吐量效率低下，这可能会导致速率限制和更高的成本。

在 Azure 门户的“指标”边栏选项卡的“存储”部分中，可以看到容器的物理分区 ：

:::image type="content" source="./media/partition-data/view-partitions-zoomed-out.png" alt-text="查看物理分区数" lightbox="./media/partition-data/view-partitions-zoomed-in.png" ::: 

在此示例容器中，已选择 `/foodGroup` 作为分区键，三个矩形中的每一个都表示一个物理分区。 在此图中，分区键范围与物理分区相同。 选定的物理分区包含三个逻辑分区：`Beef Products`、`Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies`。

如果预配每秒 18,000 个请求单位 (RU/s) 的吞吐量，则三个物理分区中的每一个都可以利用总预配吞吐量的 1/3。 在选定的物理分区中，逻辑分区键 `Beef Products`、`Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies` 可以共同利用为物理分区预配的每秒 6,000 个 RU。 由于预配的吞吐量是在容器的物理分区间平均分配的，因此，请务必通过[选择正确的逻辑分区键](partitioning-overview.md#choose-partitionkey)来选择平均分配吞吐量消耗的分区键。 如果选择在逻辑分区间平均分配吞吐量消耗的分区键，将会确保物理分区间的吞吐量消耗保持均衡。

## <a name="replica-sets"></a>副本集

每个物理分区都包含一组副本（也称为[副本集](global-dist-under-the-hood.md)）。 每个副本集托管 Azure Cosmos 数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 Azure Cosmos DB 自动管理副本集。

大多数小型 Cosmos 容器只需要一个物理分区，但仍将至少具有 4 个副本。

下图显示了逻辑分区如何映射到全局分配的物理分区：

:::image type="content" source="./media/partition-data/logical-partitions.png" alt-text="查看物理分区数" border="false":::

## <a name="next-steps"></a>后续步骤

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
