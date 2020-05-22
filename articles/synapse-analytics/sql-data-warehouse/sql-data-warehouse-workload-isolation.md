---
title: 工作负荷隔离
description: 关于在 Azure Synapse Analytics 中使用工作负荷组设置工作负荷隔离的指导。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c30429653c024c669d273c45d12236afa8cdbb83
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591499"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse 分析工作负荷组隔离

本文介绍了如何使用工作负荷组来配置工作负荷隔离、包含资源和应用用于执行查询的运行时规则。

## <a name="workload-groups"></a>工作负荷组

工作负荷组是一组请求的容器，是在系统上配置工作负荷管理（包括工作负荷隔离）的基础。  使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法创建工作负荷组。  一个简单的工作负荷管理配置可以管理数据负荷和用户查询。  例如，一个名为 `wgDataLoads` 的工作负荷组将为加载到系统中的数据定义工作负荷方面。 此外，名为 `wgUserQueries` 的工作负荷组将为运行查询以从系统读取数据的用户定义工作负荷方面。

以下各部分将重点介绍工作负荷组如何提供定义隔离、包含、请求资源定义以及遵守执行规则的功能。

## <a name="workload-isolation"></a>工作负荷隔离

工作负荷隔离意味着专门为工作负荷组保留资源。  通过在 [CREATE WORKLOAD GROUP ](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中将 MIN_PERCENTAGE_RESOURCE 参数配置为大于零的值，可以实现工作负荷隔离。  对于需要遵守严格 SLA 的连续执行工作负荷，隔离可确保资源始终可用于工作负荷组。

配置工作负荷隔离隐式定义了一个有保证的并发级别。 例如，将 `MIN_PERCENTAGE_RESOURCE` 设置为 30%，将 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 设置为 2% 的工作负荷组可以保证 15 个并发。  由于工作负荷组中始终会保留 15-2% 的资源槽（无论 `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` 配置如何），所以并发级别是有保证的。  如果 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 大于 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`，且 `CAP_PERCENTAGE_RESOURCE` 大于 `MIN_PERCENTAGE_RESOURCE`，则每个请求会增加额外的资源。  如果 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 和 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 相等，且 `CAP_PERCENTAGE_RESOURCE` 大于 `MIN_PERCENTAGE_RESOURCE`，则可以进行额外的并发。  请考虑以下方法以便确定有保证的并发：

[有保证的并发] = [`MIN_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> Min_percentage_resource 有特定的服务级别最小可行值。  有关详细信息，请参阅[有效值](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)。

如果没有工作负荷隔离，请求在资源的[共享池](#shared-pool-resources)中操作。  对共享池中资源的访问权限不能得到保证，按[重要性](sql-data-warehouse-workload-importance.md)分配。

在配置工作负荷隔离时应谨慎行事，因为即使工作负荷组中没有活动请求，也会将资源分配给工作负荷组。 过度配置隔离会导致系统整体利用率下降。

用户在配置工作负荷管理解决方案时应避免 100% 工作负荷隔离：当在所有工作负荷组中配置的 min_percentage_resource 总和等于 100% 时，就可以实现 100% 的隔离。  这种类型的配置过于严格和死板，几乎没有空间处理意外错误分类的资源请求。 提供一个预配以从未配置隔离的工作负荷组中执行一个请求。 分配给此请求的资源将在系统 DMV 中显示为零，并从系统保留资源中借用较小级别的资源授权。

> [!NOTE]
> 为了确保最佳资源利用率，可以考虑采用这样一种工作负荷管理解决方案，该解决方案利用一些隔离来确保满足 SLA，并与基于[工作负荷重要性](sql-data-warehouse-workload-importance.md)进行访问的共享资源混合在一起。

## <a name="workload-containment"></a>工作负荷限制

工作负荷限制指的是限制工作负荷组可以消耗的资源量。  通过在 [CREATE WORKLOAD GROUP ](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中将 CAP_PERCENTAGE_RESOURCE 参数配置为小于 100，可以实现工作负荷限制。  考虑这样一个场景：用户需要系统的读取访问权限，以便他们能够通过即席查询运行 what-if 分析。  这些类型的请求可能会对系统上正在运行的其他工作负荷产生负面影响。  配置工作负荷限制可确保使用有限的资源。

配置工作负荷限制隐式定义了最大的并发级别。  将 CAP_PERCENTAGE_RESOURCE 设置为 60%，并将 REQUEST_MIN_RESOURCE_GRANT_PERCENT 设置为 1%，则工作负荷组最多允许 60 并发级别。  请考虑下面包含的方法来确定最大并发：

[最大并发] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> 当创建的工作负荷组的 MIN_PERCENTAGE_RESOURCE 的级别大于零时，工作负荷组的有效 CAP_PERCENTAGE_RESOURCE 将不会达到 100%。  有关有效运行时值，请参阅 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="resources-per-request-definition"></a>每个请求定义的资源

工作负荷组提供了一种机制，用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 参数来定义每个请求分配的最小和最大资源量。  在这种情况下，资源是指 CPU 和内存。  配置这些值决定了系统上可以实现多少资源和什么级别的并发。

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT 是一个可选参数，默认是为 REQUEST_MIN_RESOURCE_GRANT_PERCENT 指定的相同值。

和选择资源类一样，配置 REQUEST_MIN_RESOURCE_GRANT_PERCENT 会设置请求所使用的资源值。  在开始执行请求之前，确保向请求分配由设定值指示的资源量。  对于从资源类迁移到工作负荷组的客户，可以考虑按照[操作说明](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)文章中的方法，首先将资源类映射到工作负荷组。

将 REQUEST_MAX_RESOURCE_GRANT_PERCENT 配置为大于 REQUEST_MIN_RESOURCE_GRANT_PERCENT 的值，可以让系统为每个请求分配更多的资源。  在计划请求时，系统根据共享池中的资源可用性和系统当前的负荷情况，确定实际向请求分配的资源，该资源分配值介于 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 之间。  计划查询时，资源必须存在于资源的[共享池](#shared-pool-resources)中。  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 的有效值取决于有效的 MIN_PERCENTAGE_RESOURCE 和 CAP_PERCENTAGE_RESOURCE 值。  有关有效运行时值，请参阅 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="execution-rules"></a>执行规则

在即席报告系统上，客户可能会不小心执行失控查询，严重影响他人的工作效率。  系统管理员不得不花时间终止失控查询，以释放系统资源。  工作负荷组提供了配置查询执行超时规则的功能，以取消超过指定值的查询。  该规则可通过设置 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 参数来配置。

## <a name="shared-pool-resources"></a>共享池资源

共享池资源是未配置隔离的资源。  将 MIN_PERCENTAGE_RESOURCE 设置为零的工作负荷组利用共享池中的资源来执行请求。  CAP_PERCENTAGE_RESOURCE 大于 MIN_PERCENTAGE_RESOURCE 的工作负荷组也会使用共享资源。  共享池中可用的资源量按如下方式计算。

[共享池] = 100 - [所有工作负荷组中 `MIN_PERCENTAGE_RESOURCE` 的总和]

对共享池中资源的访问权限会按[重要性](sql-data-warehouse-workload-importance.md)分配。  具有相同重要性级别的请求将按先进先出的方式访问共享池资源。

## <a name="next-steps"></a>后续步骤

- [快速入门：配置工作负荷隔离](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [将资源类转换为工作负荷组](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)。
- [工作负荷管理门户监视](sql-data-warehouse-workload-management-portal-monitor.md)。  
