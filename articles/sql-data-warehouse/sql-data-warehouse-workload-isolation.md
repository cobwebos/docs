---
title: 工作负荷隔离
description: 有关在 Azure SQL 数据仓库中设置工作负荷组的工作负荷隔离的指南。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/23/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 86390132be0440b197b680803e5b6032670a7d1c
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721024"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL 数据仓库工作负荷组隔离（预览）

本文介绍了如何使用工作负荷组来配置工作负荷隔离、包含资源和应用用于执行查询的运行时规则。

## <a name="workload-groups"></a>工作负荷组

工作负荷组是一组请求的容器，是在系统上配置工作负荷管理（包括工作负荷隔离）的基础。  使用 "[创建工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)" 语法创建工作负荷组。  简单的工作负荷管理配置可以管理数据加载和用户查询。  例如，名为 `wgDataLoads` 的工作负荷组将定义加载到系统中的数据的工作负荷部分。 此外，名为 `wgUserQueries` 的工作负荷组将为运行查询的用户定义工作负荷方面，以便从系统读取数据。

以下各部分将重点介绍工作负荷组如何提供定义隔离、包含、请求资源定义以及遵循执行规则的功能。

## <a name="workload-isolation"></a>工作负荷隔离

工作负荷隔离意味着工作负荷组仅保留资源。  通过在[创建工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中将 MIN_PERCENTAGE_RESOURCE 参数配置为大于零，实现工作负荷隔离。  对于需要遵守严格 Sla 的连续执行工作负荷，隔离确保资源始终可用于工作负荷组。 

配置工作负荷隔离会隐式定义保证的并发级别。 例如，将 `MIN_PERCENTAGE_RESOURCE` 设置为30% 且 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 设置为2% 的工作负荷组保证15并发。  保证并发级别，因为在工作负荷组中始终保留15-2% 的资源插槽（无论 `REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` 如何配置）。  如果 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 大于 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 并且 `CAP_PERCENTAGE_RESOURCE` 大于 `MIN_PERCENTAGE_RESOURCE` 每个请求添加的其他资源。  如果 `REQUEST_MAX_RESOURCE_GRANT_PERCENT` 和 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 相等并且 `CAP_PERCENTAGE_RESOURCE` 大于 `MIN_PERCENTAGE_RESOURCE`，则可以进行其他并发。  请考虑以下方法来确定有保证的并发：

[保证并发] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> Min_percentage_resource 有特定的服务级别最小可行值。  有关详细信息，请参阅[有效值](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)了解更多详细信息。

如果没有工作负荷隔离，请求会在资源[共享池中](#shared-pool-resources)操作。  不能保证对共享池中资源的访问权限，并且会按[重要性](sql-data-warehouse-workload-importance.md)进行分配。

如果将资源分配给工作负荷组，即使工作负荷组中没有活动请求，也应小心地配置工作负荷隔离。 过度配置隔离可能会降低整体系统利用率。

用户应避免使用配置100% 工作负荷隔离的工作负荷管理解决方案：如果在所有工作负荷组中配置的 min_percentage_resource 的总和等于100%，则实现100% 的隔离。  这种类型的配置过于严格且非常严格，几乎不需要对意外进行错误分类的资源请求留出空间。 有一个设置允许从未配置为进行隔离的工作负荷组执行一个请求。 分配给此请求的资源将在系统 Dmv 中显示为零，并借用系统保留资源的资源授权的 smallrc 级别。

> [!NOTE] 
> 若要确保最佳资源利用率，请考虑使用某些隔离的工作负荷管理解决方案，以确保满足 Sla，并使用根据[工作负荷重要性](sql-data-warehouse-workload-importance.md)访问的共享资源进行混合。

## <a name="workload-containment"></a>工作负荷包含

工作负荷包含关系是指限制工作负荷组可以使用的资源量。  通过在[创建工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中将 CAP_PERCENTAGE_RESOURCE 参数配置为小于100，可以实现工作负荷包容。  考虑到用户需要系统的读取访问权限，以便他们可以通过即席查询运行假设分析。  这些类型的请求可能会对系统上运行的其他工作负荷产生负面影响。  配置包容可以确保资源量的限制。

配置工作负荷包含隐式定义并发的最高级别。  将 CAP_PERCENTAGE_RESOURCE 设置为60%，并将 REQUEST_MIN_RESOURCE_GRANT_PERCENT 设置为1%，则工作负荷组允许最多60并发级别。  请考虑下面包含的方法来确定最大并发性：

[Max Concurrency] = [`CAP_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> 如果创建了级别大于零的 MIN_PERCENTAGE_RESOURCE 的工作负荷组，则工作负荷组的有效 CAP_PERCENTAGE_RESOURCE 将不会达到100%。  有关有效运行时值，请参阅[dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) 。

## <a name="resources-per-request-definition"></a>每个请求定义的资源

工作负荷组提供一种机制，用于使用[CREATE 工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中的 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 参数来定义每个请求分配的最小和最大资源量。  在这种情况下，资源是 CPU 和内存。  配置这些值决定了在系统上可以实现的资源量和并发级别。

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT 是一个可选参数，默认值为为 REQUEST_MIN_RESOURCE_GRANT_PERCENT 指定的相同值。

与选择资源类一样，配置 REQUEST_MIN_RESOURCE_GRANT_PERCENT 将为请求使用的资源设置值。  在开始执行之前，由集值指示的资源量可保证分配给请求。  对于从资源类迁移到工作负荷组的用户，请考虑遵循[如何](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)将资源类映射到工作负荷组作为起点。

如果将 REQUEST_MAX_RESOURCE_GRANT_PERCENT 配置为大于 REQUEST_MIN_RESOURCE_GRANT_PERCENT 的值，则允许系统为每个请求分配更多资源。  在计划请求时，系统会根据共享池中的资源可用性和当前负载，在 REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 之间确定到请求的实际资源分配。主板.  计划查询时，资源必须存在于共享的资源[池中](#shared-pool-resources)。  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT 和 REQUEST_MAX_RESOURCE_GRANT_PERCENT 具有的有效值取决于有效的 MIN_PERCENTAGE_RESOURCE 和 CAP_PERCENTAGE_RESOURCE 值。  有关有效运行时值，请参阅[dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest) 。

## <a name="execution-rules"></a>执行规则

在即席报表系统上，客户可能会意外地执行失控的查询，这会严重影响他人的工作效率。  系统管理员被迫花费时间终止失控的查询以释放系统资源。  工作负荷组提供配置查询执行超时规则以取消超过指定值的查询的功能。  规则是通过在[CREATE 工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)语法中设置 `QUERY_EXECUTION_TIMEOUT_SEC` 参数来配置的。

## <a name="shared-pool-resources"></a>共享池资源

共享池资源是没有为隔离配置的资源。  MIN_PERCENTAGE_RESOURCE 设置为零的工作负荷组利用共享池中的资源来执行请求。  CAP_PERCENTAGE_RESOURCE 大于 MIN_PERCENTAGE_RESOURCE 的工作负荷组也使用共享资源。  共享池中可用的资源量按如下方式计算。

[共享池] = 100-[所有工作负荷组之间的 `MIN_PERCENTAGE_RESOURCE` 总数]

对共享池中的资源的访问权限将按[重要性](sql-data-warehouse-workload-importance.md)分配。  具有相同重要性级别的请求将按先进先出的方式访问共享池资源。

## <a name="next-steps"></a>后续步骤

- [快速入门：配置工作负荷隔离](quickstart-configure-workload-isolation-tsql.md)
- [创建工作负荷组](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [将资源类转换为工作负荷组](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)。
- [管理门户监视工作负荷](sql-data-warehouse-workload-management-portal-monitor.md)。  
