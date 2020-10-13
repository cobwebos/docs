---
title: 发行说明
description: Azure Synapse Analytics 的发行说明
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 4/30/2020
author: anumjs
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: 6f7af74cce6bbafea7924d505f768503c7b1f108
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89457985"
---
# <a name="azure-synapse-analytics-release-notes"></a>Azure Synapse Analytics 发行说明

本文汇总了 Azure Synapse 中最新版本的 [Synapse SQL](sql-data-warehouse-overview-what-is.md) 的新功能和改进。 本文还列出了不与此版本直接相关但在同一时间范围内发布的值得注意的内容更新。 有关对其他 Azure 服务的改进，请参阅[服务更新](https://azure.microsoft.com/updates)。

## <a name="check-your-azure-synapse-version"></a>检查 Azure Synapse 版本

虽然我们已向所有区域推出新功能，但请检查部署到实例的版本以及最新的发行说明，以了解功能可用性。 若要检查版本，请通过 SQL Server Management Studio (SSMS) 连接到 SQL 池，然后运行 `SELECT @@VERSION;` 来返回当前版本。 使用此版本来确认已应用于 SQL 池的发行版。 输出中的日期确定了发行版应用于 SQL 池的月份。 这只适用于服务级别改进。 

对于工具改进，请确保安装了发行说明中指定的正确版本。 

> [!NOTE]
> SELECT @@VERSION 返回的产品名称将从“Microsoft Azure SQL 数据仓库”改为“Microsoft Azure Synapse Analytics”。 我们会在更改前提前通知你。 对于在应用代码中分析 SELECT @@VERSION 返回的产品名称的客户来说，此更改是相关的。 若要避免由于产品品牌重塑而导致应用程序代码更改，请使用以下命令在 SERVERPROPERTY 中查询数据库产品名称和版本：若要返回版本号 XX.X.XXXXX.X（不含产品名称），请运行以下命令：
>
> ```sql
> SELECT SERVERPROPERTY('ProductVersion')
>
> --To return engine edition, use this command that returns 6 for Azure Synapse Analytics (formerly SQL Data Warehouse):
>
> SELECT SERVERPROPERTY('EngineEdition')
> ```

## <a name="aug-2020"></a>2020 年 8 月

| 服务改进 | 详细信息 |
| --- | --- |
|工作负载管理 - 门户体验|用户可以通过 Azure 门户来配置和管理自己的工作负荷管理设置。 可以配置[工作负荷组](/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-portal)和[工作负荷分类器](/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)（含重要性）。|
|改进了表映射目录视图|新的目录视图 [sys.pdw_permanent_table_mappings](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-permanent-table-mappings-transact-sql?view=sqlallproducts-allversions) 将永久用户表的 object_id 映射到它们的物理表名称。|

## <a name="july-2020"></a>2020 年 7 月

| 服务改进 | 详细信息 |
| --- | --- |
|列级加密（公共预览版）|使用 Transact-sql 对数据列应用对称加密，以保护 Azure Synapse 分析中的敏感信息。 列级加密具有内置函数，可用于使用对称密钥（通过证书、密码、对称密钥或非对称密钥受到进一步保护）来加密数据。 有关详细信息，请访问[加密数据列](/sql/relational-databases/security/encryption/encrypt-a-column-of-data?view=azure-sqldw-latest)。|
|兼容性级别支持 (GA)|在此版本中，用户现在可以设置数据库的兼容性级别，以获取 Synapse SQL 引擎的特定版本的 Transact-SQL 语言和查询处理行为。 有关详细信息，请参阅 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和[更改数据库范围的配置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**行级别安全性**|此版本包含对于在强制执行了 RLS 的行上进行的更新和删除操作的改进。 在此版本中，如果内部函数（如 "is_rolemember"）包含内部函数的更新和删除操作，则这些函数将成功。 在此次改进之前，这些操作会因基础 DML 操作中的限制而失败。|
|DBCC SHRINKDATABASE (GA)|现在可以收缩指定数据库中的数据文件和日志文件的大小。 有关详细信息，请参阅[文档](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql?view=sql-server-ver15)。|

## <a name="may-2020"></a>2020 年 5 月

| 服务改进 | 详细信息 |
| --- | --- |
|**工作负荷隔离 (GA)**|[工作负荷隔离](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-isolation)现已正式发布。  通过[工作负荷组](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)，可以保留和包含资源。  还可以配置查询超时来取消失控查询。|
|**工作负荷管理门户体验（预览）**| 用户可以通过 Azure 门户来配置和管理自己的工作负荷管理设置。  可以配置[工作负荷组](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)和[工作负荷分类器](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-create-a-workload-classifier-portal)（含重要性）。|
|**ALTER WORKLOAD GROUP**|现在可以使用 [ALTER WORKLOAD GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-workload-group-transact-sql?view=azure-sqldw-latest) 命令。  使用 ALTER 命令可以更改现有[工作负荷组](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-isolation)的配置。|
|**使用 COPY 命令（预览版）对 Parquet 文件执行自动架构检测**|[COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)现在支持在加载 Parquet 文件时执行自动架构检测。 此命令会在加载文件前先自动检测 Parquet 文件架构并创建表。 若要启用此功能，请访问以下电子邮件通讯组列表： sqldwcopypreview@service.microsoft.com 。 |
|**使用 COPY 命令（预览版）加载复杂的 Parquet 数据类型**|[COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)现在支持加载复杂的 Parquet 类型。 可以将复杂类型（如 Maps 和 Lists）加载到字符串列中。  若要启用此功能，请访问以下电子邮件通讯组列表： sqldwcopypreview@service.microsoft.com 。 |
|**使用 COPY 命令对 Parquet 文件执行自动压缩检测**|[COPY 命令](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)现在支持自动检测一个或多个 Parquet 文件的压缩方法。 若要启用此功能，请访问以下电子邮件通讯组列表： sqldwcopypreview@service.microsoft.com 。|
|**其他加载建议**|[加载建议](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations)现在可用于 Synapse SQL。 在以下情况下收到主动通知：应拆分文件来最大限度地提高吞吐量、将存储帐户与 SQL 池归置在一起，或在使用加载实用工具（如 SQLBulkCopy API 或 BCP）时增加批大小|
|**T-SQL 可更新的分布列 (GA)**|用户现在可以更新在分布列中存储的数据。 有关详细信息，请查看[关于在 Synapse SQL 池中设计分布式表的指南](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute)。|
|**T-SQL 根据联接结果进行更新/删除 (GA)**|现在可以根据与其他表联接的结果进行更新和删除。 有关详细信息，请参阅 [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql?view=azure-sqldw-latest) 和 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql?view=azure-sqldw-latest) 文档。|
|**T-SQL PREDICT（预览）**|现在可以对数据仓库中的机器学习模型进行预测，从而避免需要进行大型且复杂的数据移动。 T-SQL PREDICT 函数依赖开放模型框架，需要使用数据和机器学习模型作为输入来生成预测。 有关详细信息，请参阅这篇[文档](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)。|

## <a name="april-2020"></a>2020 年 4 月

| 服务改进 | 详细信息 |
| --- | --- |
|**数据库兼容性级别（预览版）**| 在此版本中，用户现在可以设置数据库的兼容性级别，以获取 Synapse SQL 引擎的特定版本的 Transact-SQL 语言和查询处理行为。 有关详细信息，请参阅 [sys.database_scoped_configurations](/sql/relational-databases/system-catalog-views/sys-database-scoped-configurations-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 和[更改数据库范围的配置](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**Sp_describe_undeclared_parameters**| 可让用户查看有关 Transact-SQL 批中未声明的参数的元数据。 有关详细信息，请参阅 [sp_describe_undeclared_parameters](/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。| <br/><br/><br/>

| 工具改进                                         | 详细信息                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 5](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview#--visual-studio-2019-version-166-preview-5-) - SQL Server Data Tools (SSDT)** | 此版本包含以下针对 SSDT 的改进： </br> </br> - 数据发现和分类<br/> - COPY 语句 <br/> - 包含唯一约束的表<br/> - 包含有序聚集列存储索引的表<br/> <br/>此版本包含以下针对 SSDT 的修复： </br></br>  - 当你更改分布列的数据类型时，由 SSDT 生成的更新脚本会执行 CTAS 和 RENAME 操作，而不是删除表并重新创建。 </br> |

## <a name="march-2020"></a>2020 年 3 月

| 工具改进                                         | 详细信息                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **[Visual Studio 16.6 Preview 2](https://docs.microsoft.com/visualstudio/releases/2019/release-notes-preview#whats-new-in-visual-studio-2019) - SQL Server Data Tools (SSDT)** | 此版本包含下述针对 SSDT 的改进和修复： </br> </br> - 解决了以下问题：更改具体化视图 (MV) 引用的表会导致生成 MV 不支持的 Alter View 语句<br/><br/> - 实现了一项更改，以确保当数据库或项目中存在行级安全性对象时“架构比较”操作不会失败。 SSDT 目前不支持行级安全对象。  <br/><br/> - 增大了 SQL Server 对象资源管理器的超时阈值，以避免在列出数据库中的大量对象时发生超时<br/><br/> - 优化了 SQL Server 对象资源管理器检索数据库对象列表的方式，以便在填充对象资源管理器时降低不稳定性并提高性能 |

## <a name="january-2020"></a>2020 年 1 月

| 服务改进 | 详细信息 |
| --- | --- |
|**工作负载管理门户指标（预览）**|在这个过去的 10 月，随着预览版[工作负载隔离](sql-data-warehouse-workload-isolation.md)的发布，用户可以创建自己的[工作负载组](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)来有效地管理系统资源并确保满足业务 SLA。  作为 Azure Synapse Analytics 的总体[工作负载管理](sql-data-warehouse-workload-management.md)增强功能的一部分，新的[工作负载管理监视指标](sql-data-warehouse-workload-management-portal-monitor.md)现在可用。</br> </br> 监视工作负载功能现在通过以下指标提供更好的见解： </br> - 有效上限资源百分比  </br> - 有效最小资源百分比 </br> - 工作负载组活动查询 </br> - 按最大资源百分比列出的工作负载组分配 </br> - 按系统百分比列出的工作负载组分配 </br> - 工作负载组查询超时 </br> - 工作负载组排队查询 </br></br> 使用这些指标来识别[工作负荷组瓶颈](sql-data-warehouse-workload-management-portal-monitor.md#workload-group-bottleneck)或配置了[未充分利用的工作负荷隔离](sql-data-warehouse-workload-management-portal-monitor.md#underutilized-workload-isolation)的工作负荷组。  可在允许按工作负载组拆分的 Azure 门户中使用这些指标。  筛选最常用图形，并将它们固定到仪表板，以便快速访问见解。|
|**门户监视指标**| 以下指标已添加到门户，用于监视总体查询活动： </br> - 活动查询 </br> - 排队的查询 </br> </br>这些指标与[监视资源利用率和查询活动](sql-data-warehouse-concept-resource-utilization-query-activity.md)文档中的现有指标一起介绍。|

## <a name="october-2019"></a>2019 年 10 月

| 服务改进 | 详细信息 |
| --- | --- |
|**Copy（预览版）**|我们很高兴地宣布，现已推出简单灵活 COPY 语句的公共预览版用于数据引入。 现在，只需一条语句就能无缝引入数据并获得更高的灵活性，且无需分配高特权用户。 有关详细信息，请参阅 [COPY 命令](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)文档。|
|**工作负载隔离（预览版）**|为了在客户使其数据仓库大众化时为客户提供支持，我们宣布推出用于智能工作负载管理的新功能。 新的[工作负载隔离](sql-data-warehouse-workload-isolation.md)功能使你能够管理异类工作负载的执行，同时提供灵活性以及对数据仓库资源的控制。 这会提高执行可预测性，并增强满足预定义 SLA 的能力。 </br>除了工作负载隔离外，现在还有其他选项可用于[工作负载分类](sql-data-warehouse-workload-classification.md)。  除了登录分类以外，[CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法还支持根据查询标签、会话上下文和时间对请求进行分类。|
|**PREDICT（预览）**|现在可以对数据仓库中的机器学习模型进行评分，从而避免需要进行大型且复杂的数据移动。 T-SQL PREDICT 函数依赖开放模型框架，需要使用数据和机器学习模型作为输入来生成预测。
|**SSDT CI/CD (GA)**|今天，我们很高兴地宣布推出 SQL Analytics - SQL Server Data Tools (SSDT) 数据库项目的[最高请求功能](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247--in-preview-database-project-from-visual-studio-t)正式版。 此版本支持将 SSDT 与 Visual Studio 2019 配合使用，并支持与 Azure DevOps 进行本机平台集成，为企业级部署提供内置的持续集成和部署 (CI/CD) 功能。 |
|**具体化视图 (GA)**|具体化视图会保留从视图定义查询返回的数据，并在基础表中的数据更改时自动更新。 它提高了复杂查询（通常是使用联接和聚合的查询）的性能，同时提供了简单的维护操作。 有关详细信息，请参阅[使用具体化视图优化性能](performance-tuning-materialized-views.md)。  安装 [SQL Server Management Studio 18.4 或更高版本](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)可以编写具体化视图的脚本。|
|**动态数据掩码（正式版）**|动态数据掩码 (DDM) 根据定义的掩码规则在查询结果中即时将数据仓库中的敏感数据模糊化，以防止有人未经授权访问这些数据。 有关详细信息，请参阅 [SQL 数据库动态数据掩码](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**读取提交的快照隔离 (GA)**|可以使用 ALTER DATABASE 为用户数据库启用或禁用快照隔离。 为了避免对当前工作负荷造成影响，不妨在数据库维护时段期间设置此选项，或等到数据库没有其他任何活动连接时设置此选项。 有关详细信息，请参阅 [ALTER DATABASE SET 选项](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**有序聚集列存储索引（正式版）**|若要存储和有效查询大量数据，列存储至关重要。 排序聚集列存储索引支持有效的段消除，从而进一步优化了查询执行。   有关详细信息，请参阅[使用有序聚集列存储索引优化性能](performance-tuning-ordered-cci.md)。|
|**结果集缓存 (GA)**|启用结果集缓存后，查询结果会在用户数据库中自动缓存，供重复使用。 这样，后续的查询执行就能直接从持久化缓存中获取结果，因此无需重新计算。 结果集缓存提高了查询性能，并减少了计算资源的用量。 此外，使用缓存结果集的查询不会占用任何并发槽，因此不会计入现有的并发限制。 出于安全考虑，如果访问方用户的数据访问权限与创建缓存结果的用户相同，则访问方用户只能访问缓存的结果。 有关详细信息，请参阅[使用结果集缓存优化性能](performance-tuning-result-set-caching.md)。 适用于 10.0.10783.0 或更高版本。|

## <a name="september-2019"></a>2019 年 9 月

| 服务改进 | 详细信息 |
| --- | --- |
|**Azure 专用链接（预览）**|通过 [Azure 专用链接](https://azure.microsoft.com/blog/announcing-azure-private-link/)，可以在虚拟网络 (VNet) 中创建专用终结点，并将它映射到 SQL 池。 然后，可通过 VNet 中的专用 IP 地址访问这些资源，从而能够通过 Azure ExpressRoute 专用对等互连和/或 VPN 网关从本地连接。 总的来说，这简化了网络配置，因为不需要向公共 IP 地址开放它。 这还可以规避数据外泄风险。 如需了解更多详情，请参阅[概述](../../private-link/private-link-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)和 [SQL Analytics](../../azure-sql/database/private-endpoint-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 文档。|
|**数据发现和分类 (GA)**|[数据发现和分类](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)功能现已推出正式版。 此功能提供高级功能用于**发现、分类、标记和保护**数据库中的敏感数据。|
|**Azure 顾问一键式集成**|现在，Azure Synapse 中的 SQL Analytics 直接在概览边栏选项卡中与 Azure 顾问建议集成，并提供一键式体验。 现在可以在概述边栏选项卡中发现建议，而无需转到 Azure 顾问边栏选项卡。 在[此处](sql-data-warehouse-concept-recommendations.md)详细了解建议。|
|**读取提交的快照隔离（预览）**|可以使用 ALTER DATABASE 为用户数据库启用或禁用快照隔离。  为了避免对当前工作负荷造成影响，不妨在数据库维护时段期间设置此选项，或等到数据库没有其他任何活动连接时设置此选项。 有关详细信息，请参阅 [ALTER DATABASE SET 选项](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。|
|**EXECUTE AS (Transact-SQL)**| 现已提供 [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 支持，使客户能够将会话的执行上下文设置为指定的用户。|
|**更多 T-SQL 支持**|Synapse SQL 的 T-SQL 语言外围应用已扩展，现在支持： </br> - [FORMAT (Transact-SQL)](/sql/t-sql/functions/format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_PARSE (Transact-SQL)](/sql/t-sql/functions/try-parse-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CAST (Transact-SQL)](/sql/t-sql/functions/try-cast-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [TRY_CONVERT (Transact-SQL)](/sql/t-sql/functions/try-convert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.user_token (Transact-SQL)](/sql//relational-databases/system-catalog-views/sys-user-token-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|

## <a name="july-2019"></a>2019 年 7 月

| 服务改进 | 详细信息 |
| --- | --- |
|**具体化视图（预览版）**|具体化视图会保留从视图定义查询返回的数据，并在基础表中的数据更改时自动更新。 它提高了复杂查询（通常是使用联接和聚合的查询）的性能，同时提供了简单的维护操作。 有关详细信息，请参阅： </br> - [CREATE MATERIALIZED VIEW AS SELECT &#40;Transact-SQL&#41;](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER MATERIALIZED VIEW &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-materialized-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br> - [Synapse SQL 中支持的 T-SQL 语句](sql-data-warehouse-reference-tsql-statements.md)|
|**更多 T-SQL 支持**|Synapse SQL 的 T-SQL 语言外围应用已扩展，现在支持： </br> - [AT TIME ZONE (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [STRING_AGG (Transact-SQL)](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**结果集缓存（预览版）**|添加了 DBCC 命令以管理之前公布的结果集缓存。 有关详细信息，请参阅： </br> - [DBCC DROPRESULTSETCACHE &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  </br> - [DBCC SHOWRESULTCACHESPACEUSED &#40;Transact-SQL&#41;](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) </br></br> 另请参阅 [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 中新的 result_set_cache 列，该列显示执行的查询何时使用结果集缓存。|
|**排序聚集列存储索引（预览版）**|新列 column_store_order_ordinal 已添加到 [sys.index_columns](/sql/relational-databases/system-catalog-views/sys-index-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)，以标识已排序聚集列存储索引中列的顺序。|

## <a name="may-2019"></a>2019 年 5 月

| 服务改进 | 详细信息 |
| --- | --- |
|**动态数据掩码（预览版）**|动态数据掩码 (DDM) 根据定义的掩码规则在查询结果中即时将数据仓库中的敏感数据模糊化，以防止有人未经授权访问这些数据。 有关详细信息，请参阅 [SQL 数据库动态数据掩码](../../azure-sql/database/dynamic-data-masking-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**工作负荷重要性现已正式发布**|工作负荷管理分类和重要性可以影响查询的运行顺序。 有关工作负荷重要性的详细信息，请参阅文档中的[分类](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)概述文章。 另请查看 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 文档。<br/><br/>有关工作负荷重要性的功能演示，请观看以下视频：<br/> -[工作负荷管理概念](https://www.youtube.com/embed/QcCRBAhoXpM)<br/> -[工作负荷管理方案](https://www.youtube.com/embed/_2rLMljOjw8)|
|**更多 T-SQL 支持**|Synapse SQL 的 T-SQL 语言外围应用已扩展，现在支持： </br> - [TRIM](/sql/t-sql/functions/trim-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**JSON 函数**|现在，业务分析师可以使用下述新的 JSON 函数，以他们熟悉的 T-SQL 语言查询和操作采用 JSON 数据格式的文档：</br> - [ISJSON](/sql/t-sql/functions/isjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**结果集缓存（预览版）**|使用结果集缓存可以立即获得查询响应，同时减少业务分析师和报告用户获得见解所需的时间。 有关详细信息，请参阅：</br> - [ALTER DATABASE (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [ALTER DATABASE SET 选项 (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET RESULT SET CACHING (Transact-SQL)](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [SET 语句 (Transact-SQL)](/sql/t-sql/statements/set-statements-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> - [sys.databases (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**排序聚集列存储索引（预览版）**|若要存储和有效查询大量数据，列存储至关重要。 对于每个表，列存储会将传入的数据划分为行组，行组的每个列构成了磁盘上的一个段。  排序聚集列存储索引支持有效的段消除，从而进一步优化了查询执行。有关详细信息，请参阅：</br> -  [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)</br> -  [CREATE COLUMNSTORE INDEX (Transact-SQL)](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).|

## <a name="march-2019"></a>2019 年 3 月

| 服务改进 | 详细信息 |
| --- | --- |
|**数据发现和分类**|现已推出适用于 Synapse SQL 的数据发现和分类公共预览版。 此功能对于保护客户的敏感数据和隐私至关重要。 随着业务和客户数据资产的增长，发现、分类和保护数据将变得难以管理。 Synapse SQL 原生引入的数据发现和分类功能有助于轻松保护数据。 此功能的总体优势包括：<br/>&bull; &nbsp; 满足数据隐私标准与合规性要求。<br/>&bull; &nbsp; 限制对包含高度敏感数据的数据仓库的访问并增强其安全性。<br/>&bull; &nbsp; 监视对敏感数据的异常访问并发出警报。<br/>&bull; &nbsp; 在 Azure 门户上的中心仪表板内直观呈现敏感数据。 </br></br>数据发现和分类可在所有 Azure 区域中使用，它是包括漏洞评估和威胁检测在内的高级数据安全的一部分。 若要详细了解数据发现和分类，请参阅这篇[博客文章](https://azure.microsoft.com/blog/announcing-public-preview-of-data-discovery-classification-for-microsoft-azure-sql-data-warehouse/)和我们的联机[文档](../../azure-sql/database/data-discovery-and-classification-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**GROUP BY ROLLUP**|ROLLUP 现在是支持的 GROUP BY 选项。   GROUP BY ROLLUP 为每个列表达式组合创建一个组。 GROUP BY 还可将结果“汇总”为小计和总计。 GROUP BY 函数的处理顺序为从右到左，减少了创建组和聚合所依据的列表达式数目。  列的顺序会影响 ROLLUP 的输出，而且可能会影响结果集内的行数。<br/><br/>有关 GROUP BY ROLLUP 的详细信息，请参阅 [GROUP BY (Transact-SQL)](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
|**改进了已用 DWU 和 CPU 门户指标的准确度**|Synapse SQL 明显提高了 Azure 门户中的指标准确性。  此发行版修复了 CPU 和已用 DWU 指标定义，以正确反映所有计算节点的工作负荷。 在推出此项修复之前，指标值的报告程度不足。 Azure 门户中的已用 DWU 和 CPU 指标应该会增加。 |
|**行级别安全性**|早在 2017 年 11 月，我们就引入了行级别安全性功能。 我们现在还将此项支持扩展到了外部表。 此外，我们在内联表值函数（内联 TVF）中添加了调用非确定性函数来定义安全筛选器谓词的支持。 借助这一附加支持，可以在安全筛选器谓词中指定 IS_ROLEMEMBER()、USER_NAME() 等函数。 有关详细信息，请参阅[行级别安全性文档](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中的示例。|
|**更多 T-SQL 支持**|Synapse SQL 的 T-SQL 语言外围应用已扩展，现在包含对 [STRING_SPLIT (Transact-SQL)](/sql/t-sql/functions/string-split-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 的支持。
|**查询优化器增强** |查询优化是任何数据库的关键组成部分。 在如何执行查询方面做出最佳选择可以得到明显的改进。  在分布式环境中执行复杂的分析查询时，执行的操作数目非常关键。 生成更好的质量计划可以增强查询性能。 这些计划最大限度地减少了昂贵的数据传输操作和冗余计算（如重复的子查询）。 有关详细信息，请参阅此 Azure Synapse [博客文章](https://azure.microsoft.com/blog/smarter-faster-safer-azure-sql-data-warehouse-is-simply-unmatched/)。|
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
|**PartitionMove 和 BroadcastMove 的数据移动增强**|ShuffleMove 类型的数据移动步骤使用即时数据移动技术。  有关详细信息，请参阅[性能增强](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)博客。 在此版本中，PartitionMove 和 BroadcastMove 以相同的即时数据移动技术为后盾。 使用这些类型的数据移动步骤的用户查询可以提升运行性能。 无需更改代码便可利用这些性能改进。|
|**值得注意的 Bug**|错误的 Azure Synapse 版本 - `SELECT @@VERSION` 可能返回不正确的版本 10.0.9999.0。 当前版本的正确版本是 10.0.10106.0。 此 bug 已报告，正在评审。
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
|**虚拟网络服务终结点正式发布**|此版本包括公开发布的虚拟网络 (VNet) 服务终结点，适用于所有 Azure 区域内的 Azure Synapse 中的 SQL Analytics。 使用 VNet 服务终结点，可将到服务器的连接与虚拟网络中的给定子网或子网集隔离。 从 VNet 到 Azure Synapse 的流量将始终保留在 Azure 主干网络内。 这种直接路由将优先于通过虚拟设备或本地访问 Internet 流量的任何特定路由。 通过服务终结点访问虚拟网络无需额外付费。 [Azure Synapse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) 的当前定价模型按原样应用。<br/><br/>在此版本中，我们还允许使用 [Azure Blob 文件系统](../../storage/blobs/data-lake-storage-abfs-driver.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ABFS) 驱动程序通过 PolyBase 连接到 [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (ADLS)。 Azure Data Lake Storage Gen2 为 Azure 存储提供了分析数据的整个生命周期所需的所有特性。 两个现有 Azure 存储服务（Azure Blob 存储和 Azure Data Lake Storage Gen1）的功能已聚合。 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 的功能（例如文件系统语义、文件级安全性和规模）与 [Azure Blob 存储](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)中的低成本分层存储和高可用性/灾难恢复功能进行了组合。<br/><br/>使用 Polybase 时，也可将数据从以安全方式连接到 VNet 的 Azure 存储导入到 Azure Synapse 中的 SQL Analytics。 同样，也可通过 Polybase 将数据从 Azure Synapse 导出到以安全方式连接到 VNet 的 Azure 存储。<br/><br/>有关 Azure Synapse 中 VNet 服务终结点的详细信息，请参阅[博客文章](https://azure.microsoft.com/blog/general-availability-of-vnet-service-endpoints-for-azure-sql-data-warehouse/)或[文档](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。|
|**自动性能监视（预览）**|[查询存储](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)现已在 Azure Synapse 中的 SQL Analytics 中以预览版推出。 查询存储旨在帮助你进行查询性能故障诊断，可以通过跟踪查询、查询计划、运行时统计信息和查询历史记录来监视数据仓库的活动和性能。 查询存储是一组内部存储和动态管理视图 (DMV)，用于执行以下操作：<br/><br/>&bull; &nbsp; 确定和优化排名靠前的资源占用查询<br/>&bull; &nbsp; 识别并改进计划外的工作负荷<br/>&bull; &nbsp; 按统计信息、索引或系统大小（DWU 设置）方面的变化评估查询性能以及对计划的影响<br/>&bull; &nbsp; 查看所有已执行查询的完整查询文本<br/><br/>查询存储包含三个实际的存储：<br/>&bull; &nbsp; 一个计划存储，用于持久保存执行计划信息<br/>&bull; &nbsp; 一个运行时统计信息存储，用于持久保存执行统计信息<br/>&bull; &nbsp; 用于暂留等待统计信息的等待统计信息存储。<br/><br/>Azure Synapse 中的 SQL Analytics 自动管理这些存储，并提供过去七天存储的查询，不限数量且不额外收费。 启用查询存储很简单，只需运行 ALTER DATABASE T-SQL 语句即可： <br/>sql ----ALTER DATABASE [DatabaseName] SET QUERY_STORE = ON;-------若要详细了解查询存储，请参阅[使用查询存储监视性能](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)一文和查询存储 DMV（例如 [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)）。 这是宣告发布情况的[博客文章](https://azure.microsoft.com/blog/automatic-performance-monitoring-in-azure-sql-data-warehouse-with-query-store/)。|
|**SQL Analytics 的较低计算层**|Azure Synapse 中的 SQL Analytics 现在支持较低的计算层。 客户可以体验到 Azure Synapse 的领先性能、灵活性和安全功能，从 100 cDWU（[数据仓库单元](what-is-a-data-warehouse-unit-dwu-cdwu.md)）开始，并在几分钟内扩展到 30,000 cDWU。 从 2018 年 12 月中开始，通过某些[区域](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)中的较低的计算层，客户可以从 Gen2 性能和灵活性中受益。其余区域的此类计算层在 2019 年提供。<br/><br/>通过降低下一代数据仓库的入口点，Microsoft 为价值驱动型客户打开了大门，他们无需猜测哪种试用环境最适合，即可评估安全、高性能数据仓库的所有优势。 客户最低可以从 100 cDWU 开始，低于目前的 500 cDWU 入口点。 SQL Analytics 继续支持暂停和恢复操作，不仅仅是计算的灵活性。 Gen2 还支持无限的列存储容量，并且每个查询的内存增加了 1.5 倍，并发查询多达 128 个，以及[自适应缓存](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/)功能。 与同一价格的 Gen1 上的相同数据仓库单元相比，这些功能的平均性能提高了 4 倍。 异地冗余备份是 Gen2 的标准配置，具有内置的保证数据保护。 Azure Synapse 中的 SQL Analytics 可以随时进行缩放。|
|**列存储后台合并**|默认情况下，Azure SQL Data 使用称为[行组](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)的微分区以列格式存储数据。 有时候，由于构建索引或加载数据时的内存约束，行组可能会被压缩为小于最佳大小 100 万行。 行组还可能会由于删除而产生碎片。 小的或碎片化的行组会导致较高的内存消耗以及低效的查询执行。 使用此版本时，列存储后台维护任务会将较小的压缩行组进行合并以创建较大的行组，更好地利用内存并提高查询执行速度。
| | |

## <a name="october-2018"></a>2018 年 10 月

### <a name="service-improvements"></a>服务改进

| 服务改进 | 详细信息 |
| --- | --- |
|**数据仓库的 DevOps**|针对 Azure Synapse 中的 Synapse SQL 请求很多的功能现已提供预览，同时支持 Visual Studio 中的 SQL Server Data Tools (SSDT)！ 开发团队现可通过单一的版本控制基本代码进行协作，并快速将更改部署到世界上的任何实例。 是否有兴趣加入？ 此功能目前以预览版提供！ 可以通过访问 [Visual Studio SQL Server Data Tools (SSDT) - 预览许可登记表](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)进行注册。 鉴于需求量很大，我们正在对预览进行验收，以确保为客户提供最佳体验。 注册后，我们的目标是在七个工作日内确认你的状态。|
|**行级别安全性现已正式发布**|Azure Synapse 中的 Synapse SQL 现在支持行级别安全性 (RLS)，增加了保护敏感数据的强大功能。 通过引入 RLS，可以实现安全策略以控制对表中行的访问（例如哪些人员可以访问哪些行）。 RLS 支持这种精确的访问控制，而无需重新设计数据仓库。 由于访问限制逻辑位于数据库层本身，而不是脱离另一应用程序中的数据，因此 RLS 简化了整体安全模型。 RLS 还消除了引入视图以筛选出用于访问控制管理的行的需求。 我们为所有客户提供此企业级安全功能，而用户无需额外付费。|
|**高级顾问**|其他数据仓库建议和指标使 Azure Synapse 中的 Synapse SQL 的高级优化变得更加简单。 Azure 顾问还提供其他高级性能建议供你参考，包括：<br/><br/>1.自适应缓存 - 在应进行缩放以优化缓存利用率时通知用户。<br/>2.表分配 - 确定何时复制表，以减少数据移动，提高工作负载性能。<br/>3.Tempdb - 了解何时缩放和配置资源类，以减少 tempdb 争用。<br/><br/>数据仓库指标与 [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) 深度集成，包括增强的可自定义监视图表，以在概览边栏选项卡中实现近实时指标。 监视使用情况或验证和应用数据仓库建议时，无需离开数据仓库概览边栏选项卡即可访问 Azure Monitor 指标。 此外，推出了新的指标（例如 tempdb 和自适应缓存使用率）作为性能建议的补充。|
|**使用集成顾问进行高级优化**|通过其他数据仓库建议和指标，以及重新设计的提供 Azure 顾问和 Azure Monitor 的集成体验的门户概览边栏选项卡，Azure Synapse 的高级优化变得更加简单了。|
|**加速数据库恢复 (ADR)**|Azure Synapse 加速的数据库恢复 (ADR) 现在为公共预览版。 ADR 是一项新的 SQL Server 引擎功能，通过完全从头开始重新设计当前的恢复过程，极大地提高了数据库可用性（尤其是在存在长时间运行事务的情况下）。 ADR 的主要优点是快速且一致的数据库恢复和即时事务回滚。|
|**Azure Monitor 资源日志**|Azure Synapse 现可通过直接与 Azure Monitor 资源日志集成，增强对分析工作负载的见解。 这项新功能使开发者可分析长时段内的工作负载行为，并针对查询优化或容量管理作出明智决策。 我们现在通过 [Azure Monitor 资源日志](../../azure-monitor/platform/data-platform.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#logs)引入了外部日志记录过程，这些日志提供了有关数据仓库工作负载的其他见解。 现在，只需单击一下按钮，即可使用 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 为历史查询性能故障排除功能配置资源日志。 Azure Monitor 资源日志支持通过将日志保存到存储帐户以进行审核来自定义保留期，将日志流式传输到事件中心以获得近实时遥测见解，以及通过日志查询使用 Log Analytics 来分析日志。 资源日志由数据仓库的遥测视图组成，相当于 Azure Synapse 中的 SQL Analytics 的最常用性能故障排除 DMV。 对于此初始版本，我们为以下系统动态管理视图启用了视图：<br/><br/>&bull; &nbsp; [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br/>&bull; &nbsp; [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)|
|**列存储内存管理**|随着压缩列存储行组数量的增加，管理这些行组的内部列段元数据所需的内存也会增加。  因此，针对某些列存储动态管理视图 (DMV) 执行的查询和查询性能可能会降级。  此版本中的改进旨在优化这些情况下的内部元数据大小，从而改善此类查询的体验并提高此类查询的性能。|
|**Azure Data Lake Storage Gen2 集成 (GA)**|Synapse Analytics 现在与 Azure Data Lake Storage Gen2 本机集成。 客户现在可以使用外部表将数据从 ABFS 加载到 Synapse SQL 池。 此功能使客户能够与 Data Lake Storage Gen2 中的 data lake 集成。|
|**值得注意的 Bug**|在 DW2000 等数据仓库上的小资源类中执行到 Parquet 的 CETAS 时发生失败 - 此项修复可正确识别 Create External Table As to Parquet 代码路径中的 null 引用。<br/><br/>在某些 CTAS 操作中可能会丢失标识列值 - 在执行 CTAS 到另一个表后，可能不会暂留标识列的值。 已在[博客](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)中报告。<br/><br/>在某些情况下，当查询仍在运行时，在会话终止时发生内部故障 - 在查询仍在运行时，如果终止会话，此修复会触发 InvalidOperationException。<br/><br/>（部署于 2018 年 11 月）尝试使用 Polybase 从 ADLS (Gen1) 加载多个小文件时，客户体验的性能不佳。 在 AAD 安全令牌验证期间，系统性能出现瓶颈。 通过启用安全令牌的缓存来缓解性能问题。 |
| | |

## <a name="next-steps"></a>后续步骤

- [创建 SQL 池](create-data-warehouse-portal.md)

## <a name="more-information"></a>详细信息

- [博客 - Azure Synapse Analytics](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [客户顾问团队博客](https://docs.microsoft.com/archive/blogs/sqlcat/)
- [客户成功案例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stackoverflow 论坛](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [视频](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 词汇表](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
