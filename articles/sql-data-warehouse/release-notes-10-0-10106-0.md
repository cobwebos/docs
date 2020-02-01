---
title: 发行说明
description: Azure SQL 数据仓库的发行说明。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 1/14/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
ms.openlocfilehash: 917a698840850182d2d41ef780ba01d948e11c2f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896372"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL 数据仓库发行说明

本文总结了[AZURE Sql 数据仓库](sql-data-warehouse-overview-what-is.md)（AZURE sql DW）的最新版本中的新功能和改进。 本文还列出了与发布无关但在相同时间范围内发布的重要内容更新。 有关对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-sql-data-warehouse-version"></a>查看 Azure SQL 数据仓库版本

在所有区域中推出新功能后，请查看部署到实例的版本和最新的 Azure SQL DW 发行说明，了解功能可用性。 若要查看 Azure SQL DW 版本，请通过 SQL Server Management Studio （SSMS）连接到数据仓库，并运行 `SELECT @@VERSION;` 以返回当前版本的 Azure SQL DW。

示例输出：

![SQL 数据仓库版本](./media/release-notes/t47-1-version.png)

使用标识的版本来确认哪些版本已应用到 Azure SQL DW。

## <a name="january-2020"></a>2020年1月

| 服务改进 | 详细信息 |
| --- | --- |
|**工作负荷管理门户指标（预览）**|在10月过去，随着预览版的[工作负荷隔离](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)，用户可以创建自己的[工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)，以有效地管理系统资源并确保满足业务 sla。  作为 Azure Synapse Analytics 总体[工作负荷管理](/azure/sql-data-warehouse/sql-data-warehouse-workload-management)增强的一部分，现已推出新的[工作负荷管理监视指标](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor)。</br> </br> 监视工作负荷现在具有以下指标的更多见解： </br> -有效 cap 资源百分比  </br> -有效的最小资源百分比 </br> -工作负荷组活动查询 </br> -按最大资源百分比分配的工作负荷组 </br> -按系统百分比分配工作负荷组 </br> -工作负荷组查询超时 </br> -工作负荷组排队查询 </br></br> 使用这些指标来识别配置有使用不足的工作负荷[隔离](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#underutilized-workload-isolation)的[工作负荷组瓶颈](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#workload-group-bottleneck)或工作负荷组。  可在允许按工作负荷组拆分的 Azure 门户中使用这些指标。  筛选你喜爱的图形并将其固定到仪表板，以便快速访问见解。|
|**门户监视指标**| 以下指标已添加到门户，用于监视总体查询活动： </br> -活动查询 </br> -排队查询 </br> </br>这些指标连同 "[监视资源使用情况" 和 "查询活动" 文档](/azure/sql-data-warehouse/sql-data-warehouse-concept-resource-utilization-query-activity)中的现有指标一起介绍。|
|**产品名称**|2020年6月，SELECT @@VERSION 返回的产品名称将从 Microsoft Azure SQL 数据仓库更改为 Azure Synapse Analytics。 我们将在我们的发行说明中发布计划。 此更改与在应用程序代码中从 SELECT @@VERSION 的结果分析产品名称的客户相关。 若要避免由于产品品牌重塑而导致应用程序代码更改，请使用以下命令查询数据库产品名称和版本的 SERVERPROPERTY。 </br> 返回 DW 版本号 XX。X XXXXX。X （无产品名称）使用此命令： SELECT SERVERPROPERTY （' ProductVersion '） </br> 若要返回引擎版本，请使用以下命令，该命令返回 Azure Synapse Analytics （以前称为 SQL 数据仓库）6：选择 SERVERPROPERTY （"EngineEdition"）|

## <a name="october-2019"></a>2019 年 10 月

| 服务改进 | 详细信息 |
| --- | --- |
|**复制（预览版）**|我们非常高兴地宣布使用简单灵活的 COPY 语句来实现数据引入的公共预览版。 只有一条语句，你现在可以无缝引入具有更多灵活性的数据，无需高权限用户。 有关详细信息，请参阅[复制命令文档](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)。|
|**工作负荷隔离（预览）**|为了在客户变得大众化其数据仓库时为其提供支持，我们宣布推出新的智能工作负荷管理功能。 利用新的[工作负荷隔离](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)功能，你可以管理异类工作负荷的执行，同时提供对数据仓库资源的灵活性和控制。 这会提高执行可预测性，并增强满足预定义 Sla 的能力。 </br>除了工作负荷隔离外，现在还可对[工作负荷分类](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)使用其他选项。  除了登录分类以外， [Create 工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)语法还提供根据查询标签、会话上下文和当天的时间对请求进行分类的功能。|
|**预测（预览）**|你现在可以对数据仓库中的机器学习模型进行评分，以避免对大型和复杂的数据移动产生需求。 T-sql PREDICT 函数依赖开放式模型框架，并使用数据和机器学习模型作为输入来生成预测。
|**SSDT CI/CD （GA）**|今天，我们很高兴地宣布 SQL 数据仓库的[最高请求功能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)（SQL SERVER DATA TOOLS （SSDT）数据库项目的公开上市。 此版本包括对 SSDT 与 Visual Studio 2019 的支持，以及与 Azure DevOps 的本机平台集成，为企业级部署提供内置持续集成和部署（CI/CD）功能。 |
|**具体化视图（GA）**|具体化视图会保留从视图定义查询返回的数据，并自动作为基础表中的数据更改更新。 它提高了复杂查询的性能（通常是带有联接和聚合的查询），同时提供简单的维护操作。 有关详细信息，请参阅[对具体化视图进行性能优化](/azure/sql-data-warehouse/performance-tuning-materialized-views)。  安装[SQL Server Management Studio 18.4 或更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)，用于编写具体化视图的脚本。|
|**动态数据掩码（GA）**|动态数据掩码（DDM）可防止对数据仓库中敏感数据的未经授权的访问，方法是根据你定义的掩码规则在查询结果中实时对其进行模糊处理。 有关详细信息，请参阅[SQL 数据库动态数据屏蔽](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**已提交读快照隔离（GA）**|您可以使用 ALTER database 来启用或禁用用户数据库的快照隔离。 若要避免对当前工作负荷产生影响，可能需要在数据库维护时段设置此选项，或等待，直到没有其他活动连接到数据库。 有关详细信息，请参阅[Alter database set 选项](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**有序聚集列存储索引（GA）**|列存储是用于存储和高效查询大量数据的关键启用码。 排序的聚集列存储索引通过启用有效段消除，进一步优化查询执行。   有关详细信息，请参阅[具有顺序聚集列存储索引的性能优化](/azure/sql-data-warehouse/performance-tuning-ordered-cci)。|
|**结果集缓存（GA）**|启用结果集缓存后，Azure SQL 数据仓库会自动缓存用户数据库中的查询结果以供重复使用。 这允许后续查询执行直接从持久缓存中获取结果，因此不需要重新计算。 结果集缓存提高了查询性能，并减少了计算资源的使用。 此外，使用缓存的结果集的查询不使用任何并发槽，因此不会对现有并发限制进行计数。 为安全，如果用户具有与创建缓存结果相同的数据访问权限，则用户只能访问缓存的结果。 有关详细信息，请参阅[性能优化和结果集缓存](/azure/sql-data-warehouse/performance-tuning-result-set-caching)。 适用于版本10.0.10783.0 或更高版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服务改进 | 详细信息 |
| --- | --- |
|**Azure 专用链接（预览）**|使用[Azure 专用链接](https://azure.microsoft.com/blog/announcing-azure-private-link/)，你可以在虚拟网络（VNet）中创建专用终结点，并将其映射到 AZURE SQL DW。 然后，这些资源可通过 VNet 中的专用 IP 地址进行访问，从而能够通过 Azure ExpressRoute 专用对等互连和/或 VPN 网关从本地连接。 总体而言，这可以简化网络配置，而无需将其打开为公共 IP 地址。 这还可以防范数据渗透的风险。 有关更多详细信息，请参阅[概述](/azure/private-link/private-link-overview)和[SQL DW 文档](/azure/sql-database/sql-database-private-endpoint-overview)。|
|**数据发现 & 分类（GA）**|[数据发现和分类](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/sql-data-warehouse/toc.json)功能现已正式发布。 此功能提供了高级功能，用于**发现、分类、标记 & 保护**数据库中的敏感数据。|
|**Azure Advisor 一键式集成**|SQL 数据仓库现在直接与概述边栏选项卡中的 Azure 顾问建议结合，提供一次单击体验。 你现在可以在 "概述" 边栏选项卡中发现建议，而不是导航到 "Azure 顾问" 边栏选项卡。 [在此处](sql-data-warehouse-concept-recommendations.md)了解有关建议的详细信息。|
|**已提交读快照隔离（预览版）**|您可以使用 ALTER database 来启用或禁用用户数据库的快照隔离。  若要避免对当前工作负荷产生影响，可能需要在数据库维护时段设置此选项，或等待，直到没有其他活动连接到数据库。 有关详细信息，请参阅[Alter database set 选项](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**EXECUTE AS （Transact-sql）**| [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql?view=azure-sqldw-latest)T-sql 支持现在可在 SQL 数据仓库中使用，从而使客户能够将会话的执行上下文设置为指定的用户。|
|**其他 T-sql 支持**|SQL 数据仓库的 T-sql 语言外围应用已扩展为包括对以下内容的支持： </br> - [格式（transact-sql）](/sql/t-sql/functions/format-transact-sql?view=azure-sqldw-latest)</br> - [TRY_PARSE （transact-sql）](/sql/t-sql/functions/try-parse-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CAST （transact-sql）](/sql/t-sql/functions/try-cast-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CONVERT （transact-sql）](/sql/t-sql/functions/try-convert-transact-sql?view=azure-sqldw-latest)</br> - [sys.databases user_token （transact-sql）](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服务改进 | 详细信息 |
| --- | --- |
|**具体化视图（预览）**|具体化视图会保留从视图定义查询返回的数据，并自动作为基础表中的数据更改更新。 它提高了复杂查询的性能（通常是带有联接和聚合的查询），同时提供简单的维护操作。 有关详细信息，请参阅： </br> - [创建具体化视图作为 SELECT &#40;transact-sql&#41; ](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [ALTER 具体化视图&#40;transact-sql&#41; ](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> - [AZURE Sql 数据仓库中支持的 t-sql 语句](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**其他 T-sql 支持**|SQL 数据仓库的 T-sql 语言外围应用已扩展为包括对以下内容的支持： </br> 时区 - [（transact-sql）](/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG （transact-sql）](/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**结果集缓存（预览）**|添加了 DBCC 命令以管理以前公布的结果集缓存。 有关详细信息，请参阅： </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 另请参阅[sys.databases. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)中的新 result_set_cache 列，该列显示执行的查询使用结果集缓存的时间。|
|**有序聚集列存储索引（预览）**|新列 column_store_order_ordinal，已添加到[index_columns sys.databases](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest) ，以标识有序聚集列存储索引中列的顺序。|

## <a name="may-2019"></a>2019 年 5 月

| 服务改进 | 详细信息 |
| --- | --- |
|**动态数据掩码（预览版）**|动态数据掩码（DDM）可防止对数据仓库中敏感数据的未经授权的访问，方法是根据你定义的掩码规则在查询结果中实时对其进行模糊处理。 有关详细信息，请参阅[SQL 数据库动态数据屏蔽](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**工作负荷重要性现已正式发布**|工作负荷管理分类和重要性提供影响查询运行顺序的能力。 有关工作负荷重要性的详细信息，请参阅文档中的[分类](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)概述文章。 同时查看[CREATE 工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)文档。<br/><br/>请参阅以下视频中的工作负荷重要性：<br/> -[工作负荷管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作负荷管理方案](https://www.youtube.com/embed/_2rLMljOjw8)|
|**其他 T-sql 支持**|SQL 数据仓库的 T-sql 语言外围应用已扩展为包括对以下内容的支持： </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 函数**|现在，业务分析师可以使用 Azure 数据仓库中的以下新 JSON 函数，使用熟悉的 T-sql 语言来查询和操作格式设置为 JSON 数据的文档：</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**结果集缓存（预览）**|结果集缓存可实现即时查询响应时间，同时缩短业务分析人员和报告用户的洞察力。 有关详细信息，请参阅：</br> - [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 选项（transact-sql）](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [设置结果集缓存（transact-sql）](/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET 语句（transact-sql）](/sql/t-sql/statements/set-statements-transact-sql)</br> - [sys.databases （transact-sql）](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**有序聚集列存储索引（预览）**|列存储是用于存储和高效查询大量数据的关键启用码。 对于每个表，它将传入的数据划分为行组，行组中的每一列构成磁盘上的一个段。  排序的聚集列存储索引通过启用有效段消除，进一步优化查询执行。   有关详细信息，请参阅：</br> -  [CREATE TABLE （AZURE SQL 数据仓库）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  [创建列存储索引（transact-sql）](/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest)。|

## <a name="march-2019"></a>2019 年 3 月

| 服务改进 | 详细信息 |
| --- | --- |
|**& 分类的数据发现**|数据发现 & 分类现已在 Azure SQL 数据仓库的公共预览版中提供。 保护敏感数据和客户的隐私非常重要。 随着业务和客户数据资产的增长，发现、分类和保护数据变得难以管理。 通过 Azure SQL 数据仓库本机引入的数据发现和分类功能有助于提高数据的可管理性。 此功能的总体优点如下：<br/>&bull; &nbsp; 满足数据隐私标准和法规遵从性要求。<br/>&bull; &nbsp; 限制访问和强化包含高度敏感数据的数据仓库的安全性。<br/>&bull; 对敏感数据的异常访问 &nbsp; 监视和警报。<br/>&bull; Azure 门户上中央仪表板中的敏感数据 &nbsp; 可视化。 </br></br>数据发现 & 分类适用于所有 Azure 区域中的 Azure SQL 数据仓库，它是高级数据安全的一部分，包括漏洞评估和威胁检测。 有关数据发现 & 分类的详细信息，请参阅[博客文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和我们的联机[文档](/azure/sql-database/sql-database-data-discovery-and-classification)。|
|**按汇总分组**|汇总现在是 Azure 数据仓库中支持的 GROUP BY 选项。   "按汇总分组" 为列表达式的每个组合创建一个组。 分组依据还将结果 "汇总" 到小计和总计中。 GROUP BY 函数按从右到左的顺序处理，减少了用于创建组和聚合的列表达式的数目。  列顺序会影响汇总输出，并会影响结果集中的行数。<br/><br/>有关组按汇总的详细信息，请参阅[GROUP by （transact-sql）](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**提高了 DWU 和 CPU 门户指标的准确性**|SQL 数据仓库大大增强了 Azure 门户中的指标准确性。  此版本包含对 CPU 和 DWU 使用的指标定义的修复，以正确反映所有计算节点上的工作负荷。 在此修复之前，报主机并且度量值。 预计会在 Azure 门户中看到 DWU 使用情况和 CPU 指标的增长。 |
|**行级别安全性**|我们引入了2017年11月的行级安全功能。 现在，我们还将此支持扩展到外部表。 此外，我们还添加了对在定义安全筛选器谓词所需的内联表值函数（内联 Tvf）中调用非确定性函数的支持。 通过此添加，可以在安全筛选器谓词中指定 IS_ROLEMEMBER （）、USER_NAME （）等。 有关详细信息，请参阅[行级别安全性文档](/sql/relational-databases/security/row-level-security)中的示例。|
|**其他 T-sql 支持**|SQL 数据仓库的 T-sql 语言外围应用已扩展为包括对[STRING_SPLIT （transact-sql）](/sql/t-sql/functions/string-split-transact-sql)的支持。
|**查询优化器增强功能** |查询优化是任何数据库的关键组件。 最佳选择如何最好地执行查询会产生显著的改进。  在分布式环境中执行复杂的分析查询时，执行的操作的数目很重要。 通过生成更好的质量计划来增强查询性能。 这些计划最大程度地减少了代价高昂的数据传输操作和冗余计算，如重复的子查询。 有关详细信息，请参阅此 Azure SQL 数据仓库[博客文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019年1月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**返回顺序（按优化）**|选择 。在此版本中，ORDER BY 查询可以提高性能。   现在，所有计算节点都会将其结果发送到单个计算节点。 此节点合并结果并对结果进行排序，并将结果返回给用户。  如果查询结果集包含大量行，则通过单个计算节点合并会使性能得到显著提升。 之前，查询执行引擎会对每个计算节点的结果进行排序。 结果会流式传输到控制节点。 然后，控件节点会合并结果。|
|**PartitionMove 和 BroadcastMove 的数据移动增强功能**|在 Azure SQL 数据仓库 Gen2 中，ShuffleMove 类型的数据移动步骤使用即时数据移动技术。  有关详细信息，请参阅[性能增强博客](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 在此版本中，PartitionMove 和 BroadcastMove 现在由相同的即时数据移动技术提供支持。 使用这些类型的数据移动步骤的用户查询将以改善的性能运行。 若要利用这些性能改进，无需更改代码。|
|**显著的 Bug**|Azure SQL 数据仓库版本不正确-`SELECT @@VERSION` 可能返回不正确的10.0.9999.0 版本。 当前版本的正确版本为10.0.10106.0。 已报告此 bug，正在查看。
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|无 | |
| | |

## <a name="december-2018"></a>2018年12月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**虚拟网络服务终结点现已正式发布**|此版本包括所有 Azure 区域中的 Azure SQL 数据仓库的虚拟网络（VNet）服务终结点的公开上市。 使用 VNet 服务终结点，你可以从虚拟网络中的给定子网或子网集将连接隔离到逻辑服务器。 从 VNet 到 Azure SQL 数据仓库的流量始终保留在 Azure 主干网络中。 此直接路由将优先于通过虚拟设备或本地获取 Internet 流量的任何特定路由。 通过服务终结点对虚拟网络访问不收取额外的费用。 适用于[AZURE SQL 数据仓库](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的当前定价模型按原样应用。<br/><br/>在此版本中，我们还通过[Azure Blob 文件系统](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver)（ABFS）驱动程序启用了 PolyBase 到[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) （ADLS）的连接。 Azure Data Lake Storage Gen2 将分析数据的完整生命周期所需的所有质量引入到 Azure 存储中。 聚合了两个现有 Azure 存储服务、Azure Blob 存储和 Azure Data Lake Storage Gen1 的功能。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)中的功能（如文件系统语义、文件级安全性和规模）与[Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)中的低成本、分层存储和高可用性/灾难恢复功能相结合。<br/><br/>使用 Polybase，还可以将数据从受保护的 Azure 存储导入到 VNet 中的 azure SQL 数据仓库。 同样，也支持通过 Polybase 将数据从 Azure SQL 数据仓库导出到保护到 VNet 的 Azure 存储。<br/><br/>有关 Azure SQL 数据仓库中的 VNet 服务终结点的详细信息，请参阅[博客文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文档](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。|
|**自动性能监视（预览版）**|[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)现已在 Azure SQL 数据仓库预览版中使用。 查询存储旨在通过跟踪查询、查询计划、运行时统计信息和查询历史记录来帮助你监视数据仓库的活动和性能，从而帮助你进行查询性能故障排除。 查询存储是一组内部存储和动态管理视图（Dmv），可让你：<br/><br/>&bull; &nbsp; 识别和优化资源消耗最大的查询<br/>&bull; &nbsp; 确定和改进计划外工作负荷<br/>&bull; &nbsp; 根据统计信息、索引或系统大小的变化（DWU 设置）评估查询性能和对计划的影响<br/>&bull; &nbsp; 查看已执行的所有查询的完整查询文本<br/><br/>查询存储包含三个实际商店：<br/>&bull; &nbsp; 用于保存执行计划信息的计划存储<br/>&bull; &nbsp; 用于保存执行统计信息的运行时统计信息存储<br/>&bull; &nbsp; 等待统计存储保存等待统计信息。<br/><br/>SQL 数据仓库会自动管理这些存储，并在过去7天内 storied 无限制数量的查询，无额外费用。 启用查询存储就像运行 ALTER DATABASE T-sql 语句一样简单： <br/>sql----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------有关 Azure SQL 数据仓库中的查询存储的详细信息，请参阅[使用查询存储监视性能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和查询存储 dmv，如[sys.databases。](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql) 下面是公告发布的[博客文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)。|
|**Azure SQL 数据仓库的更低计算层 Gen2**|Azure SQL 数据仓库 Gen2 现在支持较低的计算层。 客户可能会遇到 Azure SQL 数据仓库领先的性能、灵活性和安全性功能，从 100 cDWU （[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)）开始，并在几分钟内扩展到 30000 cDWU。 从2018年12月开始，客户可以在[区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)中使用更低计算层的 Gen2 性能和灵活性，并在2019期间使用其余区域。<br/><br/>通过删除下一代数据仓库的入口点，Microsoft 将为想要评估安全、高性能数据仓库的所有优点而无需猜测最适合它们的试用环境的价值驱动的客户提供大门。 客户从当前 500 cDWU 入口点开始，最高可为 100 cDWU。 SQL 数据仓库 Gen2 继续支持暂停和恢复操作，并超出了计算灵活性。 Gen2 还支持无限制的列存储存储容量，每个查询的内存为2.5 倍，最高可达128个并发查询和[自适应缓存](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 与 Gen1 上相同的数据仓库单位相比，这些功能平均提供5倍以上的性能。 异地冗余备份是 Gen2 的标准，具有内置的保证数据保护。 当你是时，Azure SQL 数据仓库 Gen2 已准备好进行缩放。|
|**列存储背景合并**|默认情况下，Azure SQL 数据仓库（Azure SQL 数据仓库）以纵栏式格式存储数据，其微分区名为[行组](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。 有时，由于索引生成或数据加载时的内存限制，行组的压缩量可能小于1000000行的最佳大小。 行组可能还会因为删除而产生碎片。 小型或分段的行组会导致更高的内存消耗，并且执行效率低下的查询。 在此版本的 Azure SQL DW 中，列存储后台维护任务合并小型压缩的行组，以更好地利用内存并加速查询执行。
| | |

## <a name="october-2018"></a>2018年10月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**用于数据仓库的 DevOps**|SQL 数据仓库（SQL DW）的高度请求功能现在为预览版，在 Visual Studio 中支持 SQL Server 数据工具（SSDT）！ 开发人员团队现在可以通过单个版本控制的基本代码进行协作，并快速将更改部署到世界上的任何实例。 是否有兴趣加入？ 此功能目前可供预览！ 可以通过访问[SQL 数据仓库 Visual Studio SQL Server Data Tools （SSDT）-预览注册表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)来注册。 考虑到高需求，我们将在预览版中管理验收，以确保客户获得最佳体验。 注册后，我们的目标是在七个工作日内确认状态。|
|**行级别安全性公开上市**|Azure SQL 数据仓库（SQL DW）现在支持行级别安全性（RLS）添加强大的功能来保护敏感数据。 通过引入 RLS，你可以实施安全策略来控制对表中的行的访问，就像在谁可以访问哪些行。 RLS 实现了这一精细的访问控制，无需重新设计数据仓库。 RLS 简化了总体安全模型，因为访问限制逻辑位于数据库层本身中，而不是远离另一个应用程序中的数据。 RLS 还不必引入视图来筛选出访问控制管理的行。 对于我们的所有客户，这种企业级安全功能不会产生额外的费用。|
|**高级顾问**|对于 Azure SQL 数据仓库（SQL DW）的高级优化，只需更简单的数据仓库建议和指标。 通过 Azure 顾问，你可以使用更多的高级性能建议，包括：<br/><br/>1. 自适应缓存-建议在缩放时优化缓存利用率。<br/>2. 表分布–确定复制表以减少数据移动和提高工作负荷性能的时间。<br/>3. Tempdb –了解何时缩放和配置资源类以减少 Tempdb 争用。<br/><br/>数据仓库指标与[Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/)的集成更深入，其中包括 "概述" 边栏选项卡中适用于准实时指标的增强的可自定义监视图表。 当监视使用情况时，你不再必须让 "数据仓库概述" 边栏选项卡访问 Azure Monitor 度量值，或者验证并应用数据仓库建议。 此外，还提供了新的指标，例如 tempdb 和自适应缓存利用率来补充性能建议。|
|**集成顾问的高级优化**|对于 Azure SQL 数据仓库（SQL DW）的高级优化，只需更简单的数据仓库建议和度量值，并重新设计 "门户概述" 边栏选项卡，可提供 Azure Advisor 和 Azure Monitor 的集成体验。|
|**加速数据库恢复（ADR）**|Azure SQL 数据仓库加速数据库恢复（ADR）现已公开预览版。 ADR 是一个新的 SQL Server 引擎，可大大提高数据库的可用性，尤其是在存在长时间运行的事务的情况下，完全重新设计当前恢复过程。 ADR 的主要优势是快速一致的数据库恢复和即时事务回滚。|
|**Azure Monitor 诊断日志**|SQL 数据仓库（SQL DW）现在通过直接与 Azure Monitor 诊断日志集成，来增强分析工作负荷。 这一新功能使开发人员能够在更长的时间段内分析工作负荷行为，并对查询优化或容量管理作出明智的决策。 现在，我们通过[Azure Monitor 诊断日志](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引入了外部日志记录过程，该日志可提供对数据仓库工作负荷的更多深入了解。 只需要单击一次按钮，你就可以使用[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries)配置诊断日志，以获取历史查询性能故障排除功能。 Azure Monitor 诊断日志支持可自定义的保留期，方法是将日志保存到存储帐户进行审核，将日志流式传输到事件中心近乎实时的遥测见解，并能够使用 Log Analytics 分析日志带有日志查询。 诊断日志包含数据仓库的遥测视图，它等效于 SQL 数据仓库的最常用性能故障排除 Dmv。 对于此初始版本，我们已为以下系统动态管理视图启用了视图：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**列存储内存管理**|当压缩列存储行组的数目增加时，管理这些行组的内部列段元数据所需的内存也将增加。  因此，针对某些列存储动态管理视图（Dmv）执行的查询性能和查询可能会下降。  在此版本中进行了改进，以便在这些情况下优化内部元数据的大小，从而提高此类查询的体验和性能。|
|**Azure Data Lake Storage Gen2 集成（GA**|Azure SQL 数据仓库（SQL DW）现已与 Azure Data Lake Storage Gen2 进行本机集成。 现在，客户可以使用 ABFS 中的外部表将数据加载到 SQL DW。 此功能使客户能够与 Data Lake Storage Gen2 中的数据 lake 进行集成。|
|**显著的 Bug**|CETAS Parquet DW2000 和更多数据仓库的小资源类中的故障-此修复正确地标识了 Create External Table As to Parquet code 路径中的空引用。<br/><br/>在某些 CTAS 操作中，标识列值可能会丢失-CTASed 列的值可能在其他表时不会被保留。 在[博客](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中报告。<br/><br/>在某些情况下，在查询仍在运行时终止会话时出现内部故障-如果在查询仍在运行时终止会话，则此修补程序会触发 InvalidOperationException。<br/><br/>（部署于2018年11月）当尝试使用 Polybase 从 ADLS （Gen1）加载多个小文件时，客户遇到的性能不佳。 -AAD 安全令牌验证过程中出现系统性能瓶颈。 通过启用安全令牌的缓存，降低了性能问题。 |
| | |

## <a name="next-steps"></a>后续步骤

- [创建 SQL 数据仓库](./create-data-warehouse-portal.md)

## <a name="more-information"></a>详细信息

- [博客-Azure SQL 数据仓库](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客户顾问团队博客](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [客户成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 术语表](../azure-glossary-cloud-terminology.md)
