---
title: 发行说明
description: Azure 同步分析的发行说明。
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 3/26/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 6d04756bfba965e49a8b51457d2c679eb7bbfd13
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350756"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure 突触分析发行说明

本文总结了 Azure Synapse 中 SQL[分析](sql-data-warehouse-overview-what-is.md)的最新版本中的新功能和改进。 本文还列出了不与此版本直接相关但在同一时间范围内发布的值得注意的内容更新。 有关其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-synapse-version"></a>检查 Azure 突触版本

将新功能部署到所有区域时，请检查部署到实例的版本和最新版本说明，了解功能可用性。 要检查版本，请通过 SQL 服务器管理工作室 （SSMS） 连接到 SQL`SELECT @@VERSION;`池，然后运行以返回当前版本。

使用标识的版本确认哪个版本已应用于 SQL 池。 输出中的日期标识应用于 SQL 池的发布月的月份。

> [!NOTE]
> SELECT 返回的产品名称将从@VERSIONMicrosoft Azure SQL 数据仓库更改为 Azure 同步分析。 我们将在进行更改之前提前发出通知。 此更改与在其应用程序代码中从 SELECT +@VERSION结果解析产品名称的客户相关。 为避免由于产品品牌重塑而更改应用程序代码，请使用这些命令查询服务器属性的数据库产品名称和版本：返回版本号 XX。X.XXXXX.X（没有产品名称）使用此命令： 
> ```
> SELECT SERVERPROPERTY('ProductVersion')
> ```
> 要返回引擎版本，请使用返回 6 的 Azure 突触分析（以前的 SQL 数据仓库）的命令： 
> ```
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="march-2020"></a>2020 年 3 月

| 服务改进                                         | 详细信息                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[可视化工作室 16.6 预览 2](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview) - SQL 服务器数据工具 （SSDT）** | 此版本包括以下改进和修复： </br> </br> - 解决了更改具体视图 （MV） 引用的表导致生成 MV 不支持的"更改视图"语句的问题<br/><br/> - 实现更改，以确保在数据库或项目中存在行级安全对象时，架构比较操作不会失败。 SSDT 当前不支持行级安全对象。  <br/><br/> - SQL Server 对象资源管理器超时阈值已提高，以避免在数据库中列出大量对象时超时<br/><br/> - 优化 SQL Server 对象资源管理器检索数据库对象列表的方式，以减少在填充对象资源管理器时的不稳定性并提高性能 |

## <a name="february-2020"></a>2020 年 2 月

| 服务改进 | 详细信息 |
| --- | --- |
|**数据库兼容性级别（预览）**| 使用此版本，用户现在可以设置数据库的兼容性级别，以获取 SQL 分析引擎的特定版本的 Transact-SQL 语言和查询处理行为。 有关详细信息，请参阅[sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?view=azure-sqldw-latest&branch=pr-en-us-13797) [并更改数据库范围配置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?view=sql-server-ver15)。 |
|**Sp_describe_undeclared_parameters**| 允许用户查看有关 Transact-SQL 批处理中未声明参数的元数据。 有关详细信息，请参阅[sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?view=sql-server-ver15)。|

## <a name="january-2020"></a>2020 年 1 月

| 服务改进 | 详细信息 |
| --- | --- |
|**工作负载管理门户指标（预览）**|随着今年 10 月发布的用于预览的[工作负载隔离](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)，用户可以创建自己的[工作负载组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)，以高效管理系统资源并确保满足业务 SL。  作为 Azure Synapse 分析总体[工作负载管理](/azure/sql-data-warehouse/sql-data-warehouse-workload-management)增强功能的一部分，新的[工作负载管理监视指标](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor)现已可用。</br> </br> 现在，通过以下指标监视工作负荷，可以更深入地洞察： </br> - 有效上限资源百分比  </br> - 有效最小资源百分比 </br> - 工作负载组活动查询 </br> - 按最大资源百分比分配工作负载组 </br> - 按系统百分比分配工作负载组 </br> - 工作负载组查询超时 </br> - 工作负载组排队查询 </br></br> 使用这些指标可以标识使用[不足的工作负载隔离](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#underutilized-workload-isolation)配置[的工作负载组瓶颈](/azure/sql-data-warehouse/sql-data-warehouse-workload-management-portal-monitor#workload-group-bottleneck)或工作负载组。  这些指标可用于 Azure 门户，允许按工作负荷组拆分。  筛选您最喜爱的图形并将其固定到仪表板，以便快速访问见解。|
|**门户监控指标**| 以下指标已添加到门户中，用于监视整个查询活动： </br> - 活动查询 </br> - 排队查询 </br> </br>这些指标与[监视资源利用率和查询活动文档中](/azure/sql-data-warehouse/sql-data-warehouse-concept-resource-utilization-query-activity)的现有指标一起描述。|


## <a name="october-2019"></a>2019 年 10 月

| 服务改进 | 详细信息 |
| --- | --- |
|**Copy（预览版）**|我们很高兴地宣布，现已推出简单灵活 COPY 语句的公共预览版用于数据引入。 现在，只需一条语句就能无缝引入数据并获得更高的灵活性，且无需分配高特权用户。 有关详细信息，请参阅 [COPY 命令文档](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)。|
|**工作负载隔离（预览版）**|为了在客户使其数据仓库大众化时为客户提供支持，我们宣布推出用于智能工作负载管理的新功能。 新的[工作负载隔离](/azure/sql-data-warehouse/sql-data-warehouse-workload-isolation)功能使你能够管理异类工作负载的执行，同时提供灵活性以及对数据仓库资源的控制。 这会提高执行可预测性，并增强满足预定义 SLA 的能力。 </br>除了工作负载隔离外，现在还有其他选项可用于[工作负载分类](/azure/sql-data-warehouse/sql-data-warehouse-workload-classification)。  除了登录分类以外，[创建工作负载分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)语法还提供根据查询标签、会话上下文和时间对请求进行分类的功能。|
|**预测（预览）**|您现在可以在数据仓库中对机器学习模型进行评分，从而避免需要大型和复杂的数据移动。 T-SQLPREDICT 函数依赖于开放式模型框架，并将数据和机器学习模型作为输入来生成预测。
|**SSDT CI/CD （GA）**|今天，我们很高兴地宣布 SQL 分析 – SQL 服务器数据工具 （SSDT） 数据库项目[的最高请求功能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)的通用性。 此版本包括支持具有 Visual Studio 2019 的 SSDT，以及与 Azure DevOps 的本机平台集成，为企业级部署提供内置的持续集成和部署 （CI/CD） 功能。 |
|**具体化视图（正式版）**|具体化视图将保留视图定义查询返回的数据，并自动随着基础表中的数据更改而更新。 它可以提高复杂查询的性能（通常指使用联接和聚合的查询），同时还提供简单的维护操作。 有关详细信息，请参阅[使用具体化视图优化性能](/azure/sql-data-warehouse/performance-tuning-materialized-views)。  安装 [SQL Server Management Studio 18.4 或更高版本](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)可以编写具体化视图的脚本。|
|**动态数据掩码（正式版）**|动态数据掩码 (DDM) 根据定义的掩码规则在查询结果中即时将数据仓库中的敏感数据模糊化，以防止有人未经授权访问这些数据。有关详细信息，请参阅 [SQL 数据库动态数据掩码](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**读取提交的快照隔离（正式版）**|可以使用 ALTER DATABSE 来为用户数据库启用或禁用快照隔离。 若要避免对当前工作负荷产生影响，可以在数据库维护期间设置此选项，或等到该数据库没有其他活动的连接时设置此选项。 有关详细信息，请参阅[更改数据库集选项](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**有序聚集列存储索引（正式版）**|若要存储和有效查询大量数据，列存储至关重要。 排序聚集列存储索引支持有效的段消除，从而进一步优化了查询执行。有关详细信息，请参阅[使用有序聚集列存储索引优化性能](/azure/sql-data-warehouse/performance-tuning-ordered-cci)。|
|**结果集缓存（正式版）**|启用结果集缓存后，SQL Analytics 会自动缓存用户数据库中的查询结果，以便重复使用。 这样，后续的查询执行就能直接从持久性缓存中获取结果，因此无需重新计算。 结果集缓存提高了查询性能，并减少了计算资源的用量。 此外，使用缓存结果集的查询不会占用任何并发槽，因此不会计入现有的并发限制。 出于安全考虑，如果访问方用户的数据访问权限与创建缓存结果的用户相同，则访问方用户只能访问缓存的结果。 有关详细信息，请参阅[使用结果集缓存优化性能](/azure/sql-data-warehouse/performance-tuning-result-set-caching)。 适用于 10.0.10783.0 或更高版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服务改进 | 详细信息 |
| --- | --- |
|**Azure 专用链接（预览）**|使用[Azure 专用链接](https://azure.microsoft.com/blog/announcing-azure-private-link/)，可以在虚拟网络 （VNet） 中创建专用终结点并将其映射到 SQL 池。 然后，这些资源可通过 VNet 中的专用 IP 地址进行访问，通过 Azure ExpressRoute 专用对等互连和/或 VPN 网关实现从本地连接。 总体而言，这简化了网络配置，无需将网络配置打开到公共 IP 地址。 这也能够防止数据渗漏风险。 有关详细信息，请参阅[概述](/azure/private-link/private-link-overview)和[SQL 分析文档](/azure/sql-database/sql-database-private-endpoint-overview)。|
|**数据发现和分类（正式版）**|[数据发现和分类](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/sql-data-warehouse/toc.json)功能现已推出正式版。 此功能提供高级功能用于**发现、分类、标记和保护**数据库中的敏感数据。|
|**Azure 顾问一键式集成**|Azure Synaps 中的 SQL 分析现在直接与概述栏选项卡中的 Azure 顾问建议集成，同时提供一键式体验。 现在可以在概述边栏选项卡中发现建议，而无需导航到 Azure 顾问边栏选项卡。 在[此处](sql-data-warehouse-concept-recommendations.md)详细了解建议。|
|**读取提交的快照隔离（预览版）**|可以使用 ALTER DATABSE 来为用户数据库启用或禁用快照隔离。  若要避免对当前工作负荷产生影响，可以在数据库维护期间设置此选项，或等到该数据库没有其他活动的连接时设置此选项。 有关详细信息，请参阅[更改数据库集选项](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)。|
|**EXECUTE AS (Transact-SQL)**| [执行为](/sql/t-sql/statements/execute-as-transact-sql?view=azure-sqldw-latest)T-SQL 支持现在在 SQL Analytics 中提供，使客户能够将会话的执行上下文设置为指定的用户。|
|**其他 T-SQL 支持**|SQL 分析的 T-SQL 语言表面积已扩展，以包括对以下方面的支持： </br> - [FORMAT（转用-SQL）](/sql/t-sql/functions/format-transact-sql?view=azure-sqldw-latest)</br> - [TRY_PARSE（转算-SQL）](/sql/t-sql/functions/try-parse-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CAST（转算-SQL）](/sql/t-sql/functions/try-cast-transact-sql?view=azure-sqldw-latest)</br> - [TRY_CONVERT（转算-SQL）](/sql/t-sql/functions/try-convert-transact-sql?view=azure-sqldw-latest)</br> - [系统user_token（转算-SQL）](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服务改进 | 详细信息 |
| --- | --- |
|**具体化视图（预览版）**|具体化视图将保留视图定义查询返回的数据，并自动随着基础表中的数据更改而更新。 它可以提高复杂查询的性能（通常指使用联接和聚合的查询），同时还提供简单的维护操作。 有关详细信息，请参阅： </br> - [在"转换-SQL&#41;&#40;选择时创建具体化视图](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)</br> - [更改具体化视图&#40;转算-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?view=azure-sqldw-latest) </br> - [SQL 分析中支持的 T-SQL 语句](/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-statements)|
|**其他 T-SQL 支持**|SQL 分析的 T-SQL 语言表面积已扩展，以包括对以下方面的支持： </br> - [时区（转算-SQL）](/sql/t-sql/queries/at-time-zone-transact-sql?view=azure-sqldw-latest)</br> - [STRING_AGG（转算-SQL）](/sql/t-sql/functions/string-agg-transact-sql?view=azure-sqldw-latest)|
|**结果集缓存（预览版）**|添加了 DBCC 命令以管理之前公布的结果集缓存。 有关详细信息，请参阅： </br> - [dbCC DROPRESULTCACHE&#40;转算-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)  </br> - [DBCC 显示，&#40;转算-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest) </br></br> 另请参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) 中新的 result_set_cache 列，该列显示执行的查询何时使用结果集缓存。|
|**排序聚集列存储索引（预览版）**|新列 column_store_order_ordinal 已添加到 [sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?view=azure-sqldw-latest)，以标识已排序聚集列存储索引中列的顺序。|

## <a name="may-2019"></a>2019 年 5 月

| 服务改进 | 详细信息 |
| --- | --- |
|**动态数据掩码（预览版）**|动态数据掩码 (DDM) 根据定义的掩码规则在查询结果中即时将数据仓库中的敏感数据模糊化，以防止有人未经授权访问这些数据。有关详细信息，请参阅 [SQL 数据库动态数据掩码](/azure/sql-database/sql-database-dynamic-data-masking-get-started)。|
|**工作负荷重要性现已推出正式版**|工作负荷管理分类和重要性可以影响查询的运行顺序。 有关工作负荷重要性的详细信息，请参阅文档中的[分类](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)概述文章。 另请查看[创建工作负荷分类器](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)文档。<br/><br/>请参阅以下视频中的操作中的工作负载重要性：<br/> -[工作负载管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作负载管理方案](https://www.youtube.com/embed/_2rLMljOjw8)|
|**其他 T-SQL 支持**|SQL 分析的 T-SQL 语言表面积已扩展，以包括对以下方面的支持： </br> - [修剪](/sql/t-sql/functions/trim-transact-sql?view=azure-sqldw-latest)|
|**JSON 函数**|业务分析人员现在可以使用熟悉的 T-SQL 语言使用以下新的 JSON 函数查询和操作格式化为 JSON 数据的文档：</br> - [伊斯伊森](/sql/t-sql/functions/isjson-transact-sql?view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?view=azure-sqldw-latest)</br> - [迪维森](/sql/t-sql/functions/openjson-transact-sql?view=azure-sqldw-latest)|
|**结果集缓存（预览版）**|使用结果集缓存可以立即获得查询响应，同时减少业务分析师和报告用户获得见解所需的时间。 有关详细信息，请参阅：</br> - [交流数据库（转算-SQL）](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 选项 (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)</br> - [设置结果设置缓存（转算-SQL）](/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)</br> - [SET 语句 (Transact-SQL)](/sql/t-sql/statements/set-statements-transact-sql)</br> - [系统数据库（转用-SQL）](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?view=azure-sqldw-latest)|
|**排序聚集列存储索引（预览版）**|若要存储和有效查询大量数据，列存储至关重要。 对于每个表，列存储会将传入的数据划分为行组，行组的每个列构成了磁盘上的一个段。  排序聚集列存储索引支持有效的段消除，从而进一步优化了查询执行。有关详细信息，请参阅：</br> -  [创建表（SQL 分析）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest)</br> -  [创建列列表存储索引 （转算 SQL）](/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest)。|

## <a name="march-2019"></a>2019 年 3 月

| 服务改进 | 详细信息 |
| --- | --- |
|**数据发现和分类**|数据发现&分类现已在 SQL 分析的公共预览版中提供。 保护敏感数据和客户隐私至关重要。 随着企业和客户数据资产的不断增长，发现、分类和保护数据将越来越复杂。 我们使用 SQL Analytics 本机引入的数据发现和分类功能有助于保护您的数据更易于管理。 此功能的总体优点包括：<br/>&bull;&nbsp;满足数据隐私标准和法规合规性要求。<br/>&bull;&nbsp;限制对包含高度敏感数据的数据仓库的访问并强化安全性。<br/>&bull;&nbsp;监视和警报对敏感数据的异常访问。<br/>&bull;&nbsp; Azure 门户上的中央仪表板中敏感数据的可视化。 </br></br>数据发现&分类可用于所有 Azure 区域中的 SQL 分析，它是高级数据安全的一部分，包括漏洞评估和威胁检测。 有关数据发现和分类的详细信息，请参阅[此博客文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和[联机文档](/azure/sql-database/sql-database-data-discovery-and-classification)。|
|**GROUP BY ROLLUP**|在 Azure Synaps 中 SQL 分析中，ROLLUP 现在是受支持的 GROUP BY 选项。   GROUP BY ROLLUP 可为每个列表达式组合创建一个组。 GROUP BY 还可将结果“汇总”为小计和总计。 GROUP BY 函数的处理顺序为从右到左，减少了创建组和聚合所依据的列表达式数目。  列的顺序会影响 ROLLUP 的输出，而且可能会影响结果集内的行数。<br/><br/>有关 GROUP BY ROLLUP 的详细信息，请参阅 [GROUP BY (Transact-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?view=azure-sqldw-latest)
|**改进了已用 DWU 和 CPU 门户指标的准确性**|SQL 分析可显著提高 Azure 门户中的指标准确性。  此版本包含 CPU 和已用 DWU 指标定义的修复，可正确反映所有计算节点中的工作负荷。 在推出此项修复之前，指标值的报告程度不足。 在 Azure 门户中，已用 DWU 和 CPU 指标预期会增大。 |
|**行级安全性**|早在 2017 年 11 月，我们就引入了行级别安全性功能。 现在，我们已将此支持扩展到外部表。 此外，我们还添加了对在定义安全筛选器谓词所需的内联表值函数（内联 TVF） 中调用非确定性函数的支持。 借助此项新增功能，可以在安全筛选器谓词中指定 IS_ROLEMEMBER()、USER_NAME() 等函数。 有关详细信息，请参阅[行级别安全性文档](/sql/relational-databases/security/row-level-security)中的示例。|
|**更多 T-SQL 支持**|SQL 分析的 T-SQL 语言表面积已扩展为包括对[STRING_SPLIT （Transact-SQL）](/sql/t-sql/functions/string-split-transact-sql)的支持。
|**查询优化器增强** |查询优化是任何数据库的关键组成部分。 在如何执行查询方面做出最佳选择可以得到明显的改进。  在分布式环境中执行复杂的分析查询时，执行的操作数目非常关键。 生成更好的质量计划可以增强查询性能。 这些计划可以尽量减少高开销的数据传输操作和冗余计算，例如重复的子查询。 有关详细信息，请参阅此 Azure Synapse[博客文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
| | |

## <a name="january-2019"></a>2019 年 1 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**返回结果时的排序依据优化**|在此版本中，SELECT…ORDER BY 的性能已得到大幅提升。   现在，所有计算节点会将其结果发送到单个计算节点。 此节点会合并结果并将其排序，然后将其返回给用户。  当查询结果集包含大量的行时，通过单个计算节点进行合并可以显著提升性能。 以前，查询执行引擎会在每个计算节点上将结果排序。 然后将结果流式传输到控制节点。 然后，控制节点将合并结果。|
|**PartitionMove 和 BroadcastMove 的数据移动增强**|在 SQL 分析中，使用随机移动类型的数据移动步骤，使用即时数据移动技术。  有关详细信息，请参阅[性能增强博客文章](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)。 在此版本中，PartitionMove 和 BroadcastMove 以相同的即时数据移动技术为后盾。 使用这些类型的数据移动步骤的用户查询可以提升运行性能。 无需更改代码便可利用这些性能改进。|
|**值得注意的 Bug**|不正确的 Azure Synapse`SELECT @@VERSION`版本 - 可能会返回不正确的版本 10.0.9999.0。 当前版本的正确版本是 10.0.10106.0。 此 bug 已报告，正在评审。
| | |

### <a name="documentation-improvements"></a>文档改进

| 文档改进 | 详细信息 |
| --- | --- |
|none | |
| | |

## <a name="december-2018"></a>2018 年 12 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**虚拟网络服务终结点公开发布版**|此版本包括所有 Azure 区域中 Azure 突触中 SQL 分析的虚拟网络 （VNet） 服务终结点的通用性。 使用 VNet 服务终结点，可从虚拟网络中的给定子网或子网集隔离与逻辑服务器的连接。 从 VNet 到 Azure 突触的流量将始终保留在 Azure 主干网络内。 这种直接路由将优先于通过虚拟设备或本地访问 Internet 流量的任何特定路由。 通过服务终结点访问虚拟网络无需额外付费。 [Azure 突触](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)的当前定价模型可如现在适用。<br/><br/>在此版本中，我们还允许使用 [Azure Blob 文件系统](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-abfs-driver) (ABFS) 驱动程序通过 PolyBase 连接到 [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (ADLS)。 Azure Data Lake Storage Gen2 为 Azure 存储提供了分析数据的整个生命周期所需的所有特性。 两个现有 Azure 存储服务（Azure Blob 存储和 Azure Data Lake Storage Gen1）的功能已聚合。 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index) 的功能（例如文件系统语义、文件级安全性和规模）与 [Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)中的低成本分层存储和高可用性/灾难恢复功能进行了组合。<br/><br/>使用 Polybase 还可以将数据导入 Azure 同步器中的 SQL 分析，从保护到 VNet 的 Azure 存储。 同样，通过 Polybase 支持将数据从 Azure 突触导出到保护到 VNet 的 Azure 存储。<br/><br/>有关 Azure Synaps 中 VNet 服务终结点的详细信息，请参阅[博客文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文档](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json)。|
|**自动性能监视（预览版）**|[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?view=sql-server-2017)现在在 Azure Synaps 中的 SQL 分析中的预览版中可用。 查询存储旨在帮助你进行查询性能故障诊断，可以通过跟踪查询、查询计划、运行时统计信息和查询历史记录来监视数据仓库的活动和性能。 查询存储是一组内部存储和动态管理视图 (DMV)，用于执行以下操作：<br/><br/>&bull;&nbsp;识别和调整使用资源的顶级查询<br/>&bull;&nbsp;确定和改进计划外工作负载<br/>&bull;&nbsp;通过统计信息、索引或系统大小的更改（DWU 设置）评估查询性能和对计划的影响<br/>&bull;&nbsp;查看执行的所有查询的完整查询文本<br/><br/>查询存储包含三个实际的存储：<br/>&bull;&nbsp;用于持久化执行计划信息的计划存储<br/>&bull;&nbsp;用于持久化执行统计信息的运行时统计信息存储<br/>&bull;&nbsp;等待统计信息存储，用于持久等待统计信息。<br/><br/>Azure Synapse 中的 SQL 分析会自动管理这些存储，并提供无限数量的查询，这些查询在过去七天内无需支付额外费用。 启用查询存储很简单，只需运行 ALTER DATABASE T-SQL 语句即可： <br/>sql ----ALTER 数据库 [数据库名称] 设置QUERY_STORE = on;------- 有关查询存储的详细信息，请参阅文章，[使用查询存储监视性能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)，以及查询存储 DMV，如[sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql)。 这是宣告发布情况的[博客文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)。|
|**用于 SQL 分析的较低计算层**|Azure Synaps 中的 SQL 分析现在支持较低的计算层。 客户可以体验 Azure Synapse 领先的性能、灵活性和安全功能，从 100 cDWU（[数据仓库单元](what-is-a-data-warehouse-unit-dwu-cdwu.md)）开始，并在几分钟内扩展到 30，000 cDWU。 从 2018 年 12 月中开始，通过某些[区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)中的较低的计算层，客户可以从 Gen2 性能和灵活性中受益。其余区域的此类计算层在 2019 年提供。<br/><br/>通过降低下一代数据仓库的入口点，Microsoft 为价值驱动型客户打开了大门，他们无需猜测哪种试用环境最适合，即可评估安全、高性能数据仓库的所有优势。 客户最低可以从 100 cDWU 开始，低于目前的 500 cDWU 入口点。 SQL Analytics 继续支持暂停和恢复操作，并不仅仅局限于计算的灵活性。 Gen2 还支持无限的列存储容量，并且每个查询的内存增加了 1.5 倍，并发查询多达 128 个，以及[自适应缓存](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 与 Gen1 上相同的数据仓库单元相比，这些功能的平均性能是同一价格的五倍。 异地冗余备份是 Gen2 的标准配置，具有内置的保证数据保护。 Azure 突触中的 SQL 分析已准备就绪，可以在需要时进行扩展。|
|**列存储后台合并**|默认情况下，Azure SQL 数据以列格式存储数据，其中称为[行组](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微分区。 有时候，由于构建索引或加载数据时的内存约束，行组可能会被压缩为小于最佳大小 100 万行。 行组还可能会由于删除而产生碎片。 小的或碎片化的行组会导致较高的内存消耗以及低效的查询执行。 使用此版本，列存储后台维护任务将合并小型压缩行组以创建更大的行组，以更好地利用内存并加快查询执行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**数据仓库的 DevOps**|Azure 突触中高度请求的 SQL 分析功能现已预览，在可视化工作室中支持 SQL 服务器数据工具 （SSDT）！ 开发团队现可通过单一的版本控制基本代码进行协作，并快速将更改部署到世界上的任何实例。 是否有兴趣加入？ 此功能目前以预览版提供！ 您可以通过访问[可视化工作室 SQL 服务器数据工具 （SSDT） - 预览注册表单进行注册](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)。 鉴于需求量很大，我们正在对预览进行验收，以确保为客户提供最佳体验。 注册后，我们的目标是在七个工作日内确认你的状态。|
|**行级别安全性已公开发布**|Azure Synaps 中的 SQL 分析现在支持行级安全性 （RLS）， 添加强大的功能来保护敏感数据。 通过引入 RLS，可以实现安全策略以控制对表中行的访问（例如哪些人员可以访问哪些行）。 RLS 支持这种精确的访问控制，而无需重新设计数据仓库。 由于访问限制逻辑位于数据库层本身，而不是脱离另一应用程序中的数据，因此 RLS 简化了整体安全模型。 RLS 还消除了引入视图以筛选出用于访问控制管理的行的需求。 我们为所有客户提供此企业级安全功能，而用户无需额外付费。|
|**高级顾问**|Azure Synapse 中 SQL 分析的高级调优只需通过其他数据仓库建议和指标变得更加简单。 Azure 顾问还提供其他高级性能建议供你参考，包括：<br/><br/>1. 自适应缓存 – 建议何时缩放以优化缓存利用率。<br/>2. 表分布 – 确定何时复制表以减少数据移动并提高工作负载性能。<br/>3. Tempdb – 了解何时缩放和配置资源类以减少 tempdb 争用。<br/><br/>数据仓库指标与 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) 深度集成，包括增强的可自定义监视图表，以在概览边栏选项卡中实现近实时指标。 监视使用情况或验证和应用数据仓库建议时，无需离开数据仓库概览边栏选项卡即可访问 Azure Monitor 指标。 此外，推出了新的指标（例如 tempdb 和自适应缓存使用率）作为性能建议的补充。|
|**使用集成顾问进行高级优化**|通过其他数据仓库建议和指标以及重新设计门户概述边栏选项卡，使用 Azure Synapse 提供集成体验，Azure Synapse 的高级调优非常简单。|
|**加速的数据库恢复 (ADR)**|Azure 同步加速数据库恢复 （ADR） 现已处于公共预览版。 ADR 是一项新的 SQL Server 引擎功能，通过完全从头开始重新设计当前的恢复过程，极大地提高了数据库可用性（尤其是在存在长时间运行事务的情况下）。 ADR 的主要优点是快速且一致的数据库恢复和即时事务回滚。|
|**Azure Monitor 诊断日志**|Azure Synapse 现在通过直接与 Azure 监视器诊断日志集成，从而增强了对分析工作负荷的见解。 这项新功能使开发者可分析长时段内的工作负载行为，并针对查询优化或容量管理作出明智决策。 我们现在通过 [Azure Monitor 诊断日志](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)引入了外部日志记录过程，这些日志提供了有关数据仓库工作负载的其他见解。 只需单击一下按钮，即可使用 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) 为历史查询性能故障排除功能配置诊断日志。 Azure Monitor 诊断日志支持通过将日志保存到存储帐户以进行审核来自定义保留期，将日志流式传输到事件中心以获得近实时遥测见解，以及通过日志查询使用 Log Analytics 来分析日志。 诊断日志由数据仓库的遥测视图组成，相当于 Azure 突触中最常用的用于 SQL 分析的性能故障排除 DMV。 对于此初始版本，我们为以下系统动态管理视图启用了视图：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)|
|**列存储内存管理**|随着压缩列存储行组数量的增加，管理这些行组的内部列段元数据所需的内存也会增加。  因此，针对某些列存储动态管理视图 (DMV) 执行的查询和查询性能可能会降级。  此版本中的改进旨在优化这些情况下的内部元数据大小，从而改善此类查询的体验并提高此类查询的性能。|
|**Azure Data Lake Storage Gen2 集成 (GA)**|Synapse 分析现在具有与 Azure 数据存储 Gen2 的本机集成。 客户现在可以使用 ABFS 的外部表将数据加载到 SQL 分析中。 此功能使客户能够与 Data Lake Storage Gen2 中的 data lake 集成。|
|**值得注意的 Bug**|在 DW2000 等数据仓库上的小资源类中执行到 Parquet 的 CETAS 时发生失败 - 此项修复可正确识别 Create External Table As to Parquet 代码路径中的 null 引用。<br/><br/>在某些 CTAS 操作中可能会丢失标识列值 - CTAS 到另一个表后，可能不会保留标识列的值。 已在[博客](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中报告。<br/><br/>在某些情况下，当查询仍在运行时终止会话会发生内部失败 - 如果在查询仍在运行时终止会话，此项修复会触发 InvalidOperationException。<br/><br/>（部署于 2018 年 11 月）尝试使用 Polybase 从 ADLS (Gen1) 加载多个小文件时，客户体验的性能不佳。 在 AAD 安全令牌验证期间，系统性能出现瓶颈。 通过启用安全令牌的缓存来缓解性能问题。 |
| | |

## <a name="next-steps"></a>后续步骤
- [创建 SQL 池](create-data-warehouse-portal.md)

## <a name="more-information"></a>详细信息
- [博客 - Azure 突触分析](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客户顾问团队博客](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [客户成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stackoverflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 词汇表](../../azure-glossary-cloud-terminology.md)
