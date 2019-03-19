---
title: Azure Cosmos DB 中的分区和水平缩放
description: 了解有关 Azure Cosmos DB，如何配置分区和分区键，以及如何选择适当的分区键为应用程序中的分区工作原理的信息。
ms.author: mjbrown
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 4c6847d8f870c02228aa14ab9e11c85b091ec48b
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959946"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的分区和水平缩放

本文介绍了 Azure Cosmos DB 中的物理和逻辑分区。 它还介绍了缩放和分区最佳的实践。 

## <a name="logical-partitions"></a>逻辑分区

逻辑分区包含一组具有相同的分区键的项。 例如，在其中的所有项均都包含一个容器`City`属性，可以使用`City`作为容器的分区键。 具有特定值的项的组`City`，如`London`， `Paris`，和`NYC`，形成在不同的逻辑分区。 您无需担心如何删除基础数据时删除的分区。

在 Azure Cosmos DB 中，容器是基本的缩放单元。 添加到容器和容器预配的吞吐量的数据将自动跨一组逻辑分区 （水平） 分区。 数据和吞吐量是根据 Azure Cosmos DB 容器的分区键指定分区的。 有关详细信息，请参阅[创建 Azure Cosmos DB 容器](how-to-create-container.md)。

逻辑分区定义数据库事务的范围。 可以使用支持快照隔离的事务来更新逻辑分区中的项。 当向容器中添加新项时，系统将透明地创建新的逻辑分区。

## <a name="physical-partitions"></a>物理分区

Azure Cosmos DB 容器进行缩放跨大量的逻辑分区分布数据和吞吐量。 在内部，将一个或多个逻辑分区映射到物理分区包含一组副本，也称为*副本集*。 每个副本设置主机的 Azure Cosmos DB 数据库引擎实例。 副本集使存储在持久、 高度可用且一致的物理分区中的数据。 物理分区支持的最大存储和请求单位 (Ru) 量。 构成物理分区每个副本将继承该分区的存储配额。 物理分区的所有副本共同都支持分配给物理分区的吞吐量。 

下图显示了逻辑分区如何映射到全局分配的物理分区：

![演示 Azure Cosmos DB 分区映像](./media/partition-data/logical-partitions.png)

设置容器的吞吐量，是在物理分区之间平均划分。 不均匀地分布的吞吐量请求的分区键设计可能会创建"热"的分区。 在速率限制和预配的吞吐量的使用效率低下，可能会导致热分区。

与逻辑分区不同，物理分区是系统的内部实现。 您不能控制大小、 位置或物理分区的计数，你无法控制逻辑分区和物理分区之间的映射。 但是，可以通过选择适当的分区键来控制逻辑分区的数目以及数据和吞吐量的分配。

## <a name="next-steps"></a>后续步骤

* 了解如何[选择分区键](partitioning-overview.md#choose-partitionkey)。
* 了解 [Azure Cosmos DB 中的预配吞吐量](request-units.md)。
* 了解 [Azure Cosmos DB 中的全局分布](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos DB 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解如何[Azure Cosmos DB 数据库中的预配吞吐量](how-to-provision-database-throughput.md)。
