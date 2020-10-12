---
title: Synapse SQL 体系结构
description: 了解 Azure Synapse SQL 如何将大规模并行处理 (MPP) 与 Azure 存储结合，实现高性能和可伸缩性。
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9f2f3eee12bb8741f6d079f6f081a08f4e2db9b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87046861"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL 体系结构 

本文介绍 Synapse SQL 的体系结构组件。

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL 体系结构组件

Synapse SQL 利用横向扩展体系结构在多个节点间分布数据的计算处理。 计算与存储是分开的，这使你可以独立于系统中的数据来缩放计算。 

对于 SQL 池，缩放单位是计算能力的抽象概念，称为[数据仓库单位](resource-consumption-models.md)。 

对于无服务器 SQL 按需版本，将自动进行缩放以适应查询资源需求。 拓扑会随时间而变化（添加、删除节点或进行故障转移），SQL 按需版本可以适应变化，确保查询具有足够的资源并成功完成。 例如，下图显示了利用 4 个计算节点执行查询的 SQL 按需版本。

![Synapse SQL 体系结构](./media//overview-architecture/sql-architecture.png)

Synapse SQL 使用基于节点的体系结构。 应用程序连接到控制节点并向其发出 T-SQL 命令，该节点是 Synapse SQL 的单一入口点。 

SQL 池的控制节点利用 MPP 引擎优化查询以进行并行处理，然后将操作传递给计算节点以实现并行工作。 

SQL 按需版本的控制节点利用分布式查询处理 (DQP) 引擎，通过将用户查询拆分为将在计算节点上执行的较小查询，来优化和协调用户查询的分布式执行。 每个小查询称为一个任务，表示分布式执行单元。 它从存储中读取文件，联接其他任务的结果，对从其他任务检索到的数据进行分组或排序。 

计算节点将所有用户数据存储在 Azure 存储中并运行并行查询。 数据移动服务 (DMS) 是一项系统级内部服务，它根据需要在节点间移动数据以并行运行查询和返回准确的结果。 

通过分离存储和计算，使用 Synapse SQL 时，无论存储需求如何，都可以受益于计算能力的独立调整。 对于 SQL 按需版本，可自动进行缩放，而对于 SQL 池，可以：

* 在 SQL 池（数据仓库）内增大或缩小计算能力，而无需移动数据。
* 在保持数据不受影响的情况下暂停计算容量，因此只需为存储付费。
* 在操作期间恢复计算容量。

## <a name="azure-storage"></a>Azure 存储

Synapse SQL 使用 Azure 存储保护用户数据。 由于数据通过 Azure 存储进行存储和管理，因此会对存储消耗单独收费。 

SQL 按需版本允许你以只读方式查询数据湖中的文件，而 SQL 池还允许你引入数据。 将数据引入 SQL 池中时，数据将分片到分布区中，以优化系统性能。 可选择在定义表时用于分布数据的分片模式。 支持以下分片模式：

* 哈希
* 循环
* 复制

## <a name="control-node"></a>控制节点

控制节点是体系结构的核心。 它是与所有应用程序和连接进行交互的前端。 

在 SQL 池中，MPP 引擎在控制节点上运行，以优化和协调并行查询。 将 T-SQL 查询提交到 SQL 池时，控制节点会将其转换为可针对每个分布区并行运行的查询。

在 SQL 按需版本中，DQP 引擎在控制节点上运行，通过将用户查询拆分为将在计算节点上执行的较小查询，来优化和协调用户查询的分布式执行。 它还为每个节点分配要处理的文件集。

## <a name="compute-nodes"></a>计算节点

计算节点提供计算能力。 

在 SQL 池中，分布区映射到计算节点以进行处理。 如果支付更多计算资源费用，SQL 池会将分布区重新映射到可用的计算节点。 计算节点数的范围是 1 到 60，它由 SQL 池的服务级别确定。 每个计算节点均有一个节点 ID，该 ID 会显示在系统视图中。 在名称以 sys.pdw_nodes 开头的系统视图中找到 node_id 列即可查看计算节点 ID。 有关这些系统视图的列表，请参阅 [MPP 系统视图](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)。

在 SQL 按需版本中，将为每个计算节点分配任务以及要对其执行任务的文件集。 任务是指分布式查询执行单元，实际上是用户提交的查询的一部分。 可进行自动缩放，以确保利用足够多的计算节点来执行用户查询。

## <a name="data-movement-service"></a>数据移动服务

数据移动服务 (DMS) 是 SQL 池中的一项数据传输技术，它可协调计算节点间的数据移动。 某些查询需要移动数据以确保并行查询返回准确的结果。 需要移动数据时，DMS 可确保正确的数据到达正确的位置。

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>分发

分布区是并行查询的基本存储和处理单元，这些查询针对 SQL 池中的分布式数据运行。 当 SQL 池运行查询时，工作会被分割成 60 个并行运行的小型查询。 

每个小型查询各在一个数据分布区上运行。 每个计算节点管理其中一个或多个分布区。 具有最多计算资源的 SQL 池的每个分布区占 1 个计算节点。 具有最少计算资源的 SQL 池的所有分布区都在 1 个计算节点上。 

## <a name="hash-distributed-tables"></a>哈希分布表
哈希分布表可为大型表上的联接和聚合提供最高查询性能。 

为了将数据分片到哈希分布式表中，SQL 池使用哈希函数明确将 1 个行分配到 1 个分布区。 在表定义中，可以将一个列指定为分布列。 哈希函数使用分布列中的值将 1 个行分配到 1 个分布区。

下图说明了如何将完整的非分布式表存储为哈希分布表。 

![分布式表](media//overview-architecture/hash-distributed-table.png "分布式表") 

* 一个行属于一个分布区。 
* 通过确定性哈希算法将一个行分配到一个分布区。 
* 不同大小的表显示，每个分布区的表行的数目各不相同。

选择分布列时需考虑到性能，例如特异性、数据倾斜，以及在系统上运行的查询类型。

## <a name="round-robin-distributed-tables"></a>轮循分布表

轮循机制表是最简单的表，在被用作负载临时表时，它可创造和提供高速性能。

轮循机制分布表在表中均匀分布数据，但不会进行进一步优化。 首先随机选择一个分布区，然后将行的缓冲区按顺序分配给分布区。 将数据加载到轮循机制表速度很快，但就查询性能而言，哈希分布式表的性能更佳。 轮循机制表上的联接要求重新安排数据，这需要花费更多时间。

## <a name="replicated-tables"></a>复制表
复制表为小型表提供最快查询性能。

复制表在每个计算节点上缓存表的完整副本。 因此复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 复制表尤为适用于小型表。 它需要额外的存储并且在写入数据时会产生额外的开销，因此不适用于大型表。 

下图显示了一个复制表，该表缓存在每个计算节点的第一个分布区上。 

![复制表](media/overview-architecture/replicated-table.png "复制表") 

## <a name="next-steps"></a>后续步骤

对 Synapse SQL 有了初步的认识后，请继续了解如何快速[创建 SQL 池](../quickstart-create-sql-pool-portal.md)和[加载示例数据](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md)。 或者开始[使用 SQL 按需版本](../quickstart-sql-on-demand.md)。 如果不熟悉 Azure，遇到新术语时，[Azure 词汇表](../../azure-glossary-cloud-terminology.md) 可以提供帮助。 
