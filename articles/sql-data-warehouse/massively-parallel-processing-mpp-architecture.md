---
title: Azure Synapse Analytics （以前称为 SQL DW）体系结构
description: 了解 Azure Synapse Analytics （以前称为 SQL DW）如何将大规模并行处理（MPP）与 Azure 存储结合起来，实现高性能和可伸缩性。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: ea9629c63fcab97ba8ba83cd88592c37ae41818a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646391"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics （以前称为 SQL DW）体系结构 

Azure Synapse 是一个无限制的分析服务，它将企业数据仓库和大数据分析结合在一起。 它可让你随意使用无服务器的按需或预配的资源查询数据。 Azure Synapse 将这两个世界结合在一起，可引入、准备、管理和提供数据以满足即时的 BI 和机器学习需求。

 Azure Synapse 具有四个组件：
- SQL Analytics：完整的 T-sql 分析 
    - SQL 池（按预配的 DWU 付费）-正式发布
    - SQL 点播（处理的每 TB 付费）–（预览）
- Spark：深度集成的 Apache Spark （预览） 
- 数据集成：混合数据集成（预览版）
- Studio：统一的用户体验。  （预览版）

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>SQL Analytics MPP 体系结构组件

[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse)利用横向扩展体系结构将数据的计算处理分布到多个节点上。 缩放单位是计算能力（称为[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)）的抽象概念。 计算与存储是分开的，它使你能够独立于系统中的数据缩放计算。

![SQL 分析体系结构](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics 使用基于节点的体系结构。 应用程序将 T-sql 命令连接并颁发给控制节点，该节点是 SQL Analytics 的单一入口点。 控制节点运行用于优化并行处理查询的 MPP 引擎，然后将操作传递给计算节点以实现并行工作。 

计算节点将所有用户数据存储在 Azure 存储中并运行并行查询。 数据移动服务 (DMS) 是一项系统级内部服务，它根据需要在节点间移动数据以并行运行查询和返回准确的结果。 

使用分离的存储和计算，使用 SQL Analytics 时可以：

* 无论存储需求如何，都可独立计算大小。
* 在 SQL 池（数据仓库）内扩大或缩小计算能力，而无需移动数据。
* 在保持数据不受影响的情况下暂停计算容量，因此只需为存储付费。
* 在操作期间恢复计算容量。

### <a name="azure-storage"></a>Azure 存储

SQL Analytics 利用 Azure 存储空间来确保用户数据的安全。  由于你的数据由 Azure 存储进行存储和管理，因此，你的存储使用量会收取单独的费用。 将数据本身分片到“分布区”中来优化系统性能。 可选择在定义表时用于分布数据的分片模式。 支持以下分片模式：

* 哈希
* 轮循机制
* 复制

### <a name="control-node"></a>控制节点

控制节点是体系结构的大脑。 它是与所有应用程序和连接进行交互的前端。 MPP 引擎在控制节点上运行以优化和协调并行查询。 将 T-sql 查询提交到 SQL Analytics 时，控制节点会将其转换为并行运行的查询。

### <a name="compute-nodes"></a>计算节点数

计算节点提供计算能力。 分布区映射到计算节点以进行处理。 当你为更多计算资源付费时，SQL Analytics 会将分发重新映射到可用的计算节点。 计算节点数范围从1到60，由 SQL Analytics 的服务级别确定。

每个计算节点均有一个节点 ID，该 ID 会显示在系统视图中。 在名称以 sys.pdw_nodes 开头的系统视图中找到 node_id 列即可查看计算节点 ID。 有关这些系统视图的列表，请参阅 [MPP 系统视图](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7)。

### <a name="data-movement-service"></a>数据移动服务
数据移动服务 (DMS) 是一项数据传输技术，它可协调计算节点间的数据移动。 某些查询需要移动数据以确保并行查询返回准确的结果。 需要移动数据时，DMS 可确保正确的数据到达正确的位置。 

## <a name="distributions"></a>分发

分布区是存储和处理针对分布式数据运行的并行查询的基本单位。 当 SQL Analytics 运行查询时，工作将被划分为60的更小查询，这些查询将并行运行。 

每个小型查询各在一个数据分布区上运行。 每个计算节点管理其中一个或多个分布区。 具有最大计算资源的 SQL 池对每个计算节点都有一个分配。 具有最小计算资源的 SQL 池在一个计算节点上具有所有分布。  

## <a name="hash-distributed-tables"></a>哈希分布表
哈希分布表可为大型表上的联接和聚合提供最高查询性能。 

若要将数据分片到哈希分布式表中，SQL Analytics 将使用哈希函数来明确地将每一行分配给一个分布。 在表定义中，可以将一个列指定为分布列。 哈希函数使用分布列中的值将 1 个行分配到 1 个分布区。

下图说明了如何将完整的非分布式表存储为哈希分布表。 

![分布式表](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "分布式表")  

* 一个行属于一个分布区。  
* 通过确定性哈希算法将一个行分配到一个分布区。  
* 不同大小的表显示，每个分布区的表行的数目各不相同。

选择分布列时需考虑到性能，例如特异性、数据倾斜，以及在系统上运行的查询类型。

## <a name="round-robin-distributed-tables"></a>轮循分布表
轮循机制表是最简单的表，在被用作负载临时表时，它可创造和提供高速性能。

轮循机制分布表在表中均匀分布数据，但不会进行进一步优化。 首先随机选择一个分布区，然后将行的缓冲区按顺序分配给分布区。 将数据加载到轮循机制表速度很快，但就查询性能而言，哈希分布式表的性能更佳。 轮循机制表上的联接要求重新安排数据，因此这需要花费更多时间。


## <a name="replicated-tables"></a>复制表
复制表为小型表提供最快查询性能。

复制表在每个计算节点上缓存表的完整副本。 因此复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 复制表尤为适用于小型表。 它需要额外存储并且在写入数据时会产生额外负载，因此不适用于大型表。  

下图显示了在每个计算节点上的第一个分布上缓存的复制表。  

![复制的表](media/sql-data-warehouse-distributed-data/replicated-table.png "复制表") 

## <a name="next-steps"></a>后续步骤
现在，你已了解有关 Azure Synapse 的一点，接下来了解如何快速[创建 SQL 池][create a SQL pool]和[加载示例数据][load sample data]。 如果不熟悉 Azure，遇到新术语时，[Azure 词汇表][Azure glossary] 可以提供帮助。 或者查看其中一些其他 Azure Synapse 资源。  

* [客户成功案例]
* [博客]
* [功能请求]
* [视频]
* [客户顾问团队博客]
* [创建支持票证]
* [MSDN 论坛]
* [Stackoverflow 论坛]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[创建支持票证]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL pool]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[客户成功案例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[博客]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[客户顾问团队博客]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[功能请求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 论坛]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stackoverflow 论坛]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[视频]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
