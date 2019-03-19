---
title: Azure SQL 数据库内存中技术 | Microsoft 文档
description: Azure SQL 数据库内存中技术大幅提升了事务和分析工作负荷的性能。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jodebrui
ms.author: jodebrui
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: c873587a640bb36e9fa43e314bf789a207956ae0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57854834"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>在 SQL 数据库中使用内存中技术优化性能

使用 Azure SQL 数据库的内存中技术可以提高应用程序的性能，并潜在地降低数据库的成本。 

## <a name="when-to-use-in-memory-technologies"></a>何时使用内存中技术

使用 Azure SQL 数据库的内存中技术可以在各种工作负荷上实现性能改进：

- **事务**（联机事务处理 (OLTP)），其中的大多数请求将读取或更新少量的数据（例如 CRUD 操作）。
- **分析**（联机分析处理 (OLAP)），其中的大多数查询出于报告目的而执行复杂的计算，特定数量的查询会将数据加载和追加到现有的表中（称为“批量加载”）或者从表中删除数据。 
- **混合**（混合事务/分析处理 (HTAP)），其中针对相同的数据集同时执行 OLTP 和 OLAP 查询。

内存中技术使用查询的本机编译，或者底层硬件上提供的批处理和 SIMD 指令等高级处理来保留应在内存中处理的数据，可以提高这些工作负荷的性能。 

## <a name="overview"></a>概述

Azure SQL 数据库采用以下内存中技术：
- *[内存中 OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* 可以增加每秒处理的事务数，并降低事务处理的延迟。 可受益于内存中 OLTP 的情况有：高吞吐量事务处理（例如贸易和游戏）、从事件或 IoT 设备引入数据、缓存、数据加载以及临时表和表变量等情况。
- *聚集列存储索引*可减少存储占用（高达 10 倍）并提高报告和分析查询的性能。 将其与数据集市中的事实数据表结合使用，可在数据库中容纳更多数据并提升性能。 此外，将其与操作数据库中的历史数据结合使用，可存档并查询高达 10 倍的额外数据。
- 用于 HTAP 的*非聚集列存储索引*：通过直接查询操作数据库来帮助获取业务的实时见解，无需运行开销不菲的提取、转换和加载 (ETL) 过程并等待填充数据仓库。 使用非聚集列存储索引可以快速对 OLTP 数据库执行分析查询，同时减少对操作工作负荷的影响。
- 使用适用于 HTAP 的内存优化聚集列存储索引可以针对相同的数据快速执行事务处理和并发运行分析查询。

列存储索引和内存中 OLTP 分别在 2012 年和 2014 年加入 SQL Server 产品。 Azure SQL 数据库和 SQL Server 共享内存中技术的相同实现。 今后，这些技术的新功能将首先在 Azure SQL 数据库中发布，再加入到下一个版本的 SQL Server。

## <a name="benefits-of-in-memory-technology"></a>内存中技术的优势

由于查询和事务处理的效率提升，内存中技术还可降低成本。 用户通常不需要升级数据库的定价层即可实现性能提升。 在某些情况下，即使是降低定价层，使用内存中技术也能实现性能改善。

以下两个示例演示了如何借助内存中 OLTP 大幅改善性能：

- 使用内存中 OLTP，[仲裁商业解决方案能够使其工作负荷增加一倍，同时节省 70% 的 DTU](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)。

  - DTU 表示“数据库事务单位”，包括资源消耗的测量值。
- 以下视频通过一个示例工作负荷演示了资源使用率的显著提高：[Azure SQL 数据库的内存中 OLTP 视频](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)
  - 有关详细信息，请参阅博客文章：[In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)（“Azure SQL 数据库中的内存中 OLTP”博客文章）

> [!NOTE]  
> 可在“高级”层和“业务关键”层 Azure SQL 数据库与高级弹性池中使用内存中技术。

以下视频介绍了使用 Azure SQL 数据库中的内存中技术可带来的潜在性能提升。 请记住，实际带来的性能提升取决于许多因素，包括工作负荷和数据的性质、数据库的访问模式，等等。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

本文全面介绍特定于 Azure SQL 数据库的内存中 OLTP 和列存储索引，并提供示例：

- 介绍这些技术对存储和数据大小限制的影响。
- 介绍如何控制使用这些技术的数据库在不同定价层之间的移动。
- 介绍两个示例，演示如何使用 Azure SQL 数据库中的内存中 OLTP 和列存储索引。

有关详细信息，请参阅：

- [内存中 OLTP 的概述和使用方案](https://msdn.microsoft.com/library/mt774593.aspx)（包括客户案例研究参考和入门信息）
- [内存中 OLTP 的文档](https://msdn.microsoft.com/library/dn133186.aspx)
- [列存储索引指南](https://msdn.microsoft.com/library/gg492088.aspx)
- 混合事务/分析处理 (HTAP)，也称为[实时运营分析](https://msdn.microsoft.com/library/dn817827.aspx)

## <a name="in-memory-oltp"></a>内存中 OLTP

内存中 OLTP 技术将所有数据保留在内存中，可以提供极快的数据访问操作。 它还使用专用索引、查询本机编译和无闩锁数据访问来提高 OLTP 工作负荷的性能。 可通过两种方式来组织内存中 OLTP 数据：

- **内存优化的行存储**格式：每个行是一个独立的内存对象。 这是针对高性能 OLTP 工作负荷优化的经典内存中 OLTP 格式。 在内存优化的行存储格式中可以使用两种类型的内存优化表：
  - 持久性表 (SCHEMA_AND_DATA)：服务器重启后会保留内存中的行。 此类表的行为类似于传统的行存储表，同时具有内存中优化的附加优势。
  - 非持久性表 (SCEMA_ONLY)：重启后不保留行。 此类表适用于临时数据（例如，取代临时表），或者需要快速加载其中的数据，然后将数据移到某个持久性表（称为临时表）的表。
- **内存优化的列存储**格式：其中的数据以纵栏表的格式进行组织。 此结构适用于 HTAP 方案，其中，需要针对运行 OLTP 工作负荷的同一数据结构运行分析查询。

> [!Note]
> 内存中 OLTP 技术适用于完全驻留在内存中的数据结构。 由于无法将内存中数据卸载到磁盘，因此请确保使用具有足够内存的数据库。 有关更多详细信息，请参阅[内存中 OLTP 的数据大小和存储上限](#data-size-and-storage-cap-for-in-memory-oltp)。

有关内存中 OLTP 的快速入门：[快速入门 1：通过内存中 OLTP 技术加速 T-SQL 性能](https://msdn.microsoft.com/library/mt694156.aspx)（另一篇文章也可以帮助你入门）

深入介绍这些技术的视频：

- [Azure SQL 数据库中的内存中 OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)（包含性能优势的演示和自行重现这些结果的步骤）
- [内存中 OLTP 视频：定义及其使用时机和用法](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

可通过编程方式了解给定的数据库是否支持内存中 OLTP。 可执行以下 Transact-SQL 查询：
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
如果查询返回 **1**，则此数据库支持内存中 OLTP。 以下查询确定了将数据库降级为标准/基本版本前需要删除的所有对象：
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>内存中 OLTP 的数据大小和存储上限

内存中 OLTP 包括用于存储用户数据的内存优化表。 这些表必需在内存可容纳的范围内。 由于内存是直接在 SQL 数据库服务中管理的，因此我们提出了用户数据配额的概念。 这种概念称为*内存中 OLTP 存储*。

每个受支持的单一数据库定价层和每个弹性池定价层都包括一定量的内存中 OLTP 存储。 请参阅[基于 DTU 的资源限制 - 单一数据库](sql-database-dtu-resource-limits-single-databases.md)、[基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)、[基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和[基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。

以下各项计入内存中 OLTP 存储上限：

- 内存优化表中的活动用户数据行和表变量。 请注意，旧行版本不计入上限。
- 内存优化表中的索引。
- ALTER TABLE 操作的运营开销。

如果达到上限，将会出现超出配额错误，且无法再插入或更新数据。 若要解决此错误，可删除数据或提升数据库或池的定价层。

有关监视内存中 OLTP 存储利用率及配置即将达到上限时的警报的详细信息，请参阅[监视内存中存储](sql-database-in-memory-oltp-monitoring.md)。

#### <a name="about-elastic-pools"></a>关于弹性池

使用弹性池时，池中的所有数据库共享内存中 OLTP 存储。 因此一个数据库中的使用量可能对其他数据库造成影响。 对此，有两个缓解方法：

- 为低于池的 eDTU 或 vCore 计数的数据库整体配置 `Max-eDTU` 或 `MaxvCore`。 此最大值将池中任意数据库中的内存中 OLTP 存储利用率限制为与 eDTU 计数对应的大小。
- 配置大于 0 的 `Min-eDTU` 或 `MinvCore`。 此最小值可保证池中的每个数据库都有与配置的 `Min-eDTU` 或 `vCore` 对应的可用内存中 OLTP 存储量。

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>更改使用内存中 OLTP 技术的数据库的服务层

始终可以将数据库或实例升级到更高的层，例如，从“常规用途”层升级到“业务关键”层（或者从“标准”层升级到“高级”层）。 可用的功能和资源只会增加。

但是，将层降级可能会对数据库造成负面影响。 如果数据库包含内存中 OLTP 对象，则从“业务关键”层降级到“常规用途”层（或者从“高级”层降级到“标准”或“基本”层）时，影响就尤为明显。 降级后，内存优化表不可用（即使它们保持可见）。 降低弹性池的定价层或将使用内存中技术的数据库移动到标准或基本弹性池时，也应考虑这些问题。

> [!Important]
> “常规用途”、“标准”或“基本”层不支持内存中 OLTP。 因此，不能将包含任何内存中 OLTP 对象的数据库移到“标准”或“基本”层。

将数据库降级到标准/基本层之前，请删除所有内存优化表和表类型，以及所有本机编译的 T-SQL 模块。 

*在“业务关键”层中缩减资源*：内存优化表中的数据必须能够装入与数据库层或托管实例相关联的或者在弹性池中可用的内存中 OLTP 存储中。 如果尝试缩减层或将数据库移到可用内存中 OLTP 存储不足的池，操作将会失败。

## <a name="in-memory-columnstore"></a>内存中列存储

使用内存中列存储技术可在表中存储和查询大量数据。 列存储技术使用基于列的数据存储格式和批查询处理，与传统的行导向型存储相比，可将 OLAP 工作负荷中的查询性能提升 10 倍。 此外，与处理非压缩数据相比，处理压缩数据可将性能提升 10 倍。
可以使用两种类型的列存储模型来组织数据：

- **聚集列存储**：表中的所有数据以纵栏表的格式进行组织。 在此模型中，表中的所有行以纵栏表的格式进行定位，该格式可以高度压缩数据，并可让你快速执行分析查询，以及针对表生成报告。 根据数据的性质，数据大小可以减少 10 到 100 倍。 使用聚集列存储模型还可以快速引入大量数据（批量加载），因为超过 10 万行的大型数据批在存储到磁盘之前会经过压缩。 此模型非常适合经典数据仓库方案。 
- **非聚集列存储**：其中的数据存储在传统的行存储表中，有一个采用列存储格式的索引用于执行分析查询。 使用此模型可以实现混合事务分析处理 (HTAP)：可以针对事务工作负荷运行高性能的实时分析。 针对优化的行存储表执行 OLTP 查询以访问少量的行，同时，针对更适合用于扫描和分析的列存储索引执行 OLAP 查询。 Azure SQL 数据库查询优化器可根据查询动态选择行存储或列存储格式。 非聚集列存储索引无法减少数据大小，因为原始数据集按原样保留在原始行存储表中。 但是，其他列存储索引的大小应该比同等的 B 树索引要小几个量级。

> [!Note]
> 内存中列存储技术仅在内存中保留处理时所需的数据，不能装入内存的数据将存储在磁盘上。 因此，内存中列存储结构中的数据量可能会超出可用的内存量。 

有关该技术的深入介绍视频：

- [列存储索引：Ignite 2016 大会中发布的内存中分析视频](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>列存储索引的数据大小和存储

列存储索引不需要在内存可容纳的范围内。 因此，索引大小的唯一上限是最大整体数据库大小，此大小在[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)和[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)两篇文章中有述。

使用聚集列存储索引时，对基础表存储使用列式压缩。 这种压缩可显著减少用户数据的存储占用，意味着数据库中可容纳更多数据。 使用[纵栏表存档压缩](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression)可进一步提高压缩率。 可实现的压缩量取决于数据的性质，但 10 倍压缩并不少见。

例如，如果数据库的最大大小为 1 TB，则使用列存储索引实现 10 倍压缩时，该数据库中可容纳总共 10 TB 的用户数据。

使用非聚集列存储索引时，仍以传统行存储格式存储基础表。 因此节省的存储小于使用聚集列存储索引节省的空间。 但是，如果使用单个列存储索引取代众多传统非聚集索引，则仍可整体减少表的存储占用。

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>更改包含列存储索引的数据库的服务层

如果目标层低于 S3，可能无法将单一数据库降级到“基本”或“标准”层。 只有“业务关键”/“高级”定价层、“标准”层、“S3”及更高的层支持列存储索引，“基本”层则不支持。 将数据库降级到不受支持的层或级别时后，列存储索引不可用。 系统会保留列存储索引，但永远不会利用索引。 如果后来又升级回到受支持的层或级别，列存储索引立即可供再次利用。

如果有聚集列存储索引，则降级后，整个表不可用。 因此，我们建议在将数据库降级到不受支持的层或级别前，先删除所有聚集列存储索引。

> [!Note]
> 托管实例支持所有层中的列存储索引。

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>后续步骤

- [快速入门 1：通过内存中 OLTP 技术加速 T-SQL 性能](https://msdn.microsoft.com/library/mt694156.aspx)
- [在现有的 Azure SQL 应用程序中使用内存中 OLTP](sql-database-in-memory-oltp-migration.md)
- [监视内存中 OLTP 存储](sql-database-in-memory-oltp-monitoring.md)（适用于内存中 OLTP）
- [试用 Azure SQL 数据库的内存中功能](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>其他资源

### <a name="deeper-information"></a>深入信息

- [了解 Quorum 如何使用 SQL 数据库中的内存中 OLTP 将关键数据库的工作负荷提高一倍，并将 DTU 降低 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)（“Azure SQL 数据库中的内存中 OLTP”博客文章）
- [了解内存中 OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [了解列存储索引](https://msdn.microsoft.com/library/gg492088.aspx)
- [了解实时运行分析](https://msdn.microsoft.com/library/dn817827.aspx)
- 请参阅[有关常用工作负荷模式和迁移注意事项](https://msdn.microsoft.com/library/dn673538.aspx)（介绍内存中 OLTP 往往能够在其中提供显著性能改善的工作负荷模式）

### <a name="application-design"></a>应用程序设计

- [内存中 OLTP（内存中优化）](https://msdn.microsoft.com/library/dn133186.aspx)
- [在现有的 Azure SQL 应用程序中使用内存中 OLTP](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>工具

- [Azure 门户](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
