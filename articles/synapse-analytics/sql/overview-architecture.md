---
title: Azure Synapse SQL 体系结构
description: 了解 Azure Synapse SQL 如何将大规模并行处理（MPP）与 Azure 存储结合起来，实现高性能和可伸缩性。
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431808"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL 体系结构 

本文介绍 Synapse SQL 的体系结构组件。

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL 体系结构组件

Synapse SQL 利用 scale out 体系结构将数据的计算处理分散到多个节点。 计算与存储分离，这使你能够在系统中独立于数据扩展计算。 

对于 SQL 池，刻度单位是称为[数据仓库单位](resource-consumption-models.md)的计算能力的抽象。 

对于按需运行的 SQL，无服务器的缩放会自动进行，以满足查询资源要求。 随着拓扑随时间而变化，它会通过添加、删除节点或故障转移进行更改，并确保查询具有足够的资源并成功完成。 例如，下图显示了使用4个计算节点执行查询的 SQL 点播。

![Synapse SQL 体系结构](./media//overview-architecture/sql-architecture.png)

Synapse SQL 使用基于节点的体系结构。 应用程序将 T-sql 命令连接并颁发给控制节点，该节点是 Synapse SQL 的单一入口点。 

SQL 池控制节点利用 MPP 引擎优化查询以进行并行处理，然后将操作传递给计算节点以并行完成工作。 

SQL 点播控制节点利用分布式查询处理（DQP）引擎通过将用户查询的分布式执行拆分为要在计算节点上执行的较小的查询来优化和协调该查询。 每个小型查询均称为 "任务"，表示分布式执行单元。 它从存储读取文件，联接从其他任务检索到的其他任务、组或订单数据的结果。 

计算节点将所有用户数据存储在 Azure 存储中并运行并行查询。 数据移动服务 (DMS) 是一项系统级内部服务，它根据需要在节点间移动数据以并行运行查询和返回准确的结果。 

如果使用了分离的存储和计算，则在使用 Synapse SQL 时，无论存储需求如何，都可以受益于独立的计算能力大小。 对于 SQL 按需缩放是自动完成的，但对于 SQL 池，可以：

* 无需移动数据，即可在 SQL 池（数据仓库）中增加或减少计算能力。
* 在保持数据不受影响的情况下暂停计算容量，因此只需为存储付费。
* 在操作期间恢复计算容量。

## <a name="azure-storage"></a>Azure 存储

Synapse SQL 利用 Azure 存储来保证用户数据的安全。 由于你的数据由 Azure 存储进行存储和管理，因此，你的存储使用量会收取单独的费用。 

SQL 点播使你能够以只读方式查询 data lake 中的文件，而 SQL 池还允许你引入数据。 将数据引入到 SQL 池中时，会将数据分片到**分布**区，以优化系统的性能。 可选择在定义表时用于分布数据的分片模式。 支持以下分片模式：

* 哈希
* 循环
* 复制

## <a name="control-node"></a>控制节点

控制节点是体系结构的核心。 它是与所有应用程序和连接进行交互的前端。 

在 SQL 池中，MPP 引擎在控制节点上运行以优化和协调并行查询。 将 T-sql 查询提交到 SQL 池时，控制节点会将其转换为并行运行的查询。

在 SQL 点播中，DQP 引擎在控制节点上运行，以便优化和协调用户查询的分布式执行，方法是将其拆分为要在计算节点上执行的更小的查询。 它还会为每个节点分配要处理的文件集。

## <a name="compute-nodes"></a>计算节点

计算节点提供计算能力。 

在 SQL 池中，分发映射到计算节点进行处理。 当你为更多计算资源付费时，池会将分发重新映射到可用的计算节点。 计算节点数范围从1到60，由 SQL 池的服务级别确定。 每个计算节点均有一个节点 ID，该 ID 会显示在系统视图中。 在名称以 sys.pdw_nodes 开头的系统视图中找到 node_id 列即可查看计算节点 ID。 有关这些系统视图的列表，请参阅 [MPP 系统视图](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)。

在 SQL 点播中，为每个计算节点分配了任务和一组要执行任务的文件。 任务是分布式查询执行单元，这实际上是提交的查询用户的一部分。 自动缩放生效，可确保使用足够的计算节点执行用户查询。

## <a name="data-movement-service"></a>数据移动服务

数据移动服务（DMS）是 SQL 池中的数据传输技术，用于协调计算节点之间的数据移动。 某些查询需要移动数据以确保并行查询返回准确的结果。 需要移动数据时，DMS 可确保正确的数据到达正确的位置。

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>分发

分布是对 SQL 池中的分布式数据运行的并行查询的基本单位。 当 SQL 池运行查询时，工作将被划分为60的更小查询，这些查询将并行运行。 

每个小型查询各在一个数据分布区上运行。 每个计算节点管理其中一个或多个分布区。 具有最多计算资源的 SQL 池的每个分布区占 1 个计算节点。 具有最小计算资源的 SQL 池的所有分布区占 1 个计算节点。 

## <a name="hash-distributed-tables"></a>哈希分布表
哈希分布表可为大型表上的联接和聚合提供最高查询性能。 

若要将数据分片到哈希分布式表中，SQL 池将使用哈希函数来确定每一行都分配给一个分布。 在表定义中，可以将一个列指定为分布列。 哈希函数使用分布列中的值将 1 个行分配到 1 个分布区。

下图说明了如何将完整的非分布式表存储为哈希分布表。 

![分布式表](media//overview-architecture/hash-distributed-table.png "分布式表") 

* 一个行属于一个分布区。 
* 通过确定性哈希算法将一个行分配到一个分布区。 
* 不同大小的表显示，每个分布区的表行的数目各不相同。

选择分布列时需考虑到性能，例如特异性、数据倾斜，以及在系统上运行的查询类型。

## <a name="round-robin-distributed-tables"></a>轮循分布表

轮循机制表是最简单的表，在被用作负载临时表时，它可创造和提供高速性能。

轮循机制分布表在表中均匀分布数据，但不会进行进一步优化。 首先随机选择一个分布区，然后将行的缓冲区按顺序分配给分布区。 将数据加载到轮循机制表速度很快，但就查询性能而言，哈希分布式表的性能更佳。 轮循机制表上的联接需要重新组织的数据，这需要额外的时间。

## <a name="replicated-tables"></a>复制表
复制表为小型表提供最快查询性能。

复制表在每个计算节点上缓存表的完整副本。 因此复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 复制表尤为适用于小型表。 需要额外的存储空间，并且在写入数据时会产生额外的开销，这会使大型表不切实际。 

下图显示了在每个计算节点上的第一个分布上缓存的复制表。 

![复制表](media/overview-architecture/replicated-table.png "复制表") 

## <a name="next-steps"></a>后续步骤

现在，你已了解有关 Synapse SQL 的一点，接下来了解如何快速[创建 sql 池](../quickstart-create-sql-pool.md)并[加载示例数据](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)（./sql-data-warehouse-load-sample-databases.md）。 或者，开始[使用 SQL 点播](../quickstart-sql-on-demand.md)。 如果不熟悉 Azure，遇到新术语时，[Azure 词汇表](../../azure-glossary-cloud-terminology.md) 可以提供帮助。 
