---
title: Azure 突触 SQL 体系结构
description: 了解 Azure Synapse SQL 如何将大规模并行处理 （MPP） 与 Azure 存储相结合，以实现高性能和可扩展性。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431808"
---
# <a name="azure-synapse-sql-architecture"></a>Azure 突触 SQL 体系结构 

本文介绍了 Synapse SQL 的体系结构组件。

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>突触 SQL 体系结构组件

Synapse SQL 利用横向扩展体系结构跨多个节点分发数据的计算处理。 计算与存储是分开的，使您能够独立于系统中的数据进行缩放计算。 

对于 SQL 池，比例单位是计算能力的抽象，称为[数据仓库单元](resource-consumption-models.md)。 

对于 SQL 按需（无服务器），自动执行缩放以适应查询资源需求。 由于拓扑通过添加、删除节点或故障转移来随时间而变化，它可适应更改并确保查询有足够的资源并成功完成。 例如，下图显示了 SQL 按需使用 4 个计算节点执行查询。

![突触 SQL 体系结构](./media//overview-architecture/sql-architecture.png)

突触 SQL 使用基于节点的体系结构。 应用程序将 T-SQL 命令连接到 Control 节点，这是 Synapse SQL 的单一入口点。 

SQL 池控制节点利用 MPP 引擎优化查询进行并行处理，然后将操作传递给计算节点以并行执行其工作。 

SQL 按需控制节点利用分布式查询处理 （DQP） 引擎，通过将用户查询拆分为将在计算节点上执行的较小查询来优化和协调用户查询的分布式执行。 每个小查询都称为任务，表示分布式执行单元。 它从存储读取文件、联接从其他任务、组或从其他任务检索的数据的结果。 

计算节点将所有用户数据存储在 Azure 存储中并运行并行查询。 数据移动服务 (DMS) 是一项系统级内部服务，它根据需要在节点间移动数据以并行运行查询和返回准确的结果。 

使用分离存储和计算时，无论存储需求如何，使用 Synapse SQL 都可以从独立调整计算能力大小中获益。 对于 SQL 按需缩放，自动完成，而对于 SQL 池，可以：

* 无需移动数据，即可在 SQL 池（数据仓库）中增加或减少计算能力。
* 在保持数据不受影响的情况下暂停计算容量，因此只需为存储付费。
* 在操作期间恢复计算容量。

## <a name="azure-storage"></a>Azure 存储

Synapse SQL 利用 Azure 存储来保证用户数据的安全。 由于数据由 Azure 存储存储存储和管理，因此存储消耗需要单独收费。 

SQL 按需允许您以只读方式查询数据湖中的文件，而 SQL 池还允许您引入数据。 将数据引入 SQL 池时，数据将分片化为**分布**，以优化系统的性能。 可选择在定义表时用于分布数据的分片模式。 支持以下分片模式：

* 哈希
* 循环
* 复制

## <a name="control-node"></a>控制节点

控制节点是体系结构的核心。 它是与所有应用程序和连接进行交互的前端。 

在 SQL 池中，MPP 引擎在 Control 节点上运行以优化和协调并行查询。 将 T-SQL 查询提交到 SQL 池时，Control 节点将其转换为并行针对每个分布运行的查询。

在 SQL 按需中，DQP 引擎在 Control 节点上运行，通过将用户查询拆分为将在计算节点上执行的较小查询，从而优化和协调用户查询的分布式执行。 它还分配要由每个节点处理的文件集。

## <a name="compute-nodes"></a>计算节点

计算节点提供计算能力。 

在 SQL 池中，分布映射到计算节点进行处理。 在为更多计算资源付费时，池将分布重新映射到可用的计算节点。 计算节点的数量范围从 1 到 60，由 SQL 池的服务级别决定。 每个计算节点均有一个节点 ID，该 ID 会显示在系统视图中。 在名称以 sys.pdw_nodes 开头的系统视图中找到 node_id 列即可查看计算节点 ID。 有关这些系统视图的列表，请参阅 [MPP 系统视图](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)。

在 SQL 按需中，为每个计算节点分配任务和要执行任务的文件集。 任务是分布式查询执行单元，实际上是提交查询用户的一部分。 自动缩放有效，以确保使用足够的计算节点执行用户查询。

## <a name="data-movement-service"></a>数据移动服务

数据移动服务 （DMS） 是 SQL 池中协调计算节点之间的数据移动的数据传输技术。 某些查询需要移动数据以确保并行查询返回准确的结果。 需要移动数据时，DMS 可确保正确的数据到达正确的位置。

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>分发

分布是在 SQL 池中分布式数据上运行的并行查询的基本存储和处理单元。 当 SQL 池运行查询时，工作被划分为 60 个并行运行的较小查询。 

每个小型查询各在一个数据分布区上运行。 每个计算节点管理其中一个或多个分布区。 具有最多计算资源的 SQL 池的每个分布区占 1 个计算节点。 具有最小计算资源的 SQL 池的所有分布区占 1 个计算节点。 

## <a name="hash-distributed-tables"></a>哈希分布表
哈希分布表可为大型表上的联接和聚合提供最高查询性能。 

要将数据分片分到哈希分布的表中，SQL 池使用哈希函数确定将每行分配给一个分布。 在表定义中，可以将一个列指定为分布列。 哈希函数使用分布列中的值将 1 个行分配到 1 个分布区。

下图说明了如何将完整的非分布式表存储为哈希分布表。 

![分布式表](media//overview-architecture/hash-distributed-table.png "分布式表") 

* 一个行属于一个分布区。 
* 通过确定性哈希算法将一个行分配到一个分布区。 
* 不同大小的表显示，每个分布区的表行的数目各不相同。

选择分布列时需考虑到性能，例如特异性、数据倾斜，以及在系统上运行的查询类型。

## <a name="round-robin-distributed-tables"></a>轮循分布表

轮循机制表是最简单的表，在被用作负载临时表时，它可创造和提供高速性能。

轮循机制分布表在表中均匀分布数据，但不会进行进一步优化。 首先随机选择一个分布区，然后将行的缓冲区按顺序分配给分布区。 将数据加载到轮循机制表速度很快，但就查询性能而言，哈希分布式表的性能更佳。 在循环表上的联接需要重新洗牌数据，这需要额外的时间。

## <a name="replicated-tables"></a>复制表
复制表为小型表提供最快查询性能。

复制表在每个计算节点上缓存表的完整副本。 因此复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 复制表尤为适用于小型表。 需要额外的存储，并且写入数据时会产生额外的开销，这使得大型表不切实际。 

下图显示了缓存在每个计算节点上的第一个分布上的复制表。 

![复制表](media/overview-architecture/replicated-table.png "复制表") 

## <a name="next-steps"></a>后续步骤

现在，您已经了解了 Synapse SQL，请了解如何快速[创建 SQL 池](../quickstart-create-sql-pool.md)并[加载示例数据](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)（./sql-data-warehouse-load-sample-databases.md）。 或者您开始[使用 SQL 按需](../quickstart-sql-on-demand.md)。 如果不熟悉 Azure，遇到新术语时，[Azure 词汇表](../../azure-glossary-cloud-terminology.md) 可以提供帮助。 
