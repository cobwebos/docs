---
title: 工作负荷隔离
description: 在 Azure 同步分析中设置与工作负荷组一起设置工作负载隔离的指南。
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
ms.openlocfilehash: 5d81dc1f4da6e952061496fa348d0f8e87b00b81
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742972"
---
# <a name="azure-synapse-analytics-workload-group-isolation-preview"></a>Azure 同步分析工作负荷组隔离（预览）

本文介绍如何使用工作负荷组来为查询执行配置工作负荷隔离、遏制资源和应用运行时规则。

## <a name="workload-groups"></a>工作负荷组

工作负荷组是一组请求的容器，也是在系统上配置工作负荷管理（包括工作负荷隔离）的基础。  工作负荷组是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法创建的。  简单的工作负荷管理配置可以管理数据加载和用户查询。  例如，名为 `wgDataLoads` 的工作负荷组定义将数据载入系统时的工作负荷行为。 此外，名为 `wgUserQueries` 的工作负荷组定义用户运行查询以从系统中读取数据时的工作负荷行为。

以下部分将重点介绍工作负荷组如何提供定义隔离、遏制、请求资源定义以及遵守执行规则的功能。

## <a name="workload-isolation"></a>工作负荷隔离

工作负荷隔离意味着资源保留给工作负荷组专用。  实现工作负荷隔离的方式是在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中将 MIN_PERCENTAGE_RESOURCE 参数配置为大于零。  对于需要遵守严格 SLA 的连续执行工作负荷，隔离可确保资源始终可供工作负荷组使用。

配置工作负荷隔离会隐式定义有保证的并发度。 例如，将设置为`MIN_PERCENTAGE_RESOURCE`30% 且`REQUEST_MIN_RESOURCE_GRANT_PERCENT`设置为 2% 的工作负载组保证 15 个并发性。  并发性级别得到保证，因为工作负载组中随时保留 15-2% 的资源插槽（无论配置方式如何`REQUEST_*MAX*_RESOURCE_GRANT_PERCENT`）。  如果`REQUEST_MAX_RESOURCE_GRANT_PERCENT`大于`REQUEST_MIN_RESOURCE_GRANT_PERCENT`和`CAP_PERCENTAGE_RESOURCE`大于每个请求`MIN_PERCENTAGE_RESOURCE`添加的其他资源。  如果`REQUEST_MAX_RESOURCE_GRANT_PERCENT``REQUEST_MIN_RESOURCE_GRANT_PERCENT`和 等于`CAP_PERCENTAGE_RESOURCE`且大于`MIN_PERCENTAGE_RESOURCE`，则可能增加并发。  假设使用以下方法来确定有保证的并发度：

[有保证的并发度] = [`MIN_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> min_percentage_resource 有特定的服务级别最小可行值。  有关更多详细信息，请参阅[有效值](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)。

如果缺少工作负荷隔离，请求将在资源的[共享池](#shared-pool-resources)中运行。  不保证能够访问共享池中的资源，这种访问权限是根据[重要性](sql-data-warehouse-workload-importance.md)分配的。

应该谨慎配置工作负荷隔离，因为即使工作负荷组中没有活动的请求，也会将资源分配到工作负荷组。 过度配置隔离可能导致总体系统利用率降低。

用户应避免使用配置 100% 工作负载隔离的工作负载管理解决方案：当在所有工作负载组中配置的min_percentage_resource总和等于 100% 时，可实现 100% 隔离。  此类配置过于苛严，几乎不会给意外错误分类的资源请求留有余地。 有一项预配允许通过未配置隔离的工作负荷组执行一个请求。 分配给此请求的资源在系统 DMV 中显示为零，并从系统保留的资源借用 smallrc 级别的资源授予。

> [!NOTE]
> 为了确保最佳资源利用率，请考虑实施一个利用某种隔离来确保符合 SLA，以与基于[工作负荷重要性](sql-data-warehouse-workload-importance.md)访问的共享资源混合的工作负荷管理解决方案。

## <a name="workload-containment"></a>工作负荷遏制

工作负荷遏制是指限制工作负荷组可以消耗的资源量。  实现工作负荷遏制的方式是在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中将 CAP_PERCENTAGE_RESOURCE 参数配置为小于 100。  假设用户需要对系统拥有读取访问权限，以便可以通过临时查询运行 what-if 分析。  此类请求可能会对系统上运行的其他工作负荷产生负面影响。  配置遏制可以确保资源用量受到限制。

配置工作负荷遏制会隐式定义最大并发度。  如果将 CAP_PERCENTAGE_RESOURCE 设置为 60% 并将 REQUEST_MIN_RESOURCE_GRANT_PERCENT 设置为 1%，则最多允许工作负荷组拥有 60 的并发度。  考虑以下用于确定最大并发度的方法：

[最大并发度] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> 创建 MIN_PERCENTAGE_RESOURCE 级别大于零的工作负荷组时，该工作负荷组的有效 CAP_PERCENTAGE_RESOURCE 不会达到 100%。  有关有效的运行时值，请参阅 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="resources-per-request-definition"></a>每个请求的资源量定义

工作负荷组提供一个机制，用于在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中使用 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 参数，定义针对每个请求分配的最小和最大资源量。  在本例中，资源是 CPU 和内存。  配置这些值决定了可用的资源量，以及可在系统上实现的并发度。

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT 是一个可选参数，默认值为针对 REQUEST_MIN_RESOURCE_GRANT_PERCENT 指定的相同值。

与选择资源类一样，配置 REQUEST_MIN_RESOURCE_GRANT_PERCENT 可设置请求利用的资源值。  在请求开始执行之前，保证为该请求分配设置值所指定的资源量。  对于从资源类迁移到工作负荷组的客户，首先请考虑遵循[操作指南](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)文章从资源类映射到工作负荷组。

将 REQUEST_MAX_RESOURCE_GRANT_PERCENT 配置为大于 REQUEST_MIN_RESOURCE_GRANT_PERCENT 的值将允许系统为每个请求分配更多的资源。  在计划请求时，系统会根据共享池中的资源可用性以及系统上的当前负载，确定分配给请求的实际资源（介于 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 之间）。  计划查询时，资源必须在资源的[共享池](#shared-pool-resources)中存在。  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 的有效值取决于有效的 MIN_PERCENTAGE_RESOURCE 和 CAP_PERCENTAGE_RESOURCE 值。  有关有效的运行时值，请参阅 [sys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="execution-rules"></a>执行规则

在临时报告系统上，客户可能会意外执行严重影响他人工作效率的失控查询。  系统管理员被迫花费时间来终止失控的查询，以释放系统资源。  工作负荷组提供所需的功能用于配置查询执行超时规则，以取消超过指定值的查询。  该规则是通过在 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中设置 `QUERY_EXECUTION_TIMEOUT_SEC` 参数配置的。

## <a name="shared-pool-resources"></a>共享池资源

共享池资源是未进行隔离配置的资源。  MIN_PERCENTAGE_RESOURCE 设置为零的工作负荷组将利用共享池中的资源来执行请求。  CAP_PERCENTAGE_RESOURCE 大于 MIN_PERCENTAGE_RESOURCE 的工作负荷组也使用共享资源。  共享池中可用资源量的计算公式如下。

[共享池] = 100 - [所有工作负荷组的 `MIN_PERCENTAGE_RESOURCE` 之和]

对共享池中资源的访问权限是根据[重要性](sql-data-warehouse-workload-importance.md)分配的。  重要性级别相同的请求将按先入/先出的原则访问共享池资源。

## <a name="next-steps"></a>后续步骤

- [快速入门：配置工作负荷隔离](quickstart-configure-workload-isolation-tsql.md)
- [创建工作负载组](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [将资源类转换为工作负荷组](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)。
- [工作负载管理门户监视](sql-data-warehouse-workload-management-portal-monitor.md)。  
