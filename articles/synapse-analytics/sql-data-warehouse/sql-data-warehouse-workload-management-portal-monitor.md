---
title: 工作负荷管理门户监视
description: 有关 Azure Synapse Analytics 中的工作负荷管理门户监视的指导。
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
ms.openlocfilehash: a79e6fb2be717b5ecee243b26824039630e1a416
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80744289"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics – 工作负荷管理门户监视（预览版）

本文介绍如何监视[工作负荷组](sql-data-warehouse-workload-isolation.md#workload-groups)资源利用率和查询活动。
有关如何配置 Azure 指标资源管理器的详细信息，请参阅 [Azure 指标资源管理器入门](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)一文。  有关如何监视系统资源使用情况的详细信息，请参阅 Azure Synapse Analytics 监视文档中的[资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization)部分。
提供了两种不同类别的工作负荷组指标用于监视工作负荷管理：资源分配和查询活动。  可按工作负荷组拆分和筛选这些指标。  可以根据指标是系统定义的（资源类工作负荷组）还是用户定义的（由用户使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法创建），来拆分和筛选指标。

## <a name="workload-management-metric-definitions"></a>工作负荷管理指标定义

|指标名称                    |说明  |聚合类型 |
|-------------------------------|-------------|-----------------|
|有效上限资源百分比 | “有效上限资源百分比”是对工作负荷组可访问的资源百分比施加的硬性限制，它考虑到了为其他工作负荷组分配的“有效最小资源百分比”。   “有效上限资源百分比”指标是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `CAP_PERCENTAGE_RESOURCE` 参数配置的。   此处描述了有效值。<br><br>例如，如果工作负荷组 `DataLoads` 是使用 `CAP_PERCENTAGE_RESOURCE` = 100 创建的，另一个工作负荷组是使用“有效最小资源百分比”25% 创建的，则 `DataLoads` 工作负荷组的“有效上限资源百分比”为 75%。 <br><br>“有效上限资源百分比”确定了工作负荷组可以实现的并发性（因而也包括可实现的潜在吞吐量）的上限。   如果除了“有效上限资源百分比”指标当前报告的吞吐量以外，还需要分配更多的吞吐量，请增大 `CAP_PERCENTAGE_RESOURCE`、减小其他工作负荷组的 `MIN_PERCENTAGE_RESOURCE`，或者纵向扩展实例以添加更多资源。   减小 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会提高并发性，但可能不会提高整体吞吐量。| 最小值、平均值、最大值 |
|有效最小资源百分比 |“有效最小资源百分比”是为工作负荷组保留并隔离的资源的最小百分比，它考虑到了最低的服务级别。   “有效最小资源百分比”指标是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `MIN_PERCENTAGE_RESOURCE` 参数配置的。  [此处](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#effective-values)描述了有效值。<br><br>未筛选并拆分此指标时，请使用“总和”聚合类型来监视系统上配置的总体工作负荷隔离。<br><br>“有效最小资源百分比”确定工作负荷组可以实现的有保证的并发性（因而也包括有保证的吞吐量）下限。   如果除了“有效最小资源百分比”指标当前报告的资源以外，还需要分配更多的有保证资源，请增大为工作负荷组配置的 `MIN_PERCENTAGE_RESOURCE` 参数。   减小 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会提高并发性，但可能不会提高整体吞吐量。 |最小值、平均值、最大值|
|工作负荷组活动查询  |此指标报告工作负荷组中的活动查询。  在不筛选且不拆分的情况下使用此指标会显示系统上运行的所有活动查询。|Sum         |
|按最大资源百分比列出的工作负荷组分配 |此指标显示相对于每个工作负荷组的“有效上限资源百分比”的资源分配百分比。   此指标提供工作负荷组的有效利用率。<br><br>假设工作负荷组 `DataLoads` 的“有效上限资源百分比”为 75%，`REQUEST_MIN_RESOURCE_GRANT_PERCENT` 配置为 25%。   如果此工作负荷组中只运行了一个查询，则筛选为 `DataLoads` 的“按最大资源百分比列出的工作负荷组分配”值将是 33% (25% / 75%)。 <br><br>使用此指标标识工作负荷组的利用率。  接近 100% 的值表示正在使用工作负荷组的所有可用资源。  此外，如果同一工作负荷组的“工作负荷组排队查询指标”显示大于零的值，则表示该工作负荷组将利用额外的资源（如果已分配）。   相反，如果此指标持续较低，并且“工作负荷组活动查询”较低，则表示未利用该工作负荷组。   尤其是当“有效上限资源百分比”大于零时，这种情况会造成问题，因为这表示发生了[利用不足的工作负荷隔离](#underutilized-workload-isolation)。 |最小值、平均值、最大值 |
|按系统百分比列出的工作负荷组分配 | 此指标显示相对于整个系统的资源分配百分比。<br><br>假设工作负荷组 `DataLoads` 的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 配置为 25%。  如果此工作负荷组中只运行了一个查询，则筛选为 `DataLoads` 的“按系统百分比列出的工作负荷组分配”值将是 25% (25% / 100%)。 |最小值、平均值、最大值 |
|工作负荷组查询超时 |已超时的工作负荷组查询。只有在查询开始执行查询后，此指标才报告查询超时（不包括由于锁定或资源等待而导致的等待时间）。<br><br>查询超时是使用 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法中的 `QUERY_EXECUTION_TIMEOUT_SEC` 参数配置的。  增大该值可以减少查询超时次数。<br><br>请考虑增大工作负荷组的 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 参数，以减少超时次数并为每个查询分配更多的资源。  请注意，增大 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 会减少工作负荷组的并发量。 |Sum |
|工作负荷组排队查询 | 当前已排队等待开始执行的工作负荷组查询。  当查询等待资源或锁时，可将其排队。<br><br>查询可能会出于许多原因而处于等待状态。  如果系统过载并且并发需求超过了可用的资源，则查询将会排队。<br><br>请考虑增大 [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语句中的 `CAP_PERCENTAGE_RESOURCE` 参数，将更多资源添加到工作负荷组。  如果 `CAP_PERCENTAGE_RESOURCE` 大于“有效上限资源百分比”指标，则为其他工作负荷组配置的工作负荷隔离会影响分配到此工作负荷组的资源。   请考虑降低其他工作负荷组的 `MIN_PERCENTAGE_RESOURCE`，或纵向扩展实例以添加更多资源。 |Sum |

## <a name="monitoring-scenarios-and-actions"></a>监视方案和操作

下面是一系列图表配置，其中重点介绍了用于故障排除的工作负荷管理指标的用法，以及解决问题的相关操作。

### <a name="underutilized-workload-isolation"></a>利用不足的工作负荷隔离

假设在以下工作负荷组和分类器配置中，创建了名为 `wgPriority` 的工作负荷组，*TheCEO* `membername` 使用 `wcCEOPriority` 工作负荷分类器映射到该工作负荷组。  为 `wgPriority` 工作负荷组配置了 25% 的工作负荷隔离 (`MIN_PERCENTAGE_RESOURCE` = 25)。  为 *TheCEO* 提交的每个查询分配了 5% 的系统资源 (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5)。

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

下面是图表的配置方式：<br>
指标 1：*有效最小资源百分比*（“平均值”聚合，`blue line`）<br>
指标 2：*按系统百分比列出的工作负荷组分配*（“平均值”聚合，`purple line`）<br>
筛选器：[工作负荷组] = `wgPriority`<br>
![underutilized-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) 该图表显示，使用 25% 工作负荷隔离时，平均只使用了 10%。  在这种情况下，可将 `MIN_PERCENTAGE_RESOURCE` 参数值降低至 10 到 15 之间，并允许系统中的其他工作负荷消耗资源。

### <a name="workload-group-bottleneck"></a>工作负荷组瓶颈

假设在以下工作负荷组和分类器配置中，创建了名为 `wgDataAnalyst` 的工作负荷组，*DataAnalyst* `membername` 使用 `wcDataAnalyst` 工作负荷分类器映射到该工作负荷组。  为 `wgDataAnalyst` 工作负荷组配置了 6% 的工作负荷隔离 (`MIN_PERCENTAGE_RESOURCE` = 6)，资源限制为 9% (`CAP_PERCENTAGE_RESOURCE` = 9)。  为 *DataAnalyst* 提交的每个查询分配了 3% 的系统资源 (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3)。

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

下面是图表的配置方式：<br>
指标 1：*有效上限资源百分比*（“平均值”聚合，`blue line`）<br>
指标 2：*按最大资源百分比列出的工作负荷组分配*（“平均值”聚合，`purple line`）<br>
指标 3：*工作负荷组排队查询*（“总和”聚合，`turquoise line`）<br>
筛选器：[工作负荷组] = `wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) 该图表显示，使用 9% 的资源上限时，工作负荷组的利用率为 90% 以上（从“按最大资源百分比列出的工作负荷组分配”指标可以看出）。   从“工作负荷组排队查询”指标可以看到，查询正在稳定排队。   在这种情况下，将 `CAP_PERCENTAGE_RESOURCE` 值增大至 9% 以上可让更多查询并行执行。  增大 `CAP_PERCENTAGE_RESOURCE` 的假设条件是有足够的可用资源，并且其他工作负荷组未隔离资源。  检查“有效上限资源百分比”指标来确认上限是否增大。   如果需要更高的吞吐量，另请考虑将 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 值增大至 3 以上。  增大 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 可以提高查询的运行速度。

## <a name="next-steps"></a>后续步骤

- [快速入门：使用 T-SQL 配置工作负荷隔离](quickstart-configure-workload-isolation-tsql.md)<br>
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)<br>
- [监视资源利用率](sql-data-warehouse-concept-resource-utilization-query-activity.md)
