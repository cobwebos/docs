---
title: 将架构迁移到 SQL 数据仓库 | Microsoft 文档
description: 有关在开发解决方案时会架构迁移到 Azure SQL 数据仓库的技巧。
services: sql-data-warehouse
author: jrowlandjones
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: fb1085450a16acb0f9a06a9dea9d91fc5ca23363
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>将架构迁移到 SQL 数据仓库
有关如何将 SQL 架构迁移到 SQL 数据仓库的指南。 

## <a name="plan-your-schema-migration"></a>计划架构迁移

计划迁移时，请参阅[表概述][table overview]，熟悉表设计注意事项，如统计、分布、分区和索引。  其中还列出了一些[不支持的表功能][unsupported table features]及其解决方法。

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>使用用户定义的架构合并数据库

现有工作负载可能包含多个数据库。 例如，SQL Server 数据仓库可能包含临时数据库、数据仓库数据库和一些数据市场数据库。 在此拓扑中，每个数据库都作为独立的工作负载运行，并单独采用安全策略。

相比之下，SQL 数据仓库在一个数据库中运行整个数据仓库工作负荷。 不允许跨数据库联接。 因此，SQL 数据仓库应将数据仓库使用的所有表都存储在一个数据库中。

建议使用用户定义的架构，将现有工作负载合并到一个数据库中。 有关示例，请参阅[用户定义的架构](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>使用兼容的数据类型
将数据类型修改为与 SQL 数据仓库兼容。 有关支持和不支持的数据类型的列表，请参阅[数据类型][data types]。 此主题介绍了不支持数据类型对应的解决方法。 此外，还可以在其中执行查询，以确定 SQL 数据仓库不支持的现有类型。

## <a name="minimize-row-size"></a>最大限度地降低行大小
为实现最佳性能，请最大限度地缩短表行长度。 由于行长度越短，性能就越高，因此请使用适用于数据的最小数据类型。 

对于表行的宽度，PolyBase 设定的上限为 1MB。  如果打算使用 PolyBase 将数据加载到 SQL 数据仓库中，请将表更新为最大行宽不超过 1MB。 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>指定分布选项
SQL 数据仓库是分布式数据库系统。 每个表都是跨计算节点进行分布或复制。 可以使用表选项来指定如何分布数据。 这些选项包括“轮循机制”、“已复制”或“已哈希分布”。 每个选项都有各自的利与弊。 如果不指定分布选项，SQL 数据仓库将使用“轮循机制”这一默认选项。

- “轮循机制”为默认选项。 此选项最易于使用，并能尽快加载数据，但需要移动数据才能执行联接，这会降低查询性能。
- “已复制”在每个计算节点上存储表的副本。 复制的表性能优异，因为无需移动数据，即可执行联接和聚合。 不过，此类表需要有额外的存储空间，因此最适合小型表。
- “已哈希分布”通过哈希函数跨所有节点分布行。 哈希分布式表是 SQL 数据仓库的核心，因为它们旨在确保大型表具有高查询性能。 若要使用此选项，需要进行一些计划，以选择最适合对其分布数据的列。 不过，如果没有第一时间选择最适合的列，可以轻松地对其他列重新分布数据。 

若要选择最适合每个表的分布选项，请参阅[分布式表](sql-data-warehouse-tables-distribute.md)。


## <a name="next-steps"></a>后续步骤
成功将数据库架构迁移到 SQL 数据仓库后，请继续阅读下列文章之一：

* [迁移数据][Migrate your data]
* [迁移代码][Migrate your code]

有关 SQL 数据仓库最佳实践的详细信息，请参阅[最佳实践][best practices]一文。

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
