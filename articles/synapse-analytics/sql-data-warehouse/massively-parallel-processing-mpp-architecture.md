---
title: Azure Synapse Analytics（以前称为 SQL DW）体系结构
description: 了解 Azure 突触分析（以前为 SQL DW）如何将大规模并行处理 （MPP） 与 Azure 存储相结合，以实现高性能和可扩展性。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: e321df3f27defdceab31fe3b425a4169928ba3f6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631951"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics（以前称为 SQL DW）体系结构

Azure Synapse 是一种无限制的分析服务，它将企业数据仓库和大数据分析结合在一起。 借助它可以使用无服务器的按需资源或预配资源，任意执行自己定义的大规模数据查询。 Azure Synapse 将这两个世界与统一体验相结合，可进行摄用、准备、管理和服务数据，以满足即时 BI 和机器学习需求。

 Azure Synapse 包含四个组件：

- SQL 分析：完成基于 T-SQL 的分析

  - SQL 池（按 DWU 预配付费） = 一般可用
  - SQL 随选（按处理的 TB 付费）-（预览）
- 火花：深度集成的阿帕奇火花（预览）
- 数据集成：混合数据集成（预览）
- 工作室：统一的用户体验。  （预览版）

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>突触 SQL MPP 体系结构组件

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse)利用横向扩展体系结构跨多个节点分发数据的计算处理。 比例单位是计算能力的抽象，称为[数据仓库单元](what-is-a-data-warehouse-unit-dwu-cdwu.md)。 计算与存储是分开的，使您能够独立于系统中的数据进行缩放计算。

![突触 SQL 体系结构](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics 使用基于节点的体系结构。 应用程序将 T-SQL 命令连接到、发布给控制节点，该节点是 SQL Analytics 的单一入口点。 控制节点运行 MPP 引擎，该引擎优化用于并行处理的查询，然后将操作传递给计算节点以并行执行其工作。

计算节点将所有用户数据存储在 Azure 存储中并运行并行查询。 数据移动服务 (DMS) 是一项系统级内部服务，它根据需要在节点间移动数据以并行运行查询和返回准确的结果。

使用分离存储和计算时，使用 Synapse SQL 池可以：

- 无论存储需求如何，都可独立计算大小。
- 无需移动数据，即可在 SQL 池（数据仓库）中增加或减少计算能力。
- 在保持数据不受影响的情况下暂停计算容量，因此只需为存储付费。
- 在操作期间恢复计算容量。

### <a name="azure-storage"></a>Azure 存储

Synapse SQL 利用 Azure 存储来保证用户数据的安全。  由于数据由 Azure 存储存储存储和管理，因此存储消耗需要单独收费。 将数据分片分到**分布中**，以优化系统的性能。 可选择在定义表时用于分布数据的分片模式。 支持以下分片模式：

- 哈希
- 循环
- 复制

### <a name="control-node"></a>控制节点

控制节点是体系结构的核心。 它是与所有应用程序和连接进行交互的前端。 MPP 引擎在控制节点上运行以优化和协调并行查询。 提交 T-SQL 查询时，Control 节点将其转换为并行针对每个分布运行的查询。

### <a name="compute-nodes"></a>计算节点

计算节点提供计算能力。 分布区映射到计算节点以进行处理。 当您为更多的计算资源付费时，分发将重新映射到可用的计算节点。 计算节点的数量范围从 1 到 60，由 Synapse SQL 的服务级别决定。

每个计算节点均有一个节点 ID，该 ID 会显示在系统视图中。 在名称以 sys.pdw_nodes 开头的系统视图中找到 node_id 列即可查看计算节点 ID。 有关这些系统视图的列表，请参阅 [MPP 系统视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)。

### <a name="data-movement-service"></a>数据移动服务

数据移动服务 (DMS) 是一项数据传输技术，它可协调计算节点间的数据移动。 某些查询需要移动数据以确保并行查询返回准确的结果。 需要移动数据时，DMS 可确保正确的数据到达正确的位置。

## <a name="distributions"></a>分发

分布区是存储和处理针对分布式数据运行的并行查询的基本单位。 SQL Analytics 运行查询时，工作会被分割成 60 个并行运行的小型查询。

每个小型查询各在一个数据分布区上运行。 每个计算节点管理其中一个或多个分布区。 具有最多计算资源的 SQL 池的每个分布区占 1 个计算节点。 具有最小计算资源的 SQL 池的所有分布区占 1 个计算节点。  

## <a name="hash-distributed-tables"></a>哈希分布表

哈希分布表可为大型表上的联接和聚合提供最高查询性能。

哈希分布表可为大型表上的联接和聚合提供最高查询性能。

要将数据分片分到哈希分布的表中，哈希函数用于确定将每行分配给一个分布。 在表定义中，可以将一个列指定为分布列。 哈希函数使用分布列中的值将 1 个行分配到 1 个分布区。

下图说明了如何将完整的非分布式表存储为哈希分布表。

![分布式表](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "分布式表")  

- 一个行属于一个分布区。  
- 通过确定性哈希算法将一个行分配到一个分布区。  
- 不同大小的表显示，每个分布区的表行的数目各不相同。

选择分布列时需考虑到性能，例如特异性、数据倾斜，以及在系统上运行的查询类型。

## <a name="round-robin-distributed-tables"></a>轮循分布表

轮循机制表是最简单的表，在被用作负载临时表时，它可创造和提供高速性能。

轮循机制分布表在表中均匀分布数据，但不会进行进一步优化。 首先随机选择一个分布区，然后将行的缓冲区按顺序分配给分布区。 将数据加载到轮循机制表速度很快，但就查询性能而言，哈希分布式表的性能更佳。 在循环表上的联接需要重新洗牌数据，这需要额外的时间。

## <a name="replicated-tables"></a>复制表

复制表为小型表提供最快查询性能。

复制表在每个计算节点上缓存表的完整副本。 因此复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 复制表尤为适用于小型表。 需要额外的存储，并且写入数据时会产生额外的开销，这使得大型表不切实际。  

下图显示了缓存在每个计算节点上的第一个分布上的复制表。  

![复制表](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "复制表")

## <a name="next-steps"></a>后续步骤

对 Azure Synapse 有了初步的认识后，请学习如何快速[创建 SQL 池](create-data-warehouse-portal.md)和[加载示例数据](load-data-from-azure-blob-storage-using-polybase.md)。 如果不熟悉 Azure，遇到新术语时，[Azure 词汇表](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 可以提供帮助。 或者，查看以下一些其他 Azure Synapse 资源。  

- [客户成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [博客](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [功能请求](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [创建支持票证](sql-data-warehouse-get-started-create-support-ticket.md)
- [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- [Stackoverflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
