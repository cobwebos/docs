---
title: Azure SQL 数据仓库发行说明 | Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/27/2019
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: ae9f2cbdd659b7eaf3558717be19f324e803d906
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522326"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 数据仓库发行说明

本文汇总了 [Azure SQL 数据仓库](sql-data-warehouse-overview-what-is.md)的最新版本中的新功能和改进。 该文章还列出了值得注意的不是直接与发布相关但相同的时间段内发布的内容更新。 对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="march-2019"></a>2019 年 3 月

| 服务改进 | 详细信息 |
| --- | --- |
|**目前以预览版提供的工作负荷重要性**|工作负荷重要性使数据工程师能够使用重要性来对请求进行分类。 具有较高的优先级请求更快地访问资源，这有助于满足 Sla 保证。  工作负荷重要性允许具有高业务值工作较少的资源的共享环境中满足 sla 要求。<br/><br/>工作负荷重要性的详细信息，请参阅[分类](sql-data-warehouse-workload-classification.md)并[重要性](sql-data-warehouse-workload-importance.md)文档中的概述文章。 请查看[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)以及文档。<br/><br/>请参阅中的操作中的工作负荷重要性下方视频：<br/>[工作负荷管理概念](  https://www.youtube.com/embed/QcCRBAhoXpM)<br/>[工作负荷管理方案](https://www.youtube.com/embed/_2rLMljOjw8)|
|**GROUP BY 汇总**|汇总现已是受支持的 GROUP BY 选项在 Azure 数据仓库中。   GROUP BY ROLLUP 创建每个列表达式组合为一个组。 GROUP BY 还"汇总"结果到小计和总计。 若要执行此操作，即会从右到左，减少对其创建的组和从右向列表达式的数目。  列的顺序会影响 ROLLUP 的输出，而且可能会影响在结果集中的行数。<br/><br/>GROUP BY ROLLUP 的详细信息，请参阅文章， [GROUP BY (TRANSACT-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**改进了的准确性的已用的 DWU 和 CPU 门户指标**|SQL 数据仓库会显著提高在 Azure 门户中的度量准确性。  此版本中包括要在所有计算节点之间正确反映你的工作负荷的 CPU 和已用的指标定义的修复的程序。|
|**更多 T-SQL 支持**|SQL 数据仓库的 T-SQL 语言外围已扩展为包括对支持：<br/>&bull; &nbsp; [FORMAT (Transact-SQL)](/sql/t-sql/functions/format-transact-sql)<br/>&bull; &nbsp;    [STRING_ESCAPE (Transact-SQL)](/sql/t-sql/functions/string-escape-transact-sql)<br/>&bull; &nbsp; [STRING_SPLIT (Transact-SQL)](/sql/t-sql/functions/string-split-transact-sql)<br/>&bull; &nbsp; [TRANSLATE (Transact-SQL)](/sql/t-sql/functions/translate-transact-sql)<br/>&bull; &nbsp; [TRIM (Transact-SQL)](/sql/t-sql/functions/trim-transact-sql)
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|新的工作负荷管理文档 |有关详细信息，请参阅[重要性](sql-data-warehouse-workload-importance.md)并[分类](sql-data-warehouse-workload-classification.md)。 |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**返回结果时的排序依据优化**|在此版本中，SELECT…ORDER BY 的性能已得到大幅提升。   现在，所有计算节点都将其结果发送到单个计算节点，该节点对结果进行合并和排序，然后，这些结果通过该计算节点返回给用户。  当查询结果集包含大量的行时，通过单个计算节点进行合并可以显著提升性能。 以前，查询执行引擎按每个计算节点将结果排序，再将结果流式传输到控制节点，然后合并结果。|
|**PartitionMove 和 BroadcastMove 的数据移动增强**|在 Azure SQL 数据仓库 Gen2 中，ShuffleMove 类型的数据移动步骤使用[性能增强博客文章](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)中所述的即时数据移动技术。 在此版本中，PartitionMove 和 BroadcastMove 类型的数据移动步骤以相同的即时数据移动技术为后盾。 利用这些类型的数据移动步骤的用户查询可以提升运行性能。 无需更改代码便可利用这些性能改进。|
|**值得注意的 Bug**|Azure SQL 数据仓库版本不正确-`SELECT @@VERSION`可能返回不正确的版本，10.0.9999.0。 当前版本的正确版本是 10.0.10106.0。 此 bug 已报告，正在评审。
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|无 | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**虚拟网络服务终结点已公开发布**|此版本包括公开发布的虚拟网络 (VNet) 服务终结点，适用于所有 Azure 区域中的 Azure SQL 数据仓库。 使用 VNet 服务终结点，可从虚拟网络中的给定子网或子网集隔离与逻辑服务器的连接。 从 VNet 到 Azure SQL 数据仓库的流量始终保留在 Azure 主干网络内。 这种直接路由将优先于通过虚拟设备或本地访问 Internet 流量的任何特定路由。 通过服务终结点访问虚拟网络无需额外付费。 [Azure SQL 数据仓库](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的当前定价模型按原样应用。<br/><br/>在此版本中，我们还允许使用 [Azure Blob 文件系统](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) 驱动程序通过 PolyBase 连接到 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS)。 Azure Data Lake Storage Gen2 为 Azure 存储提供了分析数据的整个生命周期所需的所有特性。 两个现有 Azure 存储服务（Azure Blob 存储和 Azure Data Lake Storage Gen1）的功能已聚合。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能（例如文件系统语义、文件级安全性和规模）与 [Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)中的低成本分层存储和高可用性/灾难恢复功能进行了组合。<br/><br/>使用 Polybase 时，也可将数据从以安全方式连接到 VNet 的 Azure 存储导入到 Azure SQL 数据仓库中。 同样，也可通过 Polybase 将数据从 Azure SQL 数据仓库导出到以安全方式连接到 VNet 的 Azure 存储。<br/><br/>有关 Azure SQL 数据仓库中 VNet 服务终结点的详细信息，请参阅[博客文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文档](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。|
|**自动性能监视 （预览版）**|[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)现已发布适用于 Azure SQL 数据仓库的预览版。 查询存储旨在帮助你进行查询性能故障诊断，可以通过跟踪查询、查询计划、运行时统计信息和查询历史记录来监视数据仓库的活动和性能。 查询存储是一组内部存储和动态管理视图 (DMV)，用于执行以下操作：<br/><br/>&bull; &nbsp; 识别并优化资源使用查询<br/>&bull; &nbsp; 识别并提高即席工作负荷<br/>&bull; &nbsp; 评估查询性能和影响到计划中的统计信息、 索引或系统的大小 （DWU 设置） 的更改<br/>&bull; &nbsp; 请参阅完整的查询文本，以执行的所有查询<br/><br/>查询存储包含三个实际的存储：<br/>&bull; &nbsp; 计划存储用于保存执行计划信息<br/>&bull; &nbsp; 用于永久保存执行统计信息的运行时统计信息存储<br/>&bull; &nbsp; 用于保存等待统计信息存储等待统计信息。<br/><br/>SQL 数据仓库会自动管理这些存储区，并提供无限的数量的查询 storied 过去七天内不额外收费。 启用查询存储很简单，只需运行 ALTER DATABASE T-SQL 语句即可： <br/>sql---ALTER DATABASE [数据库名称] 设置 QUERY_STORE = ON;---Azure SQL 数据仓库中查询存储区的详细信息，请参阅文章[使用 Query Store 监视性能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，和查询存储区 Dmv，如[sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)。 这是宣告发布情况的[博客文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)。|
|**降低计算层的 Azure SQL 数据仓库 Gen2**|Azure SQL 数据仓库 Gen2 现支持较低的计算层。 客户可以体验 Azure SQL 数据仓库的领先性能、灵活性和安全性功能，从 100 cDWU（[数据仓库单元](what-is-a-data-warehouse-unit-dwu-cdwu.md)）开始，并在几分钟内扩展到3 0,000 cDWU。 从 2018 年 12 月中开始，通过某些[区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)中的较低的计算层，客户可以从 Gen2 性能和灵活性中受益。其余区域的此类计算层在 2019 年提供。<br/><br/>通过降低下一代数据仓库的入口点，Microsoft 为价值驱动型客户打开了大门，他们无需猜测哪种试用环境最适合，即可评估安全、高性能数据仓库的所有优势。 客户最低可以从 100 cDWU 开始，低于目前的 500 cDWU 入口点。 SQL 数据仓库 Gen2 继续支持暂停和恢复操作，并且不仅止于计算的灵活性。 Gen2 还支持无限的列存储容量，并且每个查询的内存增加了 1.5 倍，并发查询多达 128 个，以及[自适应缓存](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 与同一价格的 Gen1 上的相同数据仓库单元相比，这些功能的平均性能提高了 4 倍。 异地冗余备份是 Gen2 的标准配置，具有内置的保证数据保护。 Azure SQL 数据仓库 Gen2 随时可以缩放。|
|**列存储后台合并**|默认情况下，Azure SQL 数据仓库 (Azure SQL DW) 使用称为[行组](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微分区以列格式存储数据。 有时候，由于构建索引或加载数据时的内存约束，行组可能会被压缩为小于最佳大小 100 万行。 行组还可能会由于删除而产生碎片。 小的或碎片化的行组会导致较高的内存消耗以及低效的查询执行。 使用此版本的 Azure SQL DW 时，列存储后台维护任务会将较小的压缩行组进行合并以创建较大的行组，从而更好地利用内存并提高查询执行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**数据仓库的 DevOps**|SQL 数据仓库 (SQL DW) 的高要求功能现可供预览，它支持 Visual Studio 中的 SQL Server Data Tools (SSDT)！ 开发团队现可通过单一的版本控制基本代码进行协作，并快速将更改部署到世界上的任何实例。 是否有兴趣加入？ 此功能目前以预览版提供！ 可以通过访问 [SQL 数据仓库 Visual Studio SQL Server Data Tools (SSDT) - 预览注册表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)进行注册。 鉴于需求量很大，我们正在对预览进行验收，以确保为客户提供最佳体验。 注册后，我们的目标是在七个工作日内确认你的状态。|
|**行级别安全性已公开发布**|Azure SQL 数据仓库 (SQL DW) 现在支持行级别安全性 (RLS)，增加了保护敏感数据的强大功能。 通过引入 RLS，可以实现安全策略以控制对表中行的访问（例如哪些人员可以访问哪些行）。 RLS 支持这种精确的访问控制，而无需重新设计数据仓库。 由于访问限制逻辑位于数据库层本身，而不是脱离另一应用程序中的数据，因此 RLS 简化了整体安全模型。 RLS 还消除了引入视图以筛选出用于访问控制管理的行的需求。 我们为所有客户提供此企业级安全功能，而用户无需额外付费。|
|**高级的顾问**|其他数据仓库建议和指标使 Azure SQL 数据仓库 (SQL DW) 的高级优化变得更加简单。 Azure 顾问还提供其他高级性能建议供你参考，包括：<br/><br/>1.自适应缓存 - 在应进行缩放以优化缓存利用率时通知用户。<br/>2.表分配 - 确定何时复制表，以减少数据移动，提高工作负载性能。<br/>3.Tempdb - 了解何时缩放和配置资源类，以减少 tempdb 争用。<br/><br/>数据仓库指标与 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) 深度集成，包括增强的可自定义监视图表，以在概览边栏选项卡中实现近实时指标。 监视使用情况或验证和应用数据仓库建议时，无需离开数据仓库概览边栏选项卡即可访问 Azure Monitor 指标。 此外，推出了新的指标（例如 tempdb 和自适应缓存使用率）作为性能建议的补充。|
|**高级集成顾问优化**|通过其他数据仓库建议和指标，以及重新设计的提供 Azure Advisor 和 Azure Monitor 的集成体验的门户概述边栏选项卡，Azure SQL 数据仓库 (SQL DW) 的高级优化变得更加简单了。|
|**加速的数据库恢复 (ADR)**|Azure SQL 数据仓库加速的数据库恢复 (ADR) 现在处于公共预览状态。 ADR 是一项新的 SQL Server 引擎功能，通过完全从头开始重新设计当前的恢复过程，极大地提高了数据库可用性（尤其是在存在长时间运行事务的情况下）。 ADR 的主要优点是快速且一致的数据库恢复和即时事务回滚。|
|**Azure Monitor 诊断日志**|SQL 数据仓库 (SQL DW) 现可通过直接与 Azure Monitor 诊断日志集成来增强对分析工作负载的见解。 这项新功能使开发者可分析长时段内的工作负载行为，并针对查询优化或容量管理作出明智决策。 我们现在通过 [Azure Monitor 诊断日志](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引入了外部日志记录过程，这些日志提供了有关数据仓库工作负载的其他见解。 只需单击一下按钮，即可使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 为历史查询性能故障排除功能配置诊断日志。 Azure Monitor 诊断日志支持通过将日志保存到存储帐户以进行审核来自定义保留期，将日志流式传输到事件中心以获得近实时遥测见解，以及通过日志查询使用 Log Analytics 来分析日志。 诊断日志由数据仓库的遥测视图组成，等效于 SQL 数据仓库最常用的性能故障排除 DMV。 对于此初始版本，我们为以下系统动态管理视图启用了视图：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**列存储内存管理**|随着压缩列存储行组数量的增加，管理这些行组的内部列段元数据所需的内存也会增加。  因此，针对某些列存储动态管理视图 (DMV) 执行的查询和查询性能可能会降级。  此版本中的改进旨在优化这些情况下的内部元数据大小，从而改善此类查询的体验并提高此类查询的性能。|
|**Azure 数据湖存储第 2 代集成 (GA**|Azure SQL 数据仓库 (SQL DW) 现在已与 Azure Data Lake Storage Gen2 进行了本机集成。 客户现在使用外部表将数据从 ABFS 加载到 SQL DW。 此功能使客户能够与 Data Lake Storage Gen2 中的 data lake 集成。|
|**值得注意的 Bug**|DW2000 和的详细信息-数据仓库上的小型资源类中的 Parquet 故障 CETAS 此修补程序会正确标识到 Parquet 代码路径中创建外部表为空引用。<br/><br/>标识列的值可能会丢失某些 CTAS 操作中标识列的值可能不会保留时 CTASed 到另一个表。 在中报告[博客](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)。<br/><br/>在某些情况下，当会话终止时仍在运行查询-如果查询仍在运行时终止会话，这一修补将触发 InvalidOperationException 时的内部错误。<br/><br/>（部署在 2018 年 11 月）尝试加载多个小型文件从 ADLS (Gen1) 使用 Polybase 时，客户遇到性能不佳。 系统的性能已在 AAD 安全令牌验证期间出现瓶颈。 通过启用安全令牌的缓存来缓解性能问题。 |
| | |

## <a name="next-steps"></a>后续步骤

- [创建 SQL 数据仓库](./create-data-warehouse-portal.md)

## <a name="more-information"></a>详细信息

- [博客 - Azure SQL 数据仓库](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客户顾问团队博客](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客户成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 词汇表](../azure-glossary-cloud-terminology.md)
