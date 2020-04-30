---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解分区在 Azure Cosmos DB 中的工作原理，分区和分区键的配置方式以及应用程序分区键的选择方法。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 19e4c61ba930bb9b127e2401174bcea3fd240dce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234194"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍了逻辑分区与物理分区之间的关系。 它还讨论分区的最佳实践，并详细介绍横向缩放在 Azure Cosmos DB 中的工作方式。 不需要了解这些内部详细信息来[选择分区键](partitioning-overview.md#choose-partitionkey)，但我们已介绍了这两个细节，以便清晰地了解 Azure Cosmos DB 的工作方式。

## <a name="logical-partitions"></a>逻辑分区

逻辑分区由一组具有相同分区键的项构成。 例如，在包含食物营养相关数据的容器中，所有项目都包含一个`foodGroup`属性。 可以将`foodGroup`用作容器的分区键。 具有特定值的项组，例如`foodGroup` `Beef Products`、`Baked Products`和`Sausages and Luncheon Meats`，它们构成不同的逻辑分区。 删除基础数据时，不必担心删除逻辑分区。

逻辑分区也定义数据库事务的范围。 您可以通过使用[带有快照隔离的事务](database-transactions-optimistic-concurrency.md)来更新逻辑分区内的项。 当向容器中添加新项时，系统将透明地创建新的逻辑分区。

对于容器中的逻辑分区数没有限制。 每个逻辑分区最多可以存储20个数据。 合理的分区键选择有多种可能的值。 例如，在所有项都包含`foodGroup`属性的容器中， `Beef Products`逻辑分区内的数据可能会增长到20。 选择具有各种可能值的[分区键](partitioning-overview.md#choose-partitionkey)可确保容器能够缩放。

## <a name="physical-partitions"></a>物理分区

通过在物理分区之间分配数据和吞吐量来扩展 Azure Cosmos 容器。 在内部，一个或多个逻辑分区映射到单个物理分区。 大多数小型 Cosmos 容器有许多逻辑分区，但只需要单个物理分区。 与逻辑分区不同，物理分区是系统的内部实现，它们由 Azure Cosmos DB 完全管理。

Cosmos 容器中的物理分区数取决于以下各项：

- 预配的吞吐量量（每个单独的物理分区最多可以提供每秒10000个请求单位的吞吐量）
- 总数据存储（每个单个物理分区最多可以存储50GB）

对于容器中的物理分区总数没有限制。 当预配的吞吐量或数据大小增长时，Azure Cosmos DB 会通过拆分现有物理分区来自动创建新的物理分区。 物理分区拆分不会影响应用程序的可用性。 物理分区拆分后，单个逻辑分区内的所有数据仍将存储在同一个物理分区中。 物理分区分割只是创建逻辑分区到物理分区的新映射。

为容器预配的吞吐量在物理分区之间均匀划分。 不会均匀分配吞吐量请求的分区键设计可能会产生“热”分区。 热分区可能导致速率限制、预配吞吐量的低效使用，以及更高的成本。

可以在 "**指标" 边栏选项卡**的 "**存储**" 部分的 "存储" 部分查看容器的物理分区 Azure 门户：

[![查看物理分区](./media/partition-data/view-partitions-zoomed-out.png)数](./media/partition-data/view-partitions-zoomed-in.png#lightbox)

在此示例容器中，我们已`/foodGroup`选择此容器作为分区键，其中每个矩形都表示一个物理分区。 在映像中，**分区键范围**与物理分区相同。 所选物理分区包含三个逻辑分区`Beef Products`： `Vegetable and Vegetable Products`、和`Soups, Sauces, and Gravies`。

如果我们预配每秒18000个请求单位（RU/s）的吞吐量，则三个物理分区中的每一个都可以使用1/3 的总预配吞吐量。 在选定的物理分区中，逻辑分区键`Beef Products`、 `Vegetable and Vegetable Products`和`Soups, Sauces, and Gravies`可以一起使用物理分区的6000预配 RU/s。 由于预配的吞吐量是均匀地分布在容器的物理分区上，因此选择[适当的逻辑分区键](partitioning-overview.md#choose-partitionkey)即可选择均匀分配吞吐量的分区键，这一点非常重要。 如果选择在逻辑分区之间均匀分配吞吐量消耗的分区键，则将确保物理分区之间的吞吐量消耗达到平衡。

## <a name="replica-sets"></a>副本集

每个物理分区都包含一组副本（也称为[*副本集*](global-dist-under-the-hood.md)）。 每个副本集托管 Azure Cosmos 数据库引擎的一个实例。 副本集使物理分区中存储的数据具有持久性、高可用性和一致性。 构成物理分区的每个副本均继承该分区的存储配额。 物理分区的所有副本共同支持分配给物理分区的吞吐量。 Azure Cosmos DB 自动管理副本集。

大多数小型 Cosmos 容器只需要单个物理分区，但仍将至少具有4个副本。

下图显示了逻辑分区如何映射到全局分配的物理分区：

![演示 Azure Cosmos DB 分区的插图](./media/partition-data/logical-partitions.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解[Azure Cosmos DB 中预配的吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
